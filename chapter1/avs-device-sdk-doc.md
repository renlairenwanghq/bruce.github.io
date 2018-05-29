# Alexa Voice Service Overview(v20160207)

## 1. 概述

Alexa语音服务（AVS）使开发人员可以通过话筒和扬声器为连接的产品提供语音功能。 一旦集成，您的产品将可以访问Alexa的内置功能（如音乐播放，定时器和闹钟，包裹跟踪，电影列表，日历管理等）以及使用Alexa技能套件开发的第三方技能。

AVS由与客户端功能相对应的接口组成，如语音识别，音频回放和音量控制。 每个接口都包含称为指令和事件的逻辑分组消息。 指令是云中发送的消息，指示客户采取行动。 事件是从客户端发送到云端的消息，通知Alexa发生了一些事情。

API使用Login with Amazon（LWA）进行产品授权并公开HTTP / 2端点。

## 2. Authorization

LWA : Login with Amazon (LWA)

要访问AVS API，您的产品需要获得使用亚马逊（LWA）访问令牌的登录名称，该令牌可授予产品访问权限，以代表客户访问API。

###2.1 Remote Authorization

- [Authorize from a Companion Site](https://developer.amazon.com/docs/alexa-voice-service/authorize-companion-site.html)
- [Authorize from a Companion App](https://developer.amazon.com/docs/alexa-voice-service/authorize-companion-app.html)

###2.2 Local Authorization

[Authorize from an AVS Product](https://developer.amazon.com/docs/alexa-voice-service/authorize-on-product.html)

###2.3 Code Based Linking(CBL)

[Code Based Linking for Other Devices and Platforms](https://developer.amazon.com/docs/alexa-voice-service/code-based-linking-other-platforms.html)

CBL主要包含如下过程:

* 使能 CBL
*  认证请求
* 引导用户登录
* 获取token和refreshToken
* token过期时refresh

###2.4 认证过程整理

* 初始化，设置成员，并创建状态机的线程

```
[SampleApplication.cpp] 
[SampleApplication::initialize] 
[authorization::cblAuthDelegate::CBLAuthDelegate::create]

==> [CBLAuthDelegate.cpp] 
	[CBLAuthDelegate::create] 
	[new CBLAuthDelegate]
	
==>	[CBLAuthDelegate.cpp] 
	[CBLAuthDelegate::create] 
    [instance->init(configuration, deviceInfo)]
		
    ==> [CBLAuthDelegate.cpp] 
        [CBLAuthDelegate::init] 
        [handleAuthorizationFlow]
```

上述的初始化过程的结果是设置了CBLAuthDelegate的某些引用：

```
//CBLAuthDelegate中成员
CustomerDataHandler : 对应SampleApplication::initialize中创建的customerDataManager对象，是CustomerDataManager类型
m_storage　: 对应SampleApplication::initialize中创建的authDelegateStorage对象，是SQLiteCBLAuthDelegateStorage类型
m_httpPost : 对应CBLAuthDelegate::create中创建的httpPost对象，是HttpPost类型
m_authRequester : 对应SampleApplication::initialize中创建的userInterfaceManager对象,该对象实现了CBLAuthRequesterInterface接口
```

* 认证状态机

```
CBLAuthDelegate::handleAuthorizationFlow()
```

* 初始时状态是`FlowState::STARTING`

```
nextFlowState = handleStarting();
```

* 判断能否获取到`RefreshToken`,目前数据库中还没有`refreshToken`,所以置状态为`FlowState::REQUESTING_CODE_PAIR`

```
if (m_storage->getRefreshToken(&m_refreshToken)) {
	return FlowState::REFRESHING_TOKEN;
}
```

* 进入`FlowState::REQUESTING_CODE_PAIR`状态的处理函数,发送`requestCodePair`,并等待回复。

```
auto result = receiveCodePairResponse(requestCodePair());
```

* `requestCodePair`操作是由`m_httpPost->doPost`来完成的。

```
requestCodePair()
==> m_httpPost->doPost
```

* `receiveCodePairResponse`中收到回复时，m_authRequester在收到`m_userCode`和`verificationUri`，会将其输出到终端上

```
m_authRequester->onRequestAuthorization(verificationUri, m_userCode);
```

* 输出到终端，引导用户`log in`

```
To authorize, browse to: 'https://amazon.com/us/code' and enter the code: D35UTP   
```

* 上面获取到`CodePairResponse`之后，改变状态，进行获取token和requestToken的操作,在获取token之前会判断CodePair是否已经失效

```
handleRequestingToken()
==> std::chrono::steady_clock::now() >= m_codePairExpirationTime
==> m_authRequester->onCheckingForAuthorization();
==> auto result = receiveTokenResponse(requestToken(), true);
```

* 获取到token和refreshToken之后改变状态，在需要的时候执行refresh操作

```
handleRefreshingToken()
==> auto result = receiveTokenResponse(requestRefresh(), false);
```



## ３. Transport Protocol

传输协议部分主要包含两块内容:

- 管理HTTP2的连接

  [Managing an HTTP/2 Connection](https://developer.amazon.com/docs/alexa-voice-service/manage-http2-connection.html)

- 构建HTTP / 2请求

  [Structuring an HTTP/2 Request](https://developer.amazon.com/docs/alexa-voice-service/structure-http2-request.html) 

关键词解释:

Stream：在HTTP / 2连接中在客户端和服务器之间交换的一个独立的双向帧序列。

Downchannel：您在HTTP / 2连接中创建的流，用于将指令从云端传递到客户端。 下行信道在设备的半关闭状态下保持打开状态，并在连接期间从AVS打开。 下行频道主要用于将云启动的指令和音频附件发送到您的客户端。



#### 3.1 Prerequisites 

传输协议部分的处理，是在认证成功的前提之下完成的。

####3.2 Creating an HTTP/2 Connection

当您的产品开机时，它应该与AVS创建一个HTTP2连接。 此连接用于处理所有指令和事件，包括在下行通道流上发送到客户端的任何内容。 

维护与AVS的连接需要两件事情：

- Establishing the downchannel stream
- Synchronizing your product's component states with AVS ([SpeechRecognizer](https://developer.amazon.com/docs/alexa-voice-service/context.html#recognizerstate), [AudioPlayer](https://developer.amazon.com/docs/alexa-voice-service/context.html#playbackstate), [Alerts](https://developer.amazon.com/docs/alexa-voice-service/context.html#alertsstate), [Speaker](https://developer.amazon.com/docs/alexa-voice-service/context.html#volumestate), [SpeechSynthesizer](https://developer.amazon.com/docs/alexa-voice-service/context.html#speechstate))

在同步状态之后，客户端应该能够使用此连接来

- 发送事件并从AVS接收指令
  * **Note:** 每个事件及其关联的响应都在单个事件流上发送。 当收到响应时，流应该关闭
- 通过downchannel接收云启动的指令



#### 3.3 相关代码调用栈

代码的调用栈方式按如下形式描述，每个块包含三行内容，分别是文件，函数，和某一行关键代码。

```
[file]
[func] 
[code]
```

****

**创建线程networkLoop线程**

其中`transport`的类型是`HTTP2Transport`类型

```
[SampleApplication.cpp]
[SampleApplication::initialize]
[client->connect(m_dcfDelegate, endpoint);]

	==> [DefaultClient.cpp]
		[DefaultClient::connect]
		[m_connectionManager->setAVSEndpoint(avsEndpoint);]
		
		==> [AVSConnectionManager.cpp]
			[AVSConnectionManager::setAVSEndpoint]
			[m_messageRouter->setAVSEndpoint(avsEndpoint);]
			
			==> [MessageRouter.cpp]
			    [MessageRouter::setAVSEndpoint]
			    [createActiveTransportLocked();]
			   
                ==> [MessageRouter.cpp]
                    [MessageRouter::createActiveTransportLocked()]
                    [transport　= m_transportFactory->createTransport]

                    ==> [MessageRouter.cpp]
                        [MessageRouter::createActiveTransportLocked()]
                        [transport->connect()]
			   	   
			   	    ==> [HTTP2Transport.cpp]
			   	   	    [HTTP2Transport::connect()]
			   	   	    [avsCommon::utils::libcurlUtils::
			   	   	     CurlMultiHandleWrapper::create();]
			   	   	     
			   	   	    ==> [HTTP2Transport.cpp]
			   	   	    	[HTTP2Transport::connect()]
			   	   	    	[std::thread(&HTTP2Transport::networkLoop, this);]
```

**线程networkLoop的处理**

*  在establishConnection中建立了downchannel并且设置好了回调函数writeCallback和headerCallback,通过downchannel可以获取从服务器发过来的数据

```
[HTTP2Transport.cpp]
[HTTP2Transport::networkLoop()]
[establishConnection()]

	==> [HTTP2Transport.cpp]
		[HTTP2Transport::establishConnection()]
		[setupDownchannelStream()]
		
		==> [HTTP2Transport.cpp]
			[HTTP2Transport::setupDownchannelStream()]
			[m_streamPool.createGetStream]
			
			==>[HTTP2StreamPool.cpp]
			   [HTTP2StreamPool::createGetStream]
			   [HTTP2StreamPool::getStream]
			   
			   ==>[HTTP2StreamPool.cpp]
			   	  [HTTP2StreamPool::getStream]
			   	  [std::make_shared<HTTP2Stream>(messageConsumer, m_attachmentManager);]
			   	  
			==>[HTTP2StreamPool.cpp]
			   [HTTP2StreamPool::createGetStream]
			   [stream->initGet(url, authToken)]
			   
			   ==>[HTTP2Stream.cpp]
			   	  [HTTP2Stream::initGet]
			   	  [setCommonOptions(url, authToken)]
			   	  
			   	  ==>[HTTP2Stream.cpp]
			   	     [HTTP2Stream::setCommonOptions]
			   	     [m_transfer.setWriteCallback(&HTTP2Stream::writeCallback, this)]
			   	     HTTP2Stream
			   	  ==>[HTTP2Stream.cpp]
			   	     [HTTP2Stream::setCommonOptions]
			   	     [m_transfer.setHeaderCallback(&HTTP2Stream::headerCallback, this)]
```

* 发送一个`get request`到`/API_version/directives`

```
networkLoop():
const std::string url = m_avsEndpoint + AVS_DOWNCHANNEL_URL_PATH_EXTENSION;
m_downchannelStream = m_streamPool.createGetStream(url, authToken, m_messageConsumer);
```

* 将请求添加到发送的队列中

```
 m_activeStreams.insert(ActiveTransferEntry(m_downchannelStream->getCurlHandle(), m_downchannelStream));
```

* event消息

```
networkLoop():
processNextOutgoingMessage();
--> std::shared_ptr<HTTP2Stream> stream = m_streamPool.createPostStream
--> m_activeStreams.insert(ActiveTransferEntry(stream->getCurlHandle(), stream));
```

**接收数据**

代码的注释中对writeCallback的解释是从服务器接收数据时执行的回调函数。

接下来看看writeCallback中是如何处理的？

首先writeCallback的调用应该是在curl中完成的，对于sdk只要设置好回调函数即可。

```
HTTP2Stream::writeCallback
--> stream->getResponseCode()
--> stream->m_parser.feed(data, numChars);
	-->m_multipartReader.feed(data, length);
```

收到的数据的解析操作交由m_parser处理，其中的回调函数是:

```
//在multipart部分开始时调用的回调函数
 static void partBeginCallback(const MultipartHeaders& headers, void* userData);
//当MIME部分的数据可用时调用的回调函数
static void partDataCallback(const char* buffer, size_t size, void* userData);
//multipart部分结束时调用的回调函数
static void partEndCallback(void* userData);
```

查看回调函数partEndCallback中的处理，其中m_messageConsumer的类型是MessageRouter

```
[MimeParse.cpp]
[MimeParse中进行回调]
[MimeParser::partEndCallback(void* userData)]
	==> [MimeParser.cpp]
		[MimeParser::partEndCallback(void* userData)]
		[parser->m_messageConsumer->consumeMessage(
                    parser->m_attachmentContextId, parser->m_directiveBeingReceived);]
                    
    ==> [MimeParser.cpp]
    	[MimeParser::partEndCallback(void* userData)]
    	[notifyObserverOnReceive(contextId, message);]
    	
    	==> [MessageRouter.cpp]
    		[MessageRouter::notifyObserverOnReceive]
    		[temp->receive(contextId, message);]
```

其中`m_parser`是`MimeParser`类型,是创建stream时初始化的，下面是设置该成员的流程

```
--> HTTP2Transport::setupDownchannelStream()
	-->stream = m_testableStreamPool->createGetStream
		-->stream = getStream(messageConsumer);
			-->result = std::make_shared<HTTP2Stream>(messageConsumer, m_attachmentManager);
				-->在初始化函数中通过参数形式的初始化m_parser{messageConsumer, attachmentManager}
```

根据上述的调用栈中`parser->m_messageConsumer->consumeMessage`来看，会调用到MessageRouter的consumeMessage接口。接下来看`MessageRouter::consumeMessage`做了那些操作:

```
[MimeParser.cpp]
[MimeParser::partEndCallback(void* userData)]
[parser->m_messageConsumer->consumeMessage]
	==> [MessageRouter.cpp]
		[MessageRouter::consumeMessage]
		[MessageRouter::notifyObserverOnReceive]
		
		==>[MessageRouter.cpp]
		   [MessageRouter::notifyObserverOnReceive]
		   [temp->receive(contextId, message);]
		   
		   ==> [AVSConnectionManager.cpp]
		   	   [AVSConnectionManager::receive]
		   	   [observer->receive(contextId, message);]
		   	  
		   	   ==> [MessageInterpreter.cpp]
		   	  	   [MessageInterpreter::receive]
		   	  	   [m_directiveSequencer->onDirective(avsDirective);]
```

按照如下的过程，可以确定`temp->receive`的执行可以调用到`AVSConnectionManager::receive`接口

```
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_messageRouter = std::make_shared<acl::MessageRouter>(authDelegate, attachmentManager, transportFactory);]

[DefaultClient.cpp]
[DefaultClient::initialize]
[m_connectionManager =
        acl::AVSConnectionManager::create(m_messageRouter, false, connectionObservers, {m_dialogUXStateAggregator});]
        
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_connectionManager =
        acl::AVSConnectionManager::create(m_messageRouter, false, connectionObservers, {m_dialogUXStateAggregator});]
        
==> [AVSConnectionManager.cpp]
    [AVSConnectionManager::create]
    [messageRouter->setObserver(connectionManager);]
```

上面`AVSConnectionManager::receive`接口中调用的`observer->receive(contextId, message);`,如何又是确定`observer`是`MessageInterpreter`的呢，参考如下过程?

```
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_connectionManager =
        acl::AVSConnectionManager::create(m_messageRouter, false, connectionObservers, {m_dialogUXStateAggregator});]
        
[DefaultClient.cpp]
[DefaultClient::initialize]
[auto messageInterpreter =
        std::make_shared<adsl::MessageInterpreter>(m_exceptionSender, m_directiveSequencer, attachmentManager);]
        
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_connectionManager->addMessageObserver(messageInterpreter);]

	==> [AVSConnectionManager.cpp]
		[AVSConnectionManager::addMessageObserver]
		[m_messageObservers.insert(observer);]
```

`MessageInterpreter::receive`接口接收到传递过来的数据后，填充`avsDirective`接下来的过程是将命令添加到队列中，并通知有命令到达

其中`receive`接口中的`Document document;`是用于将JSON文本解析为DOM的文档类型。

```
[MessageInterpreter.cpp]
[MessageInterpreter::receive]
[m_directiveSequencer->onDirective(avsDirective);]

==> [MessageInterpreter.cpp]
	[MessageInterpreter::receive]
	[std::shared_ptr<AVSDirective> avsDirective =
        AVSDirective::create(message, avsMessageHeader, payload, m_attachmentManager, contextId);]
        
==> [MessageInterpreter.cpp]
	[MessageInterpreter::receive] 	
	[m_directiveSequencer->onDirective(avsDirective);]

	==>	[DirectiveSequencer.cpp] 
		[DirectiveSequencer::onDirective]
		[m_receivingQueue.push_back(directive);] : 添加到队列

		==> [DirectiveSequencer.cpp] 
			[DirectiveSequencer::onDirective] 
			[m_wakeReceivingLoop.notify_one();]　通知有命令到达
```

**取出命令，进行处理的流程**

`DirectiveSequencer`在初始化时会生成一个线程`receiveingLoop`

```
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_directiveSequencer = adsl::DirectiveSequencer::create(m_exceptionSender);]

	==> [DirectiveSequencer.cpp]
		[DirectiveSequencer::create]
		[new DirectiveSequencer(exceptionSender)]

			==> [DirectiveSequencer.cpp]
				[DirectiveSequencer::DirectiveSequencer]
				[m_receivingThread = std::thread(&DirectiveSequencer::receivingLoop, this);]
```

`receiveingLoop`线程会等待命令到达的通知，之后进行分发处理

```
[DirectiveSequencer.cpp]
[DirectiveSequencer::DirectiveSequencer]
[DirectiveSequencer::receivingLoop()]

==> [DirectiveSequencer.cpp] 
	[DirectiveSequencer::receivingLoop()] 
	[m_wakeReceivingLoop.wait(lock, wake)]

	==> [DirectiveSequencer.cpp] 
		[DirectiveSequencer::receivingLoop()] 
		[receiveDirectiveLocked(lock);]
		
		==> [DirectiveSequencer.cpp] 
			[DirectiveSequencer::receiveDirectiveLocked] 
			[auto directive = m_receivingQueue.front();]
	
		==> [DirectiveSequencer.cpp] 
			[DirectiveSequencer::receiveDirectiveLocked] 
			[handled = m_directiveRouter.handleDirectiveWithPolicyHandleImmediately(directive);]
			
			==>	[DirectiveRouter.cpp]
        		[DirectiveRouter::handleDirectiveWithPolicyHandleImmediately] 
				[auto handlerAndPolicy = getHandlerAndPolicyLocked(directive);]:获取对应能力的handlerAndPolicy
				
				==> [DirectiveRouter.cpp]
					[DirectiveRouter::getHandlerAndPolicyLocked]
					[m_configuration.find(NamespaceAndName(directive->getNamespace(), directive->getName()));]
				
			==> [DirectiveRouter.cpp] 
				[DirectiveRouter::handleDirectiveWithPolicyHandleImmediately] 
				[handlerAndPolicy.handler->handleDirectiveImmediately(directive);] : 以Alert为例，handler可以是AlertsCapabilityAgent类型
				
				==> [AlertsCapabilityAgent.cpp]
            	    [AlertsCapabilityAgent::handleDirectiveImmediately] 
                    [m_executor.submit([this, info]() { executeHandleDirectiveImmediately(info); });] 
                    
                ==>	[AlertsCapabilityAgent.cpp]
                	[AlertsCapabilityAgent::executeHandleDirectiveImmediately]
                	[handleSetAlert]
                	
            ==> [DirectiveSequencer.cpp]
            	[DirectiveSequencer::recehandlerAndPolicyiveDirectiveLocked]
             	[handled = m_directiveProcessor->onDirective(directive);]
```

以Alert为例,如下的过程是将`m_alertsCapabilityAgent`设置为`handlerAndPolicy.handler`属性，并将`handlerAndPolicy`设置为`m_configuration`的某一项。

```
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_directiveSequencer->addDirectiveHandler(m_alertsCapabilityAgent))]
	
	==> [DirectiveSequencer.cpp]
		[DirectiveSequencer::addDirectiveHandler]
		[m_directiveRouter.addDirectiveHandler(handler);]
	
		==> [DirectiveRouter.cpp]
			[DirectiveRouter::addDirectiveHandler]
			[HandlerAndPolicy handlerAndPolicy(handler, item.second);]
		
		==> [DirectiveRouter.cpp]
			[DirectiveRouter::addDirectiveHandler]
			[m_configuration[item.first] = handlerAndPolicy;]
```



**MultipartParser**

在解析的过程中使用到了`MultipartParser`,这是一个简单，高效的多部分MIME消息解析器，基于[Formidable's](http://github.com/felixge/node-formidable)解析器。

如下是三个解析函数:

```
//在multipart部分开始时调用的回调函数
 static void partBeginCallback(const MultipartHeaders& headers, void* userData);
//当MIME部分的数据可用时调用的回调函数
static void partDataCallback(const char* buffer, size_t size, void* userData);
//multipart部分结束时调用的回调函数
static void partEndCallback(void* userData);
```

创建对象的时候设置引用:

```
MimeParser::MimeParser(
    std::shared_ptr<MessageConsumerInterface> messageConsumer,
    std::shared_ptr<AttachmentManager> attachmentManager) :
        m_receivedFirstChunk{false},
        m_currDataType{ContentType::NONE},
        m_messageConsumer{messageConsumer},
        m_attachmentManager{attachmentManager},
        m_dataParsedStatus{DataParsedStatus::OK},
        m_currentByteProgress{0},
        m_totalSuccessfullyProcessedBytes{0},
        m_isAttachmentWriterBufferFull{false} {
    m_multipartReader.onPartBegin = MimeParser::partBeginCallback;
    m_multipartReader.onPartData = MimeParser::partDataCallback;
    m_multipartReader.onPartEnd = MimeParser::partEndCallback;
    m_multipartReader.userData = this;
}
```

## 4. Capabilities

AVS期望每个产品都能够报告首次启动时以及每当应用无线（OTA）更新时支持的接口和接口版本（开箱即用体验）。 

Capabilities API

https://developer.amazon.com/docs/alexa-voice-service/capabilities-api.html

以`Alert`为例,参考3.3,在初始化时就会将AlertsCapabilityAgent设置给`DirectiveRouter`的`std::unordered_map<avsCommon::avs::NamespaceAndName, avsCommon::avs::HandlerAndPolicy> m_configuration;`成员。

```
[DefaultClient.cpp]
[DefaultClient::initialize]
[m_directiveSequencer->addDirectiveHandler(m_alertsCapabilityAgent))]
	
	==> [DirectiveSequencer.cpp]
		[DirectiveSequencer::addDirectiveHandler]
		[m_directiveRouter.addDirectiveHandler(handler);]
	
		==> [DirectiveRouter.cpp]
			[DirectiveRouter::addDirectiveHandler]
			[HandlerAndPolicy handlerAndPolicy(handler, item.second);]
		
		==> [DirectiveRouter.cpp]
			[DirectiveRouter::addDirectiveHandler]
			[m_configuration[item.first] = handlerAndPolicy;]
```

在收到闹钟相关的命令时，DirectiveRouter会通过`m_configuration`通知到`AlertsCapabilityAgent::handleDirectiveImmediately`接口，所以，对于各种Agent,处理命令的入口即接口`handleDirectiveImmediately`,参考3.3,调用`handleDirectiveImmediately`过程如下:

```
[DirectiveRouter.cpp]
[DirectiveRouter::getHandlerAndPolicyLocked]
[m_configuration.find(NamespaceAndName(directive->getNamespace(), directive->getName()));]
				
==> [DirectiveRouter.cpp] 
	[DirectiveRouter::handleDirectiveWithPolicyHandleImmediately] 
	[handlerAndPolicy.handler->handleDirectiveImmediately(directive);] 
				
    ==> [AlertsCapabilityAgent.cpp]
    	[AlertsCapabilityAgent::handleDirectiveImmediately] 
    	[m_executor.submit([this, info]() { executeHandleDirectiveImmediately(info); });] 
                    
        ==>	[AlertsCapabilityAgent.cpp]
        	[AlertsCapabilityAgent::executeHandleDirectiveImmediately]
        	[handleSetAlert(directive, payload, &alertToken)]
        	
        	==> [AlertsCapabilityAgent.cpp]
        		[AlertsCapabilityAgent::handleSetAlert]
        		[m_alertScheduler.scheduleAlert(parsedAlert)]
        		
        		==> [AlertScheduler.cpp]
        			[AlertScheduler::scheduleAlert]
        			[m_scheduledAlerts.insert(alert);]
        		
        		==> [AlertScheduler.cpp]
        			[AlertScheduler::scheduleAlert]
        			[setTimerForNextAlertLocked();]			
        			
        			==> [AlertScheduler.cpp]
        				[AlertScheduler::setTimerForNextAlertLocked()]
        				[m_scheduledAlertTimer
                 .start(secondsToWait, std::bind(&AlertScheduler::onAlertReady, this, alert->getToken()))]
        				==> [AlertScheduler.cpp]
        					[AlertScheduler::onAlertReady]
        					[notifyObserver(alertToken, AlertObserverInterface::State::READY);]
```



## 5. Endpoints

## 6.Interfaces 

每个接口都是指令和事件的集合，它们对应于特定的客户端功能。

| **Interface**                                                | **Description**                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [SpeechRecognizer](https://developer.amazon.com/docs/alexa-voice-service/speechrecognizer.html) | The core interface for the Alexa Voice Service. Each user utterance leverages the Recognize event. |
| [SpeechSynthesizer](https://developer.amazon.com/docs/alexa-voice-service/speechsynthesizer.html) | The interface that returns Alexa TTS.                        |
| [Alerts](https://developer.amazon.com/docs/alexa-voice-service/alerts.html) | The interface for setting, stopping, and deleting timers and alarms. For a conceptual overview, see [Alerts Overview](https://developer.amazon.com/docs/alexa-voice-service/alerts-overview.html). |
| [AudioActivityTracker](https://developer.amazon.com/docs/alexa-voice-service/audioactivitytracker.html) | The interface that is used to inform Alexa which interface last occupied an audio channel. |
| [AudioPlayer](https://developer.amazon.com/docs/alexa-voice-service/audioplayer.html) | The interface for managing and controlling audio playback that originates from an Alexa-managed queue. For a conceptual overview, see [AudioPlayer Overview](https://developer.amazon.com/docs/alexa-voice-service/audioplayer-overview.html). |
| [Bluetooth (Developer Preview)](https://developer.amazon.com/docs/alexa-voice-service/bluetooth.html) | The interface for managing connections with peer Bluetooth devices, such as smart phones and speakers. |
| [Notifications](https://developer.amazon.com/docs/alexa-voice-service/notifications.html) | The interface that delivers visual and audio indicators when notifications are available. For a conceptual overview, see [Notifications Overview](https://developer.amazon.com/docs/alexa-voice-service/notifications-overview.html). |
| [PlaybackController](https://developer.amazon.com/docs/alexa-voice-service/playbackcontroller.html) | The interface for navigating a playback queue via button presses or GUI affordances. |
| [Settings](https://developer.amazon.com/docs/alexa-voice-service/settings.html) | The interface that is used to manage the Alexa settings on your product, such as locale. |
| [Speaker](https://developer.amazon.com/docs/alexa-voice-service/speaker.html) | The interface for controlling the volume of Alexa originated content on your product, including mute and unmute. |
| [System](https://developer.amazon.com/docs/alexa-voice-service/system.html) | The interface that is used to send Alexa information about your product. |
| [TemplateRuntime](https://developer.amazon.com/docs/alexa-voice-service/templateruntime.html) | The interface for rendering visual metadata. For a conceptual overview, see [Display Cards Overview](https://developer.amazon.com/docs/alexa-voice-service/display-cards-overview.html). |
| [VisualActivityTracker](https://developer.amazon.com/docs/alexa-voice-service/visualactivitytracker.html) | The interface that is used to inform Alexa when content is actively displayed to an end user. |

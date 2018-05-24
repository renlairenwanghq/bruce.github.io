1. 授权

Code-Based Linking (CBL) for Other Devices and Platforms
https://developer.amazon.com/docs/alexa-voice-service/code-based-linking-other-platforms.html

百度device授权过程
http://developer.baidu.com/wiki/index.php?title=docs/oauth/device

可以参考dueros的授权过程，设备端接入了授权过程之后，用户在使用您的产品时将需要登录百度，用户登录成功之后百度OAuth服务返回Access Token给设备端。后续设备端每次向DCS服务发起请求时，在HTTP/2 Header的authorization参数中需要带上Access Token。
2. 访问服务
====================================
HTTP2Transport::connect()
m_networkThread = std::thread(&HTTP2Transport::networkLoop, this);

bool PostConnectSynchronizer::doPostConnect(std::shared_ptr<HTTP2Transport> transport) {

void MessageRequest::sendCompleted(avsCommon::sdkInterfaces::MessageRequestObserverInterface::Status status) {
void PostConnectSynchronizer::onSendCompleted(MessageRequestObserverInterface::Status status) {

onPostConnected
setIsConnectedTrueUnlessStopping();
  void notifyObserversOnConnected();


================
    startMonitoring();


bool HTTP2Transport::connect()
networkLoop
PostConnectSynchronizer:doPostConnect


============
DirectiveRouter::addDirectiveHandler

=================
std::unique_ptr<DefaultClient> DefaultClient::create
m_connectionManager= acl::AVSConnectionManager::create(m_messageRouter, false, connectionObservers, {m_dialogUXStateAggregator});
void MessageRouter::setConnectionStatusLocked
void MessageRouter::notifyObserverOnConnectionStatusChanged(
============================
client->connect(m_dcfDelegate, endpoint);
void DefaultClient::connect
        m_connectionManager->setAVSEndpoint(avsEndpoint);
==============================================
authorization::cblAuthDelegate::CBLAuthDelegate::create
instance->init(configuration, deviceInfo))
bool CBLAuthDelegate::init
m_authorizationFlowThread = std::thread(&CBLAuthDelegate::handleAuthorizationFlow, this);

handleRefreshingToken
auto result = receiveTokenResponse(requestRefresh(), false);
setAuthState(nextState);
observer->onAuthStateChange(m_authState, m_authError);
	*****
	    authDelegate->addAuthObserver(userInterfaceManager);
=============================
client->connect(m_dcfDelegate, endpoint);
void DefaultClient::connect
        m_connectionManager->setAVSEndpoint(avsEndpoint);
    m_messageRouter->setAVSEndpoint(avsEndpoint);

            createActiveTransportLocked();
auto transport = m_transportFactory->createTransport
    return HTTP2Transport::create
authDelegate->addAuthObserver(transport);

===============================
m_connectionManager
void DefaultClient::connect
        m_connectionManager->setAVSEndpoint(avsEndpoint);


================================================
流程1:
1. 启动授权的线程handleAuthorizationFlow
2. 将HTTP2Transport添加为CBLAuthDelegate的观察者
3. HTTP2Transport实现了AuthObserverInterface，通过该接口来获取通知。
```
[SampleApplication.cpp] [SampleApplication::initialize] CBLAuthDelegate::create
	[CBLAuthDelegate.cpp] [CBLAuthDelegate::create] instance->init(configuration, deviceInfo)
	[CBLAuthDelegate.cpp] [CBLAuthDelegate::init] m_authorizationFlowThread = std::thread(&CBLAuthDelegate::handleAuthorizationFlow, this);

[SampleApplication.cpp] [SampleApplication::initialize] alexaClientSDK::defaultClient::DefaultClient::create
	[DefaultClient.cpp] [DefaultClient::create] defaultClient->initialize

	[DefaultClient.cpp] [DefaultClient::initialize] auto contextManager = contextManager::ContextManager::create();:创建线程updateStatesLoop
	[DefaultClient.cpp] [DefaultClient::initialize] acl::AVSConnectionManager::create :创建实例 m_connectionManager

[SampleApplication.cpp] [SampleApplication::initialize] client->connect(m_dcfDelegate, endpoint);
	[DefaultClient.cpp] [DefaultClient::connect] m_connectionManager->setAVSEndpoint(avsEndpoint);
		[AVSConnectionManager.cpp] [AVSConnectionManager::setAVSEndpoint] m_messageRouter->setAVSEndpoint(avsEndpoint);
			[MessageRouter.cpp] [MessageRouter::setAVSEndpoint] createActiveTransportLocked();
				[MessageRouter.cpp] [MessageRouter::createActiveTransportLocked()] auto transport = m_transportFactory->createTransport
					[HTTP2TransportFactory.cpp] [HTTP2TransportFactory::createTransport] return HTTP2Transport::create
						    [HTTP2Transport.cpp] [HTTP2Transport::create] authDelegate->addAuthObserver(transport);

				[MessageRouter.cpp] [MessageRouter::createActiveTransportLocked()] transport->connect()
					[HTTP2Transport.cpp] [HTTP2Transport::connect()] m_multi = avsCommon::utils::libcurlUtils::CurlMultiHandleWrapper::create();
				[HTTP2Transport.cpp] [HTTP2Transport::connect()] m_networkThread = std::thread(&HTTP2Transport::networkLoop, this);
					[HTTP2Transport.cpp] [HTTP2Transport::networkLoop()] auto result = m_multi->perform(&numTransfersLeft);

```
============handleAuthorizationFlow thred================
流程2:
授权的状态机，当状态改变时，通知注册的观察者，根据流程1,得知HTTP2Transport是CBLAuthDelegate的观察者。

handleRefreshingToken
	auto result = receiveTokenResponse(requestRefresh(), false);
	setAuthState(nextState);
		observer->onAuthStateChange(m_authState, m_authError); 

			 HTTP2Transport::onAuthStateChange
===============networkLoop　thread===============
流程3:
建立连接，并处理消息
[][] networkLoop
	[][networkLoop()]m_postConnect = m_postConnectFactory->createPostConnect();
		[PostConnectSynchronizerFactory.cpp] [PostConnectSynchronizerFactory::createPostConnect()] PostConnectSynchronizer::create(m_contextManager);
			[PostConnectSynchronizer.cpp] [PostConnectSynchronizer::create] return std::shared_ptr<PostConnectSynchronizer>{new PostConnectSynchronizer(contextManager)};

	[PostConnectSynchronizer.cpp] [networkLoop] PostConnectSynchronizer::doPostConnect
		[PostConnectSynchronizer.cpp] [PostConnectSynchronizer::doPostConnect] m_mainLoopThread = std::thread(&PostConnectSynchronizer::mainLoop, this);
			[PostConnectSynchronizer.cpp] [PostConnectSynchronizer::mainLoop()] m_contextManager->getContext(shared_from_this());
				[ContextManager.cpp] [ContextManager::getContext] m_contextRequesterQueue.push(contextRequester); : 将PostConnectSynchronizer添加到队列中

	* 建立连接，创建stream
	[PostConnectSynchronizer.cpp][networkLoop()] establishConnection()
		[HTTP2Transport.cpp] [HTTP2Transport::establishConnection()] setupDownchannelStream()
			[HTTP2Transport.cpp] [HTTP2Transport::setupDownchannelStream()] m_downchannelStream = m_streamPool.createGetStream(url, authToken, m_messageConsumer);
				[HTTP2StreamPool.cpp] [HTTP2StreamPool::createGetStream] stream = getStream(messageConsumer);
					[HTTP2StreamPool.cpp] [HTTP2StreamPool::getStream]result = std::make_shared<HTTP2Stream>(messageConsumer, m_attachmentManager);
						[HTTP2Stream.cpp] [HTTP2Stream::HTTP2Stream] :在参数中初始化m_parser(MimeParser类型),而且该类中包含一个m_transfer(CurlEasyHandleWrapper类型)
						
			[HTTP2Transport.cpp] [HTTP2Transport::setupDownchannelStream()] m_activeStreams.insert(ActiveTransferEntry(m_downchannelStream->getCurlHandle(), m_downchannelStream));
	
	
	* 设置curl的处理函数
	[PostConnectSynchronizer.cpp] [networkLoop] processNextOutgoingMessage();
		[HTTP2Transport.cpp] [HTTP2Transport::processNextOutgoingMessage()] HTTP2StreamPool::createPostStream
			[HTTP2StreamPool.cpp] [HTTP2StreamPool::createPostStream] stream->initPost(url, authToken, request)
				[HTTP2Stream.cpp] [HTTP2Stream::initPost] setCommonOptions(url, authToken)
					[HTTP2Stream.cpp] [setCommonOptions(url, authToken)] m_transfer.setWriteCallback(&HTTP2Stream::writeCallback, this)
					[HTTP2Stream.cpp] [setCommonOptions(url, authToken)] m_transfer.setHeaderCallback(&HTTP2Stream::headerCallback, this)
						[CurlEasyHandleWrapper.cpp] [CurlEasyHandleWrapper::setWriteCallback] setopt(CURLOPT_WRITEFUNCTION, callback) && (!userData || setopt(CURLOPT_WRITEDATA, userData));
							[CurlEasyHandleWrapper.h] [CurlEasyHandleWrapper::setopt] curl_easy_setopt(m_handle, option, value);


结论:
处理函数是:
HTTP2Stream::writeCallback
HTTP2Stream::headerCallback
HTTP2Stream::readCallback
=======================================================================
流程4:
对于闹钟的命令，是如何从avs收到之后处理的
step1: 初始化，注册的过程
[DefaultClient.cpp] [DefaultClient::initialize] m_directiveSequencer = adsl::DirectiveSequencer::create(m_exceptionSender); : 创建DirectiveSequencer
	[DirectiveSequencer.cpp] [DirectiveSequencer::create] new DirectiveSequencer(exceptionSender)
	[DirectiveSequencer.cpp] [DirectiveSequencer::DirectiveSequencer] m_receivingThread = std::thread(&DirectiveSequencer::receivingLoop, this);　: 创建新线程receivingLoop

[DefaultClient.cpp]　[DefaultClient::initialize]　acl::AVSConnectionManager::create(m_messageRouter, false, connectionObservers, {m_dialogUXStateAggregator}); : 创建m_connectionManager实例
	[AVSConnectionManager.cpp] [AVSConnectionManager::create] messageRouter->setObserver(connectionManager);　:　将AVSConnectionManager添加为messageRouter的观察者

[DefaultClient.cpp] [DefaultClient::initialize] std::make_shared<adsl::MessageInterpreter>(m_exceptionSender, m_directiveSequencer, attachmentManager);:创建messageInterpreter
[DefaultClient.cpp] [DefaultClient::initialize] m_connectionManager->addMessageObserver(messageInterpreter);　: 将MessageInterpreter添加为AVSConnectionManager的观察者


[DefaultClient.cpp] [DefaultClient::initialize] m_alertsCapabilityAgent = capabilityAgents::alerts::AlertsCapabilityAgent::create

[DefaultClient.cpp] [DefaultClient::initialize] m_directiveSequencer->addDirectiveHandler(m_alertsCapabilityAgent)
	[DirectiveSequencer.cpp] [DirectiveSequencer::addDirectiveHandler] m_directiveRouter.addDirectiveHandler(handler);
		[DirectiveRouter.cpp][DirectiveRouter::addDirectiveHandler] HandlerAndPolicy handlerAndPolicy(handler, item.second);
		[DirectiveRouter.cpp][DirectiveRouter::addDirectiveHandler] m_configuration[item.first] = handlerAndPolicy;

step2:　收到命令，并添加到队列
[MimeParser.cpp] [MimeParser::partEndCallback] MessageRouter::consumeMessage
[MessageRouter.cpp] [MessageRouter::consumeMessage] MessageRouter::notifyObserverOnReceive
[MessageRouter.cpp] [MessageRouter::notifyObserverOnReceive] temp->receive(contextId, message); : 会调用到AVSConnectionManager::receive
[AVSConnectionManager.cpp] [AVSConnectionManager::receive]  observer->receive(contextId, message);　: 会调用到MessageInterpreter的receive
	[MessageInterpreter.cpp] [MessageInterpreter::receive] m_directiveSequencer->onDirective(avsDirective);
    		[DirectiveSequencer.cpp] [DirectiveSequencer::onDirective] m_receivingQueue.push_back(directive); : 添加到队列
		[DirectiveSequencer.cpp] [DirectiveSequencer::onDirective] m_wakeReceivingLoop.notify_one();　:　通知有命令到达


step3: 取出命令，进行处理
[DirectiveSequencer.cpp] [DirectiveSequencer::DirectiveSequencer] DirectiveSequencer::receivingLoop()
[DirectiveSequencer.cpp] [DirectiveSequencer::receivingLoop()] m_wakeReceivingLoop.wait(lock, wake)
[DirectiveSequencer.cpp] [DirectiveSequencer::receivingLoop()] receiveDirectiveLocked(lock);
	[DirectiveSequencer.cpp] [DirectiveSequencer::receiveDirectiveLocked] auto directive = m_receivingQueue.front();
	[DirectiveSequencer.cpp] [DirectiveSequencer::receiveDirectiveLocked] handled = m_directiveRouter.handleDirectiveWithPolicyHandleImmediately(directive);
		[DirectiveRouter.cpp] [DirectiveRouter::handleDirectiveWithPolicyHandleImmediately] auto handlerAndPolicy = getHandlerAndPolicyLocked(directive);
		[DirectiveRouter.cpp] [DirectiveRouter::handleDirectiveWithPolicyHandleImmediately] handlerAndPolicy.handler->handleDirectiveImmediately(directive); : 参考step1得出handle是AlertsCapabilityAgent类型
			[AlertsCapabilityAgent.cpp] [AlertsCapabilityAgent::handleDirectiveImmediately] m_executor.submit([this, info]() { executeHandleDirectiveImmediately(info); });
				[AlertsCapabilityAgent.cpp] [AlertsCapabilityAgent::executeHandleDirectiveImmediately] handleSetAlert
==================================================
流程5:
收到数据，进行处理
HTTP2Stream::writeCallback
stream->m_parser.feed(data, numChars);
================================================== 






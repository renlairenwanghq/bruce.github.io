##ffmpeg

```
FFMPEG
使用ffmpeg进行格式转换
mp3	转wav
ffmpeg	-i	chengdu.mp3	-acodec	pcm_s16le	-ac	2	-ar	44100	-vn	chengdu.wav
mp3	→	pcm
ffmpeg	-i	chengdu.mp3	-acodec	pcm_s16le	-ac	2	-ar	44100	-vn	-f	s16le	chengdu.pcm
flac	→	pcm
ffmpeg	-i	sanshengsanshi.flac	-acodec	pcm_s16le	-ac	2	-ar	44100	-vn	-f	s16le	sanshe
ngsanshi.pcm
pcm	→	flac
ffmpeg	-f	s16le	-ar	44100	-ac	2	-i	sanshengsanshi.pcm	-acodec	flac	-vn	sanshengsans
hi_1.flac
mp3	→	aac
ffmpeg	-i	chengdu.mp3	-acodec	aac	-ac	2	-ar	44100	-vn	chengdu.aac
wav	→	mp3	sample:48K,	bit	rate:	256K
ffmpeg	-i	at.wav	-acodec	mp3	-ac	2	-ab	256000	-ar	48000	-vn	-f	mp3	at-mp3-48k-256kb
ps.mp3
mp4@aac	codec	m3u8	generate
ffmpeg	-i	chengdu.mp4	-c:a	copy	-f	hls	-threads	8	-hls_time	5	-hls_list_size	12	ind
ex.m3u8
note:	hls_time	单个文件播放时常,默认位2seconds,	hls_list_size	文件列表数目,设置为0,	保存所有
列表
SBR	恒定比特率wav	→	mp4
ffmpeg	-i	at.wav	-acodec	libfdk_aac		-ac	2	-ab	256000	-ar	48000	-vn	-f	mp4		at-aac-
48k-256kbps.mp4
可变比特率(VBR)模式
靶向质量,而不是一个特定的比特率。1是最低质量,5是最高质量。设置与该VBR水平-vbr标志。VBR
模式大致给出了每通道(以下比特率的详细信息):
3211.1	ffmpeg
|VBR|kbps/信道|AOT|	|:-|:-|:-|	|1|20-32|LC,HE,HEv2|	|2|32-40|LC,HE,HEv2|	|3|48-56|LC,HE,HEv2|
|4|64-72|LC|	|5|96-112|LC|
ffmpeg	-i	at.wav	-acodec	libfdk_aac		-ac	2		-ar	48000	-vbr	3		-vn	-f	mp4		at-aac-	4
8k-vbr3.mp4
```

### fplay

```
播放pcm
ffplay	-f	s16le	-ar	44100	-ac	2	chengdu.pcm
```


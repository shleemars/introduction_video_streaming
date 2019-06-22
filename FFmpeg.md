## FFmpeg 소개
* 비디오, 오디오 등 멀티미디어 파일과 스트림을 다룰 수 있는 소프트웨어
* 2018년 4월 20일 FFmpeg 4.0까지 버전업이 되었음
* 7개의 라이브러리와 3개의 실행파일로 구성
	* libavcodec
		* a library containing **decoders and encoders** for audio/video codecs.
	* libavutil 
		* a library containing functions **for simplifying programming**, including random number generators, data structures, mathematics routines, core multimedia utilities, and much more.
	* libavformat
		* a library containing **demuxers and muxers** for multimedia **container formats**.
	* libavfilter
		* a library containing **media filters**.
	* libavdevice
		* a library containing **input and output devices for grabbing from and rendering** to many common multimedia input/output software frameworks, including **Video4Linux, Video4Linux2, VfW, and ALSA**.
	* libswscale
		* a library performing highly optimized **image scaling and color space/pixel format conversion** operations.
	* libswresample
		* a library performing highly optimized **audio resampling, rematrixing and sample format conversion** operations.
	* ffmpeg
		* a **command line tool to convert multiemedia files** between formats
	* ffplay
		* a **simple media player** based on SDL and the FFmpeg libraries
	* ffprobe
		* a simple multimedia **stream analyzer**
* 기타 내용은 https://www.ffmpeg.org/ 을 참고
* ffmpeg command line : https://ffmpeg.org/ffmpeg.html

## FFmpeg의 주요 자료구조
* AVIOContext
	* 추상화된 입출력
	* 버퍼 관리
* AVInputFormat
	* 추상화된 디먹서
	* 아래와 같은 형태로 구현
```
AVInputFormat ff_avi_demuxer = {
    .name           = "avi",
    .long_name      = NULL_IF_CONFIG_SMALL("AVI (Audio Video Interleaved)"),
    .priv_data_size = sizeof(AVIContext),
    .extensions     = "avi",
    .read_probe     = avi_probe,
    .read_header    = avi_read_header,
    .read_packet    = avi_read_packet,
    .read_close     = avi_read_close,
    .read_seek      = avi_read_seek,
    .priv_class = &demuxer_class,
}; 
```
* AVOutputFormat
	* 추상화된 Muxer
	* 아래와 같은 형태로 구현
```
AVOutputFormat ff_avi_muxer = {
    .name           = "avi",
    .long_name      = NULL_IF_CONFIG_SMALL("AVI (Audio Video Interleaved)"),
    .mime_type      = "video/x-msvideo",
    .extensions     = "avi",
    .priv_data_size = sizeof(AVIContext),
    .audio_codec    = CONFIG_LIBMP3LAME ? AV_CODEC_ID_MP3 : AV_CODEC_ID_AC3,
    .video_codec    = AV_CODEC_ID_MPEG4,
    .init           = avi_init,
    .write_header   = avi_write_header,
    .write_packet   = avi_write_packet,
    .write_trailer  = avi_write_trailer,
    .codec_tag      = (const AVCodecTag * const []) {
        ff_codec_bmp_tags, ff_codec_wav_tags, 0
    },
    .priv_class     = &avi_muxer_class,
};
```
* AVFormatContext
	* AVIOContext+AVInputFormat 또는 AVIContext+AVOutputFormat이 결합된 형태
	* 컨테이너 정보
* AVCodec
	* 코덱 정보
```
AVCodec ff_h264_decoder = {
    .name                  = "h264",
    .long_name             = NULL_IF_CONFIG_SMALL("H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10"),
    .type                  = AVMEDIA_TYPE_VIDEO,
    .id                    = AV_CODEC_ID_H264,
    .priv_data_size        = sizeof(H264Context),
    .init                  = h264_decode_init,
    .close                 = h264_decode_end,
    .decode                = h264_decode_frame,
    .capabilities          = /*AV_CODEC_CAP_DRAW_HORIZ_BAND |*/ AV_CODEC_CAP_DR1 |
                             AV_CODEC_CAP_DELAY | AV_CODEC_CAP_SLICE_THREADS |
                             AV_CODEC_CAP_FRAME_THREADS,
    .caps_internal         = FF_CODEC_CAP_INIT_THREADSAFE | FF_CODEC_CAP_EXPORTS_CROPPING,
    .flush                 = flush_dpb,
    .init_thread_copy      = ONLY_IF_THREADS_ENABLED(decode_init_thread_copy),
    .update_thread_context = ONLY_IF_THREADS_ENABLED(ff_h264_update_thread_context),
    .profiles              = NULL_IF_CONFIG_SMALL(ff_h264_profiles),
    .priv_class            = &h264_class,
};
```
* AVCodecContext
	* 코덱 구동에 필요한 정보를 담고 있음
* AVFrame
	* 압축되지 않은 이미지나 오디오를 담고 있는 버퍼
	* data[] 필드
		* 이미지의 채널별(RGB, YUV) 페이로드
		* 오디오는 보통 data[0]에만 저장
	* linesize[] 필드
		* 이미지의 채널별 각 row의 크기(보통 4의 배수)
		* 오디오는 plane 별 크기
	* 이미지의 패킹 구조
  ![0a7056be-6371-15b0-8163-8a18f5a6556d](https://user-images.githubusercontent.com/9418025/59959098-ba940900-94eb-11e9-873f-85f5ae2033c9.png)
	* 오디오의 패킹 구조
		* https://stackoverflow.com/questions/18888986/what-is-the-difference-between-av-sample-fmt-s16p-and-av-sample-fmt-s16
* AVPacket
	* 압축된 이미지나 오디오를 담고 있는 필드
* SwsContext
	* 이미지의 크기나 픽셀 포맷을 변경하기 위한 소프트웨어 scaler



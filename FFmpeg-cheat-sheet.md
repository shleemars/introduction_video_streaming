FFmpeg cheat sheet
===

Installing FFmpeg 4.0 on Ubuntu Linux
---
http://ubuntuhandbook.org/index.php/2018/10/install-ffmpeg-4-0-2-ubuntu-18-0416-04/

Basic conversion
----
````
ffmpeg -i input.avi output.mp4
````

Supported codecs
---
```
ffmpeg -codecs
```
```
File Edit Options Buffers Tools Text Help
Codecs:
 D..... = Decoding supported
 .E.... = Encoding supported
 ..V... = Video codec
 ..A... = Audio codec
 ..S... = Subtitle codec
 ...I.. = Intra frame-only codec
 ....L. = Lossy compression
 .....S = Lossless compression
 -------
 D.VI.S 012v                 Uncompressed 4:2:2 10-bit
 D.V.L. 4xm                  4X Movie
 D.VI.S 8bps                 QuickTime 8BPS video
 .EVIL. a64_multi            Multicolor charset for Commodore 64 (encoders: a64multi )
 .EVIL. a64_multi5           Multicolor charset for Commodore 64, extended with 5th color (colram) (encoders: a64multi5 )
 D.V..S aasc                 Autodesk RLE
```


Trimming
---

````
ffmpeg -ss [start] -i input.mp4 -t [duration] output.mp4
````

- [`-ss`](http://ffmpeg.org/ffmpeg-all.html#Main-options) specified the start time, eg. `00:01:23.000` or `83` (in seconds)
- [`-t`](http://ffmpeg.org/ffmpeg-all.html#Main-options) specfieid the duration of the clip


Download "Transport Stream" and remux to mp4
---
```
ffmpeg -i playlist.m3u8 -c copy -bsf:a aac_adtstoasc output.mp4
```

segment generation
---

```
 ffmpeg -i input.mp4 -vcodec libx264 -acodec aac -f stream_segment -segment_time 10 -segment_list index.m3u8 -segment_list_type m3u8 segment_%03d.ts
```
- `-vcodec` video codec 
- `-acodec` audio codec
- `-f` format
- `-segment_list` listfile named name. If not specified no listfile is generated. 
- `-segment_list_type`
- `-segment_time`



HLS segment generation
---

```
ffmpeg -i input.mp4 -start_number 0 -hls_time 5 -hls_list_size 0 -f hls index.m3u8 segment_%03d.ts
```
- [`-start_number`](https://ffmpeg.org/faq.html) option to declare a starting number for the sequence
- [`-hls_time`](https://ffmpeg.org/ffmpeg-formats.html#Options-5) target segment length in seconds. Default value is 2. Segment will be cut on the next key frame after this time has passed.
- [`-hls_list_size`](https://ffmpeg.org/ffmpeg-formats.html#Options-5) the maximum number of playlist entries. If set to 0 the list file will contain all the segments. Default value is 5.

Multi-bitrate HLS generation
---
```
ffmpeg -i input.mp4
-c:a aac -ar 48000 -c:v h264 -profile:v main -crf 20 -sc_threshold 0 -g 60 -keyint_min 60 -hls_time 4 -hls_playlist_type vod -vf scale=w=640:h=360:force_original_aspect_ratio=decrease -b:v 800k -maxrate 856k -bufsize 1200k -b:a 96k -hls_segment_filename output/360p_%03d.ts output/360p.m3u8 \
-c:a aac -ar 48000 -c:v h264 -profile:v main -crf 20 -sc_threshold 0 -g 60 -keyint_min 60 -hls_time 4 -hls_playlist_type vod -vf scale=w=842:h=480:force_original_aspect_ratio=decrease -b:v 1400k -maxrate 1498k -bufsize 2100k -b:a 128k -hls_segment_filename output/480p_%03d.ts output/480p.m3u8 \
-c:a aac -ar 48000 -c:v h264 -profile:v main -crf 20 -sc_threshold 0 -g 60 -keyint_min 60 -hls_time 4 -hls_playlist_type vod -vf scale=w=1280:h=720:force_original_aspect_ratio=decrease -b:v 2800k -maxrate 2996k -bufsize 4200k -b:a 128k -hls_segment_filename output/720p_%03d.ts output/720p.m3u8 \
-c:a aac -ar 48000 -c:v h264 -profile:v main -crf 20 -sc_threshold 0 -g 60 -keyint_min 60 -hls_time 4 -hls_playlist_type vod -vf scale=w=1920:h=1080:force_original_aspect_ratio=decrease -b:v 5000k -maxrate 5350k -bufsize 7500k -b:a 192k -hls_segment_filename output/1080p_%03d.ts output/1080p.m3u8


```
https://gist.github.com/mrbar42/ae111731906f958b396f30906004b3fa



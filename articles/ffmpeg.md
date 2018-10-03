https://s3-ap-northeast-1.amazonaws.com/threal3d-movie-hls/out/index.m3u8
https://dpn543ao3406d.cloudfront.net/out/index.m3u8

https://s3-ap-northeast-1.amazonaws.com/threal3d-movie-hls/sharefun.jpg
https://dpn543ao3406d.cloudfront.net/sharefun.jpg


## 安裝

Ubuntu

http://ubuntuhandbook.org/index.php/2017/10/ffmpeg-3-4-released-install-ubuntu-16-04/

https://github.com/qrtt1/ffmpeg_lab/wiki/FFmpeg-Tool-Notes

macOS
```
brew install ffmpeg \
    --with-tools \
    --with-fdk-aac \
    --with-freetype \
    --with-fontconfig \
    --with-libass \
    --with-libvorbis \
    --with-libvpx \
    --with-opus \
    --with-x265
```

## 形式
```
ffmpeg 一般選項 -c:v 視訊解碼器 -c:a 音訊解碼器 -f 多工解訊器 -i 輸入檔 -c:v 視訊編碼器 -c:a 音訊編碼器 -f 多工器 輸出檔
```

通常不需要手動指定 解碼器 (Decoder)、多工解訊器 (Demuxer)，因為 ffmpeg 會依輸入自動判斷，也不需要手動指定 多工器 (Muxer)，ffmpeg 會依輸出副檔名自動判斷

## 流程
```
# Input process
File => Demuxer => Stream => Decoder => Video, Audio ...

# Output process
File <= Demuxer <= Stream <= Decoder <= Video, Audio ...
```

## 一般選項

```
-L
  授權條款

-h, -?, -help, --help [參數]
  顯示說明，如果沒有參數則顯示基本說明。

  可用參數如下:

  long
    顯示更多選項。

  full
    顯示全部選項 (包括所有格式和編解碼器特定的選項。

  decoder=解碼器名稱
    顯示指定解碼器 (Decoder) 訊息。

  encoder=編碼器名稱
    顯示指定編碼器 (Encoder) 訊息。

  demuxer=多工解訊器名稱
    顯示指定多工解訊器 (Demuxer) 訊息。

  muxer=多工器名稱
    顯示指定多工器 (Muxer) 訊息。

  filter=篩選器名稱
    顯示指定篩選器 (Filter) 訊息。

-version
  顯示版本訊息。

-formats
  列出可用檔案格式。

-codecs
  列出可用的編解碼器。

-decoders
  列出可用的解碼器。

-encoders
  列出可用的編碼器。

-bsfs
  列出可用的位元流篩選器。

-protocols
  列出可用的協議。

-filters
  列出可用的篩選器。

-pix_fmts
  列出可用的像素格式。

-sample_fmts
  列出可用的取樣格式。

-colors
  列出認可的顏色名稱。

-loglevel, -v [repeat+]日誌等級
  設定日誌等級。

  日誌等級:

  quiet, -8
    不顯示任何訊息。

  panic, 0
    只顯示可能導致程式崩潰的致命錯誤訊息。

  fatal, 8
    只顯示致命錯誤訊息。

  error, 16
    顯示全部錯誤訊息。

  warning, 24
    只顯示致命警告與錯誤訊息。

  info, 32
    在處理期間顯示資訊訊息。

  verbose, 40
    與 info 相似，但更加冗長。

  debug, 48
    顯示任何訊息。

-report
  傾印完整命列與控制台輸出至目前目錄下一個名為 program-YYYYMMDD-HHMMSS.log 的檔案。
  與 -loglevel verbose 相同詳細程度等級。

-hide_banner
  所有FFmpeg的工具通常會顯示版權聲明，建置(build)選項和程式庫(library)版本。這個選項可以用來隱藏這些訊息。
```

## 主要選項
```
-f 多工器，可用檔案格式表單見 -fromats（輸出入可用）

-c[:串流說明符] 編解碼器，可用編解碼器表單 見 -encoders, -decoders，copy為複製串流（輸出入可用）

-i 檔名（輸入可用）

-y 直接覆蓋輸出檔（全域可用）

-n 遇到相同檔名直接結束離開（全域可用）

-ss 時間碼 (輸出入可用)當作為輸入選項(位於 -i 之前)時，將會定位/跳轉(seek)到指定時間點並歸零的當前時間戳記。當作為輸出選項(位於 -i 之後)時，將會將會從頭開始解碼(並丟棄)到達指定時間點。

-t 時間碼 (輸出入可用)指定持續時間

-to 時間碼 (輸出入可用)在此時間點停止寫入輸出

-frames[:串流說明符] 數量 (輸出可用)在達到指定的格(框)數量後停止寫入串流

-filter[:串流說明符] 設定篩選器
```

## 音訊選項
```
-an (輸出)
  禁用音訊輸出/入。

-aframes 數量 (輸出)
  設定音訊框的輸出數量。與 -frames:a 相同。

-acodec 音訊編碼器 (輸出)
  設定音訊編碼器。與 -codec:a 相同。

-ar[:串流說明符] 頻率 (輸出/輸入,每個串流)
  重新取樣 (resample) 音訊，單位為 Hz。重新取樣成 48kHz: -ar 48000
  需要詳細設定請改用 aresample filter。

-ac[:串流說明符] 聲道數 (輸出/輸入,每個串流)
  縮混 (downmix) 音訊的聲道。例如 5.1 -ac 6 。需要詳細設定請改用 pan filter。
  可用值: 0, 1, 2, 4, 5, 6, 8
```

## 視訊選項

```
-vn (輸出)
  禁用視訊輸出。

-vframes 數量 (輸出)
  設定畫格的輸出數量。與 -frames:v 相同。

-vcodec 視訊編碼器 (輸出)
  設定視訊編碼器。與 -codec:v 相同。

-r[:串流說明符] 畫格率 (輸出/輸入,每個串流)
  設定視訊的畫格率 (frame rate，單位為 Hz 或 fps，分數 或 縮寫)。
  例如： -r 23.976 或 -r 24000/1001

-s[:串流說明符] WxH (輸出/輸入,每個串流)
  設定畫格大小。
  例如： -s 1280x720

-aspect[:串流說明符] 比例 (輸出,每個串流)
  設定視訊的顯示寬高比。需要詳細設定請改用 setdar 或 stesar filter。
  例如： -aspect 1.77778 或 -aspect 16:9

-vsync 參數
  視訊同步方法。

  參數:

  0, passthrough
    不改變畫格的時間戳記。

  1, cfr
    複製畫格或刪除畫格，使輸出為 恆定畫格率 (CFR)。

  2, vfr
    不改變畫格的時間戳記，但移除重複時間戳記的畫格。

  drop
    破壞原本的時間戳記，以指定的 fps 為基礎產生新的時間戳記。

  -1, auto
    根據 muxer 自動選擇 1 或 2。

-vf filtergraph (輸出)
  設定視訊篩選器 (video filter)。與 -filter:v 相同。
```

## 編碼器選項
```
flags 旗標 (編/解碼,音訊,視訊,字幕)
  設定要使用(+旗標名稱)或禁用(-旗標名稱)的旗標
  例如使用 ilme 與 ildct 旗標: "-flags ilme+ildct"

b 位元率 (編碼,視訊,音訊)
  設定串流的位元率 (bitrate)，單位為 位元/秒 (bps, bits/sec)。
  例如設定為 640kbps，設定值即 640k 或 640000

qscale 品質 (編碼,視訊,音訊)
q 品質 (輸出,每個串流)
  設定串流品質 (Variable Bit Rate 模式)。
  用法見該編碼器說明。

profile profile (編碼,視訊,音訊)
  設定輸出串流的 Profile。

preset preset (編碼,視訊,音訊)
  選擇一組預設參數。

minrate 位元率 (編碼,視訊,音訊)
  設定最小流量容許值 (每秒位元)。用處不多，最大用處是用來建立 CBR 編碼。
  例如設定為 640kbps，設定值即 640k 或 640000

maxrate 位元率 (編碼,視訊,音訊)
  設定最大流量容許值 (每秒位元)。bufsize 需要被設定。
  例如設定為 640kbps，設定值即 640k 或 640000

bufsize 位元值 (編碼,視訊,音訊)
  設定流量控制緩衝區大小 (位元)。
  例如設定為 640kbits，設定值即 640k 或 640000

level 等級 (編碼,視訊,音訊)
  設定輸出編碼等級。

pix_fmt 格式 (編碼,視訊)
  轉換像素格式。
  像素格式表單 見 -pix_fmts

keyint_min 整數 (編碼,視訊)
  設定 GOP 最小值， 即 IDR-frames (keyframes) 最短間格。

g 整數 (編碼,視訊)
  設定 GOP 最大值，即 IDR-frames (keyframes) 最長間格。

refs 整數 (編碼,視訊)
  設定參考畫格數 (reference frames)。

bf 整數 (編碼,視訊)
  設定 B-Frames 最大連續數量。

cutoff 整數 (編碼,音訊)
  設定截止頻寬。這將會過濾掉高於設定值的頻率。
```

## 範例
```
ffmpeg -i <input> -filter:v "crop=1920/2:1080:0:0" -c:a copy half.mp4

ffmpeg -i <input> -vf -s 1920x1080,setdar=16:9 <output>

# 切割影片從 1 秒開始，直到經過 5 秒後
ffmpeg -ss 00:00:01 -i <input> -t 00:00:05 -avoid_negative_ts make_zero <output>

# 切割影片從 1 秒開始，直到 output 為 5 秒
ffmpeg -ss 00:00:01 -i <input> -to 00:00:05 <output>

# 轉檔為 H.264 格式
ffmpeg -i input.wmv -c:v libx264 <output>

# 從第 126 張開始，每五秒取一張圖，並指定 pixel 格式，以免自動選擇錯誤，得到 30 fps 的影片
ffmpeg -framerate 1/5 -start_number 126 -i img%03d.png -c:v libx264 -r 30 -pix_fmt yuv420p <output>

# 利用 glob pattern 選擇圖片
ffmpeg -framerate 1 -pattern_type glob -i '*.jpg' -c:v libx264 <output>

# 用單張圖片產生影片
ffmpeg -loop 1 -i img.png -c:v libx264 -t 30 -pix_fmt yuv420p <output>

# 抓長達 70/10 = 7 秒鐘的影像，並轉換成圖檔，圖檔檔名以數字 digit 的型式自動編號
ffmpeg -i test.flv -an -ss 00:00:42 -r 10 -vframes 70 -y NolanRyan-%d.jpg 

# 合併影片 by stream
# in mylist.txt
# file '/path/to/file1'
# file '/path/to/file2'
# file '/path/to/file3'
ffmpeg -f concat -safe 0 -i mylist.txt -c copy <output>

# 合併影片 by protocol，與在 UNIX-like 系統使用 cat 類似
ffmpeg -i input1.mp4 -c copy -bsf:v h264_mp4toannexb -f mpegts intermediate1.ts
ffmpeg -i input2.mp4 -c copy -bsf:v h264_mp4toannexb -f mpegts intermediate2.ts
ffmpeg -i "concat:intermediate1.ts|intermediate2.ts" -c copy -bsf:a aac_adtstoasc <output>

# 去除背景音樂
ffmpeg -i <input> -vcodec copy -an <output>

-vn => 禁用視訊輸出。
-an => 禁用音訊輸出。
-sn => 禁用字幕輸出。

# 加入背景音樂
ffmpeg -i <input> -stream_loop -1 -i music.mp3 -shortest <output>

# To HLS
ffmpeg -i input.mp4 -profile:v baseline -level 3.0 -s 640x360 -start_number 0 -hls_time 10 -hls_list_size 0 -f hls index.m3u8

ffmpeg -i in.mkv -map 0 -codec:v libx264 -codec:a libfaac -f ssegment -segment_list out.list out%03d.ts

ffmpeg -i color_depth_test_movie.mp4 -hls_time 10 -hls_list_size 0 color_depth_movie_1024.m3u8

ffmpeg -i input \
    ... [encoding parameters 1] ... output1 \
    ... [encoding parameters 2] ... output2 \
    ....[encoding parameters 3] ... output3


# To DASH
ffmpeg -threads 4 -f v4l2 -i  /dev/video0 -acodec libfaac -ar 44100 -ab 128k -ac 2 -vcodec libx264 -r 30 -s 1280x720  -f mp4 -y "$movie" > temp1.mp4 && MP4Box -dash 10000 -frag 1000 -rap "$movie"



ffmpeg -i in.video -c:v libvpx-vp9 -keyint_min 150 \
-g 150 -tile-columns 4 -frame-parallel 1  -f webm -dash 1 \
-an -vf scale=160:190 -b:v 250k -dash 1 video_160x90_250k.webm \
-an -vf scale=320:180 -b:v 500k -dash 1 video_320x180_500k.webm \
-an -vf scale=640:360 -b:v 750k -dash 1 video_640x360_750k.webm \
-an -vf scale=640:360 -b:v 1000k -dash 1 video_640x360_1000k.webm \
-an -vf scale=1280:720 -b:v 1500k -dash 1 video_1280x720_1500k.webm
```



## 影片裁切和拉伸
Reference: https://ffmpeg.org/ffmpeg-filters.html
```
把長或寬調整到 1280 另一邊等比例縮放，但是要是偶數
-filter:v 'scale=1280:trunc(ow/dar/2)*2'
-filter:v 'scale=trunc(oh*dar/2)*2:1280'

## 兩部影片（寬被壓縮一半）合成 side-by-side
ffmpeg -i auo_trim_fhd_megadepth.mp4 -vf "movie=auo_trim_fhd_resize.mp4 [in1]; [in]pad=iw*2:ih:iw:0[in0]; [in0][in1] overlay=0:0 [out]" sidebyside.mp4

這前面 crop 是裁切一半寬 從 0:0 開始
-filter:v 'crop=in_w/2:in_h:0:0,scale=1280:trunc(ow/dar/4)*2'

ffmpeg -i input.mp4 -filter:v 'crop=in_w/2:in_h:0:0' output.mp4

scale 要我要的大小
-filter:v 'scale=ow*2:oh'
-filter:v 'scale=in_w*2:in_h'
若無法成功，手動設定 dar 就可以了
-filter:v 'scale=in_w*2:in_h,setdar=dar=16/9'
```

```
in_w, in_h => The input width and height.
iw, ih => These are the same as in_w and in_h.
```

```
out_w, out_h => The output (cropped) width and height.
ow, oh => These are the same as out_w and out_h.
```


mp4 to gif
ffmpeg -i input.mp4 -r 10 frames/frame%03d.png
convert -delay 5 -loop 0 frames/frame*.png output.gif


ffmpeg -i input.mp4 -r 10 output%05d.png
convert output*.png output.gif
rm output*.png
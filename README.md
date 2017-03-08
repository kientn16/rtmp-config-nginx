# rtmp-config-nginx
# Hướng dẫn cài đặt RTMP trong NGINX trên hệ điều hành Linux(Ubuntu 14.04) #
***RTMP là gì?***
    **RTMP** viết tắt của **Real Time Messaging Protocol** là giao thức không công khai do Adobe phát triển và giữ bản quyền, được thiết kế cho ứng dụng thời gian thực, cho phép ứng dụng sử dùng video và âm thanh với tốc độ nhanh, hạn chế bị giật hình hoặc méo tiếng.
    Streaming media là phương tiện nhận và hiển thị liên tục âm thanh, hình ảnh đến người dùng cuối. Chẳng hạn khi bạn có 1 đoạn video dài tổng cộng 1 phút và bạn muốn phát online đoạn video bắt đầu ở giây thứ 20 và kết thúc ở giây thứ 30 thì bắt buộc bạn phải có 1 Media Streaming Server. Các server này dùng phương thức RTMP là phương thức có độ bảo mật cao. Phương thức RTMP mã hóa các dữ liệu được truyền đi giữa Flash Player và Media server. Dưới đây là 1 số phương thức dạng RTMP hay được sử dụng:
    1. **RTMP:** là phương thức được truyền nhanh nhất nhưng không được bảo vệ tốt.
    2. **RTMPE** và **RTMPS:** là các phiên bản mã hóa của RTMP. Bảo mật cao hơn nhiều so với **RTMP**.
    3. **RTMPT:** được bảo mật tốt nhưng chậm.
    4. Ngoài ra còn có **RTMPTE** cũng là phiên bản mã hóa khác của **RTMP**.

    Các Protocols này sử dụng các cổng (Port) 1935, 443, 80.
    **Dưới đây là danh sách 1 số RTMP Server.**
    **Adobe:**
    – [Flash Media Server](http://www.adobe.com/products/flashmediaserver/)
    – [LiveCycle DS](http://www.adobe.com/products/livecycle/dataservices/)
    **Non-Adobe:**
    – [Red5](http://www.red5.org/) (Java)
    – [Wowza](http://www.wowzamedia.com/) (Java)
    – [WebOrb](http://www.themidnightcoders.com/products.html) (.NET, Java)
    – [ErlyVideo](http://erlyvideo.org/) (Erlang)
    – [RubyIzumi](http://code.google.com/p/rubyizumi/) (Ruby)
    – [RTMPD](http://www.rtmpd.com/) (C++)
    – [Cygnal](http://wiki.gnashdev.org/Cygnal) (C++)
    – [RTMPy](http://rtmpy.org/wiki/RTMP) (Python)
    – [RTMPlite](http://code.google.com/p/rtmplite/) (Python)
    – [MammothServer](http://mammothserver.org/) (OpenFMS) (C++)
Trong đó Red5 là miễn phí. Wowza cũng có thể download bản chạy thử để test trên máy 
**NGINX là gì?**
    **NGINX** là một máy chủ proxy ngược mã nguồn mở (open source reverse proxy server) sử dụng phổ biến giao thức HTTP, HTTPS, SMTP, POP3 và IMAP , cũng như dùng làm cân bằng tải (load balancer), HTTP cache và máy chủ web (web server). Dự án Nginx tập trung vào việc phục vụ số lượng kết nối đồng thời lớn (high concurrency), hiệu suất cao và sử dụng bộ nhớ thấp. Nginx được biết đến bởi sự ổn định cao, nhiều tính năng, cấu hình đơn giản và tiết kiệm tài nguyên.

## Nội dung ##
--------
1. Chuẩn bị
2. Compile nginx with rtmp module
3. Cấu hình file nginx.conf
4. Start nginx
5. Test live streaming
6. Nâng cao
7. Tài liệu tham khảo

## 1. Chuẩn bị: ##
-------- 
1. Download nginx-rtmp-module về máy tính bằng command sau:
    `git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git`
2. Download nginx mới nhất (Hiện tại là bản nginx-1.11.8)
    `wget http://nginx.org/download/nginx-1.11.8.tar.gz`

## 2. Compile nginx with rtmp module ##
--------
*Các bạn làm theo các bước sau:*
Bước 1: 
Cài các gói liên quan cho nginx:
    `sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev`

Bước 2:

    
    - cd ~
    - mkdir nginx
    - cd nginx
    - git clone https://github.com/sergey-dryabzhinsky/nginx-rtmp-module.git
    - wget http://nginx.org/download/nginx-1.11.8.tar.gz
    - tar -xf nginx-1.11.8.tar.gz
    - cd nginx-1.11.8
    - ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module
    - make -j 1
    - sudo make install
> **Notice:** Tham số **--add-module=../nginx-rtmp-module** là đường dẫn tới thư mục nginx-rtmp-module mà chúng ta vừa download ở trên.

## 3. Cấu hình file nginx.conf ##
--------
Sau khi thực hiện xong các bước ở bước 2 thì các bạn có thể cấu hình nginx bằng 1 trong 2 file sau: `/usr/local/nginx/conf/nginx.conf` hoặc `/etc/nginx/nginx.conf` (Thường thì sẽ cấu hình trong file `/usr/local/nginx/conf/nginx.conf`)
Xóa nội dung của file /usr/local/nginx/conf/nginx.conf và chèn nội dung bên dưới vào để kiểm tra rtmp có hoạt động không.
```
worker_processes  auto;
events {
    worker_connections  1024;
}

# RTMP configuration
rtmp {
    server {
        listen 1935; # Listen on standard RTMP port
        chunk_size 4000;
        application live {
            live on;
            record off;
        }
    }
}
```

Để kiểm tra file cấu hình có lỗi hay không dùng dòng lệnh: `/user/local/nginx/sbin/ngin -t`
TH1: Nếu khi run lệnh trên mà hiển thị lỗi này:![TH1]( https://content.screencast.com/users/thangepc/folders/Jing/media/38341d0a-c78b-4c4b-b48a-1126ce388a5f/2017-01-19_0915.png)
>Thì tạo và phân quyền cho file `/usr/local/nginx/logs/error.log`:
> `sudo touch /usr/local/nginx/logs/error.log`
 `sudo chmod 0777 /usr/local/nginx/logs/error.log`

TH2: 
![TH2](https://content.screencast.com/users/thangepc/folders/Jing/media/5a19b769-0898-42ae-8007-99f5f6ea07d9/2017-01-19_0926.png)
> Thì tạo và phân quyền cho file `/usr/local/nginx/logs/nginx.pid`:
`sudo touch /usr/local/nginx/logs/nginx.pid`
 `sudo chmod 0777 /usr/local/nginx/logs/nginx.pid`

TH3:
![TH3](https://content.screencast.com/users/thangepc/folders/Jing/media/997bf57b-1fdf-417d-9727-4a670e446582/2017-01-19_0928.png)
> Thì tạo và phân quyền cho file `/usr/local/nginx/logs/access.log`:
`sudo touch /usr/local/nginx/logs/access.log`
 `sudo chmod 0777 /usr/local/nginx/logs/access.log`
 

## 4. Start nginx ##
--------
Sau khi chèn xong bật nginx lên bằng dòng lệnh: 
- Bật nginx chạy background: `/user/local/nginx/sbin/nginx`
- Bật ngix chay : `/usr/local/nginx/sbin/nginx -g 'daemon off;'`

Để stop server nginx: `/usr/local/nginx/sbin/nginx -s stop`
## 5. Test live streaming ##
--------
Sau khi bật nginx thành công thì để test live streaming trên server nginx chúng ta cần chuẩn bị 1 số tool sau:
1. Phần mêm để push streaming lên server nginx: [OBSProject](https://obsproject.com/download)- Phần mền này sẽ nhận trách nhiệm đẩy video hoặc dữ liệu lên server
2. Phần mềm để đọc streaming(RTMP): 
    + [VLC media player](http://www.videolan.org/vlc/)
    + Trình duyệt hỗ trợ đọc RTMP(Google chrome): [https://www.hlsplayer.net/rtmp-player](https://www.hlsplayer.net/rtmp-player)
    + Hoặc có thể dùng VideoJS để đọc RTMP
#### Cấu hình cho OBS ####
    Có thể tham khảo ở đây: https://www.screencast.com/t/hwZlHZ66kj
#### Cầu hình VLC media player ####
    - Bật VLC media player lên sau đó vào `Media > Open Network Stream...`
    - Trong tab Network nhập URL RTMP vào ô "PLease enter a network URL" rồi nhấn `Play`
#### Sử dụng trình duyệt ####
    - Truy cập vào https://www.hlsplayer.net/rtmp-player 
    - Sau đó nhập URL RTMP và nhấn `Play`

## 6. Nâng cao ##
--------
Như vậy là chúng ta đã cấu hình và cho chạy thử thành công RTMP trên server nginx. Bây giờ có 2 trường hợp:
    1. Trong trường hợp tốc độ mạng thấp mà chất lượng video lại cao thì sẽ gây ra tình trạng đơ hoặc giật video. Để khắc phục tình trạng này chúng ta sẽ convert video sang độ phân giải thấp hơn.
    2. Hiện tại chúng ta đang sử dụng giao thức RTMP để play video, giao thức này chỉ phù hợp cho một số player trên trình duyệt. Để khắc phục tình trạng này chúng ta sẽ chuyển giao thức RTMP sang giao thức HTTP để mọi player có thể play được video streaming.

#### Trường hợp 1: ####
- Trong trường hợp này để có thể convert video về độ phân giải thấp hơn thì chúng ta cần cài [ffmpeg](http://ffmpeg.org/).
    ```
    sudo add-apt-repository ppa:mc3man/trusty-media
    sudo apt-get update
    sudo apt-get install ffmpeg
    sudo apt-get install libavcodec-extra
    ```
- Mở file /user/local/nginx/conf/nginx.conf
- Thêm đoạn lệnh sau vào dưới `record off;`
    `exec ffmpeg -re -i rtmp://localhost:1935/$app/$name -vcodec flv -acodec copy -s 32x32
                        -f flv rtmp://localhost:1935/small/${name};`
- Đoạn lệnh của chúng ta sẽ như sau:
    ```
        worker_processes  auto;
        events {
            worker_connections  1024;
        }
        
        # RTMP configuration
        rtmp {
            server {
                listen 1935; # Listen on standard RTMP port
                chunk_size 4000;
        
                application live {
                    live on;
                    record off;
                    exec ffmpeg -re -i rtmp://192.168.1.60:1935/$app/$name -vcodec flv -acodec copy -s 480x480 -f flv rtmp://192.168.1.60:1935/small/${name};
                }
                application small {
                    live on;
                    record off;
                }
            }
        }
    ```
    Giải thích:
    + Đoạn lệnh trên sử dụng ffmpeg để đọc `rtmp://192.168.1.60:1935/$app/$name` sau đó convert sang `rtmp://192.168.1.60/small/${name}`. Ở đây `$app` chính là `live` và `$name` chính là tên của video (Tên này có thể nhập bất ký)
    + Sau khi convert thì chúng ta có thể đọc đồng thời video gốc `rtmp://192.168.1.60:1935/live/demo` và video convert 480x480 `rtmp://192.168.1.60:1935/small/demo`
- Để có thể có nhiều độ phân giải khác nhau chúng ta cấu hình như sau:
    Thay đoạn này: 
    ```
        exec ffmpeg -re -i rtmp://192.168.1.60:1935/$app/$name -vcodec flv -acodec copy -s 480x480 -f flv rtmp://192.168.1.60:1935/small/${name};
    ```
    Bằng: 
    ```
        exec ffmpeg -i rtmp://192.168.1.60:1935/$app/$name -async 1 -vsync -1
            -c:v libx264 -c:a libvo_aacenc -b:v 256k -b:a 32k -vf "scale=480:trunc(ow/a/2)*2" -tune zerolatency -preset veryfast -crf 23 -f flv rtmp://192.168.1.60:1935/small/$name_low
            -c:v libx264 -c:a libvo_aacenc -b:v 768k -b:a 96k -vf "scale=720:trunc(ow/a/2)*2" -tune zerolatency -preset veryfast -crf 23 -f flv rtmp://192.168.1.60:1935/small/$name_mid
            -c:v libx264 -c:a libvo_aacenc -b:v 1024k -b:a 128k -vf "scale=960:trunc(ow/a/2)*2" -tune zerolatency -preset veryfast -crf 23 -f flv rtmp://192.168.1.60:1935/small/$name_high
            -c:v libx264 -c:a libvo_aacenc -b:v 1920k -b:a 128k -vf "scale=1280:trunc(ow/a/2)*2" -tune zerolatency -preset veryfast -crf 23 -f flv rtmp://192.168.1.60:1935/small/$name_hd720
            -c copy -f flv rtmp://192.168.1.60:1935/small/$name_src;
    ```
- Và thay:
    ```
        application small {
            live on;
            record off;
        }
    ```
    Bằng:
    
    ```
        application small {
            live on;
            record off;
            hls on; # Enable HTTP Live Streaming
            
            # Pointing this to an SSD is better as this involves lots of IO
            hls_path /mnt/hls/;
            
            # Instruct clients to adjust resolution according to bandwidth
            hls_variant _low BANDWIDTH=288000; # Low bitrate, sub-SD resolution
            hls_variant _mid BANDWIDTH=448000; # Medium bitrate, SD resolution
            hls_variant _high BANDWIDTH=1152000; # High bitrate, higher-than-SD resolution
            hls_variant _hd720 BANDWIDTH=2048000; # High bitrate, HD 720p resolution
            hls_variant _src BANDWIDTH=4096000; # Source bitrate, source resolution
        }
    ```
    > Chú ý: ở đây có "hls_path /mnt/hls/" thì /mnt/hls/ phải có quyền đọc và ghi


#### Trường hợp 2: ####


## 7. Tài liệu tham khảo: ##
--------
1. Source code nginx-rtmp-module: (https://github.com/sergey-dryabzhinsky/nginx-rtmp-module) Or (https://github.com/arut/nginx-rtmp-module)
2. Source code nginx lastest: http://nginx.org/en/download.html (hiện tại là bản 	nginx-1.11.8)
3. Setup multi resolutions for RTMP: [https://licson.net/post/setting-up-adaptive-streaming-with-nginx/](https://licson.net/post/setting-up-adaptive-streaming-with-nginx/)

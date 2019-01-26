# Proxy Server

Proxy server dựa trên HAProxy được sử dụng như một cách giảm việc dùng IP public của trường (đã hết) thông qua việc gom tất cả các dịch vụ vào 1 IP (IP của Proxy server) và định tuyến lại thông qua mạng nội bộ.

Ngoài ra có thể sử dụng Proxy với chức năng chính của nó là cân bằng tải.

Hiện tại Proxy có thể sử dụng cho các dịch vụ mạng thông qua giao thức TCP/IP như Web, MySQL, Mongo, Team Foundation....

## Thông tin Server

Gồm 1 server đặt trên hệ thống NW (VMware)

| HĐH | Phần mềm | IP | IP, account |
| --- | --- | --- | --- |
| CentOS 7 | HAProxy | 1 IP public (default gateway) và 1 IP nội bộ (liên lạc với các server nội bộ khác) | Liên lạc với quản trị |

[Quá trình cài đặt, cũng như giải thích cơ bản về HAProxy tham khảo ở link này](00_02_linux_server.md#haproxy)

## Một số đường dẫn quan trọng

- `/etc/haproxy/haproxy.cfg` File cấu hình toàn bộ cho hệ thống Proxy

## Các thao tác thường gặp

### Frontend / Backend và Listen

1 dịch vụ muốn sử dụng đều phải được cấu hình Frontend và Backend, Frontend là cách Proxy lắng nghe request từ bên ngoài vào, còn Backend là cách Proxy trao đổi với server nội bộ.

```
# Truy cập FTP nội bộ
# -------------------
# Ở Frontend, Proxy server lắng nghe kết các kết nối TCP ở các cổng 21
# và 60010-60500 (cổng passive mode khai báo trong cài đặt ftp).
# Sử dụng backend để chuyển kết nối đến server FTP nội bộ có địa chỉ 192.168.101.121
# thông qua cổng mặc định 21.
frontend ftp-in
    bind *:21
    bind *:60010-60500
    mode tcp
    option tcplog
    default_backend ftp-internal
...
backend ftp-internal
    mode tcp
    option tcplog
    server WIN-770CBTPAPP4 192.168.101.121 check port 21
```

Listen là cách khai báo gộp Frontend và Backend vào một section (thích hợp với các dịch vụ chỉ TCP)

```
# Truy cập MongoDB nội bộ
# -----------------------
# Proxy lắng nghe các kết nối TCP ở cổng 8080, và chuyển đến server Mongo nội bộ ở địa chỉ 192.168.101.47
# thông qua cổng mặc định 27017.
listen mongos
    option tcplog
    mode tcp
    bind *:8080
    server mongos1 192.168.101.47:27017
    redispatch
```

### Cung cấp truy cập bên ngoài cho 1 server web nội bộ thông qua subdomain .viethanit.edu.vn

Đây là trường hợp hay gặp nhất trong sử dụng Proxy tại trường. Thường các dịch vụ web đều muốn sử dụng cổng mặc định là 80, vì vậy, ta phải lắng nghe tất
cả các dịch vụ web thông qua 1 cổng này, và phân biệt backend bằng cách kiểm tra tên miền request

Ví dụ: Có một server web nội bộ có IP 192.168.101.200, từ bên ngoài có thể truy cập thông qua subdomain webtest.viethanit.edu.vn thì ta thực hiện như sau

- B1: Thiết lập DNS cho sub webtest.viethanit.edu.vn (bao gồm DNS nội bộ và DNS ngoài trên VDC/VNPT) trỏ về IP của Proxy Server
- B2: Truy cập Proxy Server bằng tài khoản root, mở file cấu hình của HAProxy `nano /etc/haproxy/haproxy.cfg`
- B3: Tìm đến `frontend http-web`
- B4: Thêm dòng `acl host_webtest   hdr(host) -i webtest.viethanit.edu.vn` trước dòng `acl host_vo11  hdr(host) -i viethanit.edu.vn`
- B5: Thêm dòng `use_backend webtest if host_webtest` ở gần cuối Frontend (sau các dòng use_backend đã có sẵn)
- B6: Khai báo backend webtest có nội dung sau

```
backend webtest
    mode http
    server webtest 192.168.101.200:80
```

- B7: Lưu và reload lại dịch vụ `systemctl reload haproxy`
- B8: Test thử truy cập web thông qua sub webtest.viethanit.edu.vn

### Thêm vào dịch vụ mới

Về cơ bản cách thêm vào được khai báo như mục hướng dẫn Frontend/Backend và Listen, khi cần khai báo cho dịch vụ nào (ví dụ MYSQL) thì nên tìm kiếm các hướng dẫn trên mạng, để việc cấu hình được đúng.
Có một số dịch vụ để chạy được đôi khi cần những thông số cấu hình riêng (ví dụ như Microsoft Team Foundation)

## Chú ý

Do đây là server có sử dụng IP public nên khi trong khai báo listen cổng nào, thì ngoài mở Firewall của hệ điều hành, người quản trị còn phải yêu cầu quản trị mạng mở firewall cứng.

Để truy cập các server nội bộ thì phải sử dụng IP nội bộ, do không phải là Default Gateway nên cần phải khai báo route để có thể truy cập được các máy chủ nội bộ nằm ở lớp mạng khác

```
route add 192.168.101.0/24 via 192.168.10.1
....
```

Thông qua HAProxy, các dịch vụ web quan trọng của trường cũng nên sử dụng nó như là dịch vụ cân bằng tải.

## Thông tin cần quản lý

- Cấu hình
- Định tuyến route cho server để nhìn thấy các lớp mạng khác
 
## Sao lưu
 
Sao lưu cấu hình với mỗi khi có thay đổi (`/etc/haproxy/haproxy.cfg`)

Nên có proxy server với cấu hình tương tự dựng sẵn ở 4 hệ thống NW, CS (WMware, Ovirt) để phòng trường hợp sự cố.

## Tạo mới và Phục hồi

Thiết lập lại 1 server Linux (CentOS, Ubuntu) và tiến hành cái đặt HAProxy như link tham khảo, sau đó copy lại file cấu hình đã được backup.

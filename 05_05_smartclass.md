# Server Lab Ngoại Ngữ

Là server để chạy chương trình học tiếng Anh của Lab ngoại ngữ. Sử dụng phần mềm SmartClass.

## Thông tin Server

Gồm 1 server đặt trên hệ thống NW (Ovirt)

| HĐH | Phần mềm | IP, account |
| --- | --- | --- |
| Windows Server 2012 | SmartClass | Liên lạc với quản trị |

Cài đặt bằng 2 file cài đặt đã được cung cấp, lưu trữ trong máy backup và FTP của trung tâm

## Một số đường dẫn quan trọng

- `C:/Smartclass-server`
- `C:/Smartclass-Web-Manager`

## Các thao tác thường gặp

### Chạy Server

- Khởi động Server trên oVirt
- Khởi động Phần mềm SmartClass trên server
 
## Thông tin cần quản lý

Bài giảng, bài làm của giảng viên nếu có.
 
## Sao lưu
 
Hiện tại phần dữ liệu bài giảng và bài làm của sinh viên không lưu trữ trên server này, nên không cần phải sao lưu

Nếu được có thể backup 2 thư mục `C:/Smartclass-server` và `C:/Smartclass-Web-Manager`
 
## Tạo mới và Phục hồi
 
- B1: Tiến hành cài đặt 1 Server Windows
- B2: Cài đặt phần mềm SmartClass bằng 2 file cài đặt
- B3: Kích hoạt bảng quyền, khi chạy server, vào mục Config, copy Site Code gửi cho công ty Sao Mai (anh Tâm), chờ nhận lại License Key để kích hoạt bản quyền.
- B4: Copy lại bài giảng, bài làm đã được backup (hiện tại không cần)

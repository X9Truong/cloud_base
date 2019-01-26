# Một số vấn đề liên quan đến bảo mật, firewall, port...

## Chung

 - Mật khẩu cho user và các tài khoản dịch vụ PHẢI đặt theo cấp độ khó
     - Có chữ thường
     - Có chữ hoa
     - Có số
     - Có ký tự đặt biệt
     - Trên 10 ký tự
     
 - Các server quan trọng PHẢI đặt chế độ cập nhật tự động, có kiểm tra theo định kỳ
 - Các server PHẢI bật firewall của hệ điều hành, chặn hết các port, chỉ mở tùy vào mục đích sử dụng

## Port mặc định

Với các máy chủ thông ra môi trường internet

 - Phải thay đổi port mặc định của dịch vụ SSH, không sử dụng port 22
 

## Firewall cứng

Đối với các dịch vụ sử dụng public IP, đi ra môi trường Internet, ngoài mở port trên firewall HĐH, còn phải mở port trên firewall cứng của hệ thống mạng.
Admin của server lập danh sách port, giao thức đưa cho người quản lý mạng để tiến hành mở server. Khi không cần dùng thì cũng báo để người
quản lý mạng đóng port.

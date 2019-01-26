# Một số thao tác dòng lệnh với các hệ điều hành Linux/Unix

## Soạn thảo, chỉnh sửa nội dung file (CentOS, Redhat, Ubuntu)

Để chỉnh sửa nội dung file (cấu hình, thiết lập...), sử dụng công cụ ``nano`` để dễ thao tác.
Trong trường hợp bất khả kháng thì sử dụng ``vi`` thì một số câu lệnh cần thiết để chỉnh sửa gồm

 - Di chuyển con trỏ: Sử dụng 4 phím mũi tên
 - Chỉnh sửa:
 - Lưu file và thoát: ``:x``
 
## Cấu hình mạng

### Chung

Hệ thống không sử DHCP, toàn bộ hướng dẫn dưới đây sử dụng IP tĩnh (static IP)

### CentOS, Redhat...
 
Cấu hình mạng thông qua các file cấu hình nằm trong thư mục ``/etc/sysconfig/network-scripts``,
các file cấu hình cho từng card mạng có dạng ``ifcfg-[tên card mạng]``

    cd /etc/sysconfig/network-scripts
    cat ifcfg-p3p1 # xem cấu hình card p3p1
    nano ifcfg-p3p1 # mở trình chỉnh sửa để cấu hình card p3p1
    
    # hoặc
    
    vi /etc/sysconfig/network-scripts/ifcfg-em3 # mở trình chỉnh sửa vi để cấu hình card em3

File cấu hình mạng cho card có nội dung sau

    DEVICE=[tên card mạng]
    BOOTPROTO=static
    ONBOOT=yes
    TYPE=Ethernet
    IPADDR=[địa chỉ IP]
    NETMASK=[địa chỉ Netmask]
    
    # nếu sử dụng card này là default gateway thì thêm địa chỉ gateway
    GATEWAY=[địa chỉ Gateway]
    
    # nếu có khai báo DNS

### Ubuntu, Debian...

Cấu hình mạng thông qua các file cấu hình  ``/etc/network/interface``, file này có dạng như sau

    # The loopback network interface
    auto lo
    iface lo inet loopback

    # card mạng ens192, static ip
    auto ens192
    iface ens192 inet staticip
      address 222.255.128.147
      netmask 255.255.255.240
      
      # nếu sử dụng card này là default gateway thì thêm địa chỉ gateway
      gateway 222.255.128.145
      
      # dns server
      dns-nameservers 8.8.8.8

## Route

Để thêm vào một route cho 1 gateway, sử dụng lệnh ``ip route add``

    ip route add 192.168.101.0/24 via 192.168.10.1
    
Lệnh trên sẽ đưa các truy cập vào gw 192.168.101.1 thông qua gw 192.168.10.1

## Thêm ổ cứng mới

Sau khi server được cấp ổ cứng mới, các thao tác để máy nhận diện và sử dụng ổ cứng mới gồm có phần vùng, định dạng, và mount

 - Sử dụng lệnh ``fdisk -l`` để xem tên ổ cứng mới thêm vào
 - Phân vùng ổ cứng bằng lệnh ``fdisk /path/new/disk``
 - Format ổ cứng sau khi phân vùng ``mkfs.xfs /path/new/pdisk`` cho định dạng xfs hoặc ``mkfs.ext4 /path/new/pdisk`` cho định dạng ext4
 - Mount ổ cứng vào một thư mục để sử dụng ``mount /path/new/pdisk /path/directory``
 - Khai báo mount cho lần khởi động tiếp theo, mở file ``/etc/fstab`` thêm dòng ``/path/new/pdisk /path/directory xfs defaults  0 0``
   
## Chia sẻ thư mục thông qua NFS (CentOS7)

Cài đặt và chạy các dịch vụ cần thiết

    yum install nfs-utils nfs-utils-lib
    systemctl enable nfs 
    systemctl start rpcbind
    systemctl start nfs
    
Để chia sẽ thư mục nào thì ta khai báo ở file ``/etc/exports``, ví dụ muốn chia sẽ thư mục ``/data01`` cho máy có địa chỉ ``100.100.20.57`` ta khai báo như sau

    nano /etc/exports
    # nhập khai báo mới
    /data01      100.100.20.57(rw,sync,no_root_squash)

Nếu muốn chia sẽ cho các máy khai trong lớp 100.100.20.0/24 ta thay đổi địa chỉ ip

    /data01      100.100.20.0/24(rw,sync,no_root_squash)

Sau đó hoàn tất việc chia sẽ bằng lệnh

    exportfs -a
    
Do chia sẽ qua mạng, nên chú ý firewall, SELinux... 

## Thay đổi Timezone

Đối với Ubuntu thực hiện lệnh sau

    sudo dpkg-reconfigure tzdata

Bảng lựa chọn Timezone của Ubuntu xuất hiện, chọn châu lục, chọn thành phố cần thiết lập Timezone.

Đối với CentOS 7, thực hiện lệnh sau

    sudo timedatectl set-timezone [time_zone]
    
    # ví dụ sudo timedatectl set-timezone America/Detroit

Trong CentOS 7, để xem danh sách Timezone, thực hiện lệnh sau

    timedatectl list-timezones
    
    # kết hợp grep để hạn chế kết quả, ví dụ để liệt kê các timezone ở Châu Á
    timedatectl list-timezones | grep Asia

Để kiểm tra lại kết quả, có thể thực hiện 2 lệnh sau

    date
    ls -l /etc/localtime

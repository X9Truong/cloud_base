# Những nội dung chưa được phân mục

## Một số vấn đề gặp phải

oVirt CS máy 64, theo dõi khởi động lại, fstab một số ổ cứng từ iSCSI Multipath không hoạt động dẫn đến khởi động vào được

    ---> reboot lại máy, ở cửa sổ boot, chọn 'e' để đăng nhập sysroot (tìm kiếm bài reset root password)
    ---> xóa các dòng /ect/fstab
    ---> khởi động
    ---> mount lại bình thường mount /dev/mapper/.... /....

## Cấu hình ổ cứng cho các server SCamp 2018

``sudo fdisk /dev/sdb`` -> n -> enter -> enter ... -> w -> enter

``sudo mkfs.xfs /dev/sdb1``

``sudo mount /dev/sdb1 /opt``

``sudo nano /etc/fstab`` -> add line ``/dev/sdb1 /opt xfs defaults 0 0``

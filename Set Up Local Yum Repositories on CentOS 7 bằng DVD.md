# Set Up Local Yum Repositories on CentOS 7 bằng DVD

Là một quản trị viên của hệ thống Linux, gần như có nghĩa vụ cài đặt các trên một máy chủ hoặc nhóm máy chủ. Tuy nhiên, không phải lúc nào cũng lý tưởng để cài đặt các gói từ các kho lưu trữ  CentOS chính thức  qua internet. Một ví dụ điển hình là khi chúng ta có kết nối internet chậm và muốn tiết kiệm băng thông bằng cách tạo một kho lưu trữ trên mạng LAN. Hoặc trên Môi trường có chính sách bảo mật nghiêm ngặt yêu cầu tất cả các bản vá của hệ thống phải được thực hiện trong mạng LAN.

Hướng dẫn này sẽ giúp chúng ta thiết lập một kho lưu trữ CentOS 7 YUM cục bộ trong máy chủ của chúng ta. Bài viết đã cung cấp tất cả các bước cần thiết để có một kho lưu trữ yum cục bộ hoạt động được tạo bằng cách sử dụng  CentOS DVD ISO image . 

## 1 Step 1: Download CentOS-7-x86_64-Everything ISO or CentOS 7 DVD ISO image

In this guide I’ll use the CentOS-7-x86_64-Everything-2009 ISO image which can be downloaded with curl or wget command:

```
curl -O http://centos.mirror.liquidtelecom.com/7.9.2009/isos/x86_64/CentOS-7-x86_64-Everything-2009.iso
```

You can as well use CentOS 7 DVD ISO:

```
curl -O http://centos.mirror.liquidtelecom.com/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso
```



# Tìm hiểu cấu trúc Ubuntu-Debian-Repository cơ bản

Một số file thông tin về Package trên hệ thống lưu trữ Debian Repository chúng ta cần lưu ý:
- File "Release" (http://us.archive.ubutu.com/ubuntu/dists/xenial-updates/Release): file chưa thông tin index và chuỗi hash của các file khác chứ thông tin dữ liệu thứ cấp nằm trong mục $distribution ($ARCHIVE_ROOT/dists/$DISTRIBUTION)
- Thông thường chương trình apt sẽ tiến hành request để lấy file này về mà đọc nó; sau đó các thông tin file thứ cấp để truy xuất DL cần thiết.
- Thư mục pool nằm dưới cấp thư mục domain uri và cùng cấp thư mục dists. Thư mục này sẽ chứa toàn bộ file cài đặt, chương trình .deb, thư viện, driver, v.v... theo cấp thư mục Aphabet. Sau khi apt đọc thông tin từ Repo, chương trình apt sẽ tìm đến thưu mục pool để tiến hành Download chương trình cần thiết về OS để cài đặt




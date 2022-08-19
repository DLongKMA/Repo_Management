# Tổng quan về Repo

## 1. Package management
Package management là một phương thức nhằm cài đặt và bảo trì các phần mềm được cài đặt trong hệ thống. Mỗi một bản phân phối Linux lại có một hệ thống phân phối package riêng được gọi là packaging system.

Các thành phần cơ bản trong packaging system đó là:

Package files: đơn vị cơ bản nhất của một package và được quản lý bởi maintainer, là một dạng file compressed chứa tất cả thông tin cần thiết để cài đặt chương trình.
Repositories: Một nơi tập trung chứa các package files, một distribution có thể có một vài repositories khác nhau phục vụ nhiều mục đích khác nhau, hoặc phục vụ cho từng quá trình phát trình phần mềm (repository cho quá trình test,...).
Dependencies: Một package thì rất it khi mang tính standalone mà nó thường được xây dựng dựa trên các package khác, cũng giống như khi các bạn lập trình, khi cài đặt sử dụng một thư viện thì thư viện đó đều xây dựng dựa trên một thư viện khác, các thư viện khác đó gọi là dependencies.
Có hai loại packaging system phổ biến nhất là Debian và RedHat:

Packaging system	Distributions
Debian-style (.deb)	Debian, Ubuntu, Linux Mint, Raspbian
Red Hat–style (.rpm)	Fedora, CentOS, Red Hat Enterprise Linux, OpenSUSE
Packaging system thì được chia ra làm hai loại công cụ, gọi là high và low level package tools:

Low-level package tool sẽ chịu trách nhiệm cho các tác vụ như là install, và remove package files.
High-level package tool xử lý các tác vụ liên quan đến tìm kiếm thông tin metadata và cài đặt dependencies.
Các công cụ package tool được liệt kê trong bảng sau:

Distributions	Low-level tool	High-level tool
Debian-style	dpkg	apt-get, apt, aptitude
Fedora, Red Hat Enterprise Linux, CentOS	rpm	yum, dnf
Một vài câu lệnh thường dùng

Tìm kiếm package trên repository
Sử dụng high-level packaging tool để tìm kiếm thông tin ở metadata của repo, một package có thể xác định bằng tên package, hoặc description của nó:

Packaging system	Command
Debian	apt-cache search search_string
RedHat	yum search search_string
Cài đặt package từ repository
Một lệnh quá quen thuộc với người dùng Linux, dùng để cài đặt package hay chương trình từ repo, lệnh này cũng sẽ auto cài đặt các dependency cần thiết cho chương trình.

Packaging system	Command
Debian	apt-get install package_name
RedHat	yum install package_name
Cài đặt package từ package file
Một lệnh khác cũng được sử dụng để cài đặt chương trình hoặc package từ package file đã được download sẵn về máy, tuy nhiên lệnh low-level không có dependency resolution nên nó sẽ không auto cài đặt các dependencies cần thiết.

Packaging system	Command
Debian	dpkg -i package_file
RedHat	rpm -i package_file
Remove package
Packaging system	Command
Debian	apt-get remove package_name
apt-get purge package_name
dpkg -P package_file
RedHat	yum erase package_name
Ở trên là các lệnh được sử dụng để remove một package ra khỏi hệ thống.

Nhiều người thường hay gặp vấn đề về việc muốn reinstall một package, xảy ra khi họ muốn xóa hết toàn bộ một package rồi install lại nhưng khi install lại thì lại gặp lỗi y hệt lần cài đặt trước do các file config vẫn giữ y hệt lúc họ remove. Mình cũng đã trải nghiệm điều này nhiều lần trên Ubuntu nên vì thế ở lệnh với Debian distribution có tới tận 3 lệnh với những lưu ý khác nhau theo kinh nghiệm của mình:

apt-get remove package_name để remove package cùng với các dependencies liên quan của nó.

apt-get purge package_name cũng như câu trên nhưng đồng thời xóa cả những file config liên quan, trừ những file tại user's home.

dpkg -P package_file cũng như câu purge của tool apt-get nhưng vì là low-level tool nên nó không remove những dependency liên quan.

Ngoài ra, có một lệnh khác liên quan tới remove của high-level tool đó là apt-get autoremove sử dụng để remove những package là dependency của package khác, nhưng do trong quá trình update không còn cần sử dụng tới nữa nên sẽ được loại bỏ khi chạy lệnh này.

Update package từ repository
Lệnh sau sử dụng để update cùng lúc tất cả các package trong system với version mới nhất trên repo.

Packaging system	Command
Debian	apt-get upgrade
RedHat	yum update
Upgrade package từ file system
Đối với những package đã download sẵn file package chứa version mới nhất về máy, ta có thể chạy lệnh low-level sau để update

Packaging system	Command
Debian	dpkg -i package_file
RedHat	rpm -U package_file
Liệt kê package
Lệnh sau được sử dụng để liệt kê các package đã được cài đặt trong hệ thống

Packaging system	Command
Debian	dpkg --list
RedHat	rpm -qa
Xác định package đã được install chưa
Packaging system	Command
Debian	dpkg -s package_name
RedHat	rpm -q package_name
Hiển thị thông tin của package đã được install
Packaging system	Command
Debian	apt-cache show package_name
RedHat	yum info package_name
Tìm xem package nào đã cài đặt một file bất kỳ nào đó.
Packaging system	Command
Debian	dpkg -S file_name
RedHat	rpm -qf file_name

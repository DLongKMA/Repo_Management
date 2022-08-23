# Set Up Local Yum Repositories on CentOS 7 bằng Nginx

## 1. Step 1: Install NGINX Web Server!

You can create repository either on a dedicated server or VPS, you can find cheap vps offers in the vps offers section to host your yum repository.

I am installing NGINX web server as a first step because we’ve to later publish our repository so that anyone from the internet can download packages from the repository.

First, let us bring all packages to their latest versions using:

```
yum update
```
NGINX is not available in any of the repositories that ships with CentOS 7, so we’ve to add EPEL repository, you can add EPEL repository using:

```
yum install epel-release
```
NGINX và tất cả các điều kiện tiên quyết của nó đều có sẵn trong kho lưu trữ này, vì vậy hãy phát hành lệnh cài đặt NGINX:

```
yum install nginx
```

First, let us start Nginx

```
systemctl start nginx
systemctl enable nginx
```

## Step 2: Setup directory for Repo!

We will now create our repository. As you can see in the screenshot above, you can see that NGINX default directory is

“/usr/share/Nginx/html”, we will use this directory to save our packages.

```
Làm việc với thư viện của nginx: /usr/share/Nginx/html
```

Chúng ta cũng có thể sử dụng bất kỳ thư mục nào khác, nhưng sau đó chúng ta sẽ phải định cấu hình một `máy chủ ảo`-`virtual host` trong NGINX và điều đó sẽ thực sự tốn thời gian. Vì vậy, nếu VPS chỉ được sử dụng cho kho lưu trữ của bạn (VPS is only being used for your repository) thì thư mục này là lựa chọn tốt nhất.

First, remove everything present inside this directory:

```
cd /usr/share/nginx/html
rm . *
```

Our directory is now ready to host packages.

## Step 3: Install Createrepo package!

“createrepo” is the package that will be used to create the repository metadata. There is no need to enable any extra repository as it is available in the default Centos 7 repos.

Createrepo” là gói sẽ được sử dụng để tạo siêu dữ liệu của kho lưu trữ. Không cần phải kích hoạt bất kỳ kho lưu trữ bổ sung nào vì nó có sẵn trong kho lưu trữ Centos 7 mặc định.

```
yum install createrepo
```

## Step 4: Download Packages to repository!
### Directly download packages

Trước khi tạo kho lưu trữ của mình, chúng ta phải thêm một số gói RPM vào đó. Bạn có thể tải về các Package tại [đây](https://centos.pkgs.org/). Ở đây, chúng ta sẽ tải xuống một gói thử nghiệm có tên GeoIP-data.

```
Nghiên cứu sự khác nhau giữa các Package
```

```
cd /usr/share/nginx/html
wget http://mirror.centos.org/centos/7/os/x86_64/Packages/GeoIP-data-1.5.0-11.el7.noarch.rpm
```

Các lệnh ở trên sẽ tải gói GeoIP-Data vào thư mục kho lưu trữ của chúng ta đã chọn ở trên. 

## Step 5: Create repository!

Khi chúng ta đã tải xuống tất cả các gói được yêu cầu hoặc chọn đồng bộ hóa một kho hiện (đã) có, chúng ta đã sẵn sàng tạo kho lưu trữ của mình bằng cách sử dụng:

```
cd /etc/yum.repos.d
vim cybercp.repo
```

Thực hiện sửa đổi nội dung file như sau:

```
[cybercp]
name=Cyber CP
baseurl=http://<IP Address of your virtual machine>
gpgcheck=0
```

Tới đây chúng ta đã hoàn thành quá trình cài đặt một local repo bằng Nginx trên CentOS 7.

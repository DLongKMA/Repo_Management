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

Chúng tôi cũng có thể sử dụng bất kỳ thư mục nào khác, nhưng sau đó chúng tôi phải định cấu hình một máy chủ ảo trong NGINX và điều đó sẽ thực sự tốn thời gian. Vì vậy, nếu VPS chỉ được sử dụng cho kho lưu trữ của bạn thì thư mục này là lựa chọn tốt nhất.

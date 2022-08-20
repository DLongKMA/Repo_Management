# Set Up Local Yum Repositories on CentOS 7 bằng DVD

Là một quản trị viên của hệ thống Linux, gần như có nghĩa vụ cài đặt các trên một máy chủ hoặc nhóm máy chủ. Tuy nhiên, không phải lúc nào cũng lý tưởng để cài đặt các gói từ các kho lưu trữ  CentOS chính thức  qua internet. Một ví dụ điển hình là khi chúng ta có kết nối internet chậm và muốn tiết kiệm băng thông bằng cách tạo một kho lưu trữ trên mạng LAN. Hoặc trên Môi trường có chính sách bảo mật nghiêm ngặt yêu cầu tất cả các bản vá của hệ thống phải được thực hiện trong mạng LAN.

Hướng dẫn này sẽ giúp chúng ta thiết lập một kho lưu trữ CentOS 7 YUM cục bộ trong máy chủ của chúng ta. Bài viết đã cung cấp tất cả các bước cần thiết để có một kho lưu trữ yum cục bộ hoạt động được tạo bằng cách sử dụng  CentOS DVD ISO image . 

## 1. Step 1: Download CentOS-7-x86_64-Everything ISO or CentOS 7 DVD ISO image

In this guide I’ll use the CentOS-7-x86_64-Everything-2009 ISO image which can be downloaded with curl or wget command:

```
curl -O http://centos.mirror.liquidtelecom.com/7.9.2009/isos/x86_64/CentOS-7-x86_64-Everything-2009.iso
```

You can as well use CentOS 7 DVD ISO:

```
curl -O http://centos.mirror.liquidtelecom.com/7.9.2009/isos/x86_64/CentOS-7-x86_64-DVD-2009.iso
```

## 2. Step 2:  Create Mount points and Mount ISO image

After downloading, we’ll have to mount it. There are two mounting Options we can use:

### 2.1. Mounting the ISO file to a local directory in our Linux system

Create a directory for mounting

```
sudo mkdir -p /mnt/centos7
```

Mount ISO image of CentOS Everything or CentOS 7 DVD ISO

```
sudo mount -t iso9660 -o loop CentOS-7-x86_64-Everything-2009.iso /mnt/centos7
```

```
NOTE
Cần tìm hiểu thông số "iso96690"
```

Confirm it’s mounted using du or ls command:

```
du -sch /mnt/centos7/*
```
```
512	/mnt/centos7/CentOS_BuildTag
8.4M	/mnt/centos7/EFI
512	/mnt/centos7/EULA
18K	/mnt/centos7/GPL
68M	/mnt/centos7/images
60M	/mnt/centos7/isolinux
498M	/mnt/centos7/LiveOS
9.0G	/mnt/centos7/Packages
29M	/mnt/centos7/repodata
2.0K	/mnt/centos7/RPM-GPG-KEY-CentOS-7
2.0K	/mnt/centos7/RPM-GPG-KEY-CentOS-Testing-7
3.0K	/mnt/centos7/TRANS.TBL
9.6G	total
```

Sau Khi mount xong, thư mục /mnt/centos7 sẽ như sau:

![image](https://user-images.githubusercontent.com/75653012/185727669-e751a1d9-2af2-4558-807e-a5e2a2b1c79b.png)


### 2.2. Mounting the ISO image file to a web server directory

```
Cách này sẽ có 1 bài viết riêng
```

I’m using Apache server running on another CentOS server.

```
sudo yum -y install httpd
sudo systemctl enable --now httpd
```

If you’re doing this on any other Linux server, replace the root directory of web server with the one provided here.

Create a directory for mounting:

```
sudo mkdir /var/www/html/centos7/
sudo mount -t iso9660 -o loop CentOS-7-x86_64-Everything-2009.iso /var/www/html/centos7/
```

The output you get from last command is:
```
mount: /dev/loop0 is write-protected, mounting read-only
```

The contents of the /var/www/html/centos/ directory should be as shown:

```
ls -lh /var/www/html/centos7/
```
```
total 1.7M
-rw-r--r--  1 root root   14 Oct 29  2020 CentOS_BuildTag
drwxr-xr-x  3 root root 2.0K Oct 26  2020 EFI
-rw-rw-r-- 17 root root  227 Aug 30  2017 EULA
-rw-rw-r-- 17 root root  18K Dec  9  2015 GPL
drwxr-xr-x  3 root root 2.0K Oct 26  2020 images
drwxr-xr-x  2 root root 2.0K Oct 26  2020 isolinux
drwxr-xr-x  2 root root 2.0K Oct 26  2020 LiveOS
drwxr-xr-x  2 root root 1.6M Oct 29  2020 Packages
drwxr-xr-x  2 root root 4.0K Oct 29  2020 repodata
-rw-rw-r-- 17 root root 1.7K Dec  9  2015 RPM-GPG-KEY-CentOS-7
-rw-rw-r-- 15 root root 1.7K Dec  9  2015 RPM-GPG-KEY-CentOS-Testing-7
-r--r--r--  1 root root 2.9K Nov  2  2020 TRANS.TBL
```

If you had CentOS 7 DVD ISO image on CD/DVD drive. Create a mount point and mount cdrom using the following commands:

```
sudo  mkdir -p /mnt/cent/cdrom
sudo mount /dev/cdrom /mnt/cent/cdrom
```

## 3. Step 3: Create a repo and put it inside /etc/yum/repos.d/ directory

This is done on Server with CentOS minimal installation, or server that you need to install packages on while pulling the packages from local repository you just added.

### 3.1. For local disk mount on /mnt

If you used local directory mount option then configure like below.

Create repository file:

```
sudo vim /etc/yum.repos.d/centos7-local.repo
```

Add contents which looks like this:

```
[centos7-local]
name=centos7-local
baseurl=file:///mnt/centos7/
enabled=0
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

### 3.2. For repository files on http server

The IP address of the server I’m using is 10.168.6.60. Below is the repo configuration.

```
[centos7-local]
name=centos7-local
baseurl=http://192.168.1.60/centos7
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

Browsing to the Web Server centos directory:

![image](https://user-images.githubusercontent.com/75653012/185557422-79ac5f37-d646-47f0-9dc8-47a56107102a.png)

### 3.3. For cdrom mount point /mnt/centos/cdrom/

If you used the mount point of /mnt/centos/cdrom/:

```
[centos7-local]
name=centos7-local
baseurl=file:///mnt/cent/cdrom/
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
```

Then do yum clean all remove cached date and update repo list

```
sudo yum clean all
```

```
NOTE: 
Enabling the repository locally added alone is not necessary enough to let us install software packages from it. 
This is because by default, any package being installed will use CentOS Base repository. We’ll discuss on a working solution to this shortly.
```

Package groups available in CentOS 7 repository are:

```
sudo yum group list
```

![image](https://user-images.githubusercontent.com/75653012/185728244-39e6a478-d0f9-4702-90dd-513d0344c1fb.png)


Khi các repo CentOS khác được bật và chúng ta chỉ định centos-local repo, ta sẽ luôn cố gắng tải xuống `gói mới nhất`-`latest package` từ repo `có gói mới nhất`-`with the latest package`. Repo mặc định là `CentOS Base repo`.

![image](https://user-images.githubusercontent.com/75653012/185728357-c2a5533c-cd64-4a35-a64b-b6c05afcf8a8.png)


Nếu chúng ta muốn `yum` sử dụng `kho lưu trữ cục bộ`-` local repository`, chúng ta phải tắt-disable tất cả các kho-repos lưu trữ khác và kích hoạt-enable chúng sau khi hoàn tất. Chúng ta có hai tùy chọn có thể sử dụng để đạt được điều này.

## 4. Option

### 4.1. Option 1: Move all other repositories to backup folder

The simplest solution can be like below:

```
mkdir ~/repos
cp -r /etc/yum.repos.d/* ~/repos/
ls -l ~/repos/
rm -rf /etc/yum.repos.d/*
ls -l /etc/yum.repos.d/
```

Copy local repository file back - Sao chép lại tệp kho lưu trữ cục bộ:

```
sudo cp ~/repos/centos7-local.repo /etc/yum.repos.d/
```

Check to see if successfully added:

```
sudo yum repolist
```

Example of package installation from Local repository:

![image](https://user-images.githubusercontent.com/75653012/185557698-49fe23cf-557f-4d05-8acc-c490ded60e66.png)


To install all packages belonging to “GNOME Desktop” Environment Group, we’ll do:

```
sudo yum groupinstall "GNOME Desktop"
```

### 4.2. Option 2: Using --enablerepo=centos7-local command option and --disablerepo=* command option

Alternatively, use `--enablerepo` and `--disablerepo` command options accordingly.

See below example which installs all packages belonging to “Virtualization Host” Environment Group, we’ll do:

```
sudo yum --disablerepo=* --enablerepo=centos7-local groupinstall "Virtualization Host"
```

Installing single packages:

```
sudo yum --disablerepo=* --enablerepo=centos7-local install vim
```

Later on you can return the repos to original directory:
```
cp -r ~/repos/* /etc/yum.repos.d/
```

That’s all for now on how to configure CentOS 7 Local Yum repositories. Remember to copy repositories back from ~/repos/ to /etc/yum.repos.d/ directory if you need to use other YUM repositories initially configured on the server. We’ll share a similar guide for EPEL and other commonly used CentOS repositories not from OS upstream project.



# Thiết lập Local Yum Repositories trên Centos 7

Trong Linux, repository là `cơ sở dữ liệu trung tâm`-`central database` của phần mềm. Các bản phân phối Linux có một kho lưu trữ trung tâm cho hệ thống và phần mềm thường được sử dụng. Thông thường, các quản trị viên có thể có nhu cầu về một gói phần mềm chuyên dụng hoặc để quản lý băng thông mạng. Vì những lý do này, chúng ta cần thiết lập một local repository tùy chỉnh mới. 
Yum là một local repository cho các tệp gói RPM. Các packages compress này có sẵn cho các bản phân phối Linux. Với repository, chúng ta có thể tải xuống, cài đặt và giữ các gói trên đĩa cục bộ hoặc từ xa.

## 1. Điều kiện tiên quyết
- Access to a user account with root or sudo privileges
- Access to a terminal window/command-line (Menu > Applications > Utilities > Terminal)
- The YUM (Yellowdog Updater Modified) package manager, installed by default

## 2. Các bước thực hiện yêu cầu 
### Step 1: Configure Network Access

Đầu tiên, việc duy trì một yum repository yêu cầu ta phải cấu hình hệ thống để truy cập mạng.

Yum thường phân phối tệp qua FTP hoặc HTTP. Tuy nhiên, ta không thể định cấu hình cho cả hai, vì vậy hãy quyết định phương pháp nào chúng ta muốn sử dụng.

If you already have the system configured as a web server with Apache or an FTP server with vsftpd, skip to Step 2.

To use HTTP, install the Apache web services package with the command:

```
sudo yum install httpd
```

If you are using FTP instead, install the vsftpd software package with:

```
sudo yum install vsftpd
```
```
Note: Instead of Apache, you can also use Nginx as a web server. 
Before doing so, make sure you open the correct ports on your firewall (Port 80 for HTTP, Port 21 for FTP).
```

### Step 2: Create Yum Local Repository

One helpful tool is the createrepo software package. Phần mềm này gộp một số tệp .rpm lại với nhau thành một kho lưu trữ repomd . Cài đặt phần mềm bằng cách sử dụng câu lệnh sau:

```
sudo yum install createrepo
```

Next, install `yum-utils` cung cấp cho hệ thống của bạn một toolbox tốt hơn để quản lý repo. Cài đặt yum-utils bằng sử dụng câu lệnh sau:

```
sudo yum install yum-utils
```

### Step 3: Create a Directory to Store the Repositories

Then, create a directory for an HTTP repository using:

```
sudo mkdir –p /var/www/html/repos/{base,centosplus,extras,updates}
```

Alternaticreate an FTP directory by typing the following:

```
sudo mkdir –p /var/ftp/repos
```

### Step 4: Synchronize HTTP Repositories

Tải xuống bản sao cục bộ của kho lưu trữ CentOS chính thức vào máy chủ của chúng ta. Điều này cho phép các clients trên cùng một mạng cài đặt các bản cập nhật hiệu quả hơn.

To download the repositories, use the commands:

```
sudo reposync -g -l -d -m --repoid=base --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=centosplus --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=extras --newest-only --download-metadata --download_path=/var/www/html/repos/
sudo reposync -g -l -d -m --repoid=updates --newest-only --download-metadata --download_path=/var/www/html/repos/
```

Hệ thống sẽ tiếp cận và tải xuống các bản sao của các kho lưu trữ chính thức.

In the previous commands, the options are as follows:

- –g: cho phép chúng ta xóa hoặc gỡ cài đặt các gói trên CentOS không kiểm tra được GPG
- –l: yum plugin support
- –d: lets you delete local packages that no longer exist in the repository
- –m: lets you download comps.xml files, hữu ích để đóng gói các nhóm gói theo chức năng
- ––repoid: chỉ định ID kho lưu trữ
- ––newest-only: chỉ tải xuống phiên bản gói mới nhất, giúp quản lý kích thước của kho lưu trữ
- ––download-metadata – download non-default metadata
- ––download-path – specifies the location to save the packages

If you’re using FTP, substitute your FTP directory in the commands above. You can also use your installation CD as a source for repositories.

First, mount the cd, then copy the files into your FTP directory with the following:

```
cp /media/packages/* /var/ftp/repos
```

### Step 5: Create the New Repository

Chúng ta sẽ sử dụng tiện ích createrepo để tạo một kho repo lưu trữ.

To create the repository for HTTP use the command:

```
sudo createrepo /var/www/html
```

Similarly, create a repository for FTP, enter the following:

```
sudo createrepo /var/ftp
```

### Step 6: Setup Local Yum Repository on Client System

Now set up a local Yum Repository on a clients machine.

- 1. First, switch to the client system and login as a user with root or sudo privileges.
- 2. Tiếp theo, chúng ta sẽ cấu hình để ngăn yum tải xuống sai vị trí. Để thực hiện việc này, hãy di chuyển các tệp kho lưu trữ yum mặc định bằng lệnh sau:
  
  ```
  mv /etc/yum.repos.d/*.repo /tmp/
  ```
  
  This command stores the files in the /tmp/ directory. You can substitute any other location you’d like.

- 3. Create and edit a new config file:
  
  ```
  sudo nano /etc/yum.repos.d/remote.repo
  ```
  
  The system should open a new file in a text editor.

- 4. In the new file, enter the command (replacing the IP address with the IP address of your server):
  
  ```
  [remote]
  name=RHEL Apache
  baseurl=http://192.168.1.10
  enabled=1
  gpgcheck=0
  ```

- 5. Finally, save the file and exit.

  If you’re configuring for FTP, use the following instead (replacing the IP address with the IP address of your server):
  
  ```
  [remote] 
  name=RHEL FTP
  baseurl=ftp://192.168.1.10
  enabled=1
  gpgcheck=0
  ```
  
- Step 7: Test the Configuration
  
  While still on the client system, run a command to install a package with the yum package manager:
  ```
  sudo yum install httpd
  ```
  
  The system should accordingly reach out to your server and install the software.

## 3. Conclusion

After following these steps, you now know how to set up a local YUM repository on CentOS 7.

In addition to downloading the default repositories (or copying them from the CD), you can also copy any other software packages into your storage folder. This provides a simple and central location that users can access and install software.

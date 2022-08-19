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

```
systemctl enable httpd
systemctl restart httpd
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

The createrepo command reads through the directory with rpm packages and creates a new directory called “repodata” in it.  This directory contains the metadata information for the repository. Every time you add additional rpm package files to your yum repository, you need to re-create the repository metadata with the “createrepo” command.

![image](https://user-images.githubusercontent.com/75653012/185539264-8381c290-e295-4412-bbfe-1a4279ed325c.png)

Lệnh `createrepo` đọc qua thư mục với các gói rpm và tạo một thư mục mới có tên là “repodata” trong đó. Thư mục này chứa thông tin siêu dữ liệu cho kho lưu trữ. Mỗi khi bạn thêm các tệp gói rpm bổ sung vào kho lưu trữ yum của mình, bạn cần tạo lại siêu dữ liệu của kho lưu trữ bằng lệnh “createrepo”.

![image](https://user-images.githubusercontent.com/75653012/185539452-ad09895f-bbcf-4a24-8860-8a115f389698.png)

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

Câu lệnh mẫu:

```
createrepo <path_to_your_directory_with_rpms>
```

To create the repository for HTTP use the command:

```
sudo createrepo /var/www/html
```

If you already created the repository metadata and you are just adding new packages to it you need to update the repo:

```
createrepo --update /var/www/html
```

Similarly, create a repository for FTP, enter the following:

```
sudo createrepo /var/ftp
```

### Step 6: Setup Local Yum Repository on Client System

#### a. Bổ sung kiến thức

A yum repository has its own configuration file and there are a few rules for it:
- It must be located in /etc/yum.repos.d/ directory
- Nó phải có phần mở rộng .repo, để được yum công nhận

File options are:
- Repository ID: One word unique repository ID (example: [myrepo])
- Name: Human-readable name of the repository (example: name=My Repository)
- Baseurl: URL to the repodata directory. You can use file://path if repository is located locally or ftp://link, http://link, https://link if repository is located
- remotely: HTTP Authentication available http://user:password@www.
- Enabled: Enable repository when performing updates and installs (example: enabled=1)
- Gpgcheck: Enable/disable GPG signature checking - Tùy chọn: Bật kho lưu trữ khi thực hiện cập nhật và cài đặt (example: gpgcheck=1)
- Gpgkey: URL to the GPG key (example: gpgkey=http://mirror.cisp.com/)
- Exclude: List of the packages to exclude - loại trừ (example: exclude=httpd,mod_ssl)
- Includepkgs: List of the packages to include (example: include=kernel)

Required yum repository configuration file options are:
- Repository ID
- Name
- Baseurl
- Enabled

For example:

```
[customrepo]
name=CustomRepository
baseurl=file:///opt/rpms
enabled=1
gpgcheck=0
```

#### b. Tiến hành cấu hình

Now set up a local Yum Repository on a clients machine.
1. First, switch to the client system and login as a user with root or sudo privileges.
2. Tiếp theo, chúng ta sẽ cấu hình để ngăn yum tải xuống sai vị trí. Để thực hiện việc này, hãy di chuyển các tệp kho lưu trữ yum mặc định bằng lệnh sau:
  
  ```
  mv /etc/yum.repos.d/*.repo /tmp/
  ```
  
  This command stores the files in the /tmp/ directory. You can substitute any other location you’d like.

  - Create and edit a new config file:
  
  ```
  sudo nano /etc/yum.repos.d/remote.repo
  ```
  
  The system should open a new file in a text editor.

  - In the new file, enter the command (replacing the IP address with the IP address of your server):
  
  ```
  [remote]
  name=RHEL Apache
  baseurl=http://192.168.1.10
  enabled=1
  gpgcheck=0
  ```

  - Finally, save the file and exit.

  If you’re configuring for FTP, use the following instead (replacing the IP address with the IP address of your server):
  
  ```
  [remote] 
  name=RHEL FTP
  baseurl=ftp://192.168.1.10
  enabled=1
  gpgcheck=0
  ```
  
### Step 7: Test the Configuration
  
  While still on the client system, run a command to install a package with the yum package manager:
  ```
  sudo yum install httpd
  ```
  
  Và đây là kết quả sau khi cài đặt:
  
  ![image](https://user-images.githubusercontent.com/75653012/185535623-675b9fc6-aa0c-46df-ae4a-0564c3f81d9c.png)

  
  Còn trên máy Server (10.168.6.61), ta cài đặt thử 1 package mới và kết quả sẽ như sau:
  
  ![image](https://user-images.githubusercontent.com/75653012/185536560-57138eff-4f29-4f5f-ab4e-1730da85abf5.png)
  
  Repo bây giờ là repo mà ta đã thiết lập trong thư mục đã tạo ở bước 2
  
  The system should accordingly reach out to your server and install the software.

## 3. Conclusion

After following these steps, you now know how to set up a local YUM repository on CentOS 7.

In addition to downloading the default repositories (or copying them from the CD), you can also copy any other software packages into your storage folder. This provides a simple and central location that users can access and install software.

```
root@ubuntu:/var/www/repo# ll
total 3996684
drwxr-xr-x 3 root     www-data       4096 Sep 14 20:17 ./
drwxr-xr-x 4 www-data www-data       4096 Jun 23 20:27 ../
lrwxrwxrwx 1 root     root             12 Dec 15  2021 centos -> /repo/centos/
-rw-r--r-- 1 www-data www-data        910 Dec 15  2021 centos_guide.html
```

Tại sao lại chia ra làm 3 folder chính:
- extras
- os
- updates

Khác biệt với Lab có 4 folder lần lượt là: base,centosplus,extras,updates, liệu base và centosplus đã xác nhập với nhau ? => Cần tìm hiểu 

```
root@ubuntu:/var/www/repo# ll /repo/centos/
total 52
drwxr-xr-x 11 www-data www-data  4096 Aug 27  2020 ./
drwxr-xr-x 17 www-data www-data  4096 Jun 23 13:40 ../
drwxr-xr-x  4 root     root      4096 Sep 11 11:43 extras.6/
drwxr-xr-x  4 root     root      4096 Sep 11 11:39 extras.7/
drwxr-xr-x  4 root     root      4096 Sep 11 11:33 extras.8/
drwxr-xr-x  4 root     root      4096 Sep 11 11:42 os.6/
drwxr-xr-x  4 root     root      4096 Sep 11 11:38 os.7/
drwxr-xr-x  4 root     root      4096 Sep 11 11:33 os.8/
drwxr-xr-x  4 root     root     12288 Sep 11 11:43 updates.6/
drwxr-xr-x  4 root     root      4096 Sep 11 11:39 updates.7/
drwxr-xr-x  3 root     root      4096 Sep 11 11:33 updates.8/
```

Cần phải tìm hiểu kỹ, các file repodata và Packages trong file /extras để làm gì:

```
root@ubuntu:/var/www/repo# ll /repo/centos/extras.6/
total 28
drwxr-xr-x  4 root     root     4096 Sep 11 11:43 ./
drwxr-xr-x 11 www-data www-data 4096 Aug 27  2020 ../
-rw-r--r--  1 root     root     1791 Mar 30  2019 3629766ce8176b397fc0408488936c7430be12d0638b75c823a74e61dcc24d28-prestodelta.xml.gz
-rw-r--r--  1 root     root     2202 Jan 16  2021 5a6cebd4808e5180233656d5f43692d0c76e44afd02d6d24f6ae99601a6755be-prestodelta.xml.gz
-rw-r--r--  1 root     root     1103 Feb 16  2019 83750cfb4041e113a13b164a6352e93b006655da4a625cbda8670a33c1fc7787-prestodelta.xml.gz
drwxr-xr-x  2 root     root     4096 Dec 21  2019 Packages/
drwxr-xr-x  2 root     root     4096 Sep 11 11:43 repodata/
```

Tiếp tục tương quan so sánh các os như sau:

```
root@ubuntu:/var/www/repo# ll /repo/centos/os.7
total 2184
drwxr-xr-x  4 root     root       4096 Sep 11 11:38 ./
drwxr-xr-x 11 www-data www-data   4096 Aug 27  2020 ../
-rw-r--r--  1 root     root     169476 Dec  1  2018 29b154c359eaf12b9e35d0d5c649ebd62ce43333f39f02f33ed7b08c3b927e20-c7-x86_64-comps.xml.gz
-rw-r--r--  1 root     root     169182 Apr 25  2020 4af1fba0c1d6175b7e3c862b4bddfef93fffb84c37f7d5f18cfbff08abc47f8a-c7-x86_64-comps.xml.gz
-rw-r--r--  1 root     root     156763 Sep 11 06:47 a4e2b46586aa556c3b6f814dad5b16db5a669984d66b68e873586cd7c7253301-c7-x86_64-comps.xml.gz
-rw-r--r--  1 root     root     169676 Sep 14  2019 bc140c8149fc43a5248fccff0daeef38182e49f6fe75d9b46db1206dc25a6c1c-c7-x86_64-comps.xml.gz
-rw-r--r--  1 root     root     744670 Sep 11 06:47 comps.xml
drwxr-xr-x  2 root     root     798720 Nov 14  2020 Packages/
drwxr-xr-x  2 root     root       4096 Sep 11 11:38 repodata/

root@ubuntu:/var/www/repo# ll /repo/centos/os.8
total 668
drwxr-xr-x  4 root     root       4096 Sep 11 11:33 ./
drwxr-xr-x 11 www-data www-data   4096 Aug 27  2020 ../
-rw-r--r--  1 root     root      56684 Jun 19 06:47 0822fd6d9953a0bdefc5e5b337865efe6a7ad2b2c90bc3005a5168ecfab57e58-comps-BaseOS.x86_64.xml.xz
-rw-r--r--  1 root     root      56676 Dec  5  2020 08a9add0907af002934460d81ea0edc8bb4154db679cdc113d4c51efcbddfce4-comps-BaseOS.x86_64.xml.xz
-rw-r--r--  1 root     root      56700 May 29  2021 8fe63b600873661d8e5c38a85eb6f29e9670393fadbc0185125d749bb3eb8131-comps-BaseOS.x86_64.xml.xz
-rw-r--r--  1 root     root     297387 Jun 19 06:47 comps.xml
-rw-r--r--  1 root     root      56660 Nov 13  2021 eca245788433d98df6edf57d3a67a728234d03cb0ad8c2b8fa5a335ddcc25893-comps-BaseOS.x86_64.xml.xz
drwxr-xr-x  2 root     root     139264 Jan  9  2022 Packages/
drwxr-xr-x  2 root     root       4096 Sep 11 11:33 repodata/

root@ubuntu:/var/www/repo# ll /repo/centos/os.6
total 2204
drwxr-xr-x  4 root     root        4096 Sep 11 11:42 ./
drwxr-xr-x 11 www-data www-data    4096 Aug 27  2020 ../
-rw-r--r--  1 root     root      248263 Jan 16  2021 47bb3f2a77d01bd38e462765ebd67f8890af8c4ea75ab1b9ba192926db7e552b-c6-x86_64-comps.xml.gz
-rw-r--r--  1 root     root     1508980 Jan 16  2021 comps.xml
drwxr-xr-x  2 root     root      483328 Sep 16  2018 Packages/
drwxr-xr-x  2 root     root        4096 Sep 11 11:42 repodata/
root@ubuntu:/var/www/repo#
```

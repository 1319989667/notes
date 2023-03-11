主机名（host name）与IP地址一一对应，并且易于记忆。

使用主机名需解决的问题： （1）主机名在全世界范围内不能重复。 （2）主机名与IP地址的转换问题。



# 因特网的域名结构

因特网采用了层次树状结构的域名命名方法。域名由若干部分组成，各部分之间用点隔开。

域名系统（DNS）：主机名及域名的分配机制，以及主机名与IP地址的转换机制。

## 顶级域名

- 国家和地区顶级域名（country code top-level domains，简称ccTLDs），例如中国是.cn ，日本是.jp 等
- 通用顶级域名（generic top-level domains，简称gTLDs），例如表示工商企业的.com ，表示网络提供商的.net，表示非盈利组织的.org等
- 国际顶级域名（international top-level domain names，简称 iTDs），例如表示教育的.edu，表示销售公司或企业的.store，表示提供信息服务的单位的.info等

我国将二级域名划分为类别域名和行政区域域名。

类别域名有六个：

1.  ac - 科研机构
2. com - 工商金融等企业
3. edu - 教育机构
4. gov - 政府部门
5. net - 互联网络
6. org - 各种非营利性组织

行政区域域名对应34个省市。



# 地址解析

把一个主机名转换为IP地址。

基本思路：建立一个主机名数据库，把主机名及其对应的IP地址存入其中，需要时查询即可。

实用办法：把主机名数据库分散地存放在多台服务器中，这样的服务器叫做DNS服务器。（DNS服务器熟知的端口号是53）

![IMG_20230304_175539](C:\Users\Kirua\Documents\Tencent Files\1319989667\FileRecv\MobileFile\IMG_20230304_175539.jpg)

![image-20230305223337051](C:\Users\Kirua\AppData\Roaming\Typora\typora-user-images\image-20230305223337051.png)
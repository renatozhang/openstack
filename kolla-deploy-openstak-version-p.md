# 使用Kolla部署Pike版本的OpenStack-allinone云平台
1. OpenStack概述
2. OpenStack的作用
3. OpenStack版本命名规则
4. OpenStack各组件关系
5. Koll概述和OpenStack所有节点linux系统初始化配置
6. 安装koll-ansible
7. 自定义kolla-ansible安装OpenStack的相关配置文件
8. 开始基于kolla-ansible安装OpenStack私有云
## OpenStack概述
### OpenStack概述：
OpenStack是一个NASS美国国家航空航天局和Rackspace合作研发的，以Apache许可证授权，并且是一个自由团建和开源代码项目。

Open是开放，Stack则是堆砌之意，合起来就是将众多的功能服务堆积起来的集合，让人们通过OpenStack云计算项目，能够将诸如计算能力、存储、网络和软件等资源抽象成服务，以便让用户通过互联网远程来享用，付费的形式也变得因需而定，调整方便，拥有极强的虚拟可拓展性，是公共和私有云的建设与管理软件中的优秀开源项目。
## OpenStack的作用
### OpenStack的作用：
OpenStack是一个云平台管理的项目，他不是一个软件。这个项目有主要的组件组合起来完成一些具体的工作。

OpenStack是一个旨在为公共及私有云建设与管理提供软件的开源项目。他的社区拥有查过130家企业及1350位开发者，这些机构与个人豆浆OpenStack作为基础设施即服务（简称IaaS）资源通用前端。

### 1、 IaaS(Infrastrure as a Service)，即基础设施即服务。 

消费者通过Internet可以从完善的计算机基础设施获得服务。这类服务成为基础设施即服务。基于Internet的服务（如存储和数据库）是IaaS的一部分

比如：在腾讯云买一台云主机（8个CPU，32G，5T硬盘云主机等）

例： 腾讯云： https://cloud.tencent.com/

### 2、PaaS是Platform-as-a-Service的缩写，意思是开发平台即服务。把服务器平台作为一种服务提供的商业模式。

例：OpenShift是红帽的云开发平台即服务（PaaS）。OpenShift的logo如下：

OpenShift是自由和开放源码的云计算平台，他可以使开发人员能够创建、测试和运行他们的应用程序，并且可以把他们部署到云中。OpenShift广泛支持多种编程语言和框架，如Java，Ruby和PHP等，另外他还提供了多种集成开发工具如Eclipse integration，JBoss Developer Studio和Jenkins等。

OpenShift基于一个开源生态系统为移动应用，数据库服务等，提供支持。

那些厂商在做PaaS平台？

例2： 新浪的SAE PaaS云平台，国内：http://www.sinacloud/sea.html

#### Paas的四大特点：
* 支持PHP、Java、Python三种语言环境，适用于多种业务场景。
* 每个应用每天都有免费额度试用，超过免费额度后才开始按需计费
* 用户吧代码放上来，无论业务凋零还是业务暴涨，都无需人工干预。
* SAE把一切功能服务化，儿这些服务都是基于高可用架构的。

### 注： IaaS 干了硬件工程师的活， PaaS干了运维的活。

### 3、SaaS是Software-as-a-Service（软件即服务）的简称

例： http://yiqixie.com 中国一起写，多人编辑一个word或excle
国外： docs.google.com 或 微软的office 365

注：有SaaS平台，不需要运维，不需要开发。要求：你越来越专业

### 总结：
	三种云平台架构：IaaS, PaaS,SaaS 都是不同的商业模式
## OpenStack版本命名规则
### OpenStack命名方式
版本发布以A-Z字母顺序来发布，很好记
例:
2010年10月  发布‘Austin’

2011年2月   发布‘Bexar’ [地名] [美国] 贝尔; 

2011年4月   发布‘Cactus’  [地名] [美国] 卡克特斯;

2011年7月   发布‘Diablo’

2012年4月   发布‘Essex’

2012年10月 发布‘Folsom’

2013年4月 发布‘Grizzly’

2013年10月  发布Havana

2014年4月  发布‘Icehouse’ 

https://release.openstack.org/

####查看最新版本openstack  yum源：https://repos.fedorapeople.org/repos/openstack/  

#### OpenStack Pike版本发布 看中国公司贡献率排行

#### 中国公司在OpenStack社区的贡献大致可以看出如下格局：

## OpenStack各组件关系
### OpenStack核心组成主要有：
#### 核心组件
* Keystone（身份认证）
* nova（计算）
* Neutron（网络）
* Glance（镜像存储）
* Cinder（块存储）
* SWift（对象存储）
* Horizon（Web UI界面）
* Ceilometer（计量）
* Heat（部署编排）
* Trove（数据库）

### 详细说明
身份验证（Keystone）：统一的授权，认证管理。所有组件都依赖于Keystone提供3A（Account，Authentication，Authorization）服务。

例1： 3A认证

1. 认证（Authentication），验证用户的身份与可使用的网络服务；
2. 授权（Authorization），依据认证结果开放网络服务给用户；
3. 记账（Accounting）： 记录用户对各种网络服务的用量，并提供给计费系统。整个系统在网略管理与安全问题中十分有效。

		比如：宽带计费就是3A认证的典型例子：输入账号密码（认证）-》 开10M带宽（授权）-》在营业厅（记账）
计算管理（Nova）：Nova是OpenStack云中的计算组织控制器。Nova自身并没有提供任何虚拟化能力，相反它使用libvirt API来与被支持的虚拟技术Hypervisors交互。如：kvm、Xen、VMware等虚拟化技术。

Neutron（网络）：实现虚拟机的网络资源管理如网络连接、ip管理、公网映射

镜像管理（Glance）：  主要存储和管理系统镜像。  centos镜像 

块存储（Cinder）：为虚拟机提供存储空间。 比如硬盘，分区，目前支持ip-san、fc-san等。

对象存储（Swift）：OpenStack Swift 开源项目提供了弹性可伸缩、高可用的分布式对象存储服务，适合存储大规模非结构化数据。通过key/value的方式实现对文件的存储，现在的云盘就是这样的，和MFS，HDFS类似

注：如果客户需要一个1000T的存储空间，使用Cinder或Glance就不行，效率太低。这时就用Swift。

界面（Horizon）：安装好后，openstack的web界面控制台DashBoard 

Ceilometer（计量）：Ceilometer是OpenStack中的一个子项目，它像一个漏斗一样，能把OpenStack内部发生的几乎所有的事件都收集起来，然后为计费和监控以及其它服务提供数据支撑。
Ceilometer  [sɪ'lɒmɪtə]  云幂测量仪

Heat（部署编排）：是一个编排引擎，它可以基于文本文件形式的模板启动多个复合云应用程序（这些文件可以被视为代码）。简单来说，Heat为OpenStack用户提供了一种自动创建云组件（如网络、实例、存储设备等）的方法。
[hɪt]

Trove（数据库）：为关系型数据库和非关系型数据库引擎提供可扩展的和可靠的云数据库服务，并继续改进其功能齐全、可扩展的开源框架。
Trove  [trəʊv] 宝库

### Openstack的网络模式有5种
#### Local模式：一般测试时使用，只需一台物理机即可。
#### GRE模式：隧道模式， VLAN数量没有限制，性能有点问题。
#### Vlan模式：vlan数量有4096的限制
#### VXlan模式：vlan数量没有限制，性能比GRE好。
#### Flat模式：管理员创建租户直接到外网，不需要NAT。

### 扩展：VXLAN概述
#### VXLAN是由思科与VMware提出的。
#### LAN     局域网
#### VLAN    虚拟局域网
#### VXLAN   虚拟扩展局域网
#### VXLAN (Virtual Extensible LAN，虚拟扩展局域网) 它是一种在UDP中封装MAC的简单机制，可以创建跨多个物理IP子网的虚拟2层子网
#### 比如： 我在北京，你在上海，通过vxlan技术，可以让我们在同一个局域网中。 （有点vpn隧道）

#### 为什么我们需要这种新技术?
现在不只一种标准可以在IP中封装MAC(包括EtherIP和GRE通道桥接)，但是它们都无法做到VLAN逻辑标记，因此您只能使用最多4096个不同的VLAN。即使可以使用这些标准来分隔逻辑网，您也必须(在负载中)深入分析MAC头信息，以查找出虚拟分片ID。而VXLAN通过一个24位分片ID，在一个数据中心内它能部署百万个虚拟网。

而且，VXLAN数据包格式很容易在硬件中实现，所以它以后也许可以更紧密地与物理网络设备集成。

#### 何时需要VXLAN?
在需要大量逻辑网时，才应该考虑使用VXLAN。如果只有少量逻辑网，那么就还是应该使用经过时间考验的技术，如VLAN。 你的逻辑网不超过4096个，都用VLAN。  

#### VXLAN硬件产品：H3C S6800数据中心以太网汇聚交换机
 
H3C S6800系列交换机支持VXLAN (Virtual Extensible LAN，虚拟扩展局域网)， VXLAN通过将虚拟机发出的数据包封装在UDP中，并使用物理网络的IP/MAC作为outer-header封装后在物理IP网上传输，到达目的地后由隧道终结点解封并将数据发送给目标虚拟机，解决了地理分散的数据中心之间远距离虚拟机迁移问题。

### VLAN与VXLAN之间有何区别：VXLAN显然更具可扩展性(4,096个VLAN网 vs 1600万个VXLAN网)

openstack项目相关资源获取
 	http://www.openstack.org
 	https://github.com/openstack
 	http://docs.openstack.org/
 	http://www.openstack.cn   openstack中文

### OpenStack部署方法，主要有以下5种：
* 社区手册： http://docs.openstack.org
* RDO：  	https://www.rdoproject.org（http://openstack.redhat.com）
* RedHat Enterprise Linux OpenStack Platform  （E210 考试）http://www.redhat.com/en/technologies/linux-platforms/openstack-platform
*	Mirantis（Fuel） https://www.mirantis.com
* 高级定制： Puppet、Chef
* kolla 基于docker安装openstack ，把openstack每个组件做成docker实例 
 	
## Koll概述和OpenStack所有节点linux系统初始化配置
kolla是openstack下面用于自动化部署的一个项目，它基于docker和ansible来实现，docker主要负责镜像制作，容器管理。而ansible主要负责环境的部署和管理。 

Kolla实际上是分为两大块的，一部分，Kolla提供了生产环境级别的镜像，涵盖了Openstack用到的各个服务，另一部分是自动化的部署，也就是上面说的ansible部分。最开始两个部分是在一个项目中的（也就是Kolla），从O版本开始将两个部分独立开来，Kolla项目用来构建所有服务的镜像，Kolla-ansible用来执行自动化部署。 
### linux系统环境配置
1. 关闭Selinux和防火墙
		
		[root@zz11 ~]# vim /etc/selinux/config 
		SELINUX=disabled
		[root@zz11 ~]# setenforce 0

2. 关闭firewalld

		[root@zz11 ~]# systemctl stop firewalld.service 
		[root@zz11 ~]# systemctl disable firewalld.service 
		[root@zz11 ~]# systemctl status firewalld.service 

3. 安装Epel源

		[root@zz11 ~]# yum install -y epel-release

4. 配置hostname
		[root@zz11 ~]# cat /etc/hostname 
		zz11
5. 配置/etc/hosts

		[root@zz11 ~]# cat /etc/hosts
		127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
		::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
		192.168.98.11 zz11
6. 时间同步
		
		[root@zz11 ~]# yum install -y ntp
		[root@zz11 ~]# systemctl start ntpd
		[root@zz11 ~]# systemctl enable ntpd 
		
7. 配置pip镜像源，方便下载Python库（这一步很重要）
	
		[root@zz11 ~]# mkdir ~/.pip
		[root@zz11 ~]# vim ~/.pip/pip.conf
		[global]
		index-url = http://mirrors.aliyun.com/pypi/simple/
		[install]
		trusted-host=mirrors.aliyun.com
8. 配置ssh秘钥登录
		root@zz11 ~]# ssh-keygen
		root@zz11 ~]# ssh-copy-id -i ./ssh/id_rsa.pub root@zz11

### 配置网卡信息
#### 在zz11上添加一个新的网卡，类型是brige模式
|---|---|---|---|

|IP地址|网路类型|网卡|在OpenStack网略中的作用|

|192.168.98.11|brige|ens33|openstack内部管理网略（managment network）日后Horizon web界面访问，就是通过这个IP地址|

|无IP（不能配置IP）|brige|ens37|外部网络（external network）,让neutron的br-ex 绑定使用，OpenStack中的虚拟机是通过这块网卡和外网通信的|

#### 注：如果服务器直接安装，两块网卡的IP，就算是同一网段或者不同网段都可以的
#####生成ens37的网配置文件


	[root@zz11 ~]# cd /etc/sysconfig/network-scripts/
	[root@zz11 network-scripts]# cp ifcfg-ens33 ifcfg-ens37
	[root@zz11 network-scripts]# cat ifcfg-ens37 
	TYPE=Ethernet
	BOOTPROTO=none
	NAME=ens37
	DEVICE=ens37
	ONBOOT=yes
	[root@zz11 network-scripts]# systemctl restart network
### 安装基础包和docker服务
1. 安装基础包
	
		[root@zz11 network-scripts]# yum install python-devel libffi-devel gcc openssl-devel git python-pip -y
		# 升级一下pip，不然后期安装会报警告
		[root@zz11 network-scripts]# pip install -U pip
		# 安装必要的一些系统工具
		[root@zz11 network-scripts]# yum install -y yum-utils device-mapper-persistent-data lvm2
2. 添加docker yum源并安装docker

		[root@zz11 network-scripts]# systemctl stop libvirtd && systemctl ^C
		[root@zz11 network-scripts]# systemctl stop libvirtd.service && systemctl disable libvirtd.service && systemctl status libvirtd.service  
		#如果有的化，卸载旧的Docker，否则可能会不兼容：
		[root@zz11 network-scripts]# yum remove  docker docker-io docker-selinux python-docker-py
		
		#添加docker-ce yum源配置文件并安装docker
		[root@zz11 network-scripts]# yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
		# 产看新生的yum源配置文件
		[root@zz11 ~]# cat /etc/yum.repos.d/docker-ce.repo 
		# 安装Docker-CE社区版本
		[root@zz11 ~]# yum -y install docker-ce
		# 启动Docker服务
		[root@zz11 ~]# systemctl start docker.s && systemctl enable docker && systemctl status docker
3. 设置docker volume卷挂载方式

		[root@zz11 ~]# mkdir /etc/systemd/system/docker.service.d
		[root@zz11 ~]# tee /etc/systemd/system/docker.service.d/koll.conf<<'EOF'
		> [Service]
		> MountFlags=shared
		> EOF
注： 加上MountFlags=sahred后，当docker宿主机新增分区时，docker服务不用重启，如果不加上docker服务重启，docker中的实例才可以使用新加的磁盘或分区，添加正餐靠后，后期再OpenStack中使用cinder存储服务是，新增加磁盘比较方便
4. 指定docker镜像加速器（很重要，不然后期从国外下载docker镜像会直接报错，而且速度慢）

		[root@zz11 ~]# mkdir /etc/docker/
		[root@zz11 ~]# vim /etc/docker/daemon.json
		[root@zz11 ~]# cat /etc/docker/daemon.json 
		{
		  "registry-mirrors": ["https://5md6dgq1.mirror.aliyuncs.com"]
		}

注： 如果需要使用自己的本地私有仓库，携程如下：

	{
	  "registry-mirrors": ["https://e9yneuy4.mirror.aliyuncs.com"]  
 	 "insecure-registries": ["192.168.98.11:4000"]
	}

重启相关服务

	systemctl restart docker && systemctl enable docker && systemctl status docker	
	
## 安装koll-ansible
1. 安装ansible与kolla-ansible的代码

		[root@zz11 ~]#  yum install ansible -y
		# 下载pike版本的OpenStack
		# 注： kolla-ansible可以实现使用ansible自动安装OpenStack
		[root@zz11 ~]# 
		[root@zz11 ~]# git clone http://git.trystack.cn/openstack/kolla-ansible -b stable/pike
		
2. 安装kolla-ansible需要依赖包
	
		[root@zz11 ~]# cd kolla-ansible/
		[root@zz11 kolla-ansible]# pip install . # 这里.代表当前目录
		
3. 复制koll-ansible的相关配置文件
		
		[root@zz11 kolla-ansible]# cp -r etc/kolla /etc/kolla/
		[root@zz11 kolla-ansible]# cp ansible/inventory/* /etc/kolla/
		[root@zz11 kolla-ansible]# ls /etc/kolla/
		all-in-one  globals.yml  multinode  passwords.yml
#### 注：

all-in-one #安装单节点openstack的ansible自动安装配置文件

multinode #安装多节点openstack的ansible自动安装配置文件

globals.yml #openstack 部署的自定义配置文件 

passwords.yml  #openstack中各个服务的密码

4. 修改虚拟机类型为qemu
#### 注：
如果是在虚拟机里装kolla，希望可以启动再启动虚拟机，那么你需要把virt_type=qemu，默认是kvm。如果vmware开了“虚拟化Intel VT”功能，就不用写这个了。

	[root@zz11 kolla-ansible]# mkdir -p /etc/kolla/config/nova
	[root@zz11 kolla-ansible]# cat << EOF > /etc/kolla/config/nova/nova-compute.conf
	> [libvirt]
	> virt_type=qemu
	> cpu_mode = none
	> EOF	

## 自定义kolla-ansible安装OpenStack的相关配置文件
1. 自动生成openstack各服务的密码文件
	
		[root@zz11 kolla-ansible]# kolla-genpwd 
		
		[root@zz11 kolla-ansible]# vim /etc/kolla/passwords.yml 
		改：158 keystone_admin_password: HsPbEQHxTqmewKYNoRPpIOyQNdEYpHy36OX67TG3
		为158 keystone_admin_password: 123456
#### 注： 这是登录dashboard，admin使用的密码，你可以根据自己需要进行修改。

2. 编辑/etc/kolla/globals.yml 自定义OpenStack中部署事项
		
		
		14 # Valid options are [ centos, oraclelinux, ubuntu ]
 		15 kolla_base_distro: "centos"  #选择下载的镜像为基于centos版本的镜像
 		16 
		17 # Valid options are [ binary, 	source ]
 		18 kolla_install_type: "binary" 
		#去了前面的#号，使用yum安装二进制包安装，源码安装，指的是使用git clone源码安装
 		19 
 		20 # Valid option is Docker repository tag
		#指定安装pike版本的openstack，后期下载的openstack相关的docker镜像的tag标记也都为pike
 		21 openstack_release: "pike"
		23 # Location of configuration overrides
		#配置文件的位置
 		24 node_custom_config: "/etc/kolla/config"
		30 # 'network_interface' as set in the Networking section below.
		# 我们没有启用高可用，所以这里的IP可以和ens33一样，也可以独立写一个和ens33同网段的IP。
 		31 kolla_internal_vip_address: "192.168.98.11"
		注：如果配置了高可用，这里要使用一个没被占用的IP。这个IP是搭建HA高可用的浮动IP。 此IP将由keepalived管理以提供高可用性，应设置为和network_interface ens33 同一个网段的地址。

		72 # followed for other types of interfaces.
		# Kolla-Ansible需要设置一些网络选项。 我们需要设置OpenStack使用的网络接口。设置的第一个接口是“network_interface”。 这是openstack内部多个管理类型网络的默认接口。
 		73 network_interface: "ens33"
		#所需的第二个接口专用于Neutron外部（或公共）网络，可以是vlan或flat，取决于网络的创建方式。 此接口应在没有IP地址的情况下处于活动 如果不是，openstack云平台中的云主机实例将无法访问外部网络。 只要网卡启动着，就可以了，不要给IP，有IP时br-ex桥接就不成功了。
		87 # addresses for that reason.
 		88 neutron_external_interface: "ens37"
		#先不开启cinder
		135 #enable_cinder: "no"
		#去了前面的#号，改yes为no。关闭高可用
		151 enable_haproxy: "no"
		
	
## 开始基于kolla-ansible安装OpenStack私有云
1. 生成SSH Key，并授信本节点：
		
		[root@zz11 ~]# ssh-keygen 
		[root@zz11 ~]# ssh-copy-id -i ~/.ssh/id_rsa.pub root@zz11
	
2. 配置单节点清单文件（目前只有一个节点）

		[root@zz11 ~]# vim /etc/kolla/all-in-one  #把localhost替换成 
		# These initial groups are the only groups required to be modified. The
		# additional groups are for more control of the environment.
		[control]
		zz11       
		
		[network]
		zz11       
		
		[compute]
		zz11       
		
		[storage]
		zz11       
		
		[monitoring]
		zz11       
		
		[deployment]
		zz11       
3. 开始部署OpenStack
	1. 对主机进行预部署检查：

			[root@zz11 ~]# kolla-ansible -i /etc/kolla/all-in-one prechecks
			zz11                       : ok=54   changed=0    unreachable=0    failed=0    skipped=176  rescued=0    ignored=0  
			注：我这里有最新显示78个ok，另外，现在还没有运行docker实例了， 
	2. 拉取镜像

			[root@zz11 ~]# ss -antup | grep 443
			tcp    ESTAB      0      0      192.168.98.11:42520              116.62.81.173443                 users:(("dockerd",pid=13731,fd=26))
			tcp    ESTAB      0      0      192.168.98.11:42518              116.62.81.173443                 users:(("dockerd",pid=13731,fd=33))
			tcp    ESTAB      0      0      192.168.98.11:42526              116.62.81.173443                 users:(("dockerd",pid=13731,fd=25))
			IP地址: 116.62.81.173浙江省杭州市 阿里云
			# 发现在走的是阿里云的docker加速结点，下载的镜像。
			[root@zz11 ~]# docker images #查看下载的镜像
			
			
			[root@zz11 ~]# docker images | wc -l # 整个过程，会下载30个images
			30

	3. 最后进入实际的OpenStack部署：
			
			[root@zz11 ~]# kolla-ansible -i /etc/kolla/all-in-one deploy 
			#因为咱们前面已经下载的镜像，所以这时，安装会快一些。如果前面没有下载镜像，那么这时，还会边下载各种openstack相关的镜像边部署docker实例。
	4. 验证部署
	
			[root@zz11 ~]# kolla-ansible -i /etc/kolla/all-in-one post-deploy
			这样就创建 /etc/kolla/admin-openrc.sh 文件

			#查看openstack登录帐号
	5. 测试：http://192.168.98.11 访问成功

## 总结：
#### openstack 概述
#### openstack的作用
#### openstack版本命名规则
#### openstack各组件关系
#### Kolla概述和openstack所有节点linux系统初始配置
#### 安装kolla-ansible
#### 自定义kolla-ansible安装openstack的相关配置文件
#### 开始基于kolla-ansible安装openstack私有云

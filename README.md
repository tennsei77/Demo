# n2n 概要

TBD

# n2n インストール

### CentOS 7にn2nインストール

#### 方法①: RPM インストール

rpm -ivh http://packages.ntop.org/centos-stable/7/x86_64/Packages/n2n-2.4-71.x86_64.rpm

(まだは yum install http://packages.ntop.org/centos-stable/7/x86_64/Packages/n2n-2.4-71.x86_64.rpm )


#### 方法②: パッケージ管理

<pre>
cd /etc/yum.repos.d/ && wget http://packages.ntop.org/centos-stable/ntop.repo -O ntop.repo
yum --enablerepo=ntop install n2n
</pre>


#### 方法③：ソースから生成

##### 開発ツールインストール
<pre>
yum -y install cmake gcc gcc-c++ git wget openssl-devel
</pre>

#### ソース取得

<pre>
git clone https://github.com/meyerd/n2n.git
</pre>

#### Configure & Makefile 作成

<pre>
[xiang@centos7 temp]$ mkdir build && cd build && cmake ../n2n/n2n_v2

[xiang@centos7 build]$ make
Scanning dependencies of target doc
[  4%] Generating doc/edge.8.gz
[  9%] Generating doc/supernode.1.gz
[ 13%] Generating doc/n2n_v2.7.gz
[ 13%] Built target doc
Scanning dependencies of target scm
[ 18%] Building C object CMakeFiles/scm.dir/unix-scm.c.o
Linking C static library libscm.a
[ 18%] Built target scm
Scanning dependencies of target n2n
[ 22%] Building C object CMakeFiles/n2n.dir/n2n.c.o
[ 27%] Building C object CMakeFiles/n2n.dir/n2n_keyfile.c.o
[ 31%] Building C object CMakeFiles/n2n.dir/wire.c.o
[ 36%] Building C object CMakeFiles/n2n.dir/minilzo.c.o
[ 40%] Building C object CMakeFiles/n2n.dir/twofish.c.o
[ 45%] Building C object CMakeFiles/n2n.dir/transform_null.c.o
[ 50%] Building C object CMakeFiles/n2n.dir/transform_tf.c.o
[ 54%] Building C object CMakeFiles/n2n.dir/transform_aes.c.o
[ 59%] Building C object CMakeFiles/n2n.dir/tuntap_freebsd.c.o
[ 63%] Building C object CMakeFiles/n2n.dir/tuntap_netbsd.c.o
[ 68%] Building C object CMakeFiles/n2n.dir/tuntap_linux.c.o
[ 72%] Building C object CMakeFiles/n2n.dir/tuntap_osx.c.o
[ 77%] Building C object CMakeFiles/n2n.dir/version.c.o
Linking C static library libn2n.a
[ 77%] Built target n2n
Scanning dependencies of target benchmark
[ 81%] Building C object CMakeFiles/benchmark.dir/benchmark.c.o
Linking C executable benchmark
[ 81%] Built target benchmark
Scanning dependencies of target benchmark_hashtable
[ 86%] Building C object CMakeFiles/benchmark_hashtable.dir/benchmark_hashtable.c.o
Linking C executable benchmark_hashtable
[ 86%] Built target benchmark_hashtable
Scanning dependencies of target edge
[ 90%] Building C object CMakeFiles/edge.dir/edge.c.o
Linking C executable edge
[ 90%] Built target edge
Scanning dependencies of target n2n_test
[ 95%] Building C object CMakeFiles/n2n_test.dir/n2n_test.c.o
Linking C executable n2n_test
[ 95%] Built target n2n_test
Scanning dependencies of target supernode
[100%] Building C object CMakeFiles/supernode.dir/sn.c.o
Linking C executable supernode
[100%] Built target supernode

</pre>

#### パッケージ内容確認

<pre>
[root@centos7 ~]# rpm -ql n2n
/etc/n2n/edge.conf.sample
/etc/n2n/supernode.conf.sample
/usr/lib/systemd/system/edge-ntopng@.service
/usr/lib/systemd/system/edge.service
/usr/lib/systemd/system/edge@.service
/usr/lib/systemd/system/supernode.service
/usr/sbin/edge
/usr/sbin/supernode
/usr/share/man/man1/supernode.1.gz
/usr/share/man/man7/n2n.7.gz
/usr/share/man/man8/edge.8.gz
</pre>



### Ubuntu／Raspberry PI

#### パッケージでインストール

<pre>
# インストール (バージョンが古い：v.1.3.2)
apt-get install n2n

# パケージ内容確認
dpkg -L n2n

</pre>

#### ソースから作成

<pre>
# 依存ツールインストール
apt-get install build-essential libssl-dev cmake gcc git wget  

# ソース取得
git clone https://github.com/meyerd/n2n.git

# Configure & Makefile 作成
mkdir build && cd build && cmake ../n2n/n2n_v2

# Build
make

</pre>


# Layer 3 実験

## ネットワーク構成図

TBD

## 設定

### Super node On AWS (Centos 7)

<pre>
# UDP Port 8888
# -l UDPポート番号指定
supernode -l 8888 -f -v 
</pre>


### Edge node ON PC (Ubuntu 18.04) 

<pre>
edge -r -a 10.100.100.102 -k password -c testnet -l 52.199.224.202:8888 -f -v -d tap0
</pre>

### Edge node on Raspberry PI 

<pre>
# /dev/net/tun
modprobe tun
edge -r -a 10.100.100.103 -k password -c testnet -l 52.199.224.202:8888 -f -v -d tap0
</pre>

### iptables 設定

TBD

### 接続確認

TBD


# Layer 2 実験

## ネットワーク構成図

TBD

## インストール

### Bridge インストール

#### Raspberry PI/Ubuntu
<pre>
sudo apt install -y bridge-utils
</pre>

#### CentOS

<pre>
yum install bridge-utils
</pre>

### n2n インストール

Layer ３ 実験と同じ

## 設定

### n2n 設定

Layer ３ 実験と同じ


### bridge 設定

#### Raspberry PI bridge 設定 

<pre>

# アドレスクリア 
ifconfig tap0 0.0.0.0
# ip addr flush dev tap0
ifconfig eth0 0.0.0.0
# ip addr flush dev eth0

# Bridge 作成
brctl addbr br0

# ネットワークデバイス関連
brctl addif br0 tap0
brctl addif br0 eth0

# Bridge アップ
ip link set dev br0 up

</pre>

#### CentOS bridge 設定

<pre>

# アドレスクリア 
ifconfig tap0 0.0.0.0
# ip addr flush dev tap0
ifconfig eno16777728 0.0.0.0
# ip addr flush dev eno16777728

# Bridge 作成
brctl addbr br0

# ネットワークデバイス関連
brctl addif br0 tap0
brctl addif br0 eno16777728

# Bridge アップ
ip link set dev br0 up

</pre>




## 実験

### 実験１、VPN越して、DHCPサーバーからIPアドレス取得する

#### 結果、取得できなっか

#### tcpdump でパケットキャプチャ

<pre>
# DHCP サーバー通信キャプチャ
tcpdump -s 0 -i eth2 -w centos6.eth2.pcap  

# Raspberry PI ETher キャプチャ
tcpdump -s 0 -i eth0 -w rpi.eth0.pcap  

# Raspberry PI Tap0 キャプチャ
tcpdump -s 0 -i tap0 -w rpi.tap0.pcap  


# Centos ETher キャプチャ
tcpdump -s 0 -i ens192 -w centos.ens192.pcap  

# Centos Tap0 キャプチャ
tcpdump -s 0 -i tap0 -w centos.tap0.pcap  

</pre>

#### n2n は ETher パケット転送していないのようです

DHCP サーバー側のTAPのパケットキャプチャ

![パケットキャプチャ](img/server.pcap.png "パケットキャプチャ")

クライアント側のTAPのパケットキャプチャ

![パケットキャプチャ](img/client.pcap.png "パケットキャプチャ")



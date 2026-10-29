# 🖥️ 运行你自己的比特币节点

你自己的节点会亲自检查每一条比特币规则，这样你就不用信任任何其他人的服务器。这些指南帮你在家里搭建一个节点。

有两份指南可以选：

- 🛠️ [在 ODROID-M1 上搭建节点](#odroid)：在一块小小的 ARM 板上一步步装好 Debian 12、Bitcoin Core、Tor 和 electrs。内容来自 [Satoshi Spritz](https://satoshispritz.it/) 比特币聚会的节点讲座（见[致谢](#credits)）。
- 🧱 [MiniBolt](#minibolt)：用一台装有 Intel 或 AMD 处理器的迷你主机搭建节点。

先是两份指南都适用的概览：为什么要运行节点、节点软件和硬件。
## 🤔 为什么要运行比特币节点？

- 亲自验证整条比特币区块链
- 不靠第三方查看钱包余额
- 自己广播自己的交易
- 最高的隐私，不用信任外部服务

## 📦 节点软件

现成方案：

| 软件 | 价格 | 特点 |
|---|---|---|
| Umbrel | 软件免费，硬件 100 到 300 € | 最容易上手，应用市场，Apple 风格 |
| MyNode | 免费 / 79 € 高级版 / 成品 399 到 599 € | 节点 + Lightning + Electrum + 区块浏览器 |
| Start9 | 即插即用 699 到 899 € / DIY 软件免费 | 开源，内置 Tor，无遥测 |

共同需求：

- 区块链：约 670 GB 以上（2025 年），每隔几天增长约 1 GB
- 至少 4 GB 内存，1 TB SSD（推荐 2 TB）
- 首次同步：2 到 7 天

**现成方案：** 安装快，有图形界面，但 Docker、脚本和安全检查都被藏起来了，额外的软件也很难看清。

**DIY（手动）：** 软件越少，攻击面越小；每个部分都由你完全掌控，还能学会怎么维护。

## 🔧 选择硬件

最低要求：

- 至少 2 GB 内存（推荐 4 GB）
- 至少 1 TB 硬盘（推荐 2 TB）
- 多核 CPU（任何较新的 CPU 都行）

选择：

- **旧电脑：** 零成本；如果是笔记本，把电池拆掉
- **Raspberry Pi：** 贵，而且 USB 硬盘不太稳定
- **ODROID-M1：** ARM，内置 SSD 插槽
- **瘦客户机（30 到 100 €）：** Fujitsu Futro S920、HP t620

操作系统用 **Debian 13**，一个支持多年的长期支持版 Linux。更新所有软件包，安装 `tor` 和 `ssh` 以便远程维护，并推荐配一台 UPS（不间断电源）。

<a id="minibolt"></a>

## 🧱 另一个选择：MiniBolt

- [MiniBolt](https://minibolt.minibolt.info/)：用一台装有大约 2010
  年以后的 Intel 或 AMD 处理器的迷你主机搭建节点。

💬 在 Malaga 2140 的 Telegram 群聊里发现的。

MiniBolt 自己的[硬件要求](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)是：

- CPU：Intel/AMD 处理器（2010 年以后的型号）
- RAM：2 GB 以上（只运行比特币），推荐 4 GB
  以上（比特币、闪电网络和额外功能）
- 存储：推荐 2 TB 以上的 SATA/NVMe SSD
- 一个 4 GB 以上的 U 盘或 SD 卡，用来安装
- 首次设置时临时使用的显示器或电视，以及一个 USB 或 PS2 键盘

<a id="odroid"></a>

# 🛠️ 在 ODROID-M1 上搭建节点

## 🧰 需要准备

- ODROID-M1
- NVMe SSD（最好 2 TB）
- 路由器
- 网线
- 带 HDMI 线的显示器
- USB 鼠标和键盘

## 🔩 第 1 步：组装

1. 把 NVMe SSD 装进 ODROID-M1。
2. 接好所有线：鼠标和键盘、显示器、网线。
3. 插上电源线，打开 ODROID-M1。

## 🐧 第 2 步：下载 Debian 12

1. ODROID-M1 启动后，在 Petitboot 里移到 **Exit to shell**，按 **Enter**。
2. 输入 `udhcpc`，按 **Enter**（可以帮助解决连接问题）。
3. 输入 `netboot_default`，按 **Enter**。

   ⌨️ 不管你用什么键盘，Petitboot 都用美式布局：要打出 `_`，按住 **Shift** 再按 **0** 旁边的键。

4. 输入 `exit`，按 **Enter**。
5. 现在 Petitboot 首页会显示可用的镜像列表。移到 **Debian 12 (Bookworm) Netboot installer**，按 **Enter**。

## 💿 第 3 步：安装 Debian 12

1. **语言：** 选 **English**，更好理解（翻译可能不太好）。
2. **位置：** 选你住的国家，这会设定你的时区。如果第一个列表里没有，就选 **other**，再选你所在的大洲和国家。
3. **区域设置：** 如果 Debian 问到，可以选 **United States** 或 **United Kingdom**，保持英文。
4. **键盘：** 选你接上的键盘的布局。
5. **Hostname：** 起一个主机名。域名可以留空。
6. **镜像所在国家：** 从哪个国家下载 Debian：选你自己的国家，或列表里最近的国家。
7. **镜像：** 列表里有 `deb.debian.org` 就选它，它会把你连到附近的快速服务器。没有的话，列表里任何一个镜像都可以。
8. **代理：** 没有代理就留空，按 **Enter**。
9. **root 密码：** 设置超级用户的密码，按 **Enter**。
10. **用户：** 给普通（非管理员）用户起个名字（不用填全名），再设置密码。
11. **硬盘：** 选 **Guided: use entire disk**，然后选你装上的 NVMe 硬盘。
12. **分区：** 全部放在一个分区里：**All files in one partition (recommended for new users)**。
13. Debian 会生成一张表，里面有 boot 分区、root 分区和 swap 分区，我们不要 swap。移到 swap 那一行，按 **Enter**，选 **Delete the partition**，按 **Enter**。
14. 选 **Finish partitioning and write changes to disk**。
15. Debian 会提醒你没有 swap 分区。选 **No**，不要回到分区菜单。
16. 用 **Yes** 确认两个分区（boot 和 root），开始安装。
17. **Popularity contest**（关于你使用的 Debian 软件包的匿名统计）：选 **No**。
18. **软件：** 我们不要图形桌面，所以只勾选 **SSH server** 和 **standard system utilities**，按 **Enter**。
19. **安装完成：** 选 **Continue** 重启。

## 🔁 第 4 步：让 Debian 自动启动

1. 在 Petitboot 里移到 **System configuration**，按 **Enter**。
2. **Autoboot** 必须是 **Enabled**。
3. **Boot order** 里必须有装在 ODROID-M1 里的硬盘，或者 **Any device**。要添加硬盘，移到 **Add Device**，选中硬盘再按 **OK**。或者选 **Clear & Boot Any** 设为 **Any device**。
4. **Timeout** 是开机后多少秒启动 Debian（默认 10 秒，不用改）。
5. 移到 **OK**，按 **Enter**，然后重启 ODROID-M1。重启后左下角会倒计时，然后启动 Debian 12。

<details>
<summary><b>📵 连不上网？</b></summary>

1. 用超级用户 `root` 登录，运行 `ping 8.8.8.8`。如果显示 **Network is unreachable**，说明设备没有联网。
2. 运行 `dhclient -v`，通过 DHCP 向路由器要一个 IP 地址。它也会显示网络接口的名字，例如 `eth0`。
3. 再运行一次 `ping 8.8.8.8`：现在通了，但下次重启问题又会出现。
4. 为了让 `eth0` 每次启动都用 DHCP，运行 `nano /etc/network/interfaces`，加入或修改这几行：

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   按 **Ctrl + X** 退出，**y** 保存，**Enter** 覆盖。

5. 重启后再检查一次连接。现在 ODROID-M1 每次重启都会自己联网。

</details>

## 💻 第 5 步：用 SSH 连接

1. 在你的电脑上打开终端，用安装时创建的用户和路由器分给 ODROID-M1 的 IP 地址进行 SSH 连接：

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian 不允许用超级用户（`root`）通过 SSH 登录。

2. 登录后输入 `su`，再输入超级用户的密码，获得它的权限。

## 📥 第 6 步：更新软件源和系统

1. 打开软件源列表：

   ```bash
   nano /etc/apt/sources.list
   ```

2. 删掉所有行，加入下面这些：

   ```
   deb http://deb.debian.org/debian/ bookworm contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-updates contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-proposed-updates contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian/ bookworm-backports contrib main non-free non-free-firmware

   deb http://deb.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
   # deb-src http://deb.debian.org/debian-security/ bookworm-security contrib main non-free non-free-firmware
   ```

   按 **Ctrl + X**，再按 **y** 保存，**Enter** 覆盖文件。

3. 更新软件包列表和已安装的软件包：

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ 现在 `apt update` 应该连接上面这些软件源，也就是 `deb.debian.org`。

## 🧅 第 7 步：安装 Tor，并通过 Tor 用 SSH 登录

1. 安装 Tor：

   ```bash
   apt install tor
   ```

2. 用 `nano /etc/tor/torrc` 打开它的配置，在最下面加入：

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

3. 重启 Tor 并查看状态（每次 ODROID-M1 启动时它都会启动）：

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. 显示你的 onion 地址：

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. 现在你可以从世界任何地方连接 ODROID-M1：

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 第 8 步：下载 Bitcoin Core

Bitcoin Core 的最新版本、哈希和签名都在 <https://bitcoincore.org/en/download/>。

1. 下载安装包、校验和和签名：

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. 检查下载的文件的校验和是否与 `SHA256SUMS` 里的一致：

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ 文件名后面必须显示 **OK**：`bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Bitcoin Core 开发者的公钥在 `bitcoin-core/guix.sigs` 仓库里。你多半还没有 git，所以先装上：

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. 导入开发者的公钥：

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. 检查 `SHA256SUMS.asc` 上的签名：

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ 每位开发者都应显示 `gpg: Good signature from "...."`，表示签名有效。警告可以忽略。

🔒 为什么这很重要：

- **完整性：** 校验和确保文件在下载过程中没有损坏或被改动。
- **真实性：** 签名确认文件真的来自 Bitcoin Core 开发者，而不是来自恶意来源。

## ⚙️ 第 9 步：安装 Bitcoin Core 并开始同步

1. 解压安装包：

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. 安装 Bitcoin Core：

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. 开始同步：

   ```bash
   bitcoind
   ```

4. 家用设置：用 `nano ~/.bitcoin/bitcoin.conf` 打开 `bitcoin.conf`，加入：

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

| 设置 | 作用 |
|---|---|
| `daemon=1` | 让 `bitcoind` 作为后台守护进程运行，关掉终端后仍继续运行 |
| `blocksonly=1` | 只下载区块，不下载 mempool（未确认的交易） |
| `maxconnections=20` | 进出连接最多 20 个 |
| `maxuploadtarget=500` | 每天最多上传 500 MB |
| `txindex=1` | 保留完整的交易索引（可以浏览整条区块链） |
| `blockfilterindex=1` | 建立并保留区块过滤器索引（让交易检查更高效） |

💡 从 Bitcoin Core 26 开始，`v2transport` 会加密节点之间的连接。全部设置请看 [Bitcoin Core 配置生成器](https://jlopp.github.io/bitcoin-core-config-generator/)。

<details>
<summary><b>🔁 作为 systemd 服务运行</b></summary>

使用 `bitcoind` 服务、专门的 `bitcoin` 用户、自动重启，日志在 `~/.bitcoin/debug.log`：

```bash
sudo systemctl enable bitcoind
```

```bash
sudo systemctl start bitcoind
```

```bash
systemctl status bitcoind
```

</details>

## 🧅 第 10 步：Bitcoin Core 只走 Tor

把 Bitcoin Core 设为只通过 Tor 连接，也只能通过 Tor 被访问。

1. 用 `nano /etc/tor/torrc` 打开 Tor 的配置，在末尾加入：

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

2. 重启 Tor：

   ```bash
   systemctl restart tor
   ```

3. 显示 Bitcoin Core 的 onion 地址：

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. 用 `nano ~/.bitcoin/bitcoin.conf` 打开 `bitcoin.conf`，加入：

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

| 设置 | 作用 |
|---|---|
| `proxy=127.0.0.1:9050` | 让 Bitcoin Core 使用 Tor 代理 |
| `listen=1` | 让节点接受传入连接 |
| `bind=127.0.0.1:8333=onion` | 只允许通过 Tor 的传入连接 |
| `externalip=ADDRESS.onion` | 把 `ADDRESS.onion` 换成上面得到的 onion 地址 |
| `onlynet=onion` | 只允许在 Tor 网络上的传出连接 |

5. 重启 Bitcoin Core：先停止，再以守护进程（后台进程）方式启动：

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. 检查网络：

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ 在 `networks` 下能看到 `ipv4`、`ipv6`、`onion`、`i2p` 和 `cjdns`。只有 `onion` 的 `reachable` 是 `true`，其他都是 `false`。在 `localaddresses` 下只看到你的 onion 地址。

7. 检查你连接的节点，或连接你的节点：

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ `addr` 字段里是对方的 onion 地址，`network` 显示 `onion`。

⏳ Tor 很慢：首次同步要花长得多的时间。

<details>
<summary><b>🔄 更换 Bitcoin Core 的 onion 地址</b></summary>

1. 删除 `bitcoin-service` 文件夹，里面存着生成 onion 地址的私钥：

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. 重启 Tor，它会用新的公钥和私钥重新生成这个文件夹：

   ```bash
   systemctl restart tor
   ```

3. 检查新的 onion 地址：

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. 重启 Bitcoin Core：

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 第 11 步：安装 Rust

electrs 是用 Rust 编程语言写的 Electrum 服务器，所以设备上需要 Rust。

1. 先安装 `curl`：

   ```bash
   apt install curl
   ```

2. 安装 Rust：

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   问你选哪种安装方式时，输入 **1** 并按 **Enter**，选默认安装。

3. 装完后它会建议重启 shell。退出再重新登录（或者用 `su` 再次成为超级用户），就能用 Rust 的命令了。

4. 安装 `clang`、`cmake` 和 `build-essential`：

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 第 12 步：下载 electrs

1. 在 <https://github.com/romanz/electrs> 查看 electrs 的最新版本。这里是 0.10.7：

   ```bash
   VERSION="0.10.7"
   ```

   （如果最新版本是比如 0.10.9，就改引号里的值。）

2. 下载这个版本：

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. 进入新建的 `electrs` 文件夹，导入开发者的密钥：

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. 检查开发者的签名：

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ 看到 `gpg: Good signature from "...."` 就说明签名有效。

## 🏗️ 第 13 步：编译并安装 electrs

1. 还在 `electrs` 文件夹里，开始编译。这要花不少时间，耐心等等 😄

   ```bash
   cargo build --locked --release
   ```

2. 安装 electrs：

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. 显示版本号，确认安装成功：

   ```bash
   electrs --version
   ```

## 📝 第 14 步：为 electrs 设置 bitcoin.conf

1. 停止 Bitcoin Core：

   ```bash
   bitcoin-cli stop
   ```

2. 用 `nano ~/.bitcoin/bitcoin.conf` 打开 `bitcoin.conf`，加入：

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

| 设置 | 作用 |
|---|---|
| `server=1` | 让 Bitcoin Core 作为 RPC 服务器工作 |
| `rpcuser=USER` | 应用通过 RPC 连接节点时必须提供的用户名（你自己定） |
| `rpcpassword=PASSWORD` | 这个用户名的密码（你自己定） |
| `rpcallowip=127.0.0.1` | 哪些 IP 地址可以使用节点的 RPC：这里只有同一台机器上的程序 |
| `whitelist=download@127.0.0.1` | 同一台机器上的程序可以不受限制地下载区块和交易，即使其他规则会限制它们（例如 `maxuploadtarget=500`）。简单说，electrs 可以自由读取节点的数据 |

3. 再次启动 Bitcoin Core：

   ```bash
   bitcoind -daemon
   ```

## 📝 第 15 步：electrs 的 config.toml

1. 在安装 electrs 的文件夹里，新建配置文件：

   ```bash
   nano config.toml
   ```

2. 加入这些行：

   ```toml
   # bitcoin core configuration
   auth = "USER:PASSWORD"
   daemon_rpc_addr = "127.0.0.1:8332"
   daemon_p2p_addr = "127.0.0.1:8333"

   # electrs configuration
   db_dir = ".electrum"
   network = "bitcoin"
   electrum_rpc_addr = "127.0.0.1:50001"
   log_filters = "INFO"
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

| 设置 | 作用 |
|---|---|
| `auth = "USER:PASSWORD"` | 把 `USER` 和 `PASSWORD` 换成你在 `bitcoin.conf` 里设的那组，用来通过 RPC 登录 |
| `daemon_rpc_addr = "127.0.0.1:8332"` | 节点的 RPC 地址和端口：`127.0.0.1` 指同一台机器，`8332` 是 Bitcoin Core 默认的 RPC 端口 |
| `daemon_p2p_addr = "127.0.0.1:8333"` | 节点的 P2P 地址和端口：`8333` 是 Bitcoin Core 默认的 P2P 端口 |
| `db_dir = ".electrum"` | electrs 存放数据库的文件夹 |
| `network = "bitcoin"` | electrs 运行的网络：这里是比特币主网 |
| `electrum_rpc_addr = "127.0.0.1:50001"` | electrs 向 Electrum 客户端提供服务的地方：`127.0.0.1` 表示只能从同一台机器访问，`50001` 是未加密 Electrum 连接的默认端口 |
| `log_filters = "INFO"` | electrs 写入日志的详细程度 |

## ⏳ 第 16 步：建立 electrs 索引

1. 在 electrs 文件夹里，开始为区块建立索引：

   ```bash
   electrs --conf config.toml
   ```

2. 让终端一直开着，直到整个网络都建好索引。这可能要很多个小时：讲座里是 12 月 5 日 6:21 开始，12 月 6 日 1:24 结束，用了 19 小时 3 分钟。

## 🧅 第 17 步：electrs 走 Tor

1. 用 `nano /etc/tor/torrc` 打开 Tor 的配置，在末尾加入：

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   按 **Ctrl + X**、**y** 和 **Enter**。

2. 重启 Tor：

   ```bash
   systemctl restart tor
   ```

3. 显示 electrs 的 onion 地址：

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 更换 electrs 的 onion 地址</b></summary>

1. 删除 `electrs_hidden_service` 文件夹，里面存着生成 onion 地址的私钥：

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. 重启 Tor，它会用新的公钥和私钥重新生成这个文件夹：

   ```bash
   systemctl restart tor
   ```

3. 检查新的 onion 地址：

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 更多

## ➕ 下一步

- **Core Lightning (CLN)：** 用于支付的闪电网络，在另外的课程里设置。
- **Mempool.space：** 自己托管的区块浏览器，使用 MariaDB、Node.js、后端和前端，用 `pm2` 管理进程。

## 🧪 测试网

用来学习：没有价值的币，手续费极低。挖矿时多时少，难度也会变。

- 钱包：Green、Electrum、Specter、Sparrow
- BTCBouncer：测试网支付
- BTCSigner：测试网多签

## ⌨️ bitcoin-cli

格式：`bitcoin-cli [options] <command> [params]`，`bitcoin-cli help` 会列出所有命令。

- 区块链：`getblockcount`、`getblockchaininfo`、`getblock`
- 网络：`getnetworkinfo`、`getpeerinfo`
- 钱包：`getbalance`、`sendtoaddress`、`listunspent`
- Raw：`createpsbt`、`decodepsbt`、`signrawtransactionwithwallet`

## 🔗 资源

- [Officine Bitcoin 课程](https://officinebitcoin.it/lezioni/fulhar/)：硬件、Bitcoin Core、electrs、测试网、bitcoin-cli
- [Officine Bitcoin 的 Debian 课程](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line)，包括 [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md) 和 [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md)
- [Bitcoin Core](https://bitcoincore.org/) 和它的 [Tor 说明](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) 和它的[配置文档](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core 配置生成器](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [ODROID-M1 的 Petitboot 自动启动](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)和 [ODROID 论坛帖子](https://forum.odroid.com/viewtopic.php?t=37109)

<a id="credits"></a>

## 🙏 致谢

译自以下 [Satoshi Spritz](https://satoshispritz.it/) 讲座（意大利语）：

- [比特币节点：软件、硬件与 DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf)，Valerio Vaccaro，Satoshi Spritz Milano，采用 [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) 许可
- [ODROID-M1 上的比特币节点，第 1 部分：安装操作系统](https://satoshispritz.it/presentazioni/241031-odroid.pdf)，Satoshi Spritz Treviso，2024 年 10 月 29 日
- [ODROID-M1 上的比特币节点，第 2 部分：安装 Bitcoin Core 并开始同步](https://satoshispritz.it/presentazioni/241031-odroid2.pdf)，Satoshi Spritz Treviso，2024 年 11 月 19 日
- [Tor 网络上的比特币节点](https://satoshispritz.it/presentazioni/241031-tor.pdf)，Satoshi Spritz Treviso，2025 年 1 月 14 日
- [Electrum 服务器：electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf)，Satoshi Spritz Treviso，2025 年 2 月 11 日

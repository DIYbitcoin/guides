# 💳 刷写 javacard：Satochip、Satodime、SeedKeeper 和 Keycard

javacard 里有一个**安全芯片**：这是一种专门用来保护机密信息（比如你的比特币密钥）的芯片，即使别人拿到了这张卡，也能保证这些信息的安全。你可以在卡上安装叫作
**applet** 的小型应用。本指南会把四个开源比特币 applet
中的一个安装到一张空白的 J3H145 javacard 上：

- **Satochip**：配合 Sparrow Wallet 使用的 DIY 比特币钱包
- **Satodime**：一张不记名卡，谁拿着这张卡，卡里的比特币就归谁，就像现金一样
- **SeedKeeper**：用 PIN 码锁住的加密备份，用来保存你的助记词和密码短语、各种密码、钱包描述符以及其他机密信息
- **Keycard**：另一款配合 Sparrow Wallet 使用的 DIY 比特币钱包

这些 applet 来自 [Satochip](https://satochip.io) 和
[Keycard](https://keycard.tech)，本指南参照的是
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) 指南。

![电脑、读卡器、卡片和四个 applet](images/00-overview.svg)

## 🧰 需要准备的东西

- 💳 一张 **J3H145** javacard。如果想买，本指南用的就是这款：[阿里巴巴上的 J3H145](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 一个 **USB 智能卡读卡器**：插在电脑上的小盒子，卡要芯片朝前插进去。不要用
  **ACS ACR122U**，它是一种把卡贴上去读取的 NFC
  读卡器：[Satochip-DIY 指南](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)提醒，用它安装
  applet 不可靠，还可能把卡弄坏。等 applet 装好之后，用它来使用这张卡没有问题。
- 🛡️ 有 [ShieldSigner](https://github.com/3rdIteration/seedsigner) 吗？它也能把这些
  applet 装到卡上，代替电脑和读卡器。
- 💻 一台 **Mac**、**Linux**（Debian、Ubuntu 或 Raspberry Pi OS）或 **Windows**
  电脑
- 🌐 用于下载的网络连接

## 🛠️ 第 1 步：设置你的电脑（只需一次）

这一步会安装 **Java** 和 **GlobalPlatformPro**，也就是把 applet 装到卡上的
`gp` 命令。

<details>
<summary><b>🍎 我用的是 Mac</b></summary>

1. 打开**终端**：按 **Cmd + Space**，输入 `Terminal`，按
   **Enter**。

2. 安装 **Homebrew**，这是在终端里使用的 Mac 应用安装工具。如果已经装过就跳过这一步（`brew --version`
   会显示版本号）。

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 它会要求输入你的 Mac 密码。输入时屏幕上什么都不会显示，这是正常的，直接输入然后按
   **Enter**。

   ⚠️ 安装完成后，它会显示 **Next steps** 和两三条命令。把这些命令也复制并运行，不然
   Mac 会找不到 `brew`。

3. 安装 Java：

   ```bash
   brew install --cask temurin
   ```

4. 安装 GlobalPlatformPro：

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 我用的是 Linux</b></summary>

1. 打开**终端**：按 **Ctrl + Alt + T**，或者在应用列表里找到**终端**。

2. 安装 Java、git 和智能卡服务（`pcscd`）：

   ```bash
   sudo apt update
   ```

   🔒 它会要求输入你电脑的密码。输入时屏幕上什么都不会显示，这是正常的，直接输入然后按
   **Enter**。

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. 下载 [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)
   的文件，其中包含 GlobalPlatformPro，文件名是 `gp.jar`：

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. 给它起个短名字 `gp`，这样本指南里的每条命令都能照原样运行：

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. **关掉终端，再打开一个新的**，这样它才能认出这个新名字。

</details>

<details>
<summary><b>🪟 我用的是 Windows</b></summary>

⚠️ 还没有在 Windows 上测试过。

1. 安装 Java：从 <https://adoptium.net> 下载 **Temurin**，用默认选项安装。

2. 把
   [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)
   从 [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) 下载到你的**下载**文件夹。

3. 打开**命令提示符**：按 **Windows** 键，输入 `cmd`，按
   **Enter**。不需要以管理员身份运行。

4. 进入你的下载文件夹：

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. 本指南里凡是写 `gp` 的地方，都改成输入 `gp.exe`，例如
   `gp.exe --info`。凡是写 `cd ~/Downloads` 的地方，都用第 4 步里的那一行。

</details>

✅ 检查是否成功：

```bash
gp --version
```

如果显示几行以 `#` 开头的内容，而且没有
`command not found`，就说明成功了。

💡 每次运行 `gp` 时，Java 都会显示几行以 `WARNING:` 开头的内容。这是正常的，可以忽略。

## 🔌 第 2 步：检查读卡器能否识别卡片

插上读卡器，把卡插进去（芯片朝前），然后运行：

```bash
gp --info
```

✅ 如果看到带 `CPLC` 的几行和 `Global Platform card`，说明读卡器和卡已经连通了：

![gp --info 的输出](images/01-gp-info.svg)

现在看看卡上已经有什么：

```bash
gp --list
```

空白卡只会显示出厂时自带的内容：卡管理器（`ISD`）和一个包（`A0000001515350`）。不要动它们。

![空白卡上的 gp --list](images/02-gp-list-blank.svg)

### 🛑 如果看到“Card cryptogram invalid”，请停下

![Card cryptogram invalid 警告](images/07-gp-cryptogram-stop.svg)

⚠️ 这说明这张卡已经用你没有的密钥锁住了。**不要再运行这条命令**：错几次之后，卡会永久锁死。请向卖卡给你的人要它的密钥。

## 📥 第 3 步：下载 applet 文件

把你想要的 applet 文件下载到**下载**文件夹。在每个页面上，点击 **Assets** 下面的文件名就能下载。

| Applet | 要下载的文件 | 用什么设置 |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 为什么用这些版本，而不是最新版？</b></summary>

- **Satochip 用 v0.15，不用 v0.16**：v0.16（2026 年 10 月）改变了卡片调整 taproot
  密钥的方式，而 Sparrow 2.5.5 仍然发送旧格式，所以 Sparrow 里的 taproot 钱包无法用 v0.16
  的卡签名。Native SegWit 在两个版本上都能用。
- **Satodime v0.1-0.2** 是最新的稳定版；v0.2 的版本仍然标记为
  beta。
- **Keycard 用 3.2，不用 4.0**：Keycard 4.0（2026 年 9 月）改变了卡片和电脑建立加密连接的方式，而 Sparrow
  2.5.5 仍在用旧方式。

等 Sparrow 更新跟上之后，再用新版本。

</details>

<details>
<summary><b>🔍 可选：检查文件有没有被篡改</b></summary>

进入你的下载文件夹：

```bash
cd ~/Downloads
```

🍎 Mac：

```bash
shasum -a 256 SatoChip-v0.15-0.1.cap
```

🐧 Linux：

```bash
sha256sum SatoChip-v0.15-0.1.cap
```

🪟 Windows：

```bat
certutil -hashfile SatoChip-v0.15-0.1.cap SHA256
```

它显示的那串长代码必须和发布页面上的一致：

- `SatoChip-v0.15-0.1.cap`：
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`：
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ 第 4 步：把 applet 装到卡上

🔄 想把卡上的 applet 换成另一个？先删除旧的，再安装新的：见下面的[换成另一个 applet](#switching)。

进入存放文件的下载文件夹：

```bash
cd ~/Downloads
```

然后**只运行你那个 applet 对应的一行**：

Satochip：

```bash
gp --install SatoChip-v0.15-0.1.cap
```

Satodime：

```bash
gp --install Satodime-0.1-0.2.cap
```

SeedKeeper：

```bash
gp --install SeedKeeper-v0.2-0.1.cap
```

Keycard（额外的设置让它成为 Sparrow 要找的那个钱包）：

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ 最后会有一行 `loaded:`：

![gp --install 的输出](images/03-gp-install-satochip.svg)

![Keycard 的 gp --install](images/09-gp-install-keycard.svg)

✅ 再运行一次 `gp --list`。如果多了一行以 `APP` 开头、以
`SELECTABLE` 结尾，就说明 applet 已经装好，可以用了：

![装好 Satochip 后的 gp --list](images/04-gp-list-satochip.svg)

现在去你那个 applet 的设置步骤：

- Satochip：[第 5a 步](#step-5a)
- SeedKeeper 或 Satodime：[第 5b 步](#step-5b)
- Keycard：[第 5c 步](#step-5c)

<a id="step-5a"></a>

## 第 5a 步：在 Sparrow 里设置 Satochip

👛 从 <https://sparrowwallet.com/download/> 下载适合你电脑的
Sparrow Wallet。

PIN 码和 12 个助记词都直接在 Sparrow 里设置，不需要 Satochip
的应用。整个过程中卡要一直插在读卡器里。

1. 在 Sparrow 里新建一个钱包并起个名字。在 **Keystores** 下面选
   **Connected Hardware Wallet**，然后按 **Scan...**

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow 找到了 **Satochip**。按 **Import Keystore**。

   ![Sparrow 找到 Satochip](images/sparrow-2-found.webp)

3. Sparrow 让你 **Choose a PIN code**。输入一个 PIN 码。

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow 显示 **Card not initialized**。在框里再输入一次同样的 PIN 码，然后按
   **Initialize**。

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. **Satochip Seed Words** 窗口会打开。按 **Generate New** 生成一组新的 12
   个助记词，或者输入你已有的 12 个助记词。✍️ **把这 12 个助记词抄在纸上**，然后按 **OK**。Sparrow
   会把它们写入卡里，不带密码短语。

   💡 这 12 个助记词是 Sparrow 在你的电脑上生成的，所以如果用来存真正的积蓄，请使用你信任的电脑。

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**。按 **OK**。

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. 回到 Satochip 那一行，再按一次 **Import Keystore**。

   ![再次 Import Keystore](images/sparrow-7-import-keystore.webp)

8. 🎉 钱包里现在显示你的 Satochip 和它的指纹：一个用来标识你密钥的短代码。按
   **Apply** 保存钱包。

   ![Sparrow 里完成的钱包](images/sparrow-8-wallet.webp)

🔢 Sparrow 给这张卡设置了 **5 次 PIN 码尝试机会**。以后你也可以在 keystore 里用
**Change Pin** 修改 PIN 码。

<a id="step-5b"></a>

## 第 5b 步：在 Sato-Tools 里设置 SeedKeeper 或 Satodime

🧰 从 <https://github.com/Toporin/Satochip-Utils/releases> 下载适合你电脑的
Sato-Tools（本指南用的是 0.4.0-beta）。

把卡插在读卡器里，打开 Sato-Tools，然后按 **Let's go!**

![Sato-Tools 欢迎界面](images/satotools-0-welcome.webp)

💡 **如果 Sato-Tools 认不出你刚刷好的卡，退出应用再重新打开。**

### SeedKeeper

1. Sato-Tools 显示 **Your SeedKeeper is connected**。按
   **Setup my card**。

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. 在 **New PIN code** 里输入 PIN 码，再在 **Confirm PIN code** 里输入一次（4 到
   16 个字符），然后按 **Save PIN**。

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** 按 **Ok**。

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 菜单里现在有 **My secrets**、**Generate secret**、**Import secret**、**Backup card**
   和 **Card logs**。你的 SeedKeeper 已经可以用来保存助记词、密码短语、各种密码和钱包描述符了。

   ![SeedKeeper 准备就绪](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools 显示 **Your Satodime is connected**，有三个保险库：
   **Vault #0**、**Vault #1** 和 **Vault #2**。

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview** 显示三个保险库都是 **uninitialized**：里面还没有密封任何东西。

   ![我的保险库](images/satotools-satodime-2-vaults.png)

3. **About** 显示 applet 的版本和 **Card has no owner**。**Take
   ownership** 会让这台电脑成为卡的所有者，密封保险库之前必须先这样做。

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 按 **Take ownership**。About 现在显示 **You are the owner**，按钮也变成了
   **Transfer ownership**，把卡交给别人时就用它。

💡 **密封**保险库会为它生成一个比特币地址，私钥藏在卡里面。**解封**会显示私钥，这样比特币才能被转走。在接受别人用
Satodime 付款之前，先检查它的保险库是否仍然是密封的。

### 📱 在手机上

SeedKeeper 和 Satodime 也有手机应用。把卡贴在带 NFC 功能的手机背面，就能在手机上使用。

### ❓ 为什么显示“Genuine: NO”？

从 Satochip 买的卡在出厂时就装有 Satochip 的证书，**Genuine**
检查的就是它。你自己刷的 DIY 卡没有这个证书，所以总是显示 **Genuine: [NO]**。applet
的功能完全一样。

<a id="step-5c"></a>

## 第 5c 步：在 Sparrow 里设置 Keycard

👛 如果还没有，请从 <https://sparrowwallet.com/download/> 下载适合你电脑的
Sparrow Wallet。

Keycard 的设置方法和 Satochip 完全一样，只有两点不同：PIN 码必须是**正好 6 位数字**，而且卡只允许**输错 3 次 PIN 码**。

1. 新建一个钱包，选 **Connected Hardware Wallet**，按
   **Scan...** Sparrow 找到了 **Keycard**。按 **Import Keystore**。

   ![Sparrow 找到 Keycard](images/keycard-1-found.webp)

2. 设一个**正好 6 位数字**的 PIN 码。更短或更长都会出现
   **PIN length must be between 6 and 6 characters**。

   ![PIN 码必须是 6 位数字](images/keycard-2-pin-error.webp)

3. 再输入一次同样的 PIN 码，然后按 **Initialize**。会打开和 Satochip
   一样的助记词窗口（[第 5a 步](#step-5a)第 5 项）：按 **Generate New**
   或输入你自己的助记词，✍️ **把这 12 个助记词抄下来**，然后按 **OK**。✅ **Card Initialized**：按 **OK**。

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 再按一次 **Import Keystore**。钱包里现在显示你的 Keycard
   和它的指纹。按 **Apply** 保存钱包。

   ![Sparrow 里完成的 Keycard 钱包](images/keycard-4-wallet.webp)

⚠️ **你的 12 个助记词是唯一的恢复途径。** PIN 码输错 3 次后
Keycard 就会锁死，Sparrow 也没有办法解锁。如果发生这种情况，删除
Keycard，重新安装，再在 Sparrow 里重新输入你的 12 个助记词。

<a id="switching"></a>

## 🔄 换成另一个 applet

⚠️ **删除 applet 会抹掉其中保存的所有内容**，包括 Satochip 或 Keycard 里的种子和
SeedKeeper 里的机密信息。只有在你已经抄下 12
个助记词，或者这个钱包只是练习用的时候才这样做。卡本身不会受损，想重新刷多少次都可以。

![卸载会抹掉内容，安装会从头开始](images/08-swap.svg)

1. 进入存放 applet 文件的下载文件夹：

   ```bash
   cd ~/Downloads
   ```

2. 不确定卡上装的是什么？运行 `gp --list`：`APP` 那一行会显示出来。

3. 删除卡上现在的 applet。**只运行它对应的那一行**：

   Satochip：

   ```bash
   gp --uninstall SatoChip-v0.15-0.1.cap
   ```

   Satodime：

   ```bash
   gp --uninstall Satodime-0.1-0.2.cap
   ```

   SeedKeeper：

   ```bash
   gp --uninstall SeedKeeper-v0.2-0.1.cap
   ```

   Keycard：

   ```bash
   gp --delete A0000008040001 -f
   ```

4. ✅ 再运行一次 `gp --list`。应该只剩下卡管理器（`ISD`）和出厂自带的包（`A0000001515350`）。

5. 安装新的 applet（[第 4 步](#step-4)）并进行设置（[第 5a 步](#step-5a)、[5b](#step-5b)
   或 [5c](#step-5c)）。

例如：

![把 Satochip 换成 Satodime](images/05-gp-swap-to-satodime.svg)

![把 Satodime 换成 SeedKeeper](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 故障排除

| 你看到的 | 怎么办 |
|---|---|
| 🍎 `brew: command not found` | 运行 Homebrew 安装完成时显示的 **Next steps** 里的命令。 |
| 🐧 `gp: command not found` | 关掉终端，再打开一个新的。 |
| `No such file` 或 `does not exist` | 先运行 `cd ~/Downloads`，并确认文件在下载文件夹里。 |
| `Card cryptogram invalid` | 这张卡已经用别的密钥锁住了。停下来，不要再试。 |
| `LOAD failed: 0x6438` | 这个 applet 是为比这张卡更新的 Java Card 版本制作的。 |
| `INSTALL ... failed: 0x6444` 或 `0x6F00` | 这张卡缺少 applet 需要的某项功能。 |
| 在已经装有 applet 的卡上 `gp --install` 失败 | 运行 `gp --list`。如果有 `APP` 那一行，先删除那个 applet（见[换成另一个 applet](#switching)）。 |
| Sparrow 的 **Scan...** 什么都找不到 | 退出 Sato-Tools 和其他所有读卡应用，把卡拔出来再插回去，然后再按一次 **Scan...**。 |
| 忘了 PIN 码，或者 PIN 码输错太多次，卡被锁住了 | 删除 applet 再重新安装（这会清空卡），然后恢复你的 12 个助记词。 |
| Sato-Tools 认不出卡 | 退出 Sato-Tools 再重新打开。 |
| `gp` 找不到读卡器，或者说没有插卡 | 把卡芯片朝前插进去。如果还是说没有卡，就是读卡器卡住了：把它从电脑上拔下来，卡留在里面再插回去，再试一次。🐧 然后运行 `sudo systemctl restart pcscd`。 |

## 🚀 更进一步

- 🎥 [视频：把 7 种不同的 applet 刷进这些卡](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 用你自己的密钥锁住这张卡，这样别人就无法更改卡上安装的内容：[Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ 自己编译 applet。上面的文件是别人编译的，所以你只能相信它们和公开的源代码一致。自己从源代码编译，就能保证装到卡上的正是任何人都能查看的那份代码：[Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 致谢

- 🙌 [Toporin](https://github.com/Toporin)，[Satochip](https://satochip.io)
  的创建者，开发了 Satochip、Satodime 和 SeedKeeper applet 以及 Sato-Tools。
- 🛠️ 3rdIteration 的 [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)，本指南参照的
  DIY 指南，提供了经过测试的卡片和读卡器清单以及 `gp.jar`。
- [Keycard](https://github.com/keycard-tech/status-keycard)，提供了 Keycard
  applet。
- 💳 Martin Paljak 的 [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)，用来把
  applet 装到卡上。

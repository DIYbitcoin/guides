# 📟 DIY 签名器：SeedSigner、Kern 和 KISS Signer

签名器是一种不联网的小设备，用来保存你的比特币密钥并签署交易，这样你的密钥永远不会接触电脑或手机。本指南会把一个开源签名器安装（也就是“刷写”）到
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)
上，这是一块带摄像头和 microSD 卡槽的 3.5 英寸触摸屏开发板。不需要焊接。

📷 **使用方式**：签名器永远不联网。你用它的摄像头扫描二维码，把它和
Sparrow 这类钱包应用配对，签署交易也是用同样的方式。

⚠️ **这三个签名器在这块开发板上都还是早期版本。只能用练习用的助记词和 testnet（没有价值的练习用比特币），或者极少的金额，绝对不要用你真正的积蓄。**

### 🧰 你需要准备

- 📟 Waveshare 3.5 英寸开发板
- 🔌 一根能传数据的 USB-C 线。只能充电的线可以给开发板供电，但电脑完全识别不到它。
- 💻 一台电脑：SeedSigner 需要 Mac 或 Linux；Kern 和 KISS Signer 只要电脑上有 **Chrome** 或 **Brave** 就行

### 👉 选择你的签名器

每一个都是一条完整的路线，从插上开发板一直到签名器的第一个画面。只跟着你选的那一个做。

- 🌐 **[Kern](#kern)**：最简单。在 Chrome 或 Brave 里点几下就能刷写。
- 🌐 **[KISS Signer](#kiss)**：也是在 Chrome 或 Brave 里刷写。
- 💻 **[SeedSigner](#seedsigner)**：在终端里输入几条命令来刷写。一共四个短页面。

<a id="kern"></a>

## 🌐 Kern

需要 **Chrome** 或 **Brave**。Safari 和 Firefox 不能刷写。

1. 把 USB-C 线插到开发板上标有 **UART** 的接口，另一头插到电脑上。

   🐧 **在 Linux 上**，先做下面的 [Linux 步骤](#linux-kern)。

2. 打开 [Kern 网页刷写工具](https://odudex.github.io/Kern/flash/)。
3. 保持 **📦 Latest CI Build** 选中。
4. 在 **Board** 里选 **Waveshare ESP32-P4 3.5" (320×480 SPI)**。列表默认是另一块板子，所以一定要确认这一项。
5. 点 **🔌 Connect**。会弹出一个小窗口：在里面选你的开发板（🍎 名字里有 `usbmodem`，🐧 有 `ttyACM`），然后点 **连接**。

   ✅ 按钮下面的黑框里会显示 `Connected to: ESP32-P4`。刷写工具会读取你的芯片，自动选 v1.x 或 v3.x 的版本。

6. 点 **⚡ Flash**，然后等待，不要拔线。

   ✅ 会显示 `Device reset. Flash successful! ✓`。

7. 拔下开发板，等 3 秒，再插回去。

   ✅ 开发板上启动了 Kern。🎉

<details id="linux-kern">
<summary><b>🐧 Linux 步骤：让浏览器能和开发板通信</b></summary>

1. 打开**终端**：按 **Ctrl + Alt + T**。
2. 运行：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 它会要你输入电脑的密码。输入时什么都不显示，这是正常的，直接输入然后按 **Enter**。

3. **注销电脑再重新登录**，然后回到上面的第 2 步。

</details>

📦 官方的 [Kern 发布版](https://github.com/odudex/Kern/releases)只支持 **v1.x** 芯片，所以请用网页刷写工具。

下一步：[🔋 安装电池](#battery)；如果出了问题，看[🩺 故障排除](#troubleshooting)。

<a id="kiss"></a>

## 🌐 KISS Signer

需要 **Chrome** 或 **Brave**。Safari 和 Firefox 不能刷写。

1. 把 USB-C 线插到开发板上标有 **UART** 的接口，另一头插到电脑上。

   🐧 **在 Linux 上**，先做下面的 [Linux 步骤](#linux-kiss)。

2. 打开 [KISS Signer 安装页面](https://diybitcoin.github.io/kiss-signer/)。
3. 在 **Which board is it?** 下面选 **Waveshare 3.5in**。
4. 勾选 **This erases the device. I'm starting with keys that hold nothing.**
5. 点 **Connect and install**。会弹出一个小窗口：在里面选你的开发板（🍎 名字里有 `usbmodem`，🐧 有 `ttyACM`），然后点 **连接**。
6. 在下一个窗口里点 **Install KISS Signer (Waveshare 3.5in)**。勾选 **Erase device**，点 **Next**，再点 **Install**。等待，不要拔线。

   ✅ 会显示 **Installation complete!**

7. 拔下开发板，等 3 秒，再插回去。

   ✅ 开发板会启动到 **FRUIT ISLAND**，这是 KISS Signer 首先显示的游戏。🎉

8. 屏幕一直是黑的？说明你的开发板是较新的 **v3.x** 芯片，而这个版本是给 v1.x 的。请用[测试安装页面](https://diybitcoin.github.io/kiss-signer/test/)重新刷写：在 **1. Your board** 下面选你的开发板，然后点 **Connect and install**。它会读取你的芯片并选出适合的版本。

   ⚠️ 测试安装页面里是没有签名的测试版本，还没有在真正的 v3.x 开发板上试过。

<details id="linux-kiss">
<summary><b>🐧 Linux 步骤：让浏览器能和开发板通信</b></summary>

1. 打开**终端**：按 **Ctrl + Alt + T**。
2. 运行：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 它会要你输入电脑的密码。输入时什么都不显示，这是正常的，直接输入然后按 **Enter**。

3. **注销电脑再重新登录**，然后回到上面的第 2 步。

</details>

下一步：[🔋 安装电池](#battery)；如果出了问题，看[🩺 故障排除](#troubleshooting)。

<a id="seedsigner"></a>

## 💻 SeedSigner 1：准备好你的电脑

这块开发板上的 SeedSigner 是 Keith（kdmukai）做的早期版本。你要在**终端**里用芯片厂商的工具 `esptool` 输入命令来刷写。下面每条命令都有一个**复制**按钮：复制，粘贴到终端里，按 **Enter**。

<details>
<summary><b>🍎 我用的是 Mac</b></summary>

1. 打开**终端**：按 **Cmd + 空格**，输入 `Terminal`，按 **Enter**。

2. 安装 **Homebrew**，它是 Mac 上给终端用的软件安装工具。如果已经装过就跳过（`brew --version` 会显示版本号）。

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 它会要你输入 Mac 的密码。输入时什么都不显示，这是正常的，直接输入然后按 **Enter**。

   ⚠️ 装完后它会显示 **Next steps**，里面有两三条命令。把它们也复制并运行，不然 Mac 找不到 `brew`。

3. 安装 esptool：

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 我用的是 Linux</b></summary>

1. 打开**终端**：按 **Ctrl + Alt + T**。

2. 让你的用户能和开发板通信：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 它会要你输入电脑的密码。输入时什么都不显示，这是正常的，直接输入然后按 **Enter**。

3. 安装 `esptool` 和 `unzip`，一次运行一行：

   ```bash
   sudo apt update
   ```

   ```bash
   sudo apt install pipx unzip
   ```

   ```bash
   pipx install esptool
   ```

   ```bash
   pipx ensurepath
   ```

4. **注销电脑再重新登录**，然后重新打开终端。

</details>

✅ 检查 esptool 能不能用：

```bash
esptool version
```

必须显示 **5.3 或更新**。旧版本刷不了新的芯片：🍎 `brew upgrade esptool`，🐧 `pipx upgrade esptool`。

下一步：[SeedSigner 2：插上开发板并查看芯片](#ss-chip)。

<a id="ss-chip"></a>

## 💻 SeedSigner 2：插上开发板并查看芯片

这块开发板的芯片有两个版本：旧板子是 **v1.x**，新板子是 **v3.x**，每个版本需要各自的 SeedSigner 版本。这一页告诉你自己的是哪一种。

1. 把 USB-C 线插到开发板上标有 **UART** 的接口，另一头插到电脑上。

2. 运行适合你电脑的那一行：

   🍎 Mac：

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux：

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. 找到写着 `ESP32-P4 (revision ...)` 的那一行：

   - `revision v1.3` 或任何 **v1**：你的是 **v1.x** 芯片。
   - `revision v3.0`、`v3.2` 或任何 **v3**：你的是 **v3.x** 芯片。

   记下来：下一页要用到。

下一步：[SeedSigner 3：下载适合你芯片的版本](#ss-download)。

<a id="ss-download"></a>

## 💻 SeedSigner 3：下载适合你芯片的版本

**只用适合你芯片的版本**（在 SeedSigner 2 里查到的）。另一个启动不了。

<details open>
<summary><b>1️⃣ 我的芯片是 v1.x</b></summary>

点击把
[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
下载到你的**下载**文件夹。

它带有一个**触摸修复**，让滑动时滚动菜单，而不是按下按钮。这个修复还是一个[未合并的 pull request](https://github.com/kdmukai/esp-board-common/pull/21)，还没有正式进入 SeedSigner。已在一块 v1.3 开发板上测试过。

zip 的 SHA256：
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ 我的芯片是 v3.x</b></summary>

Keith 的版本是在 GitHub 上构建的，下载需要一个免费的 GitHub 账号。

1. 在 [github.com/login](https://github.com/login) 登录，或者先在那里注册一个账号。
2. 打开 [SeedSigner 的最新构建](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess)。
3. 点列表**最上面**的那一个构建，它是最新的。
4. 往下滚到 **Artifacts**，点 **flashable-dist-waveshare-esp32-p4-35**，把它下载到你的**下载**文件夹。

   ⚠️ 一定要确认名字以 **35** 结尾。`43` 的是给 4.3 英寸开发板的，Keith 的 Releases 页面上的文件也是 4.3 英寸的。它们在这块开发板上都启动不了。

</details>

下一步：[SeedSigner 4：解压并刷写](#ss-flash)。

<a id="ss-flash"></a>

## 💻 SeedSigner 4：解压并刷写

1. 进入下载文件夹：

   ```bash
   cd ~/Downloads
   ```

2. 用适合**你**芯片的两行，解压并进入文件夹：

   **v1.x：**

   ```bash
   unzip seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip
   ```

   ```bash
   cd seedsigner-waveshare-p4-35-pre-rev3-touchfix
   ```

   **v3.x：**

   ```bash
   unzip flashable-dist-waveshare-esp32-p4-35.zip -d seedsigner-v3
   ```

   ```bash
   cd seedsigner-v3
   ```

   💡 在 Mac 上，Safari 可能已经帮你解压好了。如果 `unzip` 说找不到文件，就跳过那一行，只运行 `cd` 那一行。

3. 用适合你电脑的那一行刷写：

   🍎 Mac：

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux：

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   大约需要 4 分钟，所以不要拔开发板。开头出现几条 `Deprecated` 警告没关系。

   ✅ 完成后，开发板会重启进入 SeedSigner。🎉

4. 用测试数据试一试：在电脑上打开 [testqrs.com](https://testqrs.com)，把开发板的摄像头对准上面的二维码。那里的助记词是公开的，只能用来练习。

下一步：[🔋 安装电池](#battery)；如果出了问题，看[🩺 故障排除](#troubleshooting)。

<a id="battery"></a>

## 🔋 安装电池

开发板附带一块小电池和一把小螺丝刀，这样不接线也能用。

1. 用指甲撬开外壳背面的四个小盖子，螺丝就在下面。
2. 用螺丝刀拧下四颗螺丝，把外壳后盖拿下来。
3. 把电池插头插到开发板右下角的电池插座上。它只能朝一个方向插进去，插不进去就翻过来。不要硬插。
4. ✅ 电源按钮旁边应该会亮起一个绿灯。
5. 把外壳后盖装回去并拧紧螺丝。装着电池时外壳也能正常合上。

![打开外壳、插好电池的 Waveshare 3.5 英寸开发板](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 故障排除

| 你看到 | 怎么办 |
|---|---|
| 浏览器窗口里没有可以选的开发板 | 用 **Chrome** 或 **Brave**、**UART** 接口和能传数据的线。🐧 做 [Kern](#linux-kern) 或 [KISS Signer](#linux-kiss) 页面上的 Linux 步骤。 |
| `port is busy` 或 `could not open port` | 关掉网页刷写工具的标签页，以及其他正在用开发板的终端，然后再试。 |
| 🍎 `no matches found: /dev/cu.usbmodem*` 或 🐧 `No such file or directory` | 电脑没识别到开发板：用 **UART** 接口和确定能传数据的线。 |
| 🐧 `Permission denied`，或者浏览器打不开开发板 | 做 Linux 步骤，包括注销再重新登录。 |
| `Failed to connect` | 关掉所有正在用开发板的程序，拔下开发板再插回去，然后再试。 |
| 🍎 `brew: command not found` | 运行 Homebrew 装完时显示的 **Next steps** 命令。 |
| 🐧 `esptool: command not found` | 注销再重新登录，然后打开一个新终端。 |
| `requires chip revision in range` | 版本和芯片不匹配：重做 [SeedSigner 2](#ss-chip)，下载另一个版本。 |
| 找不到 `flash_args` | 你不在解压出来的文件夹里：重做 [SeedSigner 4](#ss-flash) 的第 2 步。 |
| 刷写后屏幕上什么都没有 | 拔下来，等 3 秒，再插回去。检查版本和芯片是否匹配。 |
| `esptool` 刷不了芯片 | 必须是 5.3 或更新：🍎 `brew upgrade esptool`，🐧 `pipx upgrade esptool`。 |

## 🙏 致谢

- [Kern](https://github.com/odudex/Kern)：odudex
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder)：kdmukai
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer)：DIYbitcoin

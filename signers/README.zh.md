# 📟 DIY 签名器：SeedSigner、Kern 和 KISS Signer

签名器是一种不联网的小设备，用来保存你的比特币密钥并签署交易，这样你的密钥永远不会接触电脑或手机。本指南会把一个开源签名器安装（也就是“刷写”）到
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)
上，这是一块带摄像头和 microSD 卡槽的 3.5 英寸触摸屏开发板。不需要焊接。

| 签名器 | 刷写方式 | 状态 |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | 在网页浏览器里 | 测试版 |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | 在网页浏览器里 | Beta 版 |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | 在终端里 | 测试版 |

📷 **使用方式**：签名器永远不联网。你用它的摄像头扫描二维码，把它和
Sparrow 这类钱包应用配对，签署交易也是用同样的方式。

⚠️ **这三个签名器在这块开发板上都还是早期版本。只能用练习用的助记词和 testnet（没有价值的练习用比特币），或者极少的金额，绝对不要用你真正的积蓄。**

## 🔌 第 1 步：插上开发板

把 USB-C 线插到标有 **UART** 的接口上。

💡 要用确定能传数据的线。只能充电的线可以给开发板供电，但电脑完全识别不到它。

<a id="step-2"></a>

## 💻 第 2 步：准备好你的电脑

🌐 **Kern** 和 **KISS Signer** 是在 **Chrome** 或
**Brave** 里用网页刷写工具刷写的，所以它们只需要这个浏览器（如果用 Linux，还要做下面 Linux 部分里的第 2 步）。

💻 **SeedSigner** 是在终端里用 `esptool` 刷写的：按下面的步骤安装它。

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

3. 安装 esptool：

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 我用的是 Linux</b></summary>

1. 打开**终端**：按 **Ctrl + Alt + T**，或者在应用列表里找到**终端**。

2. 让你的用户账户可以和开发板通信（三个签名器都需要，包括在浏览器里刷写的
   Kern）：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 它会要求输入你电脑的密码。输入时屏幕上什么都不会显示，这是正常的，直接输入然后按
   **Enter**。

3. **退出登录，再重新登录电脑**，让更改生效。

4. 如果要刷写 SeedSigner，安装 `esptool` 和 `unzip`。再次打开终端，运行：

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

5. **关掉终端，再打开一个新的**，这样它才能找到 `esptool`。

</details>

✅ 如果要刷写 SeedSigner，检查 esptool 是否能用：

```bash
esptool version
```

显示的必须是 **5.3 或更新的版本**。

<a id="step-3"></a>

## 🧬 第 3 步：了解你的芯片

3.5 英寸开发板出厂时带的是 ESP32-P4 芯片两个版本中的一个：较早的板子是
**v1.x**，较新的板子是 **v3.x**，所以最近买的板子很可能是
v3.x。每个签名器都需要专门为你的芯片制作的版本：v1.x 的版本在 v3.x
芯片上无法启动，反过来也一样。

| 签名器 | v1.x 芯片 | v3.x 芯片 |
|---|---|---|
| Kern | 网页刷写工具，或官方发布版 | 只能用网页刷写工具（测试版） |
| KISS Signer | 安装页面（已签名的 beta 版） | 测试安装程序（测试版） |
| SeedSigner | 触摸修复版 | builder 的测试版 |

🌐 **Kern** 和 **KISS Signer**：不需要检查，下面它们各自的部分会告诉你怎么做。

💻 **SeedSigner**：现在就用 `esptool`（[第 2 步](#step-2)）检查一下，这样才能下载正确的版本：

1. 告诉终端开发板在哪里。这会把它保存为 `$PORT`。

   🍎 Mac：

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux：

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. 检查芯片：

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ 它会显示 `ESP32-P4 (revision v1.3)` 或类似内容：这就是 **v1.x**
   芯片。`v3.0` 或更高就是 **v3.x** 芯片。

## Kern

1. 在 **Chrome** 或 **Brave** 里打开 [Kern 网页刷写工具](https://odudex.github.io/Kern/flash/)（Safari 和 Firefox 无法刷写）。
2. 连接开发板，刷写 **Latest CI Build**。刷写工具会读取你的芯片，自动选择 v1.x 或
   v3.x 版本。
3. ✅ 拔掉线，等 3 秒，再插回去。

📦 官方的 [Kern 发布版](https://github.com/odudex/Kern/releases)只支持
**v1.x** 芯片。

## KISS Signer

1. 在 **Chrome** 或 **Brave** 里打开[安装页面](https://diybitcoin.github.io/kiss-signer/)，连接开发板并刷写。
2. ✅ 拔掉线，等 3 秒，再插回去。
3. 屏幕一直是黑的？说明你的开发板用的是较新的 v3.x
   芯片：改用[测试安装程序](https://diybitcoin.github.io/kiss-signer/test/)重新刷写。

⚠️ 测试安装程序里是未签名的测试版，还没在真正的 v3.x
开发板上试过。

## SeedSigner

这块开发板上的 SeedSigner 是早期版本，在终端里用 `esptool`（[第 2 步](#step-2)）刷写。

### 1️⃣ 下载适合你芯片的版本

把它保存到你的**下载**文件夹：

- **v1.x**：[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**：`flashable-dist-waveshare-esp32-p4-35`，来自 builder 的
  [Actions 构建](https://github.com/kdmukai/seedsigner-micropython-builder/actions)（需要登录
  GitHub）

<details>
<summary><b>👆 关于 v1.x 版本</b></summary>

它包含一个**触摸修复**，让手指滑动时菜单跟着滚动，而不是变成按下按钮。这个修复目前还是一个[尚未合并的 pull request](https://github.com/kdmukai/esp-board-common/pull/21)，还没有正式加入
SeedSigner。已在 v1.3 开发板上测试过。

zip 文件的 SHA256：
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ 解压

进入下载文件夹：

```bash
cd ~/Downloads
```

然后运行**你的**版本对应的两行命令。

**v1.x:**

```bash
unzip seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip
```

```bash
cd seedsigner-waveshare-p4-35-pre-rev3-touchfix
```

**v3.x:**

```bash
unzip flashable-dist-waveshare-esp32-p4-35.zip -d seedsigner-v3
```

```bash
cd seedsigner-v3
```

### 3️⃣ 刷写

💡 如果在[第 3 步](#step-3)之后你打开过新的终端，先重新运行那一步里的
`PORT=` 这一行。

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ 完成后，开发板会重启并进入 SeedSigner。🎉

<a id="battery"></a>

## 🔋 安装电池

开发板附带一块小电池和一把小螺丝刀，这样它不插线也能运行。

1. 在外壳背面，用指甲把四个小盖子撬下来。螺丝就在下面。
2. 用螺丝刀拧下四颗螺丝，把外壳后盖取下来。
3. 把电池的插头插到开发板右下角的电池插座上。插头只有一个方向能插进去，所以插不进去的话就翻过来。不要硬插。
4. ✅ 电源按钮旁边应该会亮起绿灯。
5. 把外壳后盖装回去，拧紧螺丝。电池放在里面，外壳也能正常合上。

![外壳打开、电池已插上的 Waveshare 3.5 英寸开发板](images/battery.jpg)

## 🩺 故障排除

| 你看到的 | 怎么办 |
|---|---|
| 网页刷写工具里没有可选的开发板 | 用 **Chrome** 或 **Brave**，接 **UART** 接口，用能传数据的线。🐧 在 Linux 上要做[第 2 步](#step-2)。 |
| `port is busy` 或 `could not open port` | 关掉网页刷写工具的标签页和其他正在使用开发板的终端，然后重试。 |
| 电脑识别不到开发板，或者运行 `PORT=` 那一行后出现 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory` | 接 **UART** 接口，用确定能传数据的线。 |
| 🐧 `Permission denied`，或者浏览器无法打开开发板 | 在 Linux 上做[第 2 步](#step-2)，包括退出登录再重新登录。 |
| 🍎 `brew: command not found` | 运行 Homebrew 安装完成时显示的 **Next steps** 里的命令。 |
| 🐧 `esptool: command not found` | 关掉终端，再打开一个新的。 |
| 找不到 `flash_args` | 你不在解压出来的文件夹里：重新做 [SeedSigner 第 2 步](#seedsigner-unzip)。 |
| 刷写后屏幕上什么都没有 | 拔掉线，等 3 秒，再插回去。检查版本是否和你的芯片匹配。 |
| `esptool` 无法刷写芯片 | 必须是 5.3 或更新的版本：🍎 `brew upgrade esptool`，🐧 `pipx upgrade esptool`。 |

## 🙏 致谢

- [Kern](https://github.com/odudex/Kern)，作者 odudex。
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder)，作者 kdmukai。
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer)，作者 DIYbitcoin。

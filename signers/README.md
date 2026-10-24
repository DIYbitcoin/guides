# 📟 DIY signers: SeedSigner, Kern and KISS Signer

A signer is a small offline device that holds your bitcoin keys and signs
transactions, so your keys never touch a computer or phone. This guide
installs ("flashes") an open source signer onto the
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm),
a 3.5 inch touchscreen board with a camera and a microSD slot. No soldering.

| Signer | How you flash it | Status |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | In your web browser | Test versions |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | In your web browser | Beta |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | In the Terminal | Test versions |

📷 **How you use it:** the signer never connects to the internet. You
pair it with a wallet app like Sparrow by scanning QR codes with its
camera, and sign transactions the same way.

⚠️ **All three are early versions on this board. Use practice seed words
and testnet (practice bitcoin with no value) or tiny amounts only, never
your real savings.**

## 🔌 Step 1: plug in the board

Plug a USB-C cable into the port marked **UART**.

💡 Use a cable you know carries data. A charge only cable powers the
board, but the computer sees nothing at all.

<a id="step-2"></a>

## 💻 Step 2: get your computer ready

🌐 **Kern** and **KISS Signer** flash from a web flasher in **Chrome** or
**Brave**, so that browser is all you need for them (on Linux, also do
step 2 in the Linux box below).

💻 **SeedSigner** flashes from the Terminal with `esptool`: install it
below.

<details>
<summary><b>🍎 I'm on a Mac</b></summary>

1. Open the **Terminal**: press **Cmd + Space**, type `Terminal`, press
   **Enter**.

2. Install **Homebrew**, the Mac app installer for the Terminal. Skip
   this if you already have it (`brew --version` prints a version).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 It asks for your Mac's password. Nothing shows while you type;
   that's normal, just type it and press **Enter**.

   ⚠️ When it finishes, it prints **Next steps** with two or three
   commands. Copy and run those too, or the Mac won't find `brew`.

3. Install esptool:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 I'm on Linux</b></summary>

1. Open the **Terminal**: press **Ctrl + Alt + T**, or find **Terminal**
   in your apps.

2. Let your user talk to the board (needed for all three signers,
   including Kern in the browser):

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 It asks for your computer's password. Nothing shows while you type;
   that's normal, just type it and press **Enter**.

3. **Log out of your computer and log back in**, so the change applies.

4. For SeedSigner, install `esptool` and `unzip`. Open the Terminal again
   and run:

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

5. **Close the Terminal and open a new one**, so it finds `esptool`.

</details>

✅ For SeedSigner, check esptool works:

```bash
esptool version
```

It must print **5.3 or newer**.

<a id="step-3"></a>

## 🧬 Step 3: know your chip

The 3.5 inch board ships with one of two versions of the ESP32-P4 chip:
**v1.x** on older boards, or **v3.x** on newer ones, so a board bought
recently likely has v3.x. Each signer needs the build made for your chip:
a v1.x build does not start on a v3.x chip, and the other way round.

| Signer | v1.x chip | v3.x chip |
|---|---|---|
| Kern | Web flasher, or the official release | Web flasher only (test version) |
| KISS Signer | Install page (signed beta) | Test installer (test version) |
| SeedSigner | Touch fix build | Builder's test build |

🌐 **Kern** and **KISS Signer:** no need to check, their sections below
show you how.

💻 **SeedSigner:** check it now with `esptool` ([Step 2](#step-2)), so you
download the right build:

1. Tell the Terminal where the board is. This saves it as `$PORT`.

   🍎 Mac:

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux:

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. Check the chip:

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ It prints `ESP32-P4 (revision v1.3)` or similar: that's a **v1.x**
   chip. `v3.0` or higher is a **v3.x** chip.

## Kern

1. Open the [Kern web flasher](https://odudex.github.io/Kern/flash/) in
   **Chrome** or **Brave** (Safari and Firefox can't flash).
2. Connect the board and flash **Latest CI Build**. The flasher reads
   your chip and picks the v1.x or v3.x build itself.
3. ✅ Unplug, wait 3 seconds, plug back in.

📦 The official [Kern releases](https://github.com/odudex/Kern/releases)
only cover **v1.x** chips.

## KISS Signer

1. Open the [install page](https://diybitcoin.github.io/kiss-signer/) in
   **Chrome** or **Brave**, connect the board and flash.
2. ✅ Unplug, wait 3 seconds, plug back in.
3. Screen stays black? Your board has the newer v3.x chip: flash it again
   from the [test installer](https://diybitcoin.github.io/kiss-signer/test/)
   instead.

⚠️ The test installer has unsigned test versions, not yet tried on a real
v3.x board.

## SeedSigner

SeedSigner for this board is an early version, flashed from the Terminal
with `esptool` ([Step 2](#step-2)).

### 1️⃣ Download the build for your chip

Save it into your **Downloads** folder:

- **v1.x**:
  [seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**: `flashable-dist-waveshare-esp32-p4-35` from the builder's
  [Actions builds](https://github.com/kdmukai/seedsigner-micropython-builder/actions)
  (needs a GitHub login)

<details>
<summary><b>👆 About the v1.x build</b></summary>

It includes a **touch fix** so swipes scroll through menus instead of
pressing a button. The fix is still an
[open pull request](https://github.com/kdmukai/esp-board-common/pull/21),
not yet part of SeedSigner. Tested on a v1.3 board.

SHA256 of the zip:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ Unzip it

Move into Downloads:

```bash
cd ~/Downloads
```

Then run the two lines for **your** build.

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

### 3️⃣ Flash

💡 If you opened a new Terminal since [Step 3](#step-3), run its `PORT=`
line again first.

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ When it finishes, the board restarts into SeedSigner. 🎉

<a id="battery"></a>

## 🔋 Install the battery

The board comes with a small battery and a small screwdriver, so it can
run without a cable.

1. On the back of the case, pop off the four small covers with your
   fingernails. The screws are underneath.
2. Unscrew the four screws with the screwdriver and lift the back of the
   case off.
3. Plug the battery's plug into the battery socket at the bottom right of
   the board. It only fits one way up, so if it doesn't go in, turn it
   over. Don't force it.
4. ✅ A green light should come on next to the power button.
5. Put the back of the case on again and screw it shut. The case closes
   normally with the battery inside.

![Waveshare 3.5 inch board with the case open and the battery plugged in](images/battery.jpg)

## 🩺 Troubleshooting

| You see | What to do |
|---|---|
| The browser flasher shows no board to pick | Use **Chrome** or **Brave**, the **UART** port and a data cable. 🐧 Do [Step 2](#step-2) on Linux. |
| `port is busy` or `could not open port` | Close the browser flasher tab and any other Terminal using the board, then try again. |
| The computer doesn't see the board, or 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory` after the `PORT=` line | Use the **UART** port and a cable you know carries data. |
| 🐧 `Permission denied`, or the browser can't open the board | Do [Step 2](#step-2) on Linux, including logging out and back in. |
| 🍎 `brew: command not found` | Run the **Next steps** commands Homebrew printed when it finished installing. |
| 🐧 `esptool: command not found` | Close the Terminal and open a new one. |
| `flash_args` not found | You're not in the unzipped folder: redo [SeedSigner step 2](#seedsigner-unzip). |
| Nothing on screen after flashing | Unplug, wait 3 seconds, plug back in. Check the build matches your chip. |
| `esptool` can't flash the chip | It must be 5.3 or newer: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Credits

- [Kern](https://github.com/odudex/Kern) by odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) by kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) by DIYbitcoin.

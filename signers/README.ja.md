# 📟 DIY 署名デバイス：SeedSigner、Kern、KISS Signer

署名デバイスは、ビットコインの鍵を保管して取引に署名する、オフラインの小さな機器です。そのため、鍵がパソコンやスマートフォンに触れることはありません。このガイドでは、オープンソースの署名デバイスを
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)
にインストール（「書き込み」）します。カメラと microSD スロットが付いた3.5インチのタッチスクリーンのボードです。はんだ付けは不要です。

📷 **使い方：** 署名デバイスはインターネットに一切つながりません。カメラで QR コードを読み取って Sparrow などのウォレットアプリと連携し、取引の署名も同じ方法で行います。

⚠️ **3つとも、このボードではまだ初期のバージョンです。練習用のシードワードと testnet（価値のない練習用のビットコイン）か、ごく少額だけを使い、本当の貯蓄には絶対に使わないでください。**

### 🧰 必要なもの

- 📟 Waveshare 3.5インチのボード
- 🔌 データ通信ができる USB-C ケーブル。充電専用のケーブルでもボードに電源は入りますが、パソコンはボードをまったく認識しません。
- 💻 パソコン：SeedSigner には Mac か Linux、Kern と KISS Signer には **Chrome** か **Brave** が入ったパソコンなら何でも大丈夫です

### 👉 署名デバイスを選ぶ

どれも、ボードをつなぐところから署名デバイスの最初の画面までがひと続きの手順です。選んだものだけを進めてください。

- 🌐 **[Kern](#kern)：** いちばん簡単です。Chrome か Brave で数回クリックするだけで書き込めます。
- 🌐 **[KISS Signer](#kiss)：** こちらも Chrome か Brave で書き込みます。
- 💻 **[SeedSigner](#seedsigner)：** ターミナルにいくつかコマンドを入力して書き込みます。短いページが4つです。

<a id="kern"></a>

## 🌐 Kern

**Chrome** か **Brave** が必要です。Safari と Firefox では書き込めません。

1. USB-C ケーブルをボードの **UART** と書かれたポートに差し込み、反対側をパソコンにつなぎます。

   🐧 **Linux の場合**は、先に下の [Linux の手順](#linux-kern)を行ってください。

2. [Kern の Web 書き込みツール](https://odudex.github.io/Kern/flash/)を開きます。
3. **📦 Latest CI Build** は選ばれたままにします。
4. **Board** で **Waveshare ESP32-P4 3.5" (320×480 SPI)** を選びます。リストは別のボードから始まるので、必ず確認してください。
5. **🔌 Connect** をクリックします。小さなウィンドウが開いたら自分のボードを選び（🍎 名前に `usbmodem`、🐧 `ttyACM` が入っています）、**接続** をクリックします。

   ✅ ボタンの下の黒い枠に `Connected to: ESP32-P4` と表示されます。ツールがチップを読み取り、v1.x と v3.x のどちらのビルドかを自動で選びます。

6. **⚡ Flash** をクリックし、ケーブルを抜かずに待ちます。

   ✅ `Device reset. Flash successful! ✓` と表示されます。

7. ボードを抜き、3秒待ってから、もう一度差し込みます。

   ✅ ボードで Kern が起動します。🎉

<details id="linux-kern">
<summary><b>🐧 Linux の手順：ブラウザがボードと通信できるようにする</b></summary>

1. **ターミナル**を開きます：**Ctrl + Alt + T** を押します。
2. 次を実行します：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 パソコンのパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押してください。

3. **パソコンからいったんログアウトして、もう一度ログイン**してから、上の手順2に戻ります。

</details>

📦 公式の [Kern リリース](https://github.com/odudex/Kern/releases)は **v1.x** チップにしか対応していないので、Web 書き込みツールを使ってください。

次へ：[🔋 バッテリーを取り付ける](#battery)、うまくいかなかったときは[🩺 困ったときは](#troubleshooting)。

<a id="kiss"></a>

## 🌐 KISS Signer

**Chrome** か **Brave** が必要です。Safari と Firefox では書き込めません。

1. USB-C ケーブルをボードの **UART** と書かれたポートに差し込み、反対側をパソコンにつなぎます。

   🐧 **Linux の場合**は、先に下の [Linux の手順](#linux-kiss)を行ってください。

2. [KISS Signer のインストールページ](https://diybitcoin.github.io/kiss-signer/)を開きます。
3. **Which board is it?** で **Waveshare 3.5in** を選びます。
4. **This erases the device. I'm starting with keys that hold nothing.** のチェックボックスにチェックを入れます。
5. **Connect and install** をクリックします。小さなウィンドウが開いたら自分のボードを選び（🍎 名前に `usbmodem`、🐧 `ttyACM` が入っています）、**接続** をクリックします。
6. 次のウィンドウで **Install KISS Signer (Waveshare 3.5in)** をクリックします。**Erase device** にチェックを入れて **Next**、続けて **Install** をクリックします。ケーブルを抜かずに待ちます。

   ✅ **Installation complete!** と表示されます。

7. ボードを抜き、3秒待ってから、もう一度差し込みます。

   ✅ ボードが、KISS Signer が最初に表示するゲーム **FRUIT ISLAND** で起動します。🎉

8. 画面が黒いままですか？ボードに新しい **v3.x** チップが入っていて、このリリースは v1.x 用です。[テスト用インストーラー](https://diybitcoin.github.io/kiss-signer/test/)からもう一度書き込んでください：**1. Your board** でボードを選び、**Connect and install** をクリックします。チップを読み取って、合うビルドを選びます。

   ⚠️ テスト用インストーラーにあるのは署名なしのテスト版で、実際の v3.x ボードではまだ試していません。

<details id="linux-kiss">
<summary><b>🐧 Linux の手順：ブラウザがボードと通信できるようにする</b></summary>

1. **ターミナル**を開きます：**Ctrl + Alt + T** を押します。
2. 次を実行します：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 パソコンのパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押してください。

3. **パソコンからいったんログアウトして、もう一度ログイン**してから、上の手順2に戻ります。

</details>

次へ：[🔋 バッテリーを取り付ける](#battery)、うまくいかなかったときは[🩺 困ったときは](#troubleshooting)。

<a id="seedsigner"></a>

## 💻 SeedSigner 1：パソコンの準備

このボード用の SeedSigner は、Keith（kdmukai）による初期のバージョンです。チップのメーカーが作ったツール `esptool` を使い、**ターミナル**にコマンドを入力して書き込みます。下のコマンドにはそれぞれ**コピー**ボタンがあります：コピーしてターミナルに貼り付け、**Enter** を押してください。

<details>
<summary><b>🍎 Mac の場合</b></summary>

1. **ターミナル**を開きます：**Cmd + Space** を押し、`Terminal` と入力して **Enter** を押します。

2. ターミナル用の Mac アプリインストーラー **Homebrew** をインストールします。すでにある場合は飛ばしてください（`brew --version` でバージョンが表示されます）。

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac のパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押してください。

   ⚠️ 終わると、2〜3個のコマンドが書かれた **Next steps** が表示されます。それもコピーして実行してください。そうしないと Mac が `brew` を見つけられません。

3. esptool をインストールします：

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Linux の場合</b></summary>

1. **ターミナル**を開きます：**Ctrl + Alt + T** を押します。

2. 自分のユーザーがボードと通信できるようにします：

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 パソコンのパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押してください。

3. `esptool` と `unzip` をインストールします。1行ずつ実行してください：

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

4. **パソコンからいったんログアウトして、もう一度ログイン**してから、ターミナルをもう一度開きます。

</details>

✅ esptool が動くか確認します：

```bash
esptool version
```

**5.3 以上**と表示される必要があります。古いバージョンでは新しいチップに書き込めません：🍎 `brew upgrade esptool`、🐧 `pipx upgrade esptool`。

次へ：[SeedSigner 2：ボードをつないでチップを確認する](#ss-chip)。

<a id="ss-chip"></a>

## 💻 SeedSigner 2：ボードをつないでチップを確認する

このボードには2種類のチップのどちらかが入っています。古いボードは **v1.x**、新しいボードは **v3.x** で、それぞれ専用の SeedSigner ビルドが必要です。このページで、自分のチップがどちらかがわかります。

1. USB-C ケーブルをボードの **UART** と書かれたポートに差し込み、反対側をパソコンにつなぎます。

2. 自分のパソコンに合う行を実行します：

   🍎 Mac：

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux：

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. `ESP32-P4 (revision ...)` と書かれた行を探します：

   - `revision v1.3` など **v1** なら：**v1.x** チップです。
   - `revision v3.0`、`v3.2` など **v3** なら：**v3.x** チップです。

   メモしておいてください：次のページで使います。

次へ：[SeedSigner 3：自分のチップ用のビルドをダウンロード](#ss-download)。

<a id="ss-download"></a>

## 💻 SeedSigner 3：自分のチップ用のビルドをダウンロード

SeedSigner 2 で確認した**自分のチップ用のビルドだけ**を使ってください。もう一方は起動しません。

<details open>
<summary><b>1️⃣ チップが v1.x の場合</b></summary>

クリックして
[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
を**ダウンロード**フォルダに保存します。

スワイプでボタンが押されるのではなく、メニューがスクロールするようにする**タッチ修正**が入っています。この修正はまだ[オープン中のプルリクエスト](https://github.com/kdmukai/esp-board-common/pull/21)で、SeedSigner には正式に入っていません。v1.3 のボードで試しました。

zip の SHA256：
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ チップが v3.x の場合</b></summary>

Keith のビルドは GitHub で作られていて、ダウンロードには無料の GitHub アカウントが必要です。

1. [github.com/login](https://github.com/login) でログインするか、先にアカウントを作ります。
2. [SeedSigner の最新ビルド](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess)を開きます。
3. リストの**いちばん上**のビルドをクリックします。それが最新です。
4. **Artifacts** までスクロールし、**flashable-dist-waveshare-esp32-p4-35** をクリックして**ダウンロード**フォルダに保存します。

   ⚠️ 名前が **35** で終わっていることを必ず確認してください。`43` のものは4.3インチのボード用で、Keith の Releases ページにあるファイルも4.3インチ用です。どちらもこのボードでは起動しません。

</details>

次へ：[SeedSigner 4：展開して書き込む](#ss-flash)。

<a id="ss-flash"></a>

## 💻 SeedSigner 4：展開して書き込む

1. ダウンロードフォルダに移動します：

   ```bash
   cd ~/Downloads
   ```

2. **自分の**チップに合う2行で、展開してフォルダに入ります：

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

   💡 Mac では Safari がすでに展開していることがあります。`unzip` がファイルを見つけられないと言ったら、その行は飛ばして `cd` の行だけを実行してください。

3. 自分のパソコンに合う行で書き込みます：

   🍎 Mac：

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux：

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   4分ほどかかるので、ボードを抜かないでください。最初に出るいくつかの `Deprecated` の警告は問題ありません。

   ✅ 終わると、ボードが再起動して SeedSigner が立ち上がります。🎉

4. テスト用データで試してみましょう：パソコンで [testqrs.com](https://testqrs.com) を開き、ボードのカメラをその QR コードに向けます。そのシードワードは公開されているので、練習だけに使ってください。

次へ：[🔋 バッテリーを取り付ける](#battery)、うまくいかなかったときは[🩺 困ったときは](#troubleshooting)。

<a id="battery"></a>

## 🔋 バッテリーを取り付ける

ボードには小さなバッテリーと小さなドライバーが付いているので、ケーブルなしでも使えます。

1. ケースの裏にある4つの小さなカバーを、爪で外します。その下にネジがあります。
2. ドライバーで4本のネジを外し、ケースの裏ぶたを持ち上げます。
3. バッテリーのプラグを、ボードの右下にあるバッテリー用ソケットに差し込みます。向きは一方向にしか入らないので、入らなければ裏返してください。無理に押し込まないでください。
4. ✅ 電源ボタンの横に緑のランプがつくはずです。
5. ケースの裏ぶたを戻してネジを締めます。バッテリーを入れたままでも、ケースは普通に閉まります。

![ケースを開けてバッテリーをつないだ Waveshare 3.5インチのボード](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 困ったときは

| 表示される内容 | 対処法 |
|---|---|
| ブラウザのウィンドウに選べるボードがない | **Chrome** か **Brave**、**UART** ポート、データ通信ができるケーブルを使ってください。🐧 [Kern](#linux-kern) か [KISS Signer](#linux-kiss) のページにある Linux の手順を行ってください。 |
| `port is busy` または `could not open port` | Web 書き込みツールのタブと、ボードを使っているほかのターミナルを閉じてから、もう一度試してください。 |
| 🍎 `no matches found: /dev/cu.usbmodem*` または 🐧 `No such file or directory` | パソコンがボードを認識していません：**UART** ポートと、データ通信ができるとわかっているケーブルを使ってください。 |
| 🐧 `Permission denied`、またはブラウザがボードを開けない | ログアウトしてもう一度ログインするところまで含めて、Linux の手順を行ってください。 |
| `Failed to connect` | ボードを使っているものを閉じ、ボードを抜いて差し直してから、もう一度試してください。 |
| 🍎 `brew: command not found` | Homebrew のインストールが終わったときに表示された **Next steps** のコマンドを実行してください。 |
| 🐧 `esptool: command not found` | ログアウトしてもう一度ログインし、新しいターミナルを開いてください。 |
| `requires chip revision in range` | ビルドがチップに合っていません：[SeedSigner 2](#ss-chip) をやり直して、もう一方のビルドをダウンロードしてください。 |
| `flash_args` が見つからない | 展開したフォルダの中にいません：[SeedSigner 4](#ss-flash) の手順2をやり直してください。 |
| 書き込み後、画面に何も表示されない | 抜いて3秒待ってから差し直してください。ビルドがチップに合っているか確認してください。 |
| `esptool` がチップに書き込めない | 5.3 以上が必要です：🍎 `brew upgrade esptool`、🐧 `pipx upgrade esptool`。 |

## 🙏 謝辞

- [Kern](https://github.com/odudex/Kern)：odudex
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder)：kdmukai
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer)：DIYbitcoin

# 💳 javacard への書き込み：Satochip、Satodime、SeedKeeper、Keycard

javacard には **セキュアエレメント** があります。カードが誰かの手に渡っても、ビットコインの鍵などの秘密情報を安全に守るために作られたチップです。カードには **アプレット** と呼ばれる小さなアプリを入れられます。このガイドでは、オープンソースのビットコイン用アプレット4つのうち1つを、空の J3H145 javacard にインストールします。

- **Satochip:** Sparrow Wallet と一緒に使う DIY のビットコインウォレット
- **Satodime:** 無記名カード。現金のように、カードを持っている人がそこに入っているビットコインの持ち主になります
- **SeedKeeper:** シードワードとパスフレーズ、パスワード、ウォレットディスクリプタなどの秘密情報を入れておく、PIN で守られた暗号化バックアップ
- **Keycard:** Sparrow Wallet と一緒に使う、もう1つの DIY ビットコインウォレット

アプレットは [Satochip](https://satochip.io) と
[Keycard](https://keycard.tech) のもので、このガイドは
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) のガイドに沿っています。

![パソコン、リーダー、カード、4つのアプレット](images/00-overview.svg)

## 🧰 必要なもの

- 💳 **J3H145** の javacard。買う場合は、このガイドで使ったものがこちらです：[Alibaba の J3H145](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 **USB スマートカードリーダー**：パソコンにつないで、カードをチップ側から差し込む小さな箱です。カードをかざして使う NFC リーダーの **ACS ACR122U** は使わないでください。[Satochip-DIY のガイド](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)では、アプレットをインストールするときは動作が不安定で、カードを壊すおそれがあると注意しています。アプレットをインストールした後にカードを使うだけなら問題ありません。
- 🛡️ [ShieldSigner](https://github.com/3rdIteration/seedsigner) を持っていますか？パソコンとリーダーの代わりに、ShieldSigner でもこれらのアプレットをカードにインストールできます。
- 💻 **Mac**、**Linux**（Debian、Ubuntu、Raspberry Pi OS）または **Windows** のパソコン
- 🌐 ダウンロード用のインターネット接続

## 🛠️ ステップ1：パソコンの準備（最初の1回だけ）

**Java** と、アプレットをカードに入れる `gp` コマンドである **GlobalPlatformPro** をインストールします。

<details>
<summary><b>🍎 Mac の場合</b></summary>

1. **ターミナル** を開きます：**Cmd + Space** を押し、`Terminal` と入力して **Enter** を押します。

2. ターミナルで使う Mac 用のアプリのインストーラー、**Homebrew** をインストールします。すでに入っている場合は、この手順を飛ばします（`brew --version` でバージョンが表示されます）。

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac のパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押します。

   ⚠️ 終わると、**Next steps** として2つか3つのコマンドが表示されます。それらもコピーして実行してください。実行しないと Mac が `brew` を見つけられません。

3. Java をインストールします。

   ```bash
   brew install --cask temurin
   ```

4. GlobalPlatformPro をインストールします。

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 Linux の場合</b></summary>

1. **ターミナル** を開きます：**Ctrl + Alt + T** を押すか、アプリの中から **ターミナル** を探します。

2. Java、git、スマートカードのサービス（`pcscd`）をインストールします。

   ```bash
   sudo apt update
   ```

   🔒 パソコンのパスワードを聞かれます。入力中は何も表示されませんが、それで正常です。そのまま入力して **Enter** を押します。

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) のファイルをダウンロードします。GlobalPlatformPro も `gp.jar` として入っています。

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. `gp` という短い名前を付けます。これで、このガイドのコマンドがすべて書いてあるとおりに動きます。

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. 新しい名前が使えるように、**ターミナルを閉じて新しく開きます**。

</details>

<details>
<summary><b>🪟 Windows の場合</b></summary>

⚠️ Windows では試していません。

1. Java をインストールします：<https://adoptium.net> から **Temurin** をダウンロードし、初期設定のままインストールします。

2. [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)
   を [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) から
   **ダウンロード** フォルダに保存します。

3. **コマンドプロンプト** を開きます：**Windows** キーを押し、`cmd` と入力して **Enter** を押します。管理者として実行する必要はありません。

4. ダウンロードフォルダに移動します。

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. このガイドで `gp` と書いてあるところは、代わりに `gp.exe` と入力します（例：`gp.exe --info`）。`cd ~/Downloads` と書いてあるところは、手順4の行を使います。

</details>

✅ うまくいったか確認します。

```bash
gp --version
```

`#` で始まる行がいくつか表示され、`command not found` が出なければ成功です。

💡 Java は `WARNING:` で始まる行を、`gp` を実行するたびにいくつか表示します。これは正常なので、無視してかまいません。

## 🔌 ステップ2：リーダーがカードを認識しているか確認

リーダーをつなぎ、カードを（チップ側から）差し込んで、次を実行します。

```bash
gp --info
```

✅ `CPLC` の行と `Global Platform card` が表示されれば、リーダーとカードは通信できています。

![gp --info の出力](images/01-gp-info.svg)

次に、カードにすでに何が入っているかを見ます。

```bash
gp --list
```

空のカードでは、工場出荷時から入っているものだけが表示されます：カードマネージャー（`ISD`）と1つのパッケージ（`A0000001515350`）です。これらには触らないでください。

![空のカードでの gp --list](images/02-gp-list-blank.svg)

### 🛑 「Card cryptogram invalid」と出たら中止

![Card cryptogram invalid の警告](images/07-gp-cryptogram-stop.svg)

⚠️ これは、カードがあなたの持っていない鍵でロックされているという意味です。**コマンドをもう一度実行しないでください**：何度か失敗すると、カード自体が永久にロックされます。カードの販売者に鍵を問い合わせてください。

## 📥 ステップ3：アプレットのファイルをダウンロード

使いたいアプレットのファイルを **ダウンロード** フォルダにダウンロードします。各ページで、**Assets** の下にあるファイル名をクリックしてダウンロードします。

| アプレット | ダウンロードするファイル | 設定に使うもの |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 なぜ最新版ではなく、このバージョンなのですか？</b></summary>

- **Satochip は v0.16 ではなく v0.15:** v0.16（2026年10月）でカードが taproot の鍵を調整する方法が変わりましたが、Sparrow 2.5.5 はまだ古い形式で送るため、Sparrow の taproot ウォレットは v0.16 のカードで署名できません。Native SegWit はどちらでも動きます。
- **Satodime v0.1-0.2** が最新の安定版です。v0.2 のビルドはまだベータ版扱いです。
- **Keycard は 4.0 ではなく 3.2:** Keycard 4.0（2026年9月）でカードとパソコンが暗号化された接続を確立する方法が変わりましたが、Sparrow 2.5.5 はまだ古い方法を使っています。

Sparrow が対応したら、新しいバージョンを使ってください。

</details>

<details>
<summary><b>🔍 任意：ファイルが改ざんされていないか確認</b></summary>

ダウンロードフォルダに移動します。

```bash
cd ~/Downloads
```

🍎 Mac:

```bash
shasum -a 256 SatoChip-v0.15-0.1.cap
```

🐧 Linux:

```bash
sha256sum SatoChip-v0.15-0.1.cap
```

🪟 Windows:

```bat
certutil -hashfile SatoChip-v0.15-0.1.cap SHA256
```

表示される長いコードは、リリースページのものと一致する必要があります。

- `SatoChip-v0.15-0.1.cap`:
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`:
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ ステップ4：アプレットをカードに入れる

🔄 カードのアプレットを別のものに入れ替えたいですか？先に古いものを削除してから、新しいものをインストールします：下の [別のアプレットに切り替える](#switching) を見てください。

ファイルがあるダウンロードフォルダに移動します。

```bash
cd ~/Downloads
```

次に、**自分のアプレットの行だけ** を実行します。

Satochip:

```bash
gp --install SatoChip-v0.15-0.1.cap
```

Satodime:

```bash
gp --install Satodime-0.1-0.2.cap
```

SeedKeeper:

```bash
gp --install SeedKeeper-v0.2-0.1.cap
```

Keycard（追加の設定で、Sparrow が探すウォレットになります）：

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ 最後に `loaded:` の行が表示されます。

![gp --install の出力](images/03-gp-install-satochip.svg)

![Keycard の gp --install](images/09-gp-install-keycard.svg)

✅ もう一度 `gp --list` を実行します。`APP` で始まり `SELECTABLE` で終わる新しい行があれば、アプレットはインストールされ、使える状態です。

![Satochip をインストールした後の gp --list](images/04-gp-list-satochip.svg)

次に、自分のアプレットの設定ステップに進みます。

- Satochip: [ステップ5a](#step-5a)
- SeedKeeper または Satodime: [ステップ5b](#step-5b)
- Keycard: [ステップ5c](#step-5c)

<a id="step-5a"></a>

## ステップ5a：Sparrow で Satochip を設定

👛 パソコン用の Sparrow Wallet を
<https://sparrowwallet.com/download/> から入手します。

PIN の設定も、12個のシードワードをカードに入れるのも、Sparrow から直接行います。Satochip のアプリは必要ありません。カードはずっとリーダーに入れたままにしておきます。

1. Sparrow で新しいウォレットを作り、名前を付けます。**Keystores** の下で **Connected Hardware Wallet** を選び、**Scan...** を押します。

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow が **Satochip** を見つけます。**Import Keystore** を押します。

   ![Sparrow が Satochip を見つけたところ](images/sparrow-2-found.webp)

3. Sparrow に **Choose a PIN code** と聞かれます。PIN を入力します。

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow に **Card not initialized** と表示されます。欄に同じ PIN をもう一度入力し、**Initialize** を押します。

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. **Satochip Seed Words** の画面が開きます。新しい12個のシードワードを作るには **Generate New** を押し、すでに持っている12個のシードワードを使うにはそれを入力します。
   ✍️ **12個のシードワードを紙に書き留めて** から、**OK** を押します。Sparrow はパスフレーズなしで、それをカードに入れます。

   💡 Sparrow はこの12個のシードワードをパソコン上で作ります。本当の貯蓄に使うなら、信頼できるパソコンを使ってください。

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**。**OK** を押します。

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. Satochip の行に戻り、もう一度 **Import Keystore** を押します。

   ![もう一度 Import Keystore](images/sparrow-7-import-keystore.webp)

8. 🎉 ウォレットにあなたの Satochip と、そのフィンガープリント（あなたの鍵を表す短いコード）が表示されます。**Apply** を押してウォレットを保存します。

   ![Sparrow で完成したウォレット](images/sparrow-8-wallet.webp)

🔢 Sparrow で設定したカードは、**PIN を5回まで** 試せます。後からキーストアの **Change Pin** で PIN を変えることもできます。

<a id="step-5b"></a>

## ステップ5b：Sato-Tools で SeedKeeper または Satodime を設定

🧰 パソコン用の Sato-Tools を
<https://github.com/Toporin/Satochip-Utils/releases> から入手します（このガイドでは
0.4.0-beta を使いました）。

カードをリーダーに入れた状態で Sato-Tools を開き、**Let's go!** を押します。

![Sato-Tools の最初の画面](images/satotools-0-welcome.webp)

💡 **書き込んだばかりのカードを Sato-Tools が認識しない場合は、アプリを終了してもう一度開きます。**

### SeedKeeper

1. Sato-Tools に **Your SeedKeeper is connected** と表示されます。**Setup my card** を押します。

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. **New PIN code** に PIN（4文字から16文字）を入力し、**Confirm PIN code** にもう一度入力して、**Save PIN** を押します。

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** **Ok** を押します。

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 メニューに **My secrets**、**Generate secret**、**Import secret**、**Backup card**、**Card logs** が表示されます。これで SeedKeeper に、シードワード、パスフレーズ、パスワード、ウォレットディスクリプタを保存できます。

   ![準備ができた SeedKeeper](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools に **Your Satodime is connected** と表示され、3つの金庫があります：**Vault #0**、**Vault #1**、**Vault #2**。

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview** では、3つの金庫すべてが **uninitialized** と表示されます：まだ何も封印されていません。

   ![自分の金庫](images/satotools-satodime-2-vaults.png)

3. **About** には、アプレットのバージョンと **Card has no owner** が表示されます。**Take ownership** を使うと、このパソコンがカードの持ち主になります。金庫を封印する前に必要です。

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 **Take ownership** を押します。About に **You are the owner** と表示され、ボタンが **Transfer ownership** に変わります。これはカードを他の人に渡すときに使います。

💡 金庫を **封印する** と、その金庫用のビットコインアドレスが作られ、秘密鍵はカードの中に隠されます。**封印を解く** と秘密鍵が表示され、ビットコインを動かせるようになります。Satodime を支払いとして受け取る前に、金庫がまだ封印されていることを確認してください。

### 📱 スマートフォンで

SeedKeeper と Satodime にはスマートフォンのアプリもあります。NFC 付きのスマートフォンの背面にカードをかざすと、スマートフォンで使えます。

### ❓ なぜ「Genuine: NO」と表示されるのですか？

Satochip から買ったカードには、工場で Satochip の証明書が入れられていて、**Genuine** はそれを確認します。自分で書き込んだ DIY カードにはその証明書がないので、いつも **Genuine: [NO]** と表示されます。アプレットはまったく同じように動きます。

<a id="step-5c"></a>

## ステップ5c：Sparrow で Keycard を設定

👛 まだ持っていなければ、パソコン用の Sparrow Wallet を
<https://sparrowwallet.com/download/> から入手します。

Keycard の設定は Satochip とまったく同じですが、違いが2つあります：PIN は **ちょうど6桁** で、カードは **間違った PIN を3回まで** しか許しません。

1. 新しいウォレットを作り、**Connected Hardware Wallet** を選んで **Scan...** を押します。Sparrow が **Keycard** を見つけます。**Import Keystore** を押します。

   ![Sparrow が Keycard を見つけたところ](images/keycard-1-found.webp)

2. **ちょうど6桁** の PIN を選びます。それより短くても長くても **PIN length must be between 6 and 6 characters** と表示されます。

   ![PIN は6桁でなければなりません](images/keycard-2-pin-error.webp)

3. 同じ PIN をもう一度入力し、**Initialize** を押します。Satochip のときと同じシードワードの画面が開きます（[ステップ5a](#step-5a) の5番）：**Generate New** を押すか自分のシードワードを入力し、✍️ **12個のシードワードを書き留めて** から **OK** を押します。
   ✅ **Card Initialized**：**OK** を押します。

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 もう一度 **Import Keystore** を押します。ウォレットにあなたの Keycard と、そのフィンガープリントが表示されます。**Apply** を押してウォレットを保存します。

   ![Sparrow で完成した Keycard のウォレット](images/keycard-4-wallet.webp)

⚠️ **復元する手段は12個のシードワードだけです。** PIN を3回間違えると Keycard はロックされ、Sparrow にはロックを解除する方法がありません。そうなったら、Keycard を削除してもう一度インストールし、Sparrow で12個のシードワードを入力し直します。

<a id="switching"></a>

## 🔄 別のアプレットに切り替える

⚠️ **アプレットを削除すると、その中に保存したものはすべて消えます**。Satochip や Keycard のシード、SeedKeeper の秘密情報も消えます。12個のシードワードを書き留めてあるか、練習用のウォレットの場合だけ行ってください。カード自体が壊れることはなく、何度でも書き込み直せます。

![アンインストールで消え、インストールで最初から](images/08-swap.svg)

1. アプレットのファイルがあるダウンロードフォルダに移動します。

   ```bash
   cd ~/Downloads
   ```

2. カードに何が入っているかわからない場合は、`gp --list` を実行します：`APP` の行に表示されます。

3. 今カードに入っているアプレットを削除します。**そのアプレットの行だけ** を実行します。

   Satochip:

   ```bash
   gp --uninstall SatoChip-v0.15-0.1.cap
   ```

   Satodime:

   ```bash
   gp --uninstall Satodime-0.1-0.2.cap
   ```

   SeedKeeper:

   ```bash
   gp --uninstall SeedKeeper-v0.2-0.1.cap
   ```

   Keycard:

   ```bash
   gp --delete A0000008040001 -f
   ```

4. ✅ もう一度 `gp --list` を実行します。カードマネージャー（`ISD`）と工場出荷時のパッケージ（`A0000001515350`）だけが残っているはずです。

5. 新しいアプレットをインストールし（[ステップ4](#step-4)）、設定します（[ステップ5a](#step-5a)、[5b](#step-5b) または [5c](#step-5c)）。

たとえば：

![Satochip を Satodime に入れ替える](images/05-gp-swap-to-satodime.svg)

![Satodime を SeedKeeper に入れ替える](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 困ったときは

| 表示されたもの | 対処法 |
|---|---|
| 🍎 `brew: command not found` | Homebrew がインストールの最後に表示した **Next steps** のコマンドを実行します。 |
| 🐧 `gp: command not found` | ターミナルを閉じて、新しく開きます。 |
| `No such file` または `does not exist` | 先に `cd ~/Downloads` を実行し、ファイルがダウンロードフォルダにあるか確認します。 |
| `Card cryptogram invalid` | カードは別の鍵でロックされています。中止して、やり直さないでください。 |
| `LOAD failed: 0x6438` | このアプレットは、カードの Java Card より新しい Java Card 用に作られています。 |
| `INSTALL ... failed: 0x6444` または `0x6F00` | アプレットが必要とする機能がカードにありません。 |
| すでにアプレットが入っているカードで `gp --install` が失敗する | `gp --list` を実行します。`APP` の行があれば、先にそのアプレットを削除します（[別のアプレットに切り替える](#switching) を参照）。 |
| Sparrow の **Scan...** で何も見つからない | Sato-Tools やほかのカード用アプリを終了し、カードを抜いて差し直してから、もう一度 **Scan...** を押します。 |
| PIN を忘れた、または PIN を何度も間違えてカードがブロックされた | アプレットを削除してもう一度インストールし（カードの中身は消えます）、12個のシードワードで復元します。 |
| Sato-Tools がカードを認識しない | Sato-Tools を終了して、もう一度開きます。 |
| `gp` がリーダーを見つけられない、またはカードが入っていないと表示される | カードをチップ側から差し込みます。それでもカードがないと表示される場合は、リーダーが固まっています：リーダーをパソコンから抜き、カードを入れたまま差し直して、もう一度試します。🐧 そのあと `sudo systemctl restart pcscd` を実行します。 |

## 🚀 さらに進む

- 🎥 [動画：このカードに7種類のアプレットを書き込む](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 自分の鍵でカードをロックして、ほかの人がインストール内容を変えられないようにします：
  [Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ アプレットを自分でビルドします。上のファイルは他の人がビルドしたものなので、公開されているソースコードと一致していると信じて使うことになります。自分でソースコードからビルドすれば、カードに入るのは誰でも読めるコードそのものです：
  [Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 謝辞

- 🙌 [Toporin](https://github.com/Toporin)（[Satochip](https://satochip.io) の作者）：Satochip、Satodime、SeedKeeper のアプレットと Sato-Tools。
- 🛠️ 3rdIteration の [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)：このガイドが沿っている DIY ガイドで、動作確認済みのカードとリーダーの一覧と `gp.jar` があります。
- [Keycard](https://github.com/keycard-tech/status-keycard)：Keycard アプレット。
- 💳 Martin Paljak の [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)：アプレットをカードに入れるのに使います。

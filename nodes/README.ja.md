# 🖥️ 自分のビットコインノードを動かす

自分のノードはビットコインのルールをすべて自ら確認するので、他人のサーバーを信頼する必要がありません。これらのガイドを使えば、自宅でノードを立ち上げられます。

選べるガイドは2つあります。

- 🛠️ [ODROID-M1 でノードを組み立てる](#odroid)：小さな ARM ボードに Debian 12、Bitcoin Core、Tor、electrs を順番に入れていきます。[Satoshi Spritz](https://satoshispritz.it/) のビットコインミートアップでのノードの発表がもとになっています（[クレジット](#credits)を参照）。
- 🧱 [MiniBolt](#minibolt)：Intel または AMD のプロセッサを積んだミニ PC で、ノードを組み立てます。

まず、どちらにも共通の概要があります：なぜノードを動かすのか、ノードのソフトウェア、ハードウェア。
## 🤔 なぜビットコインノードを動かすのか

- ビットコインのブロックチェーン全体を自分で検証する
- 第三者なしでウォレットの残高を確認する
- 自分の取引を自分でブロードキャストする
- 最大限のプライバシー、外部サービスを信頼しなくていい

## 📦 ノード用ソフトウェア

完成品のソリューション：

| ソフトウェア | 価格 | 特徴 |
|---|---|---|
| Umbrel | ソフトウェアは無料、ハードウェアは 100〜300 € | いちばん使いやすい、アプリマーケット、Apple 風 |
| MyNode | 無料 / 79 € プレミアム / 組み立て済み 399〜599 € | ノード + Lightning + Electrum + ブロックエクスプローラー |
| Start9 | 挿すだけで使える 699〜899 € / DIY ならソフトウェアは無料 | オープンソース、Tor 内蔵、テレメトリーなし |

共通の要件：

- ブロックチェーン：約 670 GB 以上（2025年）、数日ごとに約 1 GB 増える
- 最低 4 GB RAM、1 TB SSD（2 TB を推奨）
- 最初の同期：2〜7日

**完成品：** すぐに設定できてグラフィカルな画面があるが、Docker、スクリプト、セキュリティチェックが見えないところに隠れ、追加ソフトウェアは把握しにくい。

**DIY（手動）：** ソフトウェアが少ないほど攻撃面が小さく、すべての部分を自分で管理でき、保守のしかたも身につく。

## 🔧 ハードウェアの選び方

最低限：

- 最低 2 GB RAM（4 GB を推奨）
- 最低 1 TB のディスク（2 TB を推奨）
- マルチコア CPU（最近の CPU ならどれでも）

選択肢：

- **古い PC：** 費用ゼロ。ノート PC ならバッテリーを外す
- **Raspberry Pi：** 高価で、USB ディスクは安定性が低い
- **ODROID-M1：** ARM、SSD 用の内部スロットあり
- **シンクライアント（30〜100 €）：** Fujitsu Futro S920、HP t620

OS には、何年もサポートされる長期サポート版 Linux の **Debian 13** を使います。すべてのパッケージを更新し、リモートで保守するために `tor` と `ssh` を入れ、UPS（無停電電源装置）を推奨します。

<a id="minibolt"></a>

## 🧱 もうひとつの方法：MiniBolt

- [MiniBolt](https://minibolt.minibolt.info/)：2010年ごろ以降の Intel または AMD のプロセッサを積んだミニ PC で、ノードを組み立てます。

💬 Malaga 2140 の Telegram チャットで見つけました。

MiniBolt 自身の [ハードウェア要件](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)では、次のものが必要です。

- CPU：2010年以降の世代の Intel/AMD プロセッサ
- RAM：2 GB 以上（ビットコインのみ）、4 GB 以上を推奨（ビットコイン、Lightning、追加機能）
- ストレージ：2 TB 以上の SATA/NVMe SSD を推奨
- インストール用の 4 GB 以上の USB メモリまたは SD カード
- 最初の設定のときだけ使うモニターかテレビと、USB または PS2 のキーボード

<a id="odroid"></a>

# 🛠️ ODROID-M1 でノードを組み立てる

## 🧰 必要なもの

- ODROID-M1
- NVMe SSD（2 TB がおすすめ）
- ルーター
- イーサネットケーブル
- HDMI ケーブル付きのモニター
- USB マウスとキーボード

## 🔩 ステップ1：組み立てる

1. NVMe SSD を ODROID-M1 に取り付けます。
2. すべてのケーブルをつなぎます：マウスとキーボード、モニター、イーサネットケーブル。
3. 電源ケーブルをつないで ODROID-M1 の電源を入れます。

## 🐧 ステップ2：Debian 12 をダウンロードする

1. ODROID-M1 が起動したら、Petitboot で **Exit to shell** に移動し、**Enter** を押します。
2. `udhcpc` と入力して **Enter** を押します（接続の問題を直すのに役立つことがあります）。
3. `netboot_default` と入力して **Enter** を押します。

   ⌨️ Petitboot は、どのキーボードを使っていても米国配列になります。`_` を入力するには **Shift** を押しながら **0** の隣のキーを押します。

4. `exit` と入力して **Enter** を押します。
5. Petitboot の最初の画面に、使えるイメージの一覧が表示されます。**Debian 12 (Bookworm) Netboot installer** に移動して **Enter** を押します。

## 💿 ステップ3：Debian 12 をインストールする

1. **言語：** **English** を選びます。そのほうがわかりやすいです（翻訳がよくない場合があります）。
2. **場所：** 住んでいる国を選びます。これでタイムゾーンが決まります。最初の一覧になければ **other** を選び、大陸、国の順に選びます。
3. **ロケール：** Debian に聞かれたら、**United States** か **United Kingdom** を選んで英語のままにしてかまいません。
4. **キーボード：** つないだキーボードの配列を選びます。
5. **Hostname：** ホスト名を決めます。ドメインは空のままでかまいません。
6. **ミラーの国：** Debian をダウンロードする国。自分の国か、一覧でいちばん近い国を選びます。
7. **ミラー：** 一覧に `deb.debian.org` があればそれを選びます。近くの速いサーバーにつないでくれます。なければ一覧のどのミラーでもかまいません。
8. **プロキシ：** プロキシがなければ空のまま **Enter** を押します。
9. **root パスワード：** スーパーユーザーのパスワードを設定して **Enter** を押します。
10. **ユーザー：** 一般（管理者ではない）ユーザーの名前を決め（フルネームは入れなくてかまいません）、パスワードを設定します。
11. **ディスク：** **Guided: use entire disk** を選び、取り付けた NVMe ディスクを選びます。
12. **パーティション：** すべてを1つのパーティションにします：**All files in one partition (recommended for new users)**。
13. Debian は boot、root、swap のパーティションがある表を作りますが、swap は要りません。swap の行に移動して **Enter** を押し、**Delete the partition** を選んで **Enter** を押します。
14. **Finish partitioning and write changes to disk** を選びます。
15. Debian が swap パーティションがないと知らせてきます。パーティションのメニューに戻らないよう **No** を選びます。
16. 2つのパーティション（boot と root）を **Yes** で確定し、インストールを始めます。
17. **Popularity contest**（使っている Debian パッケージの匿名統計）：**No** を選びます。
18. **ソフトウェア：** グラフィカルなデスクトップは要らないので、**SSH server** と **standard system utilities** だけにチェックを残して **Enter** を押します。
19. **インストール完了：** **Continue** を選んで再起動します。

## 🔁 ステップ4：Debian を自動で起動する

1. Petitboot で **System configuration** に移動して **Enter** を押します。
2. **Autoboot** は **Enabled** にします。
3. **Boot order** には ODROID-M1 に取り付けたディスクか **Any device** が必要です。ディスクを追加するには **Add Device** に移動し、ディスクを選んで **OK** を押します。または **Clear & Boot Any** で **Any device** にします。
4. **Timeout** は電源を入れてから Debian が起動するまでの秒数です（初期値は10秒、変える必要はありません）。
5. **OK** に移動して **Enter** を押し、ODROID-M1 を再起動します。再起動すると左下のカウントダウンのあと Debian 12 が起動します。

<details>
<summary><b>📵 インターネットにつながらない？</b></summary>

1. スーパーユーザー `root` でログインし、`ping 8.8.8.8` を実行します。**Network is unreachable** と表示されたら、機器はつながっていません。
2. `dhclient -v` を実行して、DHCP でルーターに IP アドレスを求めます。ネットワークインターフェースの名前（例：`eth0`）も表示されます。
3. もう一度 `ping 8.8.8.8` を実行します。今度はつながりますが、次の再起動でまた同じ問題が起きます。
4. 起動のたびに `eth0` が DHCP を使うよう、`nano /etc/network/interfaces` を実行して次の行を追加または変更します：

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   **Ctrl + X** で終了し、**y** で保存、**Enter** で上書きします。

5. 再起動して、もう一度接続を確認します。これで ODROID-M1 は再起動のたびに自分でつながります。

</details>

## 💻 ステップ5：SSH で接続する

1. 自分のコンピューターでターミナルを開き、インストール時に作ったユーザーと、ルーターが ODROID-M1 に割り当てた IP アドレスで SSH 接続します：

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian はスーパーユーザー（`root`）での SSH ログインを許可していません。

2. ログインしたら `su` と入力し、スーパーユーザーのパスワードを入れて権限を得ます。

## 📥 ステップ6：リポジトリとシステムを更新する

1. リポジトリの一覧を開きます：

   ```bash
   nano /etc/apt/sources.list
   ```

2. すべての行を消して、次の行を入れます：

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

   **Ctrl + X** を押し、**y** で保存して **Enter** でファイルを上書きします。

3. パッケージの一覧とインストール済みのパッケージを更新します：

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` が、上のリポジトリの `deb.debian.org` につながるはずです。

## 🧅 ステップ7：Tor を入れて、Tor 経由で SSH に入る

1. Tor をインストールします：

   ```bash
   apt install tor
   ```

2. `nano /etc/tor/torrc` で設定を開き、いちばん下に追加します：

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

3. Tor を再起動して状態を確認します（ODROID-M1 が起動するたびに起動します）：

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. 自分の onion アドレスを表示します：

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. これで世界中どこからでも ODROID-M1 に接続できます：

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 ステップ8：Bitcoin Core をダウンロードする

Bitcoin Core の最新版、ハッシュ、署名は <https://bitcoincore.org/en/download/> にあります。

1. インストール用パッケージ、チェックサム、署名をダウンロードします：

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. ダウンロードしたファイルのチェックサムが `SHA256SUMS` のものと一致するか確認します：

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ ファイル名のあとに **OK** と表示されるはずです：`bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Bitcoin Core 開発者の公開鍵は `bitcoin-core/guix.sigs` リポジトリにあります。まだ git がないはずなので、先にインストールします：

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. 開発者の公開鍵を読み込みます：

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. `SHA256SUMS.asc` の署名を確認します：

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ 開発者ごとに `gpg: Good signature from "...."` と表示されれば、署名は有効です。警告は無視してかまいません。

🔒 なぜ大事か：

- **完全性：** チェックサムで、ダウンロード中にファイルが壊れたり書き換えられたりしていないことを確かめます。
- **真正性：** 署名で、ファイルが本当に Bitcoin Core の開発者から来たもので、悪意のある出所からではないことを確かめます。

## ⚙️ ステップ9：Bitcoin Core をインストールして同期を始める

1. パッケージを展開します：

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Bitcoin Core をインストールします：

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. 同期を始めます：

   ```bash
   bitcoind
   ```

4. 家庭向けの設定：`nano ~/.bitcoin/bitcoin.conf` で `bitcoin.conf` を開き、追加します：

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

| 設定 | 役割 |
|---|---|
| `daemon=1` | `bitcoind` をバックグラウンドのデーモンとして動かし、ターミナルを閉じても動き続けるようにする |
| `blocksonly=1` | ブロックだけをダウンロードし、mempool（未承認の取引）は受け取らない |
| `maxconnections=20` | 入出力の接続を最大20に制限する |
| `maxuploadtarget=500` | 1日のアップロードを最大 500 MB に制限する |
| `txindex=1` | 取引の完全なインデックスを保持する（ブロックチェーン全体を探索できる） |
| `blockfilterindex=1` | ブロックフィルターのインデックスを作って保持する（取引をより効率よく確認できる） |

💡 Bitcoin Core 26 から、`v2transport` がノード間の接続を暗号化します。すべての設定は [Bitcoin Core 設定ジェネレーター](https://jlopp.github.io/bitcoin-core-config-generator/)を参照してください。

<details>
<summary><b>🔁 systemd サービスとして動かす</b></summary>

`bitcoind` サービス、専用の `bitcoin` ユーザー、自動再起動、そして `~/.bitcoin/debug.log` のログ：

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

## 🧅 ステップ10：Bitcoin Core を Tor だけで使う

Bitcoin Core が Tor だけで接続し、Tor だけで到達できるように設定します。

1. `nano /etc/tor/torrc` で Tor の設定を開き、最後に追加します：

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

2. Tor を再起動します：

   ```bash
   systemctl restart tor
   ```

3. Bitcoin Core の onion アドレスを表示します：

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. `nano ~/.bitcoin/bitcoin.conf` で `bitcoin.conf` を開き、追加します：

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

| 設定 | 役割 |
|---|---|
| `proxy=127.0.0.1:9050` | Bitcoin Core に Tor のプロキシを使わせる |
| `listen=1` | ノードが入ってくる接続を受け付けるようにする |
| `bind=127.0.0.1:8333=onion` | 入ってくる接続を Tor 経由だけにする |
| `externalip=ADDRESS.onion` | `ADDRESS.onion` を上で得た onion アドレスに置き換える |
| `onlynet=onion` | 出ていく接続を Tor ネットワークだけにする |

5. Bitcoin Core を再起動します。止めてから、デーモン（バックグラウンドのプロセス）としてもう一度起動します：

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. ネットワークを確認します：

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ `networks` の下に `ipv4`、`ipv6`、`onion`、`i2p`、`cjdns` が表示されます。`reachable` が `true` なのは `onion` だけで、ほかはすべて `false` です。`localaddresses` の下には自分の onion アドレスだけが表示されます。

7. 自分が接続しているピア、または自分に接続しているピアを確認します：

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ `addr` 欄にピアの onion アドレスがあり、`network` は `onion` です。

⏳ Tor は遅いので、最初の同期にずっと長くかかります。

<details>
<summary><b>🔄 Bitcoin Core の onion アドレスを変える</b></summary>

1. onion アドレスのもとになる秘密鍵が入った `bitcoin-service` フォルダーを削除します：

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Tor を再起動すると、新しい公開鍵と秘密鍵でフォルダーを作り直します：

   ```bash
   systemctl restart tor
   ```

3. 新しい onion アドレスを確認します：

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Bitcoin Core を再起動します：

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 ステップ11：Rust をインストールする

electrs は Rust というプログラミング言語で書かれた Electrum サーバーなので、機器に Rust が必要です。

1. まず `curl` をインストールします：

   ```bash
   apt install curl
   ```

2. Rust をインストールします：

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   インストールの方法を聞かれたら、**1** と入力して **Enter** を押し、標準のインストールにします。

3. 終わるとシェルの再起動をすすめられます。いったんログアウトして入り直す（または `su` でもう一度スーパーユーザーになる）と、Rust のコマンドが使えます。

4. `clang`、`cmake`、`build-essential` をインストールします：

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 ステップ12：electrs をダウンロードする

1. <https://github.com/romanz/electrs> で electrs の最新版を確認します。ここでは 0.10.7 です：

   ```bash
   VERSION="0.10.7"
   ```

   （最新版がたとえば 0.10.9 なら、引用符の中の値を変えます。）

2. そのバージョンをダウンロードします：

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. 新しくできた `electrs` フォルダーに入り、開発者の鍵を読み込みます：

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. 開発者の署名を確認します：

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ `gpg: Good signature from "...."` と表示されれば、署名は有効です。

## 🏗️ ステップ13：electrs をビルドしてインストールする

1. `electrs` フォルダーの中のまま、ビルドを始めます。かなり時間がかかるので、気長に待ちましょう 😄

   ```bash
   cargo build --locked --release
   ```

2. electrs をインストールします：

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. バージョンを表示して、インストールできたか確認します：

   ```bash
   electrs --version
   ```

## 📝 ステップ14：electrs のための bitcoin.conf の設定

1. Bitcoin Core を止めます：

   ```bash
   bitcoin-cli stop
   ```

2. `nano ~/.bitcoin/bitcoin.conf` で `bitcoin.conf` を開き、追加します：

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

| 設定 | 役割 |
|---|---|
| `server=1` | Bitcoin Core を RPC サーバーとして動かす |
| `rpcuser=USER` | アプリが RPC でノードに接続するときに示すユーザー名（自分で決める） |
| `rpcpassword=PASSWORD` | そのユーザー名のパスワード（自分で決める） |
| `rpcallowip=127.0.0.1` | ノードの RPC を使える IP アドレス：ここでは同じマシンのプログラムだけ |
| `whitelist=download@127.0.0.1` | 同じマシンのプログラムは、ほかのルール（例：`maxuploadtarget=500`）が止める場合でも、制限なくブロックと取引をダウンロードできる。つまり electrs がノードのデータに自由にアクセスできる |

3. Bitcoin Core をもう一度起動します：

   ```bash
   bitcoind -daemon
   ```

## 📝 ステップ15：electrs の config.toml

1. electrs をインストールしたフォルダーの中で、設定ファイルを作ります：

   ```bash
   nano config.toml
   ```

2. 次の行を入れます：

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

   **Ctrl + X**、**y**、**Enter** を押します。

| 設定 | 役割 |
|---|---|
| `auth = "USER:PASSWORD"` | `USER` と `PASSWORD` を `bitcoin.conf` に入れたものと同じにして、RPC でログインする |
| `daemon_rpc_addr = "127.0.0.1:8332"` | ノードの RPC のアドレスとポート：`127.0.0.1` は同じマシン、`8332` は Bitcoin Core の標準 RPC ポート |
| `daemon_p2p_addr = "127.0.0.1:8333"` | ノードの P2P のアドレスとポート：`8333` は Bitcoin Core の標準 P2P ポート |
| `db_dir = ".electrum"` | electrs がデータベースを保存するフォルダー |
| `network = "bitcoin"` | electrs が動くネットワーク：ここではビットコインのメインネットワーク |
| `electrum_rpc_addr = "127.0.0.1:50001"` | electrs が Electrum クライアントにサービスを提供する場所：`127.0.0.1` は同じマシンからだけ、`50001` は暗号化しない Electrum 接続の標準ポート |
| `log_filters = "INFO"` | electrs がログに書く詳しさ |

## ⏳ ステップ16：electrs のインデックスを作る

1. electrs のフォルダーの中で、ブロックのインデックス作成を始めます：

   ```bash
   electrs --conf config.toml
   ```

2. ネットワーク全体のインデックスができるまで、ターミナルを開いたままにします。何時間もかかることがあります。発表では12月5日6時21分に始まり、12月6日1時24分、19時間3分で終わりました。

## 🧅 ステップ17：electrs を Tor で使う

1. `nano /etc/tor/torrc` で Tor の設定を開き、最後に追加します：

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   **Ctrl + X**、**y**、**Enter** を押します。

2. Tor を再起動します：

   ```bash
   systemctl restart tor
   ```

3. electrs の onion アドレスを表示します：

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 electrs の onion アドレスを変える</b></summary>

1. onion アドレスのもとになる秘密鍵が入った `electrs_hidden_service` フォルダーを削除します：

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Tor を再起動すると、新しい公開鍵と秘密鍵でフォルダーを作り直します：

   ```bash
   systemctl restart tor
   ```

3. 新しい onion アドレスを確認します：

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 さらに

## ➕ 次のステップ

- **Core Lightning (CLN)：** 支払いのための Lightning Network。設定は別の講座で扱います。
- **Mempool.space：** 自分でホストするブロックエクスプローラー。MariaDB、Node.js、バックエンドとフロントエンドを使い、`pm2` でプロセスを管理します。

## 🧪 テストネット

学ぶためのもの：価値のないコインとごく小さな手数料。マイニングは上下し、難易度も変わります。

- ウォレット：Green、Electrum、Specter、Sparrow
- BTCBouncer：テストネットの支払い
- BTCSigner：テストネットのマルチシグ

## ⌨️ bitcoin-cli

書式：`bitcoin-cli [options] <command> [params]`。`bitcoin-cli help` ですべてのコマンドが表示されます。

- ブロックチェーン：`getblockcount`、`getblockchaininfo`、`getblock`
- ネットワーク：`getnetworkinfo`、`getpeerinfo`
- ウォレット：`getbalance`、`sendtoaddress`、`listunspent`
- Raw：`createpsbt`、`decodepsbt`、`signrawtransactionwithwallet`

## 🔗 資料

- [Officine Bitcoin の講座](https://officinebitcoin.it/lezioni/fulhar/)：ハードウェア、Bitcoin Core、electrs、テストネット、bitcoin-cli
- [Officine Bitcoin の Debian 講座](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line)（[Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md) と [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md) を含む）
- [Bitcoin Core](https://bitcoincore.org/) と [Tor についての文書](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) と [設定の文書](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core 設定ジェネレーター](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [ODROID-M1 の Petitboot 自動起動](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)と [ODROID フォーラムのスレッド](https://forum.odroid.com/viewtopic.php?t=37109)

<a id="credits"></a>

## 🙏 クレジット

次の [Satoshi Spritz](https://satoshispritz.it/) の発表（イタリア語）から訳しました：

- [ビットコインノード：ソフトウェア、ハードウェア、DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf)、Valerio Vaccaro、Satoshi Spritz Milano、[CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) ライセンス
- [ODROID-M1 のビットコインノード、パート1：OS のインストール](https://satoshispritz.it/presentazioni/241031-odroid.pdf)、Satoshi Spritz Treviso、2024年10月29日
- [ODROID-M1 のビットコインノード、パート2：Bitcoin Core のインストールと同期の開始](https://satoshispritz.it/presentazioni/241031-odroid2.pdf)、Satoshi Spritz Treviso、2024年11月19日
- [Tor ネットワーク上のビットコインノード](https://satoshispritz.it/presentazioni/241031-tor.pdf)、Satoshi Spritz Treviso、2025年1月14日
- [Electrum サーバー：electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf)、Satoshi Spritz Treviso、2025年2月11日

# 🖥️ Run your own bitcoin node

Your own node checks every bitcoin rule itself, so you don't have to trust
anyone else's server. These guides help you set one up at home.

This guide is an English version of the node talks from
[Satoshi Spritz](https://satoshispritz.it/) in Italy (see
[Credits](#credits)): first an overview of node software and hardware,
then a step by step build on an ODROID-M1 with Bitcoin Core, Tor and
electrs.

## 🤔 Why run a bitcoin node?

- Check the whole bitcoin blockchain yourself
- Check your wallets' balances without third parties
- Broadcast your own transactions
- The most privacy, no trust in outside services

## 📦 Node software

Ready made options:

| Software | Price | Features |
|---|---|---|
| Umbrel | Software free, hardware 100 to 300 € | The most user friendly, app marketplace, Apple style |
| MyNode | Free / 79 € premium / 399 to 599 € pre built | Node + Lightning + Electrum + block explorer |
| Start9 | 699 to 899 € plug and play / software free for DIY | Open source, Tor built in, no telemetry |

What they all need:

- Blockchain: about 670+ GB (2025), growing about 1 GB every few days
- At least 4 GB RAM, 1 TB SSD (2 TB recommended)
- First sync: 2 to 7 days

**Ready made:** quick setup and a graphical interface, but Docker,
scripts and security checks are hidden from you, and the extra software
is hard to keep an eye on.

**DIY (manual):** less software means less attack surface, full control
over every part, and you learn how to look after it.

## 🔧 Choosing hardware

Minimum:

- At least 2 GB RAM (4 GB recommended)
- A disk of at least 1 TB (2 TB recommended)
- A multi core CPU (any recent CPU)

Options:

- **Old PC:** costs nothing; take the battery out if it's a laptop
- **Raspberry Pi:** expensive, and a USB disk is less stable
- **ODROID-M1:** ARM, with an internal slot for the SSD
- **Thin client (30 to 100 €):** Fujitsu Futro S920, HP t620

For the operating system use **Debian 13**, a long term support Linux
with years of updates.
Update all packages, install `tor` and `ssh` to look after the node
remotely, and a UPS (backup battery) is recommended.

# 🛠️ Build a node on an ODROID-M1

## 🧰 What you need

- ODROID-M1
- NVMe SSD (2 TB is best)
- Router
- Ethernet cable
- Monitor with an HDMI cable
- USB mouse and keyboard

## 🔩 Step 1: put it together

1. Fit the NVMe SSD into the ODROID-M1.
2. Plug in all the cables: mouse and keyboard, monitor, Ethernet cable.
3. Plug in the power cable to turn the ODROID-M1 on.

## 🐧 Step 2: download Debian 12

1. Once the ODROID-M1 has started, go to **Exit to shell** in Petitboot
   and press **Enter**.
2. Type `udhcpc` and press **Enter** (this can help fix connection
   problems).
3. Type `netboot_default` and press **Enter**.

   ⌨️ The keyboard is American: to get `_` hold **Shift** and press the
   key next to **0**.

4. Type `exit` and press **Enter**.
5. Petitboot's start screen now shows the list of available images. Go
   to **Debian 12 (Bookworm) Netboot installer** and press **Enter**.

## 💿 Step 3: install Debian 12

1. **Language:** choose **English**, it's easier to follow (the
   translations may not be good).
2. **Location:** pick your country (for Italy: **other**, then
   **Europe**, then **Italy**).
3. **Locale:** you can keep English by choosing **United Kingdom**.
4. **Keyboard:** pick the keyboard you have (for an Italian QWERTY
   keyboard, **Italian**).
5. **Hostname:** choose a hostname. The domain can stay empty.
6. **Mirror country:** the country Debian downloads from (in Italy,
   **Italy**).
7. **Mirror:** in Italy, `debian.mirror.garr.it` is usually fast and
   reliable.
8. **Proxy:** if you have no proxy, leave it empty and press **Enter**.
9. **Root password:** set the password for the superuser and press
   **Enter**.
10. **User:** make up a name for the normal (non admin) user (no need to
    fill in a full name), then set its password.
11. **Disk:** choose **Guided: use entire disk**, then the NVMe disk you
    fitted.
12. **Partitions:** keep everything in one partition: **All files in one
    partition (recommended for new users)**.
13. Debian makes a table with a boot partition, a root partition and a
    swap partition, which we don't want. Go to the swap line, press
    **Enter**, choose **Delete the partition** and press **Enter**.
14. Choose **Finish partitioning and write changes to disk**.
15. Debian reminds you there is no swap partition. Choose **No** so you
    don't go back to the partition menu.
16. Confirm the two partitions (boot and root) with **Yes** to start the
    install.
17. **Popularity contest** (anonymous statistics on the Debian packages
    you use): choose **No**.
18. **Software:** we don't want a graphical desktop, so leave only
    **SSH server** and **standard system utilities** ticked, and press
    **Enter**.
19. **Installation complete:** choose **Continue** to restart.

## 🔁 Step 4: start Debian automatically

1. In Petitboot, go to **System configuration** and press **Enter**.
2. **Autoboot** must be **Enabled**.
3. **Boot order** must have the disk fitted in the ODROID-M1, or **Any
   device**. To add the disk, go to **Add Device**, select the disk and
   **OK**. Or choose **Clear & Boot Any** for **Any device**.
4. **Timeout** is how many seconds after power on Debian starts (10 by
   default, no need to change it).
5. Go to **OK** and press **Enter**, then restart the ODROID-M1. After
   the restart a countdown in the bottom left starts Debian 12.

<details>
<summary><b>📵 No internet connection?</b></summary>

1. Log in as the superuser `root` and run `ping 8.8.8.8`. If it says
   **Network is unreachable**, the device isn't connected.
2. Run `dhclient -v` to ask the router for an IP address over DHCP. This
   also shows the network interface's name, for example `eth0`.
3. Run `ping 8.8.8.8` again: it works now, but the problem comes back
   at the next restart.
4. To make `eth0` use DHCP every time it starts, run
   `nano /etc/network/interfaces` and add or change these lines:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   Press **Ctrl + X** to exit, **y** to save and **Enter** to overwrite.

5. Restart and check the connection again. The ODROID-M1 now connects
   by itself at every restart.

</details>

## 💻 Step 5: connect with SSH

1. On your computer, open the terminal and connect with SSH, using the
   user you made during the install and the IP address your router gave
   the ODROID-M1:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian doesn't allow logging in over SSH as the superuser (`root`).

2. Once logged in, type `su` and enter the superuser password to get
   its rights.

## 📥 Step 6: update the repositories and the system

1. Open the repository list:

   ```bash
   nano /etc/apt/sources.list
   ```

2. Delete every line and add these:

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

   Press **Ctrl + X**, then **y** to save and **Enter** to overwrite the
   file.

3. Update the package list and the installed packages:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` should now connect to the repositories above, not to
   the default `debian.mirror.garr.it/debian`.

## 🧅 Step 7: install Tor and reach SSH over Tor

1. Install Tor:

   ```bash
   apt install tor
   ```

2. Open its settings with `nano /etc/tor/torrc` and add at the bottom:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   Press **Ctrl + X**, **y** and **Enter**.

3. Restart Tor and check its status (it starts every time the ODROID-M1
   starts):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. Show your onion address:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. Now you can connect to the ODROID-M1 from anywhere in the world:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 Step 8: download Bitcoin Core

The newest Bitcoin Core version, its hashes and signatures are on
<https://bitcoincore.org/en/download/>.

1. Download the install package, the checksums and the signatures:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. Check the checksum of the downloaded file matches the one in
   `SHA256SUMS`:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ It must say **OK** after the file name:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Get the Bitcoin Core developers' public keys from the
   `bitcoin-core/guix.sigs` repository. You probably don't have git yet,
   so install it first:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. Import the developers' public keys:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. Check the signatures on `SHA256SUMS.asc`:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ For each developer you should see `gpg: Good signature from "...."`,
   meaning the signature is valid. You can ignore any warnings.

🔒 Why this matters:

- **Integrity:** the checksum makes sure the file wasn't damaged or
  changed during the download.
- **Authenticity:** the signatures confirm the file really comes from
  the Bitcoin Core developers and not from a malicious source.

## ⚙️ Step 9: install Bitcoin Core and start syncing

1. Unpack the package:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Install Bitcoin Core:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. Start syncing:

   ```bash
   bitcoind
   ```

4. Home setup: open `bitcoin.conf` with `nano ~/.bitcoin/bitcoin.conf`
   and add:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   Press **Ctrl + X**, **y** and **Enter**.

| Setting | What it does |
|---|---|
| `daemon=1` | Runs `bitcoind` as a daemon in the background, so it keeps running after you close the terminal |
| `blocksonly=1` | Downloads only blocks, not the mempool (unconfirmed transactions) |
| `maxconnections=20` | At most 20 connections in and out |
| `maxuploadtarget=500` | Uploads at most 500 MB a day |
| `txindex=1` | Keeps a full index of transactions (explore the whole blockchain) |
| `blockfilterindex=1` | Builds and keeps an index of block filters (lets transactions be checked more efficiently) |

💡 Since Bitcoin Core 26, `v2transport` encrypts the connections
between nodes. For every setting, see the
[Bitcoin Core config generator](https://jlopp.github.io/bitcoin-core-config-generator/).

<details>
<summary><b>🔁 Run it as a systemd service</b></summary>

With a `bitcoind` service, a dedicated `bitcoin` user, automatic
restarts and the log in `~/.bitcoin/debug.log`:

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

## 🧅 Step 10: Bitcoin Core on Tor only

Set Bitcoin Core to connect, and be reachable, only over Tor.

1. Open Tor's settings with `nano /etc/tor/torrc` and add at the end:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   Press **Ctrl + X**, **y** and **Enter**.

2. Restart Tor:

   ```bash
   systemctl restart tor
   ```

3. Show Bitcoin Core's onion address:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Open `bitcoin.conf` with `nano ~/.bitcoin/bitcoin.conf` and add:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   Press **Ctrl + X**, **y** and **Enter**.

| Setting | What it does |
|---|---|
| `proxy=127.0.0.1:9050` | Makes Bitcoin Core use the Tor proxy |
| `listen=1` | Lets the node accept incoming connections |
| `bind=127.0.0.1:8333=onion` | Allows incoming connections only through Tor |
| `externalip=ADDRESS.onion` | Replace `ADDRESS.onion` with the onion address you got above |
| `onlynet=onion` | Allows outgoing connections only on the Tor network |

5. Restart Bitcoin Core: stop it, then start it again as a daemon
   (a process in the background):

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. Check the networks:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ Under `networks` you see `ipv4`, `ipv6`, `onion`, `i2p` and
   `cjdns`. Only `onion` has `reachable` set to `true`; all the others
   are `false`. Under `localaddresses` you see only your onion address.

7. Check the peers you connect to, or that connect to you:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ The `addr` field holds the peer's onion address and `network` says
   `onion`.

⏳ Tor is slow: the first sync takes much longer.

<details>
<summary><b>🔄 Change Bitcoin Core's onion address</b></summary>

1. Delete the `bitcoin-service` folder, which holds the private key
   behind the onion address:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Restart Tor, which makes the folder again with a new public and
   private key:

   ```bash
   systemctl restart tor
   ```

3. Check the new onion address:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Restart Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 Step 11: install Rust

electrs is an Electrum server written in the Rust programming language,
so you need Rust on the device.

1. Install `curl` first:

   ```bash
   apt install curl
   ```

2. Install Rust:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   When it asks for an install option, type **1** and press **Enter** for
   the default install.

3. When it finishes it suggests restarting the shell. Log out and back
   in (or become superuser again with `su`) to use the Rust commands.

4. Install `clang`, `cmake` and `build-essential`:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 Step 12: download electrs

1. Check the newest electrs version on
   <https://github.com/romanz/electrs>. Here it is 0.10.7:

   ```bash
   VERSION="0.10.7"
   ```

   (If the newest version is, say, 0.10.9, change the value in quotes.)

2. Download that version:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. Go into the new `electrs` folder and import the developer's key:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. Check the developer's signature:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ The signature is valid if you see `gpg: Good signature from "...."`.

## 🏗️ Step 13: build and install electrs

1. Still inside the `electrs` folder, start the build. It takes quite a
   while, so be patient 😄

   ```bash
   cargo build --locked --release
   ```

2. Install electrs:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. Check it installed by printing its version:

   ```bash
   electrs --version
   ```

## 📝 Step 14: bitcoin.conf settings for electrs

1. Stop Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

2. Open `bitcoin.conf` with `nano ~/.bitcoin/bitcoin.conf` and add:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   Press **Ctrl + X**, **y** and **Enter**.

| Setting | What it does |
|---|---|
| `server=1` | Lets Bitcoin Core work as an RPC server |
| `rpcuser=USER` | The username (you choose it) that apps must give to connect to the node over RPC |
| `rpcpassword=PASSWORD` | The password (you choose it) for that username |
| `rpcallowip=127.0.0.1` | Which IP addresses may use the node's RPC: here only programs on the same machine |
| `whitelist=download@127.0.0.1` | Programs on the same machine can download blocks and transactions with no limits, even when other rules would stop them (for example `maxuploadtarget=500`). In short, electrs gets free access to the node's data |

3. Start Bitcoin Core again:

   ```bash
   bitcoind -daemon
   ```

## 📝 Step 15: electrs config.toml

1. Inside the folder where you installed electrs, create the settings
   file:

   ```bash
   nano config.toml
   ```

2. Add these lines:

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

   Press **Ctrl + X**, **y** and **Enter**.

| Setting | What it does |
|---|---|
| `auth = "USER:PASSWORD"` | Replace `USER` and `PASSWORD` with the same ones you set in `bitcoin.conf`, to log in over RPC |
| `daemon_rpc_addr = "127.0.0.1:8332"` | The node's RPC address and port: `127.0.0.1` means the same machine, `8332` is Bitcoin Core's default RPC port |
| `daemon_p2p_addr = "127.0.0.1:8333"` | The node's P2P address and port: `8333` is Bitcoin Core's default P2P port |
| `db_dir = ".electrum"` | The folder where electrs keeps its database |
| `network = "bitcoin"` | The network electrs runs on: here the main bitcoin network |
| `electrum_rpc_addr = "127.0.0.1:50001"` | Where electrs offers its service to Electrum clients: `127.0.0.1` means only from the same machine, `50001` is the default port for unencrypted Electrum connections |
| `log_filters = "INFO"` | How much detail electrs writes to its log |

## ⏳ Step 16: build the electrs index

1. Inside the electrs folder, start indexing the blocks:

   ```bash
   electrs --conf config.toml
   ```

2. Leave the terminal open until the whole network is indexed. It can
   take many hours: in the talk it started on 5 December at 6:21 and
   finished on 6 December at 1:24, after 19 hours and 3 minutes.

## 🧅 Step 17: electrs on Tor

1. Open Tor's settings with `nano /etc/tor/torrc` and add at the end:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   Press **Ctrl + X**, **y** and **Enter**.

2. Restart Tor:

   ```bash
   systemctl restart tor
   ```

3. Show electrs's onion address:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 Change electrs's onion address</b></summary>

1. Delete the `electrs_hidden_service` folder, which holds the private
   key behind the onion address:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Restart Tor, which makes the folder again with a new public and
   private key:

   ```bash
   systemctl restart tor
   ```

3. Check the new onion address:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 More

## ➕ Next steps

- **Core Lightning (CLN):** the Lightning Network for payments, set up
  in separate lessons.
- **Mempool.space:** a self hosted block explorer, using MariaDB,
  Node.js, a backend and a frontend, with `pm2` to manage the processes.

## 🧪 Testnet

For learning: coins with no value and tiny fees. Mining goes up and
down and the difficulty changes.

- Wallets: Green, Electrum, Specter, Sparrow
- BTCBouncer: testnet payments
- BTCSigner: testnet multisig

## ⌨️ bitcoin-cli

Format: `bitcoin-cli [options] <command> [params]`, and `bitcoin-cli
help` lists everything.

- Blockchain: `getblockcount`, `getblockchaininfo`, `getblock`
- Network: `getnetworkinfo`, `getpeerinfo`
- Wallet: `getbalance`, `sendtoaddress`, `listunspent`
- Raw: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 Resources

- [Officine Bitcoin lessons](https://officinebitcoin.it/lezioni/fulhar/):
  hardware, Bitcoin Core, electrs, testnet, bitcoin-cli
- [Officine Bitcoin Debian lesson](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  including [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)
  and [Changing your Bitcoin hidden services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md)
- [Bitcoin Core](https://bitcoincore.org/) and its
  [Tor notes](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) and its
  [config docs](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core config generator](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [ODROID-M1 Petitboot autoboot](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)
  and the [ODROID forum thread](https://forum.odroid.com/viewtopic.php?t=37109)

## 🧱 Another guide

- [MiniBolt](https://minibolt.minibolt.info/): build a node on a mini PC
  with an Intel or AMD processor from about 2010 onwards.

💬 Found in the Malaga 2140 Telegram chat.

MiniBolt's own [hardware requirements](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)
ask for:

- CPU: "Intel/AMD processor (2010+ gen)"
- RAM: "2+ GB RAM (only Bitcoin), 4+ GB RAM is recommended (Bitcoin +
  Lightning + bonus)"
- Storage: "SSD SATA/NVMe 2+ TB is recommended"
- A USB pen drive or SD card of "4+ GB" to install from
- A temporary monitor or TV and a USB or PS2 keyboard for the first setup

<a id="credits"></a>

## 🙏 Credits

Translated from these [Satoshi Spritz](https://satoshispritz.it/) talks
(in Italian):

- [bitcoin node: software, hardware and DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano, licensed
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
- [bitcoin node on ODROID-M1, part 1: installing the operating system](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  Satoshi Spritz Treviso, 29 October 2024
- [bitcoin node on ODROID-M1, part 2: installing Bitcoin Core and starting the sync](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  Satoshi Spritz Treviso, 19 November 2024
- [bitcoin node on the Tor network](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  Satoshi Spritz Treviso, 14 January 2025
- [Electrum server: electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  Satoshi Spritz Treviso, 11 February 2025

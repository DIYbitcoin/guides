# 🖥️ Kendi bitcoin düğümünü çalıştır

Kendi düğümün bitcoinin her kuralını kendisi kontrol eder, böylece başka
kimsenin sunucusuna güvenmek zorunda kalmazsın. Bu rehberler evde bir tane
kurmana yardım eder.

Bu rehber, İtalya'daki [Satoshi Spritz](https://satoshispritz.it/)
düğüm sunumlarının Türkçe hâlidir ([Emeği geçenler](#credits) bölümüne bak):
önce düğüm yazılımı ve donanımına genel bir bakış, sonra bir ODROID-M1
üzerinde Bitcoin Core, Tor ve electrs ile adım adım kurulum.

## 🤔 Neden bir bitcoin düğümü?

- Bitcoin blok zincirinin tamamını kendin doğrula
- Cüzdanlarının bakiyesini üçüncü taraflar olmadan kontrol et
- Kendi işlemlerini kendin yayınla
- En yüksek gizlilik, dış hizmetlere güven yok

## 📦 Düğüm yazılımları

Hazır çözümler:

| Yazılım | Fiyat | Özellikler |
|---|---|---|
| Umbrel | Yazılım ücretsiz, donanım 100 ile 300 € arası | En kolay kullanılan, uygulama mağazası, Apple tarzı |
| MyNode | Ücretsiz / 79 € premium / hazır kurulu 399 ile 599 € arası | Düğüm + Lightning + Electrum + blok gezgini |
| Start9 | Tak çalıştır 699 ile 899 € arası / DIY için yazılım ücretsiz | Açık kaynak, dahili Tor, telemetri yok |

Hepsinin ortak gereksinimleri:

- Blok zinciri: yaklaşık 670+ GB (2025), birkaç günde bir yaklaşık 1 GB
  büyüyor
- En az 4 GB RAM, 1 TB SSD (2 TB önerilir)
- İlk senkronizasyon: 2 ile 7 gün

**Hazır çözümler:** hızlı kurulum ve grafik arayüz, ama Docker, betikler
ve güvenlik kontrolleri gözünden saklanır ve ek yazılımları takip etmek
zordur.

**DIY (elle):** daha az yazılım daha az saldırı yüzeyi demektir, her
parça üzerinde tam kontrol sağlar ve bakımını yapmayı öğrenirsin.

## 🔧 Donanım seçimi

En az:

- En az 2 GB RAM (4 GB önerilir)
- En az 1 TB disk (2 TB önerilir)
- Çok çekirdekli CPU (yeni sayılabilecek herhangi bir CPU)

Seçenekler:

- **Eski PC:** maliyeti yok; dizüstüyse pilini çıkar
- **Raspberry Pi:** pahalı ve USB disk daha az kararlı
- **ODROID-M1:** ARM, SSD için dahili yuvası var
- **İnce istemci (30 ile 100 € arası):** Fujitsu Futro S920, HP t620

İşletim sistemi olarak yıllarca desteklenen uzun süreli destekli bir
Linux olan **Debian 13**'ü kullan. Tüm paketleri güncelle, düğümü uzaktan
yönetmek için `tor` ve `ssh` kur, bir UPS (kesintisiz güç kaynağı)
önerilir.

# 🛠️ ODROID-M1 üzerinde düğüm kur

## 🧰 Gerekenler

- ODROID-M1
- NVMe SSD (en iyisi 2 TB)
- Modem/router
- Ethernet kablosu
- HDMI kablolu monitör
- USB fare ve klavye

## 🔩 Adım 1: birleştir

1. NVMe SSD'yi ODROID-M1'e tak.
2. Tüm kabloları bağla: fare ve klavye, monitör, Ethernet kablosu.
3. ODROID-M1'i açmak için güç kablosunu tak.

## 🐧 Adım 2: Debian 12'yi indir

1. ODROID-M1 açıldıktan sonra Petitboot'ta **Exit to shell**'e git ve
   **Enter**'a bas.
2. `udhcpc` yaz ve **Enter**'a bas (bağlantı sorunlarını çözmeye
   yardımcı olabilir).
3. `netboot_default` yaz ve **Enter**'a bas.

   ⌨️ Klavye Amerikan düzenindedir: `_` için **Shift**'e basılı tut ve
   **0**'ın yanındaki tuşa bas.

4. `exit` yaz ve **Enter**'a bas.
5. Petitboot'un açılış ekranında artık kullanılabilir imajların listesi
   görünür. **Debian 12 (Bookworm) Netboot installer**'a git ve
   **Enter**'a bas.

## 💿 Adım 3: Debian 12'yi kur

1. **Dil:** **English**'i seç, takip etmesi daha kolay (çeviriler iyi
   olmayabilir).
2. **Konum:** ülkeni seç (İtalya için: **other**, sonra **Europe**,
   sonra **Italy**).
3. **Yerel ayar:** **United Kingdom**'ı seçip İngilizce bırakabilirsin.
4. **Klavye:** elindeki klavyeyi seç (İtalyan QWERTY klavye için
   **Italian**).
5. **Hostname:** bir ana bilgisayar adı seç. Alan adı boş kalabilir.
6. **Yansı ülkesi:** Debian'ın indirileceği ülke (İtalya'da **Italy**).
7. **Yansı:** İtalya'da `debian.mirror.garr.it` genellikle hızlı ve
   güvenilirdir.
8. **Proxy:** proxy'n yoksa boş bırak ve **Enter**'a bas.
9. **Root parolası:** süper kullanıcı parolasını belirle ve **Enter**'a
   bas.
10. **Kullanıcı:** normal (yönetici olmayan) kullanıcı için bir ad uydur
    (tam ad girmen gerekmez), sonra parolasını belirle.
11. **Disk:** **Guided: use entire disk**'i seç, sonra taktığın NVMe
    diski seç.
12. **Bölümler:** her şeyi tek bölümde tut: **All files in one partition
    (recommended for new users)**.
13. Debian boot, root ve swap bölümleri olan bir tablo yapar; swap
    istemiyoruz. Swap satırına git, **Enter**'a bas, **Delete the
    partition**'ı seç ve **Enter**'a bas.
14. **Finish partitioning and write changes to disk**'i seç.
15. Debian swap bölümü olmadığını hatırlatır. Bölüm menüsüne dönmemek
    için **No**'yu seç.
16. Kurulumu başlatmak için iki bölümü (boot ve root) **Yes** ile onayla.
17. **Popularity contest** (kullandığın Debian paketleri hakkında anonim
    istatistik): **No**'yu seç.
18. **Yazılım:** grafik masaüstü istemiyoruz, bu yüzden yalnızca **SSH
    server** ve **standard system utilities** işaretli kalsın ve
    **Enter**'a bas.
19. **Kurulum tamamlandı:** yeniden başlatmak için **Continue**'yu seç.

## 🔁 Adım 4: Debian'ı otomatik başlat

1. Petitboot'ta **System configuration**'a git ve **Enter**'a bas.
2. **Autoboot** **Enabled** olmalı.
3. **Boot order**'da ODROID-M1'e takılı disk ya da **Any device**
   olmalı. Diski eklemek için **Add Device**'a git, diski seç ve **OK**.
   Ya da **Any device** için **Clear & Boot Any**'yi seç.
4. **Timeout**, açıldıktan kaç saniye sonra Debian'ın başlayacağıdır
   (varsayılan 10, değiştirmen gerekmez).
5. **OK**'a git, **Enter**'a bas ve ODROID-M1'i yeniden başlat. Yeniden
   başladıktan sonra sol alttaki geri sayım Debian 12'yi başlatır.

<details>
<summary><b>📵 İnternet bağlantısı yok mu?</b></summary>

1. Süper kullanıcı `root` olarak giriş yap ve `ping 8.8.8.8` çalıştır.
   **Network is unreachable** diyorsa cihaz bağlı değildir.
2. Router'dan DHCP ile IP adresi istemek için `dhclient -v` çalıştır.
   Bu, ağ arayüzünün adını da gösterir, örneğin `eth0`.
3. `ping 8.8.8.8`'i tekrar çalıştır: artık çalışıyor, ama bir sonraki
   yeniden başlatmada sorun geri gelir.
4. `eth0`'ın her açılışta DHCP kullanması için
   `nano /etc/network/interfaces` çalıştır ve şu satırları ekle ya da
   değiştir:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   Çıkmak için **Ctrl + X**, kaydetmek için **y** ve üzerine yazmak için
   **Enter**'a bas.

5. Yeniden başlat ve bağlantıyı tekrar kontrol et. Artık ODROID-M1 her
   yeniden başlatmada kendiliğinden bağlanır.

</details>

## 💻 Adım 5: SSH ile bağlan

1. Bilgisayarında terminali aç ve kurulumda oluşturduğun kullanıcıyla ve
   router'ın ODROID-M1'e verdiği IP adresiyle SSH ile bağlan:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian, süper kullanıcı (`root`) olarak SSH ile girişe izin vermez.

2. Giriş yaptıktan sonra `su` yaz ve yetkilerini almak için süper
   kullanıcı parolasını gir.

## 📥 Adım 6: depoları ve sistemi güncelle

1. Depo listesini aç:

   ```bash
   nano /etc/apt/sources.list
   ```

2. Tüm satırları sil ve şunları ekle:

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

   **Ctrl + X**'e, sonra kaydetmek için **y**'ye ve dosyanın üzerine
   yazmak için **Enter**'a bas.

3. Paket listesini ve kurulu paketleri güncelle:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` artık varsayılan `debian.mirror.garr.it/debian` yerine
   yukarıdaki depolara bağlanmalı.

## 🧅 Adım 7: Tor'u kur ve SSH'a Tor üzerinden eriş

1. Tor'u kur:

   ```bash
   apt install tor
   ```

2. Ayarlarını `nano /etc/tor/torrc` ile aç ve en alta ekle:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

3. Tor'u yeniden başlat ve durumunu kontrol et (ODROID-M1 her açıldığında
   başlar):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. Onion adresini göster:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. Artık ODROID-M1'e dünyanın her yerinden bağlanabilirsin:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 Adım 8: Bitcoin Core'u indir

Bitcoin Core'un en yeni sürümü, hash'leri ve imzaları
<https://bitcoincore.org/en/download/> adresindedir.

1. Kurulum paketini, sağlama toplamlarını ve imzaları indir:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. İndirilen dosyanın sağlama toplamının `SHA256SUMS`'takiyle aynı
   olduğunu kontrol et:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ Dosya adından sonra **OK** yazmalı:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Bitcoin Core geliştiricilerinin açık anahtarları
   `bitcoin-core/guix.sigs` deposundadır. Muhtemelen henüz git yok, o
   yüzden önce onu kur:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. Geliştiricilerin açık anahtarlarını içe aktar:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. `SHA256SUMS.asc` üzerindeki imzaları kontrol et:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ Her geliştirici için `gpg: Good signature from "...."` görmelisin,
   bu imzanın geçerli olduğu anlamına gelir. Uyarıları yok sayabilirsin.

🔒 Bu neden önemli:

- **Bütünlük:** sağlama toplamı, dosyanın indirme sırasında bozulmadığını
  ya da değiştirilmediğini garanti eder.
- **Gerçeklik:** imzalar, dosyanın gerçekten Bitcoin Core
  geliştiricilerinden geldiğini ve kötü niyetli bir kaynaktan
  gelmediğini doğrular.

## ⚙️ Adım 9: Bitcoin Core'u kur ve senkronizasyonu başlat

1. Paketi aç:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Bitcoin Core'u kur:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. Senkronizasyonu başlat:

   ```bash
   bitcoind
   ```

4. Ev kurulumu: `bitcoin.conf`'u `nano ~/.bitcoin/bitcoin.conf` ile aç ve
   ekle:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

| Ayar | Ne yapar |
|---|---|
| `daemon=1` | `bitcoind`'yi arka planda daemon olarak çalıştırır, terminali kapatınca da çalışmaya devam eder |
| `blocksonly=1` | Yalnızca blokları indirir, mempool'u (onaylanmamış işlemler) indirmez |
| `maxconnections=20` | Gelen ve giden en fazla 20 bağlantı |
| `maxuploadtarget=500` | Günde en fazla 500 MB yükler |
| `txindex=1` | İşlemlerin tam dizinini tutar (tüm blok zincirini keşfet) |
| `blockfilterindex=1` | Blok filtreleri dizini oluşturur ve tutar (işlemler daha verimli kontrol edilir) |

💡 Bitcoin Core 26'dan beri `v2transport` düğümler arası bağlantıları
şifreler. Tüm ayarlar için
[Bitcoin Core yapılandırma oluşturucuya](https://jlopp.github.io/bitcoin-core-config-generator/)
bak.

<details>
<summary><b>🔁 systemd hizmeti olarak çalıştır</b></summary>

Bir `bitcoind` hizmeti, ayrı bir `bitcoin` kullanıcısı, otomatik
yeniden başlatma ve `~/.bitcoin/debug.log` içindeki günlükle:

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

## 🧅 Adım 10: Bitcoin Core yalnızca Tor üzerinden

Bitcoin Core'u yalnızca Tor üzerinden bağlanacak ve yalnızca Tor
üzerinden erişilebilecek şekilde ayarla.

1. Tor ayarlarını `nano /etc/tor/torrc` ile aç ve sona ekle:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

2. Tor'u yeniden başlat:

   ```bash
   systemctl restart tor
   ```

3. Bitcoin Core'un onion adresini göster:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. `bitcoin.conf`'u `nano ~/.bitcoin/bitcoin.conf` ile aç ve ekle:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

| Ayar | Ne yapar |
|---|---|
| `proxy=127.0.0.1:9050` | Bitcoin Core'un Tor proxy'sini kullanmasını sağlar |
| `listen=1` | Düğümün gelen bağlantıları kabul etmesine izin verir |
| `bind=127.0.0.1:8333=onion` | Gelen bağlantılara yalnızca Tor üzerinden izin verir |
| `externalip=ADDRESS.onion` | `ADDRESS.onion`'u yukarıda aldığın onion adresiyle değiştir |
| `onlynet=onion` | Giden bağlantılara yalnızca Tor ağında izin verir |

5. Bitcoin Core'u yeniden başlat: durdur, sonra daemon (arka plan süreci)
   olarak tekrar başlat:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. Ağları kontrol et:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ `networks` altında `ipv4`, `ipv6`, `onion`, `i2p` ve `cjdns`
   görürsün. Yalnızca `onion`'da `reachable` `true`; diğerlerinin hepsi
   `false`. `localaddresses` altında yalnızca kendi onion adresini
   görürsün.

7. Bağlandığın ya da sana bağlanan eşleri kontrol et:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ `addr` alanında eşin onion adresi var ve `network` `onion` diyor.

⏳ Tor yavaştır: ilk senkronizasyon çok daha uzun sürer.

<details>
<summary><b>🔄 Bitcoin Core'un onion adresini değiştir</b></summary>

1. Onion adresinin arkasındaki özel anahtarı tutan `bitcoin-service`
   klasörünü sil:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Tor'u yeniden başlat; klasörü yeni bir açık ve özel anahtarla yeniden
   oluşturur:

   ```bash
   systemctl restart tor
   ```

3. Yeni onion adresini kontrol et:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Bitcoin Core'u yeniden başlat:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 Adım 11: Rust'ı kur

electrs, Rust programlama diliyle yazılmış bir Electrum sunucusudur, bu
yüzden cihazda Rust gerekir.

1. Önce `curl`'ü kur:

   ```bash
   apt install curl
   ```

2. Rust'ı kur:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   Kurulum seçeneği sorduğunda varsayılan kurulum için **1** yaz ve
   **Enter**'a bas.

3. Bitince kabuğu yeniden başlatmanı önerir. Rust komutlarını kullanmak
   için çıkış yapıp tekrar gir (ya da `su` ile yeniden süper kullanıcı
   ol).

4. `clang`, `cmake` ve `build-essential`'ı kur:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 Adım 12: electrs'i indir

1. electrs'in en yeni sürümünü <https://github.com/romanz/electrs>
   adresinde kontrol et. Burada 0.10.7:

   ```bash
   VERSION="0.10.7"
   ```

   (En yeni sürüm örneğin 0.10.9 ise tırnak içindeki değeri değiştir.)

2. O sürümü indir:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. Yeni `electrs` klasörüne gir ve geliştiricinin anahtarını içe aktar:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. Geliştiricinin imzasını kontrol et:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ `gpg: Good signature from "...."` görüyorsan imza geçerlidir.

## 🏗️ Adım 13: electrs'i derle ve kur

1. Hâlâ `electrs` klasörünün içindeyken derlemeyi başlat. Epey uzun
   sürer, sabırlı ol 😄

   ```bash
   cargo build --locked --release
   ```

2. electrs'i kur:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. Sürümünü yazdırarak kurulduğunu kontrol et:

   ```bash
   electrs --version
   ```

## 📝 Adım 14: electrs için bitcoin.conf ayarları

1. Bitcoin Core'u durdur:

   ```bash
   bitcoin-cli stop
   ```

2. `bitcoin.conf`'u `nano ~/.bitcoin/bitcoin.conf` ile aç ve ekle:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

| Ayar | Ne yapar |
|---|---|
| `server=1` | Bitcoin Core'un RPC sunucusu olarak çalışmasını sağlar |
| `rpcuser=USER` | Uygulamaların RPC ile düğüme bağlanırken vermesi gereken kullanıcı adı (sen seçersin) |
| `rpcpassword=PASSWORD` | O kullanıcı adının parolası (sen seçersin) |
| `rpcallowip=127.0.0.1` | Düğümün RPC'sini hangi IP adreslerinin kullanabileceği: burada yalnızca aynı makinedeki programlar |
| `whitelist=download@127.0.0.1` | Aynı makinedeki programlar, başka kurallar engellese bile (örneğin `maxuploadtarget=500`) blokları ve işlemleri sınırsız indirebilir. Kısacası electrs düğümün verilerine serbestçe erişir |

3. Bitcoin Core'u tekrar başlat:

   ```bash
   bitcoind -daemon
   ```

## 📝 Adım 15: electrs config.toml

1. electrs'i kurduğun klasörün içinde ayar dosyasını oluştur:

   ```bash
   nano config.toml
   ```

2. Şu satırları ekle:

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

   **Ctrl + X**, **y** ve **Enter**'a bas.

| Ayar | Ne yapar |
|---|---|
| `auth = "USER:PASSWORD"` | `USER` ve `PASSWORD`'u `bitcoin.conf`'ta koyduklarınla aynı yap, RPC ile giriş için |
| `daemon_rpc_addr = "127.0.0.1:8332"` | Düğümün RPC adresi ve portu: `127.0.0.1` aynı makine, `8332` Bitcoin Core'un varsayılan RPC portu |
| `daemon_p2p_addr = "127.0.0.1:8333"` | Düğümün P2P adresi ve portu: `8333` Bitcoin Core'un varsayılan P2P portu |
| `db_dir = ".electrum"` | electrs'in veritabanını tuttuğu klasör |
| `network = "bitcoin"` | electrs'in çalıştığı ağ: burada bitcoinin ana ağı |
| `electrum_rpc_addr = "127.0.0.1:50001"` | electrs'in Electrum istemcilerine hizmet verdiği yer: `127.0.0.1` yalnızca aynı makineden, `50001` şifresiz Electrum bağlantıları için varsayılan port |
| `log_filters = "INFO"` | electrs'in günlüğüne ne kadar ayrıntı yazdığı |

## ⏳ Adım 16: electrs dizinini oluştur

1. electrs klasörünün içinde blokların dizinlenmesini başlat:

   ```bash
   electrs --conf config.toml
   ```

2. Tüm ağ dizinlenene kadar terminali açık bırak. Saatlerce sürebilir:
   sunumda 5 Aralık saat 6:21'de başladı ve 6 Aralık saat 1:24'te, 19
   saat 3 dakika sonra bitti.

## 🧅 Adım 17: electrs Tor üzerinden

1. Tor ayarlarını `nano /etc/tor/torrc` ile aç ve sona ekle:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   **Ctrl + X**, **y** ve **Enter**'a bas.

2. Tor'u yeniden başlat:

   ```bash
   systemctl restart tor
   ```

3. electrs'in onion adresini göster:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 electrs'in onion adresini değiştir</b></summary>

1. Onion adresinin arkasındaki özel anahtarı tutan
   `electrs_hidden_service` klasörünü sil:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Tor'u yeniden başlat; klasörü yeni bir açık ve özel anahtarla yeniden
   oluşturur:

   ```bash
   systemctl restart tor
   ```

3. Yeni onion adresini kontrol et:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 Daha fazlası

## ➕ Sonraki adımlar

- **Core Lightning (CLN):** ödemeler için Lightning Network, kurulumu
  ayrı derslerde.
- **Mempool.space:** kendi sunucunda çalışan bir blok gezgini; MariaDB,
  Node.js, bir backend ve frontend kullanır, süreçleri `pm2` yönetir.

## 🧪 Testnet

Öğrenmek için: değeri olmayan coinler ve çok düşük ücretler. Madencilik
iner çıkar, zorluk değişir.

- Cüzdanlar: Green, Electrum, Specter, Sparrow
- BTCBouncer: testnet ödemeleri
- BTCSigner: testnet multisig

## ⌨️ bitcoin-cli

Biçim: `bitcoin-cli [options] <command> [params]`, `bitcoin-cli help`
her şeyi listeler.

- Blok zinciri: `getblockcount`, `getblockchaininfo`, `getblock`
- Ağ: `getnetworkinfo`, `getpeerinfo`
- Cüzdan: `getbalance`, `sendtoaddress`, `listunspent`
- Ham: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 Kaynaklar

- [Officine Bitcoin dersleri](https://officinebitcoin.it/lezioni/fulhar/):
  donanım, Bitcoin Core, electrs, testnet, bitcoin-cli
- [Officine Bitcoin Debian dersi](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)
  ve [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md) dahil
- [Bitcoin Core](https://bitcoincore.org/) ve
  [Tor notları](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) ve
  [yapılandırma belgeleri](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core yapılandırma oluşturucu](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [ODROID-M1 Petitboot otomatik açılış](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)
  ve [ODROID forum konusu](https://forum.odroid.com/viewtopic.php?t=37109)

## 🧱 Başka bir rehber

- [MiniBolt](https://minibolt.minibolt.info/): yaklaşık 2010 ve sonrasına
  ait Intel ya da AMD işlemcili bir mini PC üzerinde düğüm kur.

💬 Malaga 2140 Telegram sohbetinde bulundu.

MiniBolt'un kendi [donanım gereksinimleri](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)
şunları istiyor:

- CPU: 2010 ve sonrası nesil Intel/AMD işlemci
- RAM: 2 GB ya da üzeri (yalnızca Bitcoin), 4 GB ya da üzeri önerilir
  (Bitcoin, Lightning ve ekstralar)
- Depolama: 2 TB ya da üzeri SSD SATA/NVMe önerilir
- Kurulum için 4 GB ya da üzeri bir USB bellek ya da SD kart
- İlk kurulum için geçici olarak bir monitör ya da televizyon ve bir USB ya
  da PS2 klavye

<a id="credits"></a>

## 🙏 Emeği geçenler

Şu [Satoshi Spritz](https://satoshispritz.it/) sunumlarından (İtalyanca)
çevrildi:

- [bitcoin düğümü: yazılım, donanım ve DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano,
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) lisanslı
- [ODROID-M1 üzerinde bitcoin düğümü, bölüm 1: işletim sistemini kurmak](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  Satoshi Spritz Treviso, 29 Ekim 2024
- [ODROID-M1 üzerinde bitcoin düğümü, bölüm 2: Bitcoin Core'u kurmak ve senkronizasyonu başlatmak](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  Satoshi Spritz Treviso, 19 Kasım 2024
- [Tor ağında bitcoin düğümü](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  Satoshi Spritz Treviso, 14 Ocak 2025
- [Electrum sunucusu: electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  Satoshi Spritz Treviso, 11 Şubat 2025

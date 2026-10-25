# 💳 Javacard flaşlama: Satochip, Satodime, SeedKeeper ve Keycard

Javacard'da bir **güvenli eleman** bulunur: bitcoin anahtarların gibi
sırları, kart başkasının eline geçse bile güvende tutmak için yapılmış bir
çip. Üzerine **applet** denen küçük uygulamalar kurabilirsin. Bu rehber,
dört açık kaynak bitcoin applet'inden birini boş bir J3H145 javacard'a
kurar:

- **Satochip:** Sparrow Wallet ile kullandığın DIY bir bitcoin cüzdanı
- **Satodime:** hamiline bir kart, yani kart kimin elindeyse, üzerindeki
  bitcoinin sahibi odur, tıpkı nakit gibi
- **SeedKeeper:** seed kelimelerin ve passphrase'in, parolaların, cüzdan
  tanımlayıcıların ve diğer sırların için PIN ile kilitli, şifreli bir yedek
- **Keycard:** Sparrow Wallet ile kullandığın başka bir DIY bitcoin cüzdanı

Applet'ler [Satochip](https://satochip.io) ve
[Keycard](https://keycard.tech) projelerinden geliyor ve bu rehber
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) rehberini takip ediyor.

![Bilgisayar, okuyucu, kart ve dört applet](images/00-overview.svg)

## 🧰 İhtiyacın olanlar

- 💳 Bir **J3H145** javacard. Satın almak istersen, bu rehberde kullanılanlar
  şunlar: [Alibaba'da J3H145](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 Bir **USB akıllı kart okuyucu**: bilgisayarına taktığın ve kartı çip
  önde olacak şekilde içine sürdüğün küçük bir kutu. Kartı üzerine
  dokundurduğun bir NFC okuyucu olan **ACS ACR122U**'yu kullanma:
  [Satochip-DIY rehberi](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)
  applet kurmak için güvenilir olmadığı ve kartı bozabileceği konusunda
  uyarıyor. Applet kurulduktan sonra kartı kullanmak içinse sorun yok.
- 🛡️ Elinde bir [ShieldSigner](https://github.com/3rdIteration/seedsigner) mı var?
  Bilgisayar ve okuyucu yerine o da bu applet'leri karta kurabilir.
- 💻 Bir **Mac**, **Linux** (Debian, Ubuntu ya da Raspberry Pi OS) ya da
  **Windows** bilgisayar
- 🌐 İndirmeler için internet bağlantısı

## 🛠️ Adım 1: bilgisayarını hazırla (bir kez)

Bu, **Java**'yı ve applet'leri karta koyan `gp` komutu olan
**GlobalPlatformPro**'yu kurar.

<details>
<summary><b>🍎 Mac kullanıyorum</b></summary>

1. **Terminal**'i aç: **Cmd + Space** tuşlarına bas, `Terminal` yaz,
   **Enter** tuşuna bas.

2. Terminal için Mac uygulama yükleyicisi olan **Homebrew**'u kur. Zaten
   varsa bu adımı atla (`brew --version` bir sürüm yazdırır).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac'inin parolasını sorar. Yazarken hiçbir şey görünmez; bu normal,
   sadece yaz ve **Enter** tuşuna bas.

   ⚠️ Bittiğinde, iki ya da üç komutla birlikte **Next steps** yazdırır.
   Onları da kopyalayıp çalıştır, yoksa Mac `brew` komutunu bulamaz.

3. Java'yı kur:

   ```bash
   brew install --cask temurin
   ```

4. GlobalPlatformPro'yu kur:

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 Linux kullanıyorum</b></summary>

1. **Terminal**'i aç: **Ctrl + Alt + T** tuşlarına bas ya da
   uygulamaların arasında **Terminal**'i bul.

2. Java'yı, git'i ve akıllı kart hizmetini (`pcscd`) kur:

   ```bash
   sudo apt update
   ```

   🔒 Bilgisayarının parolasını sorar. Yazarken hiçbir şey görünmez; bu
   normal, sadece yaz ve **Enter** tuşuna bas.

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. GlobalPlatformPro'yu `gp.jar` olarak içeren
   [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) dosyalarını
   indir:

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. Bu rehberdeki her komut yazıldığı gibi çalışsın diye ona kısa `gp`
   adını ver:

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. **Terminal'i kapat ve yenisini aç**, böylece yeni adı tanır.

</details>

<details>
<summary><b>🪟 Windows kullanıyorum</b></summary>

⚠️ Windows'ta test edilmedi.

1. Java'yı kur: <https://adoptium.net> adresinden **Temurin**'i indir ve
   varsayılan seçeneklerle kur.

2. [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)
   dosyasını [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)
   sayfasından **İndirilenler** klasörüne indir.

3. **Komut İstemi**'ni aç: **Windows** tuşuna bas, `cmd` yaz, **Enter**
   tuşuna bas. Yönetici olarak çalıştırman gerekmez.

4. İndirilenler klasörüne gir:

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. Bu rehberde `gp` yazan her yerde onun yerine `gp.exe` yaz, örneğin
   `gp.exe --info`. `cd ~/Downloads` yazan her yerde 4. adımdaki satırı
   kullan.

</details>

✅ Çalıştığını kontrol et:

```bash
gp --version
```

`#` ile başlayan birkaç satır yazdırıyorsa ve hiç `command not found`
yoksa çalışıyor demektir.

💡 Java, `gp` her çalıştığında `WARNING:` ile başlayan birkaç satır
yazdırır. Bunlar normaldir, görmezden gelebilirsin.

## 🔌 Adım 2: okuyucunun kartı gördüğünü kontrol et

Okuyucuyu tak, kartı (çip önde) içine yerleştir ve şunu çalıştır:

```bash
gp --info
```

✅ `CPLC` satırlarını ve `Global Platform card` yazısını görüyorsan, okuyucu
ile kart birbiriyle konuşuyor demektir:

![gp --info çıktısı](images/01-gp-info.svg)

Şimdi kartta zaten ne olduğuna bak:

```bash
gp --list
```

Boş bir kart yalnızca fabrikadan gelenleri gösterir: kart yöneticisi
(`ISD`) ve bir paket (`A0000001515350`). Bunlara dokunma.

![Boş bir kartta gp --list](images/02-gp-list-blank.svg)

### 🛑 "Card cryptogram invalid" görürsen dur

![Card cryptogram invalid uyarısı](images/07-gp-cryptogram-stop.svg)

⚠️ Bu, kartın sende olmayan anahtarlarla kilitlendiği anlamına gelir.
**Komutu tekrar çalıştırma**: birkaç yanlış denemeden sonra kart kendini
kalıcı olarak kilitler. Kartı sana satan kişiden kartın anahtarlarını iste.

## 📥 Adım 3: applet dosyasını indir

İstediğin applet'in dosyasını **İndirilenler** klasörüne indir. Her
sayfada, indirmek için **Assets** altındaki dosya adına tıkla.

| Applet | İndirilecek dosya | Ayarlandığı uygulama |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 Neden en yenileri değil de bu sürümler?</b></summary>

- **Satochip v0.15, v0.16 değil:** v0.16 (Ekim 2026), kartın taproot
  anahtarlarını ayarlama şeklini değiştirdi ve Sparrow 2.5.5 hâlâ eski
  formatı gönderiyor, bu yüzden Sparrow'daki bir taproot cüzdanı v0.16
  kartla imza atamaz. Native SegWit ikisinde de çalışır.
- **Satodime v0.1-0.2** en son kararlı sürüm; v0.2 derlemeleri hâlâ beta
  olarak işaretli.
- **Keycard 3.2, 4.0 değil:** Keycard 4.0 (Eylül 2026), kart ile
  bilgisayarın şifreli bağlantılarını kurma şeklini değiştirdi ve Sparrow
  2.5.5 hâlâ eski yolu kullanıyor.

Sparrow bu değişikliklere uyum sağladığında daha yeni sürümleri kullan.

</details>

<details>
<summary><b>🔍 İsteğe bağlı: dosyayla oynanmadığını kontrol et</b></summary>

İndirilenler klasörüne gir:

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

Yazdırdığı uzun kod, sürüm sayfasındakiyle aynı olmalı:

- `SatoChip-v0.15-0.1.cap`:
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`:
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ Adım 4: applet'i karta koy

🔄 Kartındaki applet'i başka biriyle değiştirmek mi istiyorsun? Önce
eskisini kaldır, sonra yenisini kur: aşağıdaki
[Başka bir applet'e geçmek](#switching) bölümüne bak.

Dosyanın bulunduğu İndirilenler klasörüne gir:

```bash
cd ~/Downloads
```

Sonra **yalnızca kendi applet'inin satırını** çalıştır:

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

Keycard (ek ayarlar onu Sparrow'un aradığı cüzdan yapar):

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ Bir `loaded:` satırıyla biter:

![gp --install çıktısı](images/03-gp-install-satochip.svg)

![Keycard için gp --install](images/09-gp-install-keycard.svg)

✅ `gp --list` komutunu tekrar çalıştır. `APP` ile başlayıp `SELECTABLE`
ile biten yeni bir satır, applet'in kurulduğunu ve hazır olduğunu gösterir:

![Satochip kuruluyken gp --list](images/04-gp-list-satochip.svg)

Şimdi applet'inin ayar adımına geç:

- Satochip: [Adım 5a](#step-5a)
- SeedKeeper ya da Satodime: [Adım 5b](#step-5b)
- Keycard: [Adım 5c](#step-5c)

<a id="step-5a"></a>

## Adım 5a: Satochip'i Sparrow ile ayarla

👛 Bilgisayarın için Sparrow Wallet'ı
<https://sparrowwallet.com/download/> adresinden indir.

PIN'i belirlemeyi ve 12 seed kelimesini yüklemeyi doğrudan Sparrow'dan
yaparsın; Satochip uygulamasına gerek yok. Kartı baştan sona okuyucuda
bırak.

1. Sparrow'da yeni bir cüzdan oluştur ve ona bir ad ver. **Keystores**
   altında **Connected Hardware Wallet** seçeneğini seç ve **Scan...**
   düğmesine bas.

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow **Satochip**'i bulur. **Import Keystore** düğmesine bas.

   ![Sparrow Satochip'i bulur](images/sparrow-2-found.webp)

3. Sparrow **Choose a PIN code** der. Bir PIN yaz.

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow **Card not initialized** der. Aynı PIN'i kutuya tekrar yaz ve
   **Initialize** düğmesine bas.

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. **Satochip Seed Words** penceresi açılır. Yeni 12 seed kelimesi için
   **Generate New** düğmesine bas ya da zaten elinde olan 12 seed
   kelimesini yaz. ✍️ **12 seed kelimesini kâğıda yaz**, sonra **OK**
   düğmesine bas. Sparrow onları passphrase olmadan karta yükler.

   💡 Sparrow bu 12 seed kelimesini senin bilgisayarında üretir, bu yüzden
   gerçek birikimler için güvendiğin bir bilgisayar kullan.

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**. **OK** düğmesine bas.

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. Satochip satırına geri dönüp **Import Keystore** düğmesine tekrar bas.

   ![Tekrar Import Keystore](images/sparrow-7-import-keystore.webp)

8. 🎉 Cüzdan artık Satochip'ini parmak iziyle (fingerprint) birlikte
   gösterir: anahtarlarını adlandıran kısa bir kod. Cüzdanı kaydetmek için
   **Apply** düğmesine bas.

   ![Sparrow'da tamamlanmış cüzdan](images/sparrow-8-wallet.webp)

🔢 Sparrow karta **5 PIN deneme hakkı** verir. Keystore üzerinden daha
sonra **Change Pin** ile PIN'ini de değiştirebilirsin.

<a id="step-5b"></a>

## Adım 5b: SeedKeeper'ı ya da Satodime'ı Sato-Tools ile ayarla

🧰 Bilgisayarın için Sato-Tools'u
<https://github.com/Toporin/Satochip-Utils/releases> adresinden indir (bu
rehberde 0.4.0-beta kullanıldı).

Kart okuyucudayken Sato-Tools'u aç ve **Let's go!** düğmesine bas.

![Sato-Tools karşılama ekranı](images/satotools-0-welcome.webp)

💡 **Sato-Tools yeni flaşladığın bir kartı görmüyorsa, uygulamayı kapatıp
yeniden aç.**

### SeedKeeper

1. Sato-Tools **Your SeedKeeper is connected** der. **Setup my card**
   düğmesine bas.

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. **New PIN code** alanına bir PIN yaz, **Confirm PIN code** alanına da
   aynısını yaz (4 ila 16 karakter), sonra **Save PIN** düğmesine bas.

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** **Ok** düğmesine bas.

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 Menüde artık **My secrets**, **Generate secret**, **Import secret**,
   **Backup card** ve **Card logs** var. SeedKeeper'ın seed kelimelerini,
   passphrase'leri, parolaları ve cüzdan tanımlayıcılarını saklamaya hazır.

   ![SeedKeeper hazır](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools **Your Satodime is connected** der. Üç kasa vardır:
   **Vault #0**, **Vault #1** ve **Vault #2**.

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview** üç kasanın hepsini **uninitialized** olarak gösterir:
   henüz hiçbirinde mühürlenmiş bir şey yok.

   ![Kasalarım](images/satotools-satodime-2-vaults.png)

3. **About**, applet sürümünü ve **Card has no owner** yazısını gösterir.
   **Take ownership** bu bilgisayarı kartın sahibi yapar; bir kasayı
   mühürlemeden önce buna ihtiyacın var.

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 **Take ownership** düğmesine bas. About artık **You are the owner**
   der ve düğme, kartı başka birine verdiğinde kullandığın
   **Transfer ownership** düğmesine dönüşür.

💡 Bir kasayı **mühürlemek**, onun için bir bitcoin adresi oluşturur; özel
anahtarı kartın içinde gizli kalır. **Mührü açmak** özel anahtarı gösterir,
böylece bitcoin taşınabilir. Bir Satodime'ı ödeme olarak kabul etmeden önce
kasalarının hâlâ mühürlü olduğunu kontrol et.

### 📱 Telefonunda

SeedKeeper ve Satodime'ın telefon uygulamaları da var. Orada kullanmak için
kartı NFC'li bir telefonun arkasına dokundur.

### ❓ Neden "Genuine: NO" diyor?

Satochip'ten satın alınan kartlar, fabrikada yüklenmiş bir Satochip
sertifikasıyla gelir ve **Genuine** bunu kontrol eder. Kendin flaşladığın
bir DIY kartta böyle bir sertifika yoktur, bu yüzden her zaman
**Genuine: [NO]** gösterir. Applet tamamen aynı şekilde çalışır.

<a id="step-5c"></a>

## Adım 5c: Keycard'ı Sparrow ile ayarla

👛 Henüz indirmediysen, bilgisayarın için Sparrow Wallet'ı
<https://sparrowwallet.com/download/> adresinden indir.

Keycard tıpkı Satochip gibi ayarlanır, iki farkla: PIN **tam 6 haneli**
olmalı ve kart yalnızca **3 yanlış PIN**'e izin verir.

1. Yeni bir cüzdan oluştur, **Connected Hardware Wallet** seçeneğini seç,
   **Scan...** düğmesine bas. Sparrow **Keycard**'ı bulur.
   **Import Keystore** düğmesine bas.

   ![Sparrow Keycard'ı bulur](images/keycard-1-found.webp)

2. **Tam 6 haneli** bir PIN seç. Daha kısa ya da daha uzun olursa
   **PIN length must be between 6 and 6 characters** hatası çıkar.

   ![PIN 6 haneli olmalı](images/keycard-2-pin-error.webp)

3. Aynı PIN'i tekrar yaz ve **Initialize** düğmesine bas. Satochip'teki
   seed kelimeleri penceresinin aynısı açılır ([Adım 5a](#step-5a), 5.
   madde): **Generate New** düğmesine bas ya da kendi kelimelerini yaz,
   ✍️ **12 seed kelimesini not et**, sonra **OK** düğmesine bas.
   ✅ **Card Initialized**: **OK** düğmesine bas.

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 **Import Keystore** düğmesine tekrar bas. Cüzdan artık Keycard'ını
   parmak iziyle birlikte gösterir. Cüzdanı kaydetmek için **Apply**
   düğmesine bas.

   ![Sparrow'da tamamlanmış Keycard cüzdanı](images/keycard-4-wallet.webp)

⚠️ **Geri dönmenin tek yolu 12 seed kelimen.** 3 yanlış PIN'den sonra
Keycard kilitlenir ve Sparrow kilidi açmak için hiçbir yol sunmaz. Bu
olursa Keycard'ı kaldır, yeniden kur ve 12 seed kelimeni Sparrow'a tekrar
gir.

<a id="switching"></a>

## 🔄 Başka bir applet'e geçmek

⚠️ **Bir applet'i kaldırmak, içinde saklanan her şeyi siler**; Satochip ya
da Keycard'daki seed ve SeedKeeper'daki sırlar da buna dahil. Bunu yalnızca
12 seed kelimesini bir yere yazdıysan ya da cüzdan bir deneme cüzdanıysa
yap. Kartın kendisi zarar görmez ve istediğin kadar tekrar flaşlanabilir.

![Kaldırmak siler, kurmak sıfırdan başlar](images/08-swap.svg)

1. Applet dosyalarının bulunduğu İndirilenler klasörüne gir:

   ```bash
   cd ~/Downloads
   ```

2. Kartta ne olduğundan emin değil misin? `gp --list` komutunu çalıştır:
   `APP` satırı bunu gösterir.

3. Şu anda kartta olan applet'i kaldır. **Yalnızca onun satırını**
   çalıştır:

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

4. ✅ `gp --list` komutunu tekrar çalıştır. Yalnızca kart yöneticisi
   (`ISD`) ve fabrika paketi (`A0000001515350`) kalmış olmalı.

5. Yeni applet'i kur ([Adım 4](#step-4)) ve ayarla
   ([Adım 5a](#step-5a), [5b](#step-5b) ya da [5c](#step-5c)).

Örneğin:

![Satochip'i Satodime ile değiştirmek](images/05-gp-swap-to-satodime.svg)

![Satodime'ı SeedKeeper ile değiştirmek](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 Sorun giderme

| Gördüğün | Ne yapmalı |
|---|---|
| 🍎 `brew: command not found` | Homebrew kurulumu bittiğinde yazdırdığı **Next steps** komutlarını çalıştır. |
| 🐧 `gp: command not found` | Terminal'i kapat ve yenisini aç. |
| `No such file` ya da `does not exist` | Önce `cd ~/Downloads` komutunu çalıştır ve dosyanın İndirilenler klasöründe olduğunu kontrol et. |
| `Card cryptogram invalid` | Kart başka anahtarlarla kilitli. Dur, tekrar deneme. |
| `LOAD failed: 0x6438` | Applet, kartta olandan daha yeni bir Java Card için derlenmiş. |
| `INSTALL ... failed: 0x6444` ya da `0x6F00` | Kartta, applet'in ihtiyaç duyduğu bir özellik eksik. |
| Zaten applet olan bir kartta `gp --install` başarısız oluyor | `gp --list` komutunu çalıştır. Bir `APP` satırı varsa önce o applet'i kaldır ([Başka bir applet'e geçmek](#switching) bölümüne bak). |
| Sparrow'da **Scan...** hiçbir şey bulmuyor | Sato-Tools'u ve diğer tüm kart uygulamalarını kapat, kartı çıkarıp tekrar tak, sonra yeniden **Scan...** düğmesine bas. |
| PIN'i unuttun ya da çok fazla yanlış PIN yüzünden kart kilitlendi | Applet'i kaldırıp yeniden kur (bu, kartı siler), sonra 12 seed kelimeni geri yükle. |
| Sato-Tools kartı görmüyor | Sato-Tools'u kapatıp yeniden aç. |
| `gp` okuyucu bulamıyor ya da kart takılı değil diyor | Kartı çip önde tak. Hâlâ kart yok diyorsa okuyucu donmuştur: okuyucuyu bilgisayardan çıkar, kart içindeyken tekrar tak ve yeniden dene. 🐧 Sonra `sudo systemctl restart pcscd` komutunu çalıştır. |

## 🚀 Daha ileri gitmek

- 🎥 [Video: bu kartlara 7 farklı applet flaşlamak](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 Kartı kendi anahtarınla kilitle, böylece başka kimse üzerine kurulu
  olanı değiştiremez:
  [Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ Applet'leri kendin derle. Yukarıdaki dosyaları başka biri derledi,
  yani onların herkese açık kaynak kodla eşleştiğine güveniyorsun. Kaynak
  koddan kendin derlediğinde, kartına giren şey tam olarak herkesin
  okuyabildiği koddur:
  [Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 Teşekkürler

- 🙌 [Toporin](https://github.com/Toporin), [Satochip](https://satochip.io)
  yaratıcısı, Satochip, Satodime ve SeedKeeper applet'leri ile Sato-Tools
  için.
- 🛠️ 3rdIteration'ın [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)
  projesi: bu rehberin takip ettiği DIY rehberi, test edilmiş kart ve
  okuyucu listeleri ve `gp.jar` için.
- [Keycard](https://github.com/keycard-tech/status-keycard), Keycard
  applet'i için.
- 💳 Martin Paljak'ın [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)
  aracı, applet'leri karta yüklemek için.

# 📟 DIY signer'lar: SeedSigner, Kern ve KISS Signer

Signer (imzalayıcı), bitcoin anahtarlarını tutan ve işlemleri imzalayan
küçük, çevrimdışı bir cihazdır; böylece anahtarların hiçbir zaman bir
bilgisayara ya da telefona dokunmaz. Bu rehber, açık kaynak bir signer'ı
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)
kartına kurar ("flaşlar"): kamerası ve microSD yuvası olan, 3,5 inç
dokunmatik ekranlı bir kart. Lehim gerekmez.

📷 **Nasıl kullanılır:** signer asla internete bağlanmaz. Onu kamerasıyla
QR kodları tarayarak Sparrow gibi bir cüzdan uygulamasıyla eşleştirirsin
ve işlemleri de aynı şekilde imzalarsın.

⚠️ **Üçü de bu kartta erken sürümler. Yalnızca deneme seed kelimeleri ve
testnet (değeri olmayan deneme bitcoini) ya da çok küçük miktarlar kullan,
asla gerçek birikimlerini kullanma.**

### 🧰 Neye ihtiyacın var

- 📟 Waveshare 3,5 inç kart
- 🔌 Veri taşıyan bir USB-C kablo. Yalnızca şarj eden bir kablo karta
  güç verir ama bilgisayar hiçbir şey görmez.
- 💻 Bir bilgisayar: SeedSigner için Mac ya da Linux; Kern ve KISS
  Signer için **Chrome** ya da **Brave** olan herhangi bir bilgisayar

### 👉 Signer'ını seç

Her biri, kartı takmaktan signer'ın ilk ekranına kadar eksiksiz bir yol.
Yalnızca seçtiğini takip et.

- 🌐 **[Kern](#kern):** en kolayı. Chrome ya da Brave'de birkaç tıklamayla
  flaşlanır.
- 🌐 **[KISS Signer](#kiss):** o da Chrome ya da Brave'de flaşlanır.
- 💻 **[SeedSigner](#seedsigner):** Terminal'e birkaç komut yazarak
  flaşlanır. Dört kısa sayfa.

<a id="kern"></a>

## 🌐 Kern

**Chrome** ya da **Brave** gerekir. Safari ve Firefox flaşlayamaz.

1. USB-C kabloyu kartın üzerinde **UART** yazan porta, diğer ucunu da
   bilgisayarına tak.

   🐧 **Linux'ta**, önce aşağıdaki [Linux adımını](#linux-kern) yap.

2. [Kern web flaşlayıcısını](https://odudex.github.io/Kern/flash/) aç.
3. **📦 Latest CI Build** seçili kalsın.
4. **Board** altında **Waveshare ESP32-P4 3.5" (320×480 SPI)** seç. Liste
   başka bir kartla başlıyor, bu yüzden bunu kontrol et.
5. **🔌 Connect**'e tıkla. Küçük bir pencere açılır: içinde kartını seç
   (🍎 adında `usbmodem`, 🐧 `ttyACM` geçer), sonra **Bağlan**'a tıkla.

   ✅ Butonların altındaki siyah kutuda `Connected to: ESP32-P4` yazar.
   Flaşlayıcı çipini okur ve v1.x ya da v3.x derlemesini kendisi seçer.

6. **⚡ Flash**'a tıkla ve kabloyu çıkarmadan bekle.

   ✅ `Device reset. Flash successful! ✓` yazar.

7. Kartı çıkar, 3 saniye bekle, yeniden tak.

   ✅ Kern kartta açılır. 🎉

<details id="linux-kern">
<summary><b>🐧 Linux adımı: tarayıcının kartla konuşmasına izin ver</b></summary>

1. **Terminal**'i aç: **Ctrl + Alt + T**'ye bas.
2. Çalıştır:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Bilgisayarının parolasını sorar. Yazarken hiçbir şey görünmez; bu
   normal, yaz ve **Enter**'a bas.

3. **Bilgisayarından çıkış yap ve yeniden giriş yap**, sonra yukarıdaki
   2. adıma dön.

</details>

📦 Resmi [Kern sürümleri](https://github.com/odudex/Kern/releases)
yalnızca **v1.x** çipleri kapsar, bu yüzden web flaşlayıcıyı kullan.

Sonraki: [🔋 pili tak](#battery), ya da bir şey ters gittiyse
[🩺 sorun giderme](#troubleshooting).

<a id="kiss"></a>

## 🌐 KISS Signer

**Chrome** ya da **Brave** gerekir. Safari ve Firefox flaşlayamaz.

1. USB-C kabloyu kartın üzerinde **UART** yazan porta, diğer ucunu da
   bilgisayarına tak.

   🐧 **Linux'ta**, önce aşağıdaki [Linux adımını](#linux-kiss) yap.

2. [KISS Signer kurulum sayfasını](https://diybitcoin.github.io/kiss-signer/) aç.
3. **Which board is it?** altında **Waveshare 3.5in** seç.
4. **This erases the device. I'm starting with keys that hold nothing.**
   kutusunu işaretle.
5. **Connect and install**'a tıkla. Küçük bir pencere açılır: içinde
   kartını seç (🍎 adında `usbmodem`, 🐧 `ttyACM` geçer), sonra
   **Bağlan**'a tıkla.
6. Sonraki pencerede **Install KISS Signer (Waveshare 3.5in)**'e tıkla.
   **Erase device**'ı işaretle, **Next**'e, sonra **Install**'a tıkla.
   Kabloyu çıkarmadan bekle.

   ✅ **Installation complete!** yazar.

7. Kartı çıkar, 3 saniye bekle, yeniden tak.

   ✅ Kart, KISS Signer'ın ilk gösterdiği oyun olan **FRUIT ISLAND** ile
   açılır. 🎉

8. Ekran siyah mı kalıyor? Kartında daha yeni **v3.x** çip var ve bu
   sürüm v1.x için. Kartı
   [test kurulum sayfasından](https://diybitcoin.github.io/kiss-signer/test/)
   yeniden flaşla: **1. Your board** altında kartını seç, sonra **Connect
   and install**'a tıkla. Çipini okur ve ona uyan derlemeyi seçer.

   ⚠️ Test kurulum sayfasında imzasız test sürümleri var, henüz gerçek
   bir v3.x kartta denenmedi.

<details id="linux-kiss">
<summary><b>🐧 Linux adımı: tarayıcının kartla konuşmasına izin ver</b></summary>

1. **Terminal**'i aç: **Ctrl + Alt + T**'ye bas.
2. Çalıştır:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Bilgisayarının parolasını sorar. Yazarken hiçbir şey görünmez; bu
   normal, yaz ve **Enter**'a bas.

3. **Bilgisayarından çıkış yap ve yeniden giriş yap**, sonra yukarıdaki
   2. adıma dön.

</details>

Sonraki: [🔋 pili tak](#battery), ya da bir şey ters gittiyse
[🩺 sorun giderme](#troubleshooting).

<a id="seedsigner"></a>

## 💻 SeedSigner 1: bilgisayarını hazırla

Bu kart için SeedSigner, Keith'in (kdmukai) erken bir sürümü. Onu
**Terminal**'e, çip üreticisinin aracı `esptool` ile komutlar yazarak
flaşlarsın. Aşağıdaki her komutun bir **kopyala** butonu var: kopyala,
Terminal'e yapıştır, **Enter**'a bas.

<details>
<summary><b>🍎 Mac kullanıyorum</b></summary>

1. **Terminal**'i aç: **Cmd + Space**'e bas, `Terminal` yaz, **Enter**'a
   bas.

2. Terminal için Mac uygulama yükleyicisi olan **Homebrew**'u kur. Zaten
   varsa bu adımı atla (`brew --version` bir sürüm yazar).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac'inin parolasını sorar. Yazarken hiçbir şey görünmez; bu normal,
   yaz ve **Enter**'a bas.

   ⚠️ Bitince iki üç komutla **Next steps** yazar. Onları da kopyalayıp
   çalıştır, yoksa Mac `brew`'u bulamaz.

3. esptool'u kur:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Linux kullanıyorum</b></summary>

1. **Terminal**'i aç: **Ctrl + Alt + T**'ye bas.

2. Kullanıcının kartla konuşmasına izin ver:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Bilgisayarının parolasını sorar. Yazarken hiçbir şey görünmez; bu
   normal, yaz ve **Enter**'a bas.

3. `esptool` ve `unzip`'i kur, her seferinde bir satır:

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

4. **Bilgisayarından çıkış yap ve yeniden giriş yap**, sonra Terminal'i
   yeniden aç.

</details>

✅ esptool'un çalıştığını kontrol et:

```bash
esptool version
```

**5.3 ya da daha yeni** yazmalı. Daha eskisi yeni çipleri flaşlayamaz:
🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`.

Sonraki: [SeedSigner 2: kartı tak ve çipini kontrol et](#ss-chip).

<a id="ss-chip"></a>

## 💻 SeedSigner 2: kartı tak ve çipini kontrol et

Kart iki çip sürümünden biriyle gelir: eski kartlarda **v1.x**, yenilerde
**v3.x**. Her biri kendi SeedSigner derlemesini ister. Bu sayfa hangisine
sahip olduğunu söyler.

1. USB-C kabloyu kartın üzerinde **UART** yazan porta, diğer ucunu da
   bilgisayarına tak.

2. Bilgisayarına uyan satırı çalıştır:

   🍎 Mac:

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux:

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. `ESP32-P4 (revision ...)` yazan satırı bul:

   - `revision v1.3` ya da herhangi bir **v1**: çipin **v1.x**.
   - `revision v3.0`, `v3.2` ya da herhangi bir **v3**: çipin **v3.x**.

   Not al: sonraki sayfa bunu soruyor.

Sonraki: [SeedSigner 3: çipin için derlemeyi indir](#ss-download).

<a id="ss-download"></a>

## 💻 SeedSigner 3: çipin için derlemeyi indir

SeedSigner 2'deki **yalnızca çipine uyan derlemeyi** kullan. Diğeri
açılmaz.

<details open>
<summary><b>1️⃣ Çipim v1.x</b></summary>

[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
dosyasını **İndirilenler** klasörüne indirmek için tıkla.

İçinde bir **dokunma düzeltmesi** var: kaydırmak bir butona basmak yerine
menülerde gezinir. Düzeltme hâlâ
[açık bir pull request](https://github.com/kdmukai/esp-board-common/pull/21),
henüz SeedSigner'ın parçası değil. v1.3 bir kartta denendi.

Zip'in SHA256 değeri:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ Çipim v3.x</b></summary>

Keith'in derlemeleri GitHub'dan gelir ve indirmek için ücretsiz bir
GitHub hesabı gerekir.

1. [github.com/login](https://github.com/login) adresinde giriş yap ya da
   önce orada bir hesap aç.
2. [SeedSigner'ın son derlemelerini](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess)
   aç.
3. Listenin **en üstündeki** derlemeye tıkla, en yenisi o.
4. **Artifacts** bölümüne in ve **İndirilenler** klasörüne indirmek için
   **flashable-dist-waveshare-esp32-p4-35**'e tıkla.

   ⚠️ Adının **35** ile bittiğinden emin ol. `43` olanlar 4,3 inç kart
   için, Keith'in Releases sayfasındaki dosya da 4,3 inç için. İkisi de
   bu kartta açılmaz.

</details>

Sonraki: [SeedSigner 4: zip'i aç ve flaşla](#ss-flash).

<a id="ss-flash"></a>

## 💻 SeedSigner 4: zip'i aç ve flaşla

1. İndirilenler'e geç:

   ```bash
   cd ~/Downloads
   ```

2. **Senin** çipine uyan iki satırla zip'i aç ve klasöre gir:

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

   💡 Mac'te Safari zip'i zaten açmış olabilir. `unzip` dosyayı
   bulamadığını söylerse o satırı atla ve yalnızca `cd` satırını çalıştır.

3. Bilgisayarına uyan satırla flaşla:

   🍎 Mac:

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux:

   ```bash
   esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   Yaklaşık 4 dakika sürer, bu yüzden kartı çıkarma. Başta çıkan birkaç
   `Deprecated` uyarısı zararsızdır.

   ✅ Bitince kart yeniden başlar ve SeedSigner açılır. 🎉

4. Test verisiyle dene: bilgisayarında [testqrs.com](https://testqrs.com)
   adresini aç ve kartın kamerasını oradaki QR kodlara tut. Seed
   kelimeleri herkese açık, bu yüzden yalnızca alıştırma için kullan.

Sonraki: [🔋 pili tak](#battery), ya da bir şey ters gittiyse
[🩺 sorun giderme](#troubleshooting).

<a id="battery"></a>

## 🔋 Pili tak

Kart, kablosuz çalışabilmesi için küçük bir pil ve küçük bir tornavidayla
gelir.

1. Kasanın arkasındaki dört küçük kapağı tırnağınla çıkar. Vidalar
   altında.
2. Dört vidayı tornavidayla sök ve kasanın arkasını kaldır.
3. Pilin fişini kartın sağ alt köşesindeki pil soketine tak. Yalnızca tek
   yönde girer, girmiyorsa ters çevir. Zorlama.
4. ✅ Güç butonunun yanında yeşil bir ışık yanmalı.
5. Kasanın arkasını yeniden tak ve vidala. Kasa, pil içindeyken normal
   şekilde kapanır.

![Kasası açık ve pili takılı Waveshare 3,5 inç kart](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 Sorun giderme

| Gördüğün | Ne yapmalı |
|---|---|
| Tarayıcı penceresinde seçilecek kart yok | **Chrome** ya da **Brave**, **UART** portu ve bir veri kablosu kullan. 🐧 [Kern](#linux-kern) ya da [KISS Signer](#linux-kiss) sayfasındaki Linux adımını yap. |
| `port is busy` ya da `could not open port` | Web flaşlayıcı sekmesini ve kartı kullanan başka Terminal'leri kapat, sonra yeniden dene. |
| 🍎 `no matches found: /dev/cu.usbmodem*` ya da 🐧 `No such file or directory` | Bilgisayar kartı görmüyor: **UART** portunu ve veri taşıdığını bildiğin bir kablo kullan. |
| 🐧 `Permission denied`, ya da tarayıcı kartı açamıyor | Çıkış yapıp yeniden giriş yapmak dahil Linux adımını yap. |
| `Failed to connect` | Kartı kullanan her şeyi kapat, kartı çıkar, yeniden tak ve tekrar dene. |
| 🍎 `brew: command not found` | Homebrew kurulumu bitince yazdığı **Next steps** komutlarını çalıştır. |
| 🐧 `esptool: command not found` | Çıkış yapıp yeniden giriş yap, sonra yeni bir Terminal aç. |
| `requires chip revision in range` | Derleme çipine uymuyor: [SeedSigner 2](#ss-chip)'yi yeniden yap ve diğer derlemeyi indir. |
| `flash_args` bulunamadı | Zip'ten çıkan klasörde değilsin: [SeedSigner 4](#ss-flash)'ün 2. adımını yeniden yap. |
| Flaşladıktan sonra ekranda bir şey yok | Çıkar, 3 saniye bekle, yeniden tak. Derlemenin çipine uyduğunu kontrol et. |
| `esptool` çipi flaşlayamıyor | 5.3 ya da daha yeni olmalı: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Teşekkürler

- [Kern](https://github.com/odudex/Kern), odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder), kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer), DIYbitcoin.

# 📟 DIY signer'lar: SeedSigner, Kern ve KISS Signer

Signer (imzalayıcı), bitcoin anahtarlarını tutan ve işlemleri imzalayan
küçük, çevrimdışı bir cihazdır; böylece anahtarların hiçbir zaman bir
bilgisayara ya da telefona dokunmaz. Bu rehber, açık kaynak bir signer'ı
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)
kartına kurar ("flaşlar"): kamerası ve microSD yuvası olan, 3,5 inç
dokunmatik ekranlı bir kart. Lehim gerekmez.

| Signer | Nasıl flaşlanır | Durum |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | Web tarayıcında | Test sürümleri |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | Web tarayıcında | Beta |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | Terminal'de | Test sürümleri |

📷 **Nasıl kullanılır:** signer asla internete bağlanmaz. Onu kamerasıyla
QR kodları tarayarak Sparrow gibi bir cüzdan uygulamasıyla eşleştirirsin
ve işlemleri de aynı şekilde imzalarsın.

⚠️ **Üçü de bu kartta erken sürümler. Yalnızca deneme seed kelimeleri ve
testnet (değeri olmayan deneme bitcoini) ya da çok küçük miktarlar kullan,
asla gerçek birikimlerini kullanma.**

## 🔌 Adım 1: kartı bağla

**UART** yazan porta bir USB-C kablo tak.

💡 Veri taşıdığını bildiğin bir kablo kullan. Yalnızca şarj eden bir kablo
karta güç verir ama bilgisayar hiçbir şey görmez.

<a id="step-2"></a>

## 💻 Adım 2: bilgisayarını hazırla

🌐 **Kern** ve **KISS Signer**, **Chrome** ya da **Brave** tarayıcısındaki
bir web yükleyiciden flaşlanır, yani bunlar için tek ihtiyacın o tarayıcı
(Linux'ta aşağıdaki Linux kutusundaki 2. adımı da yap).

💻 **SeedSigner** Terminal'den `esptool` ile flaşlanır: onu aşağıda kur.

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

3. esptool'u kur:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Linux kullanıyorum</b></summary>

1. **Terminal**'i aç: **Ctrl + Alt + T** tuşlarına bas ya da
   uygulamaların arasında **Terminal**'i bul.

2. Kullanıcı hesabının kartla konuşabilmesini sağla (tarayıcıdaki Kern
   dahil, üç signer için de gerekli):

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Bilgisayarının parolasını sorar. Yazarken hiçbir şey görünmez; bu
   normal, sadece yaz ve **Enter** tuşuna bas.

3. **Bilgisayarında oturumu kapatıp yeniden aç**, böylece değişiklik
   uygulanır.

4. SeedSigner için `esptool` ve `unzip` kur. Terminal'i yeniden aç ve
   şunları çalıştır:

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

5. **Terminal'i kapat ve yenisini aç**, böylece `esptool` bulunur.

</details>

✅ SeedSigner için esptool'un çalıştığını kontrol et:

```bash
esptool version
```

**5.3 ya da daha yeni** bir sürüm yazdırmalı.

<a id="step-3"></a>

## 🧬 Adım 3: çipini tanı

3,5 inç kart, ESP32-P4 çipinin iki sürümünden biriyle gelir: eski
kartlarda **v1.x**, yenilerde **v3.x**; yani yakın zamanda alınmış bir
kartta büyük ihtimalle v3.x vardır. Her signer için çipine göre yapılmış
derlemeyi kullanman gerekir: v1.x derlemesi v3.x çipte açılmaz, tersi de
öyle.

| Signer | v1.x çip | v3.x çip |
|---|---|---|
| Kern | Web yükleyici ya da resmi sürüm | Yalnızca web yükleyici (test sürümü) |
| KISS Signer | Kurulum sayfası (imzalı beta) | Test yükleyicisi (test sürümü) |
| SeedSigner | Dokunmatik düzeltmeli derleme | Builder'ın test derlemesi |

🌐 **Kern** ve **KISS Signer:** kontrol etmene gerek yok, aşağıdaki
bölümleri sana nasıl yapılacağını gösteriyor.

💻 **SeedSigner:** doğru derlemeyi indirmek için çipini şimdi `esptool`
([Adım 2](#step-2)) ile kontrol et:

1. Terminal'e kartın nerede olduğunu söyle. Bu, onu `$PORT` olarak kaydeder.

   🍎 Mac:

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux:

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. Çipi kontrol et:

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ `ESP32-P4 (revision v1.3)` ya da benzerini yazdırır: bu bir **v1.x**
   çiptir. `v3.0` ya da daha yükseği bir **v3.x** çiptir.

## Kern

1. [Kern web yükleyicisini](https://odudex.github.io/Kern/flash/)
   **Chrome** ya da **Brave** ile aç (Safari ve Firefox flaşlayamaz).
2. Kartı bağla ve **Latest CI Build** derlemesini flaşla. Yükleyici çipini
   okur ve v1.x ya da v3.x derlemesini kendisi seçer.
3. ✅ Çıkar, 3 saniye bekle, tekrar tak.

📦 Resmi [Kern sürümleri](https://github.com/odudex/Kern/releases)
yalnızca **v1.x** çipler için.

## KISS Signer

1. [Kurulum sayfasını](https://diybitcoin.github.io/kiss-signer/)
   **Chrome** ya da **Brave** ile aç, kartı bağla ve flaşla.
2. ✅ Çıkar, 3 saniye bekle, tekrar tak.
3. Ekran siyah mı kalıyor? Kartında daha yeni olan v3.x çip var: onun
   yerine [test yükleyicisinden](https://diybitcoin.github.io/kiss-signer/test/)
   tekrar flaşla.

⚠️ Test yükleyicisinde imzasız test sürümleri var, henüz gerçek bir v3.x
kartta denenmedi.

## SeedSigner

Bu kart için SeedSigner erken bir sürüm ve Terminal'den `esptool`
([Adım 2](#step-2)) ile flaşlanır.

### 1️⃣ Çipin için derlemeyi indir

**İndirilenler** klasörüne kaydet:

- **v1.x**:
  [seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**: `flashable-dist-waveshare-esp32-p4-35`, builder'ın
  [Actions derlemelerinden](https://github.com/kdmukai/seedsigner-micropython-builder/actions)
  (GitHub hesabıyla giriş gerekir)

<details>
<summary><b>👆 v1.x derlemesi hakkında</b></summary>

Bir **dokunmatik düzeltme** içerir: parmağını kaydırınca bir düğmeye
basılmaz, menüler kayar. Bu düzeltme hâlâ
[açık bir pull request](https://github.com/kdmukai/esp-board-common/pull/21),
henüz SeedSigner'ın parçası değil. v1.3 bir kartta test edildi.

Zip dosyasının SHA256 değeri:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ Zip dosyasını aç

İndirilenler klasörüne gir:

```bash
cd ~/Downloads
```

Sonra **kendi** derlemenin iki satırını çalıştır.

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

### 3️⃣ Flaşla

💡 [Adım 3](#step-3) bittikten sonra yeni bir Terminal açtıysan, önce
oradaki `PORT=` satırını tekrar çalıştır.

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ Bittiğinde kart yeniden başlar ve SeedSigner açılır. 🎉

<a id="battery"></a>

## 🔋 Pili tak

Kart küçük bir pil ve küçük bir tornavidayla gelir, böylece kablo olmadan
çalışabilir.

1. Kasanın arkasındaki dört küçük kapağı tırnaklarınla çıkar. Vidalar
   altlarında.
2. Dört vidayı tornavidayla sök ve kasanın arka kapağını kaldır.
3. Pilin fişini kartın sağ altındaki pil soketine tak. Yalnızca tek yönde
   girer, girmezse ters çevir. Zorlama.
4. ✅ Güç düğmesinin yanında yeşil bir ışık yanmalı.
5. Kasanın arka kapağını tekrar yerine koy ve vidala. Kasa, pil içerideyken
   normal şekilde kapanır.

![Kasası açık ve pili takılı Waveshare 3,5 inç kart](images/battery.jpg)

## 🩺 Sorun giderme

| Gördüğün | Ne yapmalı |
|---|---|
| Tarayıcıdaki yükleyici seçilecek bir kart göstermiyor | **Chrome** ya da **Brave**, **UART** portu ve bir veri kablosu kullan. 🐧 Linux'ta [Adım 2](#step-2)'yi yap. |
| `port is busy` ya da `could not open port` | Tarayıcıdaki yükleyici sekmesini ve kartı kullanan diğer Terminal'leri kapat, sonra tekrar dene. |
| Bilgisayar kartı görmüyor ya da 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory` hatası `PORT=` satırından sonra çıkıyor | **UART** portunu ve veri taşıdığını bildiğin bir kablo kullan. |
| 🐧 `Permission denied` ya da tarayıcı kartı açamıyor | Oturumu kapatıp yeniden açmak dahil, Linux'ta [Adım 2](#step-2)'yi yap. |
| 🍎 `brew: command not found` | Homebrew kurulumu bittiğinde yazdırdığı **Next steps** komutlarını çalıştır. |
| 🐧 `esptool: command not found` | Terminal'i kapat ve yenisini aç. |
| `flash_args` bulunamadı | Zip dosyasından çıkan klasörde değilsin: [SeedSigner'ın 2. adımını](#seedsigner-unzip) tekrar yap. |
| Flaşladıktan sonra ekranda hiçbir şey yok | Çıkar, 3 saniye bekle, tekrar tak. Derlemenin çipine uyduğunu kontrol et. |
| `esptool` çipi flaşlayamıyor | 5.3 ya da daha yeni olmalı: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Teşekkürler

- [Kern](https://github.com/odudex/Kern), odudex tarafından.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder), kdmukai tarafından.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer), DIYbitcoin tarafından.

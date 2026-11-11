# 📟 Signers DIY: SeedSigner, Kern y KISS Signer

Un signer (firmador) es un pequeño dispositivo sin conexión que guarda tus
claves de bitcoin y firma transacciones, así tus claves nunca tocan un
ordenador ni un móvil. Esta guía instala ("flashea") un signer de código
abierto en la
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm),
una placa con pantalla táctil de 3,5 pulgadas, cámara y ranura microSD.
Sin soldar.

📷 **Cómo se usa:** el signer nunca se conecta a internet. Lo emparejas
con una app de cartera como Sparrow escaneando códigos QR con su cámara, y
firmas las transacciones igual.

⚠️ **Los tres son versiones tempranas en esta placa. Usa solo palabras
semilla de práctica y testnet (bitcoin de práctica sin valor) o
cantidades muy pequeñas, nunca tus ahorros de verdad.**

### 🧰 Qué necesitas

- 📟 La placa Waveshare de 3,5 pulgadas
- 🔌 Un cable USB-C que transmita datos. Un cable solo de carga enciende
  la placa, pero el ordenador no ve nada.
- 💻 Un ordenador: Mac o Linux para SeedSigner; cualquier ordenador con
  **Chrome** o **Brave** para Kern y KISS Signer

### 👉 Elige tu signer

Cada uno es un camino completo, desde conectar la placa hasta la primera
pantalla del signer. Sigue solo el que elijas.

- 🌐 **[Kern](#kern):** el más fácil. Se flashea en Chrome o Brave con
  unos clics.
- 🌐 **[KISS Signer](#kiss):** también se flashea en Chrome o Brave.
- 💻 **[SeedSigner](#seedsigner):** se flashea escribiendo unos comandos
  en el Terminal. Cuatro páginas cortas.

<a id="kern"></a>

## 🌐 Kern

Necesitas **Chrome** o **Brave**. Safari y Firefox no pueden flashear.

1. Conecta el cable USB-C al puerto de la placa marcado **UART**, y el
   otro extremo a tu ordenador.

   🐧 **En Linux**, haz primero el [paso de Linux](#linux-kern) de abajo.

2. Abre el [instalador web de Kern](https://odudex.github.io/Kern/flash/).
3. Deja seleccionado **📦 Latest CI Build**.
4. En **Board**, elige **Waveshare ESP32-P4 3.5" (320×480 SPI)**. La
   lista empieza en otra placa, así que comprueba esta.
5. Haz clic en **🔌 Connect**. Se abre una ventanita: elige tu placa
   (🍎 su nombre lleva `usbmodem`, 🐧 `ttyACM`) y haz clic en
   **Conectar**.

   ✅ El recuadro negro bajo los botones muestra `Connected to: ESP32-P4`.
   El instalador lee tu chip y elige solo la versión v1.x o v3.x.

6. Haz clic en **⚡ Flash** y espera, sin desconectar.

   ✅ Muestra `Device reset. Flash successful! ✓`.

7. Desconecta la placa, espera 3 segundos y vuelve a conectarla.

   ✅ Kern arranca en la placa. 🎉

<details id="linux-kern">
<summary><b>🐧 Paso de Linux: deja que el navegador hable con la placa</b></summary>

1. Abre el **Terminal**: pulsa **Ctrl + Alt + T**.
2. Ejecuta:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Te pide la contraseña del ordenador. No se ve nada mientras
   escribes; es normal, escríbela y pulsa **Enter**.

3. **Cierra la sesión del ordenador y vuelve a entrar**, y sigue con el
   paso 2 de arriba.

</details>

📦 Las [versiones oficiales de Kern](https://github.com/odudex/Kern/releases)
solo sirven para chips **v1.x**, así que usa el instalador web.

Siguiente: [🔋 pon la batería](#battery), o
[🩺 problemas y soluciones](#troubleshooting) si algo salió mal.

<a id="kiss"></a>

## 🌐 KISS Signer

Necesitas **Chrome** o **Brave**. Safari y Firefox no pueden flashear.

1. Conecta el cable USB-C al puerto de la placa marcado **UART**, y el
   otro extremo a tu ordenador.

   🐧 **En Linux**, haz primero el [paso de Linux](#linux-kiss) de abajo.

2. Abre la [página de instalación de KISS Signer](https://diybitcoin.github.io/kiss-signer/).
3. En **Which board is it?**, elige **Waveshare 3.5in**.
4. Marca la casilla **This erases the device. I'm starting with keys that
   hold nothing.**
5. Haz clic en **Connect and install**. Se abre una ventanita: elige tu
   placa (🍎 su nombre lleva `usbmodem`, 🐧 `ttyACM`) y haz clic en
   **Conectar**.
6. En la siguiente ventana, haz clic en **Install KISS Signer (Waveshare
   3.5in)**. Marca **Erase device**, haz clic en **Next** y luego en
   **Install**. Espera, sin desconectar.

   ✅ Dice **Installation complete!**

7. Desconecta la placa, espera 3 segundos y vuelve a conectarla.

   ✅ La placa arranca en **FRUIT ISLAND**, el juego que KISS Signer
   muestra primero. 🎉

8. ¿La pantalla sigue negra? Tu placa tiene el chip nuevo **v3.x**, y esta
   versión es para v1.x. Flashéala otra vez desde el
   [instalador de prueba](https://diybitcoin.github.io/kiss-signer/test/):
   elige tu placa en **1. Your board** y haz clic en **Connect and
   install**. Lee tu chip y elige la versión que le va.

   ⚠️ El instalador de prueba tiene versiones de prueba sin firmar, aún
   no probadas en una placa v3.x real.

<details id="linux-kiss">
<summary><b>🐧 Paso de Linux: deja que el navegador hable con la placa</b></summary>

1. Abre el **Terminal**: pulsa **Ctrl + Alt + T**.
2. Ejecuta:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Te pide la contraseña del ordenador. No se ve nada mientras
   escribes; es normal, escríbela y pulsa **Enter**.

3. **Cierra la sesión del ordenador y vuelve a entrar**, y sigue con el
   paso 2 de arriba.

</details>

Siguiente: [🔋 pon la batería](#battery), o
[🩺 problemas y soluciones](#troubleshooting) si algo salió mal.

<a id="seedsigner"></a>

## 💻 SeedSigner 1: prepara tu ordenador

SeedSigner para esta placa es una versión temprana de Keith (kdmukai). Se
flashea escribiendo comandos en el **Terminal** con `esptool`, una
herramienta del fabricante del chip. Cada comando de abajo tiene un botón
**copiar**: cópialo, pégalo en el Terminal y pulsa **Enter**.

<details>
<summary><b>🍎 Uso Mac</b></summary>

1. Abre el **Terminal**: pulsa **Cmd + Espacio**, escribe `Terminal` y
   pulsa **Enter**.

2. Instala **Homebrew**, el instalador de apps de Mac para el Terminal.
   Sáltate esto si ya lo tienes (`brew --version` muestra una versión).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Te pide la contraseña del Mac. No se ve nada mientras escribes; es
   normal, escríbela y pulsa **Enter**.

   ⚠️ Al terminar, muestra **Next steps** con dos o tres comandos.
   Cópialos y ejecútalos también, o el Mac no encontrará `brew`.

3. Instala esptool:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Uso Linux</b></summary>

1. Abre el **Terminal**: pulsa **Ctrl + Alt + T**.

2. Deja que tu usuario hable con la placa:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Te pide la contraseña del ordenador. No se ve nada mientras
   escribes; es normal, escríbela y pulsa **Enter**.

3. Instala `esptool` y `unzip`, una línea cada vez:

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

4. **Cierra la sesión del ordenador y vuelve a entrar**, y abre el
   Terminal otra vez.

</details>

✅ Comprueba que esptool funciona:

```bash
esptool version
```

Tiene que mostrar **5.3 o superior**. Una más antigua no puede flashear
los chips nuevos: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`.

Siguiente: [SeedSigner 2: conecta la placa y mira tu chip](#ss-chip).

<a id="ss-chip"></a>

## 💻 SeedSigner 2: conecta la placa y mira tu chip

La placa viene con una de dos versiones de su chip, **v1.x** en placas
antiguas o **v3.x** en las nuevas, y cada una necesita su propia versión
de SeedSigner. Esta página te dice cuál tienes.

1. Conecta el cable USB-C al puerto de la placa marcado **UART**, y el
   otro extremo a tu ordenador.

2. Ejecuta la línea de tu ordenador:

   🍎 Mac:

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux:

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. Busca la línea que dice `ESP32-P4 (revision ...)`:

   - `revision v1.3` o cualquier **v1**: tienes un chip **v1.x**.
   - `revision v3.0`, `v3.2` o cualquier **v3**: tienes un chip **v3.x**.

   Apúntalo: la página siguiente te lo pide.

Siguiente: [SeedSigner 3: descarga la versión para tu chip](#ss-download).

<a id="ss-download"></a>

## 💻 SeedSigner 3: descarga la versión para tu chip

Usa **solo la versión para tu chip** de SeedSigner 2. La otra no
arranca.

<details open>
<summary><b>1️⃣ Mi chip es v1.x</b></summary>

Haz clic para descargar
[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
en tu carpeta **Descargas**.

Incluye un **arreglo táctil** para que deslizar el dedo recorra los menús
en vez de pulsar un botón. El arreglo es todavía un
[pull request abierto](https://github.com/kdmukai/esp-board-common/pull/21),
aún no forma parte de SeedSigner. Probado en una placa v1.3.

SHA256 del zip:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ Mi chip es v3.x</b></summary>

Las versiones de Keith salen de GitHub, y necesitas una cuenta gratuita
de GitHub para descargarlas.

1. Inicia sesión en [github.com/login](https://github.com/login), o crea
   una cuenta allí primero.
2. Abre
   [las últimas versiones de SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess).
3. Haz clic en la versión de **arriba** de la lista, que es la más nueva.
4. Baja hasta **Artifacts** y haz clic en
   **flashable-dist-waveshare-esp32-p4-35** para descargarla en tu
   carpeta **Descargas**.

   ⚠️ Comprueba que el nombre termina en **35**. Los de `43` son para la
   placa de 4,3 pulgadas, y el archivo de la página Releases de Keith
   también es el de 4,3 pulgadas. Ninguno arranca en esta placa.

</details>

Siguiente: [SeedSigner 4: descomprime y flashea](#ss-flash).

<a id="ss-flash"></a>

## 💻 SeedSigner 4: descomprime y flashea

1. Deja la placa conectada al puerto **UART**. Ejecuta **una** sola línea, la
   de tu ordenador y tu chip. Va a tu carpeta Descargas, descomprime la
   versión (o usa la carpeta si tu navegador ya la descomprimió) y la
   flashea.

   🍎 Mac, v1.x chip:

   ```bash
   cd ~/Downloads && { [ -f seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip ] && unzip -oq seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip; cd seedsigner-waveshare-p4-35-pre-rev3-touchfix; } && esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🍎 Mac, v3.x chip:

   ```bash
   cd ~/Downloads && { [ -f flashable-dist-waveshare-esp32-p4-35.zip ] && unzip -oq flashable-dist-waveshare-esp32-p4-35.zip -d flashable-dist-waveshare-esp32-p4-35; cd flashable-dist-waveshare-esp32-p4-35; } && esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux, v1.x chip:

   ```bash
   cd ~/Downloads && { [ -f seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip ] && unzip -oq seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip; cd seedsigner-waveshare-p4-35-pre-rev3-touchfix; } && esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux, v3.x chip:

   ```bash
   cd ~/Downloads && { [ -f flashable-dist-waveshare-esp32-p4-35.zip ] && unzip -oq flashable-dist-waveshare-esp32-p4-35.zip -d flashable-dist-waveshare-esp32-p4-35; cd flashable-dist-waveshare-esp32-p4-35; } && esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   Tarda unos 4 minutos, así que no desconectes la placa. Unos avisos
   `Deprecated` al principio son normales.

   ✅ Al terminar, la placa se reinicia en SeedSigner. 🎉

2. Pruébalo con datos de prueba: abre [testqrs.com](https://testqrs.com)
   en tu ordenador y apunta la cámara de la placa a sus códigos QR. Sus
   palabras semilla son públicas, así que úsalas solo para practicar.

Siguiente: [🔋 pon la batería](#battery), o
[🩺 problemas y soluciones](#troubleshooting) si algo salió mal.

<a id="battery"></a>

## 🔋 Pon la batería

La placa viene con una batería pequeña y un destornillador pequeño, para
que pueda funcionar sin cable.

1. En la parte de atrás de la carcasa, quita con las uñas las cuatro
   tapitas. Los tornillos están debajo.
2. Desatornilla los cuatro tornillos con el destornillador y levanta la
   tapa trasera de la carcasa.
3. Conecta el enchufe de la batería en el conector de batería, abajo a la
   derecha de la placa. Solo entra en una posición, así que si no entra,
   dale la vuelta. No lo fuerces.
4. ✅ Debería encenderse una luz verde junto al botón de encendido.
5. Vuelve a poner la tapa trasera y atorníllala. La carcasa cierra
   normalmente con la batería dentro.

![Placa Waveshare de 3,5 pulgadas con la carcasa abierta y la batería conectada](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 Problemas y soluciones

| Ves | Qué hacer |
|---|---|
| La ventana del navegador no muestra ninguna placa para elegir | Usa **Chrome** o **Brave**, el puerto **UART** y un cable de datos. 🐧 Haz el paso de Linux en la página de [Kern](#linux-kern) o de [KISS Signer](#linux-kiss). |
| `port is busy` o `could not open port` | Cierra la pestaña del instalador web y cualquier otro Terminal que use la placa, e inténtalo de nuevo. |
| 🍎 `no matches found: /dev/cu.usbmodem*` o 🐧 `No such file or directory` | El ordenador no ve la placa: usa el puerto **UART** y un cable que sepas que transmite datos. |
| 🐧 `Permission denied`, o el navegador no puede abrir la placa | Haz el paso de Linux, incluido cerrar la sesión y volver a entrar. |
| `Failed to connect` | Cierra lo que esté usando la placa, desconéctala, vuelve a conectarla e inténtalo de nuevo. |
| 🍎 `brew: command not found` | Ejecuta los comandos de **Next steps** que mostró Homebrew al terminar de instalarse. |
| 🐧 `esptool: command not found` | Cierra la sesión y vuelve a entrar, y abre un Terminal nuevo. |
| `requires chip revision in range` | La versión no coincide con tu chip: repite [SeedSigner 2](#ss-chip) y descarga la otra versión. |
| `cd: no such file or directory: …` or `cd: …: No such file or directory` | La versión no está en tu carpeta Descargas, o es la del otro chip: repite [SeedSigner 3](#ss-download). |
| No sale nada en la pantalla tras flashear | Desconecta, espera 3 segundos y vuelve a conectar. Comprueba que la versión coincide con tu chip. |
| `esptool` no puede flashear el chip | Tiene que ser la 5.3 o superior: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Créditos

- [Kern](https://github.com/odudex/Kern) de odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) de kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) de DIYbitcoin.

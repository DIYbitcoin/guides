# 📟 Signers DIY: SeedSigner, Kern y KISS Signer

Un signer (firmador) es un pequeño dispositivo sin conexión que guarda tus
claves de bitcoin y firma transacciones, así tus claves nunca tocan un
ordenador ni un móvil. Esta guía instala ("flashea") un signer de código
abierto en la
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm),
una placa con pantalla táctil de 3,5 pulgadas, cámara y ranura microSD.
Sin soldar.

| Signer | Cómo se flashea | Estado |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | En tu navegador | Versiones de prueba |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | En tu navegador | Beta |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | En el Terminal | Versiones de prueba |

📷 **Cómo se usa:** el signer nunca se conecta a internet. Lo emparejas
con una app de cartera como Sparrow escaneando códigos QR con su cámara, y
firmas las transacciones igual.

⚠️ **Los tres son versiones tempranas en esta placa. Usa solo palabras
semilla de práctica y testnet (bitcoin de práctica sin valor) o
cantidades muy pequeñas, nunca tus ahorros de verdad.**

## 🔌 Paso 1: conecta la placa

Conecta un cable USB-C al puerto marcado **UART**.

💡 Usa un cable que sepas que transmite datos. Un cable solo de carga da
corriente a la placa, pero el ordenador no ve nada.

<a id="step-2"></a>

## 💻 Paso 2: prepara tu ordenador

🌐 **Kern** y **KISS Signer** se flashean desde un instalador web en
**Chrome** o **Brave**, así que para ellos solo necesitas ese navegador
(en Linux, haz también el paso 2 del bloque de Linux de abajo).

💻 **SeedSigner** se flashea desde el Terminal con `esptool`: instálalo
abajo.

<details>
<summary><b>🍎 Uso Mac</b></summary>

1. Abre el **Terminal**: pulsa **Cmd + Espacio**, escribe `Terminal` y
   pulsa **Enter**.

2. Instala **Homebrew**, el instalador de apps de Mac para el Terminal.
   Sáltate esto si ya lo tienes (`brew --version` muestra una versión).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Te pide la contraseña de tu Mac. No se ve nada mientras escribes;
   es normal, escríbela y pulsa **Enter**.

   ⚠️ Al terminar, muestra **Next steps** con dos o tres comandos.
   Cópialos y ejecútalos también, o el Mac no encontrará `brew`.

3. Instala esptool:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Uso Linux</b></summary>

1. Abre el **Terminal**: pulsa **Ctrl + Alt + T**, o busca **Terminal**
   en tus apps.

2. Da permiso a tu usuario para hablar con la placa (necesario para los
   tres signers, también para Kern en el navegador):

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 Te pide la contraseña de tu ordenador. No se ve nada mientras
   escribes; es normal, escríbela y pulsa **Enter**.

3. **Cierra la sesión del ordenador y vuelve a entrar**, para que se
   aplique el cambio.

4. Para SeedSigner, instala `esptool` y `unzip`. Abre el Terminal otra vez
   y ejecuta:

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

5. **Cierra el Terminal y abre uno nuevo**, para que encuentre `esptool`.

</details>

✅ Para SeedSigner, comprueba que esptool funciona:

```bash
esptool version
```

Tiene que mostrar **5.3 o superior**.

<a id="step-3"></a>

## 🧬 Paso 3: conoce tu chip

La placa de 3,5 pulgadas viene con una de dos versiones del chip ESP32-P4:
**v1.x** en las placas antiguas, o **v3.x** en las nuevas, así que una
placa comprada hace poco probablemente tiene v3.x. Cada signer necesita la
versión hecha para tu chip: una versión v1.x no arranca en un chip v3.x,
ni al revés.

| Signer | Chip v1.x | Chip v3.x |
|---|---|---|
| Kern | Instalador web, o la versión oficial | Solo instalador web (versión de prueba) |
| KISS Signer | Página de instalación (beta firmada) | Instalador de prueba (versión de prueba) |
| SeedSigner | Versión con arreglo táctil | Versión de prueba del builder |

🌐 **Kern** y **KISS Signer:** no hace falta comprobarlo, sus apartados de
abajo te explican cómo.

💻 **SeedSigner:** compruébalo ahora con `esptool` ([Paso 2](#step-2)),
para descargar la versión correcta:

1. Dile al Terminal dónde está la placa. Esto lo guarda como `$PORT`.

   🍎 Mac:

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux:

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. Comprueba el chip:

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ Muestra `ESP32-P4 (revision v1.3)` o parecido: es un chip **v1.x**.
   `v3.0` o superior es un chip **v3.x**.

## Kern

1. Abre el [instalador web de Kern](https://odudex.github.io/Kern/flash/)
   en **Chrome** o **Brave** (Safari y Firefox no pueden flashear).
2. Conecta la placa y flashea **Latest CI Build**. El instalador lee tu
   chip y elige solo la versión v1.x o v3.x.
3. ✅ Desconecta, espera 3 segundos y vuelve a conectar.

📦 Las [versiones oficiales de Kern](https://github.com/odudex/Kern/releases)
solo sirven para chips **v1.x**.

## KISS Signer

1. Abre la [página de instalación](https://diybitcoin.github.io/kiss-signer/)
   en **Chrome** o **Brave**, conecta la placa y flashea.
2. ✅ Desconecta, espera 3 segundos y vuelve a conectar.
3. ¿La pantalla sigue en negro? Tu placa tiene el chip nuevo v3.x:
   flashéala otra vez desde el
   [instalador de prueba](https://diybitcoin.github.io/kiss-signer/test/).

⚠️ El instalador de prueba tiene versiones de prueba sin firmar, aún no
probadas en una placa v3.x real.

## SeedSigner

SeedSigner para esta placa es una versión temprana, que se flashea desde
el Terminal con `esptool` ([Paso 2](#step-2)).

### 1️⃣ Descarga la versión para tu chip

Guárdala en tu carpeta **Descargas**:

- **v1.x**:
  [seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**: `flashable-dist-waveshare-esp32-p4-35` de las
  [compilaciones de Actions](https://github.com/kdmukai/seedsigner-micropython-builder/actions)
  del builder (necesita iniciar sesión en GitHub)

<details>
<summary><b>👆 Sobre la versión v1.x</b></summary>

Incluye un **arreglo táctil** para que al deslizar el dedo los menús se
desplacen en lugar de pulsar un botón. El arreglo aún es una
[pull request abierta](https://github.com/kdmukai/esp-board-common/pull/21),
todavía no forma parte de SeedSigner. Probado en una placa v1.3.

SHA256 del zip:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ Descomprímela

Entra en Descargas:

```bash
cd ~/Downloads
```

Luego ejecuta las dos líneas de **tu** versión.

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

### 3️⃣ Flashea

💡 Si has abierto un Terminal nuevo desde el [Paso 3](#step-3), ejecuta
antes otra vez su línea `PORT=`.

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ Al terminar, la placa se reinicia en SeedSigner. 🎉

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

## 🩺 Problemas y soluciones

| Ves | Qué hacer |
|---|---|
| El instalador web no muestra ninguna placa para elegir | Usa **Chrome** o **Brave**, el puerto **UART** y un cable de datos. 🐧 Haz el [Paso 2](#step-2) en Linux. |
| `port is busy` o `could not open port` | Cierra la pestaña del instalador web y cualquier otro Terminal que use la placa, e inténtalo de nuevo. |
| El ordenador no ve la placa, o 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory` tras la línea `PORT=` | Usa el puerto **UART** y un cable que sepas que transmite datos. |
| 🐧 `Permission denied`, o el navegador no puede abrir la placa | Haz el [Paso 2](#step-2) en Linux, incluido cerrar la sesión y volver a entrar. |
| 🍎 `brew: command not found` | Ejecuta los comandos de **Next steps** que mostró Homebrew al terminar de instalarse. |
| 🐧 `esptool: command not found` | Cierra el Terminal y abre uno nuevo. |
| No encuentra `flash_args` | No estás en la carpeta descomprimida: repite el [paso 2 de SeedSigner](#seedsigner-unzip). |
| No sale nada en la pantalla tras flashear | Desconecta, espera 3 segundos y vuelve a conectar. Comprueba que la versión coincide con tu chip. |
| `esptool` no puede flashear el chip | Tiene que ser la 5.3 o superior: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 Créditos

- [Kern](https://github.com/odudex/Kern) de odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) de kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) de DIYbitcoin.

# 💳 Flashear javacards: Satochip, Satodime, SeedKeeper y Keycard

Una javacard tiene un **elemento seguro**: un chip hecho para guardar
secretos como tus claves de bitcoin, aunque alguien se haga con la
tarjeta. Puedes instalarle pequeñas apps, llamadas **applets**. Esta guía
instala uno de cuatro applets de bitcoin de código abierto en una javacard
J3H145 vacía:

- **Satochip:** una cartera de bitcoin DIY que usas con Sparrow Wallet
- **Satodime:** una tarjeta al portador: quien tiene la tarjeta es dueño
  del bitcoin que guarda, como el efectivo
- **SeedKeeper:** una copia de seguridad cifrada, protegida con un PIN,
  para tus palabras semilla y passphrase, contraseñas, descriptores de
  cartera y otros secretos
- **Keycard:** otra cartera de bitcoin DIY que usas con Sparrow Wallet

Los applets son de [Satochip](https://satochip.io) y
[Keycard](https://keycard.tech), y esta guía sigue la guía
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY).

![Ordenador, lector, tarjeta y los cuatro applets](images/00-overview.svg)

## 🧰 Qué necesitas

- 💳 Una javacard **J3H145**. Si quieres comprar, estas son las que se
  usaron para esta guía: [J3H145 en Alibaba](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 Un **lector de tarjetas inteligentes USB**: una cajita que conectas
  al ordenador y en la que metes la tarjeta, con el chip por delante. No
  uses el **ACS ACR122U**, un lector NFC en el que apoyas la tarjeta: la
  [guía Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)
  avisa de que falla al instalar applets y puede estropear la tarjeta. Sí
  sirve para usar la tarjeta una vez instalado el applet.
- 🛡️ ¿Tienes un [ShieldSigner](https://github.com/3rdIteration/seedsigner)?
  También puede instalar estos applets en la tarjeta, en lugar de un
  ordenador y un lector.
- 💻 Un ordenador **Mac**, **Linux** (Debian, Ubuntu o Raspberry Pi OS) o
  **Windows**
- 🌐 Conexión a internet para las descargas

## 🛠️ Paso 1: prepara tu ordenador (solo una vez)

Esto instala **Java** y **GlobalPlatformPro**, el comando `gp` que pone
applets en la tarjeta.

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

3. Instala Java:

   ```bash
   brew install --cask temurin
   ```

4. Instala GlobalPlatformPro:

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 Uso Linux</b></summary>

1. Abre el **Terminal**: pulsa **Ctrl + Alt + T**, o busca **Terminal**
   en tus apps.

2. Instala Java, git y el servicio de tarjetas inteligentes (`pcscd`):

   ```bash
   sudo apt update
   ```

   🔒 Te pide la contraseña de tu ordenador. No se ve nada mientras
   escribes; es normal, escríbela y pulsa **Enter**.

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. Descarga los archivos de
   [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY), que
   incluyen GlobalPlatformPro como `gp.jar`:

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. Dale el nombre corto `gp`, para que todos los comandos de esta guía
   funcionen tal cual:

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. **Cierra el Terminal y abre uno nuevo**, para que coja el nombre nuevo.

</details>

<details>
<summary><b>🪟 Uso Windows</b></summary>

⚠️ No está probado en Windows.

1. Instala Java: descarga **Temurin** desde <https://adoptium.net> e
   instálalo con las opciones por defecto.

2. Descarga
   [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)
   de [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) en tu
   carpeta **Descargas**.

3. Abre el **Símbolo del sistema**: pulsa la tecla **Windows**, escribe
   `cmd` y pulsa **Enter**. No hace falta ejecutarlo como administrador.

4. Entra en tu carpeta Descargas:

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. Donde esta guía pone `gp`, escribe `gp.exe`, por ejemplo
   `gp.exe --info`. Donde pone `cd ~/Downloads`, usa la línea del paso 4.

</details>

✅ Comprueba que funciona:

```bash
gp --version
```

Funciona si muestra unas líneas que empiezan por `#` y ningún
`command not found`.

💡 Java muestra unas líneas que empiezan por `WARNING:` cada vez que se
ejecuta `gp`. Son normales y puedes ignorarlas.

## 🔌 Paso 2: comprueba que el lector ve la tarjeta

Conecta el lector, mete la tarjeta (con el chip por delante) y ejecuta:

```bash
gp --info
```

✅ Si ves líneas `CPLC` y `Global Platform card`, el lector y la tarjeta
se entienden:

![Salida de gp --info](images/01-gp-info.svg)

Ahora mira qué hay ya en la tarjeta:

```bash
gp --list
```

Una tarjeta vacía solo muestra lo que trae de fábrica: el gestor de la
tarjeta (`ISD`) y un paquete (`A0000001515350`). No los toques.

![gp --list en una tarjeta vacía](images/02-gp-list-blank.svg)

### 🛑 Para si ves "Card cryptogram invalid"

![Aviso Card cryptogram invalid](images/07-gp-cryptogram-stop.svg)

⚠️ Significa que la tarjeta está bloqueada con claves que no tienes.
**No vuelvas a ejecutar el comando**: tras unos pocos intentos fallidos la
tarjeta se bloquea para siempre. Pide las claves a quien te vendió la
tarjeta.

## 📥 Paso 3: descarga el archivo del applet

Descarga el archivo del applet que quieras en tu carpeta **Descargas**.
En cada página, haz clic en el nombre del archivo bajo **Assets** para
descargarlo.

| Applet | Archivo a descargar | Se configura con |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 ¿Por qué estas versiones y no las más nuevas?</b></summary>

- **Satochip v0.15, no v0.16:** la v0.16 (octubre de 2026) cambió cómo la
  tarjeta ajusta las claves taproot, y Sparrow 2.5.5 aún envía el formato
  antiguo, así que una cartera taproot en Sparrow no puede firmar con una
  tarjeta v0.16. Native SegWit funciona con las dos.
- **Satodime v0.1-0.2** es la última versión estable; las v0.2 siguen
  marcadas como beta.
- **Keycard 3.2, no 4.0:** Keycard 4.0 (septiembre de 2026) cambió cómo la
  tarjeta y el ordenador establecen su conexión cifrada, y Sparrow 2.5.5
  aún usa la forma antigua.

Usa las versiones nuevas cuando Sparrow se ponga al día.

</details>

<details>
<summary><b>🔍 Opcional: comprueba que el archivo no ha sido manipulado</b></summary>

Entra en tu carpeta Descargas:

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

El código largo que muestra tiene que coincidir con el de la página de la
versión:

- `SatoChip-v0.15-0.1.cap`:
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`:
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ Paso 4: pon el applet en la tarjeta

🔄 ¿Quieres cambiar el applet de tu tarjeta por otro? Quita primero el
antiguo y luego instala el nuevo: mira
[Cambiar a otro applet](#switching) más abajo.

Entra en tu carpeta Descargas, donde está el archivo:

```bash
cd ~/Downloads
```

Luego ejecuta **solo la línea de tu applet**:

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

Keycard (los ajustes extra lo convierten en la cartera que busca Sparrow):

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ Termina con una línea `loaded:`:

![Salida de gp --install](images/03-gp-install-satochip.svg)

![gp --install para Keycard](images/09-gp-install-keycard.svg)

✅ Ejecuta `gp --list` otra vez. Una línea nueva que empieza por `APP` y
termina en `SELECTABLE` significa que el applet está instalado y listo:

![gp --list con Satochip instalado](images/04-gp-list-satochip.svg)

Ahora ve al paso de configuración de tu applet:

- Satochip: [Paso 5a](#step-5a)
- SeedKeeper o Satodime: [Paso 5b](#step-5b)
- Keycard: [Paso 5c](#step-5c)

<a id="step-5a"></a>

## Paso 5a: configura Satochip en Sparrow

👛 Descarga Sparrow Wallet para tu ordenador desde
<https://sparrowwallet.com/download/>.

El PIN y las 12 palabras semilla se ponen directamente desde Sparrow; no
hace falta la app de Satochip. Deja la tarjeta en el lector todo el rato.

1. Crea una cartera nueva en Sparrow y ponle un nombre. En **Keystores**,
   elige **Connected Hardware Wallet** y pulsa **Scan...**

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow encuentra el **Satochip**. Pulsa **Import Keystore**.

   ![Sparrow encuentra el Satochip](images/sparrow-2-found.webp)

3. Sparrow te pide **Choose a PIN code**. Escribe un PIN.

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow dice **Card not initialized**. Escribe el mismo PIN otra vez en
   la casilla y pulsa **Initialize**.

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. Se abre la ventana **Satochip Seed Words**. Pulsa **Generate New** para
   crear 12 palabras semilla nuevas, o escribe 12 palabras semilla que ya
   tengas. ✍️ **Apunta las 12 palabras semilla en papel** y pulsa **OK**.
   Sparrow las carga en la tarjeta, sin passphrase.

   💡 Sparrow crea estas 12 palabras semilla en tu ordenador, así que para
   ahorros de verdad usa un ordenador de confianza.

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**. Pulsa **OK**.

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. De vuelta en la línea del Satochip, pulsa **Import Keystore** otra vez.

   ![Import Keystore otra vez](images/sparrow-7-import-keystore.webp)

8. 🎉 La cartera muestra ahora tu Satochip, con su huella (fingerprint):
   un código corto que identifica tus claves. Pulsa **Apply** para guardar
   la cartera.

   ![La cartera terminada en Sparrow](images/sparrow-8-wallet.webp)

🔢 Sparrow da a la tarjeta **5 intentos de PIN**. Desde el keystore también
puedes cambiar el PIN después con **Change Pin**.

<a id="step-5b"></a>

## Paso 5b: configura SeedKeeper o Satodime en Sato-Tools

🧰 Descarga Sato-Tools para tu ordenador desde
<https://github.com/Toporin/Satochip-Utils/releases> (esta guía usó la
0.4.0-beta).

Abre Sato-Tools con la tarjeta en el lector y pulsa **Let's go!**

![Pantalla de bienvenida de Sato-Tools](images/satotools-0-welcome.webp)

💡 **Si Sato-Tools no ve una tarjeta que acabas de flashear, cierra la app
y vuelve a abrirla.**

### SeedKeeper

1. Sato-Tools dice **Your SeedKeeper is connected**. Pulsa
   **Setup my card**.

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. Escribe un PIN en **New PIN code** y otra vez en **Confirm PIN code**
   (de 4 a 16 caracteres) y pulsa **Save PIN**.

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** Pulsa **Ok**.

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 El menú ya tiene **My secrets**, **Generate secret**,
   **Import secret**, **Backup card** y **Card logs**. Tu SeedKeeper está
   listo para guardar palabras semilla, passphrases, contraseñas y
   descriptores de cartera.

   ![SeedKeeper listo](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools dice **Your Satodime is connected**, con tres bóvedas:
   **Vault #0**, **Vault #1** y **Vault #2**.

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview** muestra las tres bóvedas como **uninitialized**: todavía no
   hay nada sellado en ellas.

   ![Mis bóvedas](images/satotools-satodime-2-vaults.png)

3. **About** muestra la versión del applet y **Card has no owner**.
   **Take ownership** hace a este ordenador dueño de la tarjeta, algo que
   necesitas antes de sellar una bóveda.

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 Pulsa **Take ownership**. About dice ahora **You are the owner**, y
   el botón cambia a **Transfer ownership**, que usas cuando das la
   tarjeta a otra persona.

💡 **Sellar** una bóveda crea una dirección de bitcoin para ella, con su
clave privada escondida dentro de la tarjeta. **Desellar** muestra la
clave privada para poder mover el bitcoin. Antes de aceptar un Satodime
como pago, comprueba que sus bóvedas siguen selladas.

### 📱 En tu móvil

SeedKeeper y Satodime también tienen apps para el móvil. Apoya la tarjeta
en la parte de atrás de un móvil con NFC para usarla ahí.

### ❓ ¿Por qué dice "Genuine: NO"?

Las tarjetas compradas a Satochip traen de fábrica un certificado de
Satochip, y **Genuine** lo comprueba. Una tarjeta DIY que has flasheado tú
no tiene ese certificado, así que siempre muestra **Genuine: [NO]**. El
applet funciona exactamente igual.

<a id="step-5c"></a>

## Paso 5c: configura Keycard en Sparrow

👛 Descarga Sparrow Wallet para tu ordenador desde
<https://sparrowwallet.com/download/> si aún no lo tienes.

Keycard se configura igual que Satochip, con dos diferencias: el PIN tiene
**exactamente 6 cifras**, y la tarjeta solo permite **3 PIN erróneos**.

1. Crea una cartera nueva, elige **Connected Hardware Wallet** y pulsa
   **Scan...** Sparrow encuentra la **Keycard**. Pulsa **Import Keystore**.

   ![Sparrow encuentra la Keycard](images/keycard-1-found.webp)

2. Elige un PIN de **exactamente 6 cifras**. Si es más corto o más largo
   sale **PIN length must be between 6 and 6 characters**.

   ![El PIN debe tener 6 cifras](images/keycard-2-pin-error.webp)

3. Escribe el mismo PIN otra vez y pulsa **Initialize**. Se abre la misma
   ventana de palabras semilla que con Satochip ([Paso 5a](#step-5a),
   número 5): pulsa **Generate New** o escribe las tuyas,
   ✍️ **apunta las 12 palabras semilla** y pulsa **OK**.
   ✅ **Card Initialized**: pulsa **OK**.

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 Pulsa **Import Keystore** otra vez. La cartera muestra ahora tu
   Keycard, con su huella. Pulsa **Apply** para guardar la cartera.

   ![La cartera Keycard terminada en Sparrow](images/keycard-4-wallet.webp)

⚠️ **Tus 12 palabras semilla son la única forma de recuperarla.** Tras 3
PIN erróneos la Keycard se bloquea, y Sparrow no ofrece forma de
desbloquearla. Si pasa, quita Keycard, instálala de nuevo y vuelve a
escribir tus 12 palabras semilla en Sparrow.

<a id="switching"></a>

## 🔄 Cambiar a otro applet

⚠️ **Quitar un applet borra todo lo que guarda**, incluida la semilla de
un Satochip o una Keycard y los secretos de un SeedKeeper. Hazlo solo si
tienes apuntadas las 12 palabras semilla o si la cartera es de práctica.
La tarjeta no sufre y se puede flashear tantas veces como quieras.

![Desinstalar borra, instalar empieza de cero](images/08-swap.svg)

1. Entra en tu carpeta Descargas, donde están los archivos de los applets:

   ```bash
   cd ~/Downloads
   ```

2. ¿No sabes qué hay en la tarjeta? Ejecuta `gp --list`: la línea `APP`
   lo dice.

3. Quita el applet que tiene ahora la tarjeta. Ejecuta **solo su línea**:

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

4. ✅ Ejecuta `gp --list` otra vez. Solo deberían quedar el gestor de la
   tarjeta (`ISD`) y el paquete de fábrica (`A0000001515350`).

5. Instala el applet nuevo ([Paso 4](#step-4)) y configúralo
   ([Paso 5a](#step-5a), [5b](#step-5b) o [5c](#step-5c)).

Por ejemplo:

![Cambiar Satochip por Satodime](images/05-gp-swap-to-satodime.svg)

![Cambiar Satodime por SeedKeeper](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 Problemas y soluciones

| Ves | Qué hacer |
|---|---|
| 🍎 `brew: command not found` | Ejecuta los comandos de **Next steps** que mostró Homebrew al terminar de instalarse. |
| 🐧 `gp: command not found` | Cierra el Terminal y abre uno nuevo. |
| `No such file` o `does not exist` | Ejecuta antes `cd ~/Downloads` y comprueba que el archivo está en Descargas. |
| `Card cryptogram invalid` | La tarjeta está bloqueada con otras claves. Para, no lo vuelvas a intentar. |
| `LOAD failed: 0x6438` | El applet se hizo para una Java Card más nueva que la de la tarjeta. |
| `INSTALL ... failed: 0x6444` o `0x6F00` | A la tarjeta le falta una función que necesita el applet. |
| `gp --install` falla en una tarjeta que ya tiene un applet | Ejecuta `gp --list`. Si hay una línea `APP`, quita antes ese applet (mira [Cambiar a otro applet](#switching)). |
| **Scan...** de Sparrow no encuentra nada | Cierra Sato-Tools y cualquier otra app de tarjetas, saca la tarjeta y vuelve a meterla, y pulsa **Scan...** otra vez. |
| Has olvidado el PIN, o la tarjeta se ha bloqueado por demasiados PIN erróneos | Quita el applet e instálalo de nuevo (esto borra la tarjeta) y luego restaura tus 12 palabras semilla. |
| Sato-Tools no ve la tarjeta | Cierra Sato-Tools y vuelve a abrirlo. |
| `gp` no encuentra el lector, o dice que no hay tarjeta | Mete la tarjeta con el chip por delante. Si sigue diciendo que no hay tarjeta, el lector se ha atascado: desconéctalo del ordenador, vuelve a conectarlo con la tarjeta dentro e inténtalo de nuevo. 🐧 Luego ejecuta `sudo systemctl restart pcscd`. |

## 🚀 Para ir más allá

- 🎥 [Vídeo: flashear 7 applets distintos en estas tarjetas](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 Bloquea la tarjeta con tu propia clave, para que nadie más pueda
  cambiar lo que tiene instalado:
  [Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ Compila los applets tú mismo. Los archivos de arriba los compiló otra
  persona, así que confías en que coinciden con el código fuente público.
  Si los compilas tú desde el código fuente, lo que va en tu tarjeta es
  exactamente el código que cualquiera puede leer:
  [Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 Créditos

- 🙌 [Toporin](https://github.com/Toporin), creador de
  [Satochip](https://satochip.io), por los applets Satochip, Satodime y
  SeedKeeper y por Sato-Tools.
- 🛠️ [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) de
  3rdIteration, la guía DIY que sigue esta, con sus listas de tarjetas y
  lectores probados y `gp.jar`.
- [Keycard](https://github.com/keycard-tech/status-keycard) por el applet
  Keycard.
- 💳 [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)
  de Martin Paljak para cargar applets en la tarjeta.

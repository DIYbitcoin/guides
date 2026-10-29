# 🖥️ Monta tu propio nodo de bitcoin

Tu propio nodo comprueba él mismo todas las reglas de bitcoin, así no
tienes que fiarte del servidor de nadie. Estas guías te ayudan a montar
uno en casa.

Esta guía es una versión en español de las charlas sobre nodos de
[Satoshi Spritz](https://satoshispritz.it/) en Italia (ver
[Créditos](#credits)): primero un repaso del software y el hardware
para nodos, después el montaje paso a paso en un ODROID-M1 con Bitcoin
Core, Tor y electrs.

## 🤔 ¿Por qué un nodo de bitcoin?

- Comprobar tú mismo toda la blockchain de bitcoin
- Comprobar el saldo de tus carteras sin terceros
- Emitir tus propias transacciones
- La máxima privacidad, sin confiar en servicios externos

## 📦 Software para nodos

Opciones ya hechas:

| Software | Precio | Características |
|---|---|---|
| Umbrel | Software gratis, hardware de 100 a 300 € | El más fácil de usar, tienda de apps, estilo Apple |
| MyNode | Gratis / 79 € premium / de 399 a 599 € ya montado | Nodo + Lightning + Electrum + explorador de bloques |
| Start9 | De 699 a 899 € listo para enchufar / software gratis para DIY | Código abierto, Tor integrado, sin telemetría |

Lo que necesitan todas:

- Blockchain: unos 670+ GB (2025), crece alrededor de 1 GB cada pocos
  días
- Mínimo 4 GB de RAM, SSD de 1 TB (2 TB recomendado)
- Primera sincronización: de 2 a 7 días

**Ya hechas:** se instalan rápido y tienen interfaz gráfica, pero Docker,
los scripts y las comprobaciones de seguridad quedan ocultos, y el
software extra es difícil de vigilar.

**DIY (manual):** menos software es menos superficie de ataque, control
total sobre cada pieza, y aprendes a mantenerlo.

## 🔧 Elegir el hardware

Mínimo:

- Al menos 2 GB de RAM (4 GB recomendado)
- Un disco de al menos 1 TB (2 TB recomendado)
- Una CPU de varios núcleos (cualquier CPU reciente)

Opciones:

- **PC viejo:** no cuesta nada; quita la batería si es un portátil
- **Raspberry Pi:** caro, y un disco USB es menos estable
- **ODROID-M1:** ARM, con una ranura interna para el SSD
- **Thin client (de 30 a 100 €):** Fujitsu Futro S920, HP t620

Como sistema operativo usa **Debian 13**, un Linux con soporte de largo
plazo durante años. Actualiza todos los paquetes, instala `tor` y `ssh`
para mantener el nodo a distancia, y se recomienda un SAI (batería de
respaldo).

# 🛠️ Monta un nodo en un ODROID-M1

## 🧰 Qué necesitas

- ODROID-M1
- SSD NVMe (mejor de 2 TB)
- Router
- Cable Ethernet
- Monitor con cable HDMI
- Ratón y teclado USB

## 🔩 Paso 1: móntalo

1. Coloca el SSD NVMe en el ODROID-M1.
2. Conecta todos los cables: ratón y teclado, monitor, cable Ethernet.
3. Conecta el cable de alimentación para encender el ODROID-M1.

## 🐧 Paso 2: descarga Debian 12

1. Cuando el ODROID-M1 haya arrancado, ve a **Exit to shell** en
   Petitboot y pulsa **Enter**.
2. Escribe `udhcpc` y pulsa **Enter** (puede ayudar a resolver
   problemas de conexión).
3. Escribe `netboot_default` y pulsa **Enter**.

   ⌨️ El teclado es americano: para escribir `_` mantén **Shift** y
   pulsa la tecla al lado del **0**.

4. Escribe `exit` y pulsa **Enter**.
5. La pantalla de inicio de Petitboot muestra ahora la lista de imágenes
   disponibles. Ve a **Debian 12 (Bookworm) Netboot installer** y pulsa
   **Enter**.

## 💿 Paso 3: instala Debian 12

1. **Idioma:** elige **English**, es más fácil de seguir (las
   traducciones pueden no estar bien).
2. **Ubicación:** elige tu país (para Italia: **other**, luego
   **Europe**, luego **Italy**).
3. **Configuración regional:** puedes quedarte con inglés eligiendo
   **United Kingdom**.
4. **Teclado:** elige el teclado que tengas (para un teclado QWERTY
   italiano, **Italian**).
5. **Hostname:** elige un nombre de equipo. El dominio puede quedar
   vacío.
6. **País del mirror:** el país desde el que se descarga Debian (en
   Italia, **Italy**).
7. **Mirror:** en Italia, `debian.mirror.garr.it` suele ser rápido y
   fiable.
8. **Proxy:** si no tienes proxy, déjalo vacío y pulsa **Enter**.
9. **Contraseña de root:** pon la contraseña del superusuario y pulsa
   **Enter**.
10. **Usuario:** inventa un nombre para el usuario normal (no admin) (no
    hace falta poner nombre completo) y ponle contraseña.
11. **Disco:** elige **Guided: use entire disk** y luego el disco NVMe
    que colocaste.
12. **Particiones:** deja todo en una sola partición: **All files in one
    partition (recommended for new users)**.
13. Debian crea una tabla con una partición de arranque, una de root y
    una de swap, que no queremos. Ve a la línea de swap, pulsa
    **Enter**, elige **Delete the partition** y pulsa **Enter**.
14. Elige **Finish partitioning and write changes to disk**.
15. Debian te recuerda que no hay partición de swap. Elige **No** para no
    volver al menú de particiones.
16. Confirma las dos particiones (arranque y root) con **Yes** para
    empezar la instalación.
17. **Popularity contest** (estadísticas anónimas de los paquetes Debian
    que usas): elige **No**.
18. **Software:** no queremos escritorio gráfico, así que deja marcados
    solo **SSH server** y **standard system utilities** y pulsa
    **Enter**.
19. **Instalación terminada:** elige **Continue** para reiniciar.

## 🔁 Paso 4: arranca Debian automáticamente

1. En Petitboot, ve a **System configuration** y pulsa **Enter**.
2. **Autoboot** tiene que estar en **Enabled**.
3. En **Boot order** tiene que estar el disco del ODROID-M1, o **Any
   device**. Para añadir el disco ve a **Add Device**, elige el disco y
   **OK**. O elige **Clear & Boot Any** para **Any device**.
4. **Timeout** son los segundos tras encender hasta que arranca Debian
   (10 por defecto, no hace falta cambiarlo).
5. Ve a **OK** y pulsa **Enter**, y reinicia el ODROID-M1. Tras el
   reinicio, una cuenta atrás abajo a la izquierda arranca Debian 12.

<details>
<summary><b>📵 ¿No hay conexión a internet?</b></summary>

1. Entra como superusuario `root` y ejecuta `ping 8.8.8.8`. Si dice
   **Network is unreachable**, el aparato no está conectado.
2. Ejecuta `dhclient -v` para pedir al router una dirección IP por DHCP.
   También muestra el nombre de la interfaz de red, por ejemplo `eth0`.
3. Ejecuta `ping 8.8.8.8` otra vez: ahora funciona, pero el problema
   vuelve en el siguiente reinicio.
4. Para que `eth0` use DHCP cada vez que arranca, ejecuta
   `nano /etc/network/interfaces` y añade o cambia estas líneas:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   Pulsa **Ctrl + X** para salir, **y** para guardar y **Enter** para
   sobrescribir.

5. Reinicia y comprueba la conexión otra vez. Ahora el ODROID-M1 se
   conecta solo en cada reinicio.

</details>

## 💻 Paso 5: conéctate por SSH

1. En tu ordenador, abre el terminal y conéctate por SSH con el usuario
   que creaste en la instalación y la dirección IP que el router dio al
   ODROID-M1:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian no deja entrar por SSH como superusuario (`root`).

2. Una vez dentro, escribe `su` y la contraseña del superusuario para
   tener sus permisos.

## 📥 Paso 6: actualiza los repositorios y el sistema

1. Abre la lista de repositorios:

   ```bash
   nano /etc/apt/sources.list
   ```

2. Borra todas las líneas y añade estas:

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

   Pulsa **Ctrl + X**, luego **y** para guardar y **Enter** para
   sobrescribir el archivo.

3. Actualiza la lista de paquetes y los paquetes instalados:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` debería conectarse ahora a los repositorios de arriba,
   no al predeterminado `debian.mirror.garr.it/debian`.

## 🧅 Paso 7: instala Tor y entra por SSH a través de Tor

1. Instala Tor:

   ```bash
   apt install tor
   ```

2. Abre su configuración con `nano /etc/tor/torrc` y añade al final:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

3. Reinicia Tor y mira su estado (arranca cada vez que arranca el
   ODROID-M1):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. Muestra tu dirección onion:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. Ahora puedes conectarte al ODROID-M1 desde cualquier parte del mundo:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 Paso 8: descarga Bitcoin Core

La última versión de Bitcoin Core, sus hashes y sus firmas están en
<https://bitcoincore.org/en/download/>.

1. Descarga el paquete de instalación, los checksums y las firmas:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. Comprueba que el checksum del archivo descargado coincide con el de
   `SHA256SUMS`:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ Tiene que poner **OK** detrás del nombre del archivo:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Las claves públicas de los desarrolladores de Bitcoin Core están en
   el repositorio `bitcoin-core/guix.sigs`. Seguramente aún no tienes
   git, así que instálalo primero:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. Importa las claves públicas de los desarrolladores:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. Comprueba las firmas de `SHA256SUMS.asc`:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ Por cada desarrollador deberías ver `gpg: Good signature from "...."`,
   que significa que la firma es válida. Puedes ignorar los avisos.

🔒 Por qué importa:

- **Integridad:** el checksum asegura que el archivo no se dañó ni se
  cambió durante la descarga.
- **Autenticidad:** las firmas confirman que el archivo viene de verdad
  de los desarrolladores de Bitcoin Core y no de una fuente maliciosa.

## ⚙️ Paso 9: instala Bitcoin Core y empieza a sincronizar

1. Descomprime el paquete:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Instala Bitcoin Core:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. Empieza a sincronizar:

   ```bash
   bitcoind
   ```

4. Configuración para casa: abre `bitcoin.conf` con
   `nano ~/.bitcoin/bitcoin.conf` y añade:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

| Ajuste | Qué hace |
|---|---|
| `daemon=1` | Ejecuta `bitcoind` como daemon en segundo plano, así sigue funcionando al cerrar el terminal |
| `blocksonly=1` | Descarga solo bloques, no la mempool (transacciones sin confirmar) |
| `maxconnections=20` | Como mucho 20 conexiones de entrada y salida |
| `maxuploadtarget=500` | Sube como mucho 500 MB al día |
| `txindex=1` | Mantiene un índice completo de transacciones (explorar toda la blockchain) |
| `blockfilterindex=1` | Crea y mantiene un índice de filtros de bloque (permite comprobar transacciones de forma más eficiente) |

💡 Desde Bitcoin Core 26, `v2transport` cifra las conexiones entre
nodos. Para todos los ajustes, mira el
[generador de configuración de Bitcoin Core](https://jlopp.github.io/bitcoin-core-config-generator/).

<details>
<summary><b>🔁 Ejecútalo como servicio systemd</b></summary>

Con un servicio `bitcoind`, un usuario `bitcoin` dedicado, reinicio
automático y el registro en `~/.bitcoin/debug.log`:

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

## 🧅 Paso 10: Bitcoin Core solo por Tor

Configura Bitcoin Core para que se conecte, y se le pueda alcanzar,
solo por Tor.

1. Abre la configuración de Tor con `nano /etc/tor/torrc` y añade al
   final:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

2. Reinicia Tor:

   ```bash
   systemctl restart tor
   ```

3. Muestra la dirección onion de Bitcoin Core:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Abre `bitcoin.conf` con `nano ~/.bitcoin/bitcoin.conf` y añade:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

| Ajuste | Qué hace |
|---|---|
| `proxy=127.0.0.1:9050` | Hace que Bitcoin Core use el proxy de Tor |
| `listen=1` | Deja que el nodo acepte conexiones de entrada |
| `bind=127.0.0.1:8333=onion` | Permite conexiones de entrada solo a través de Tor |
| `externalip=ADDRESS.onion` | Cambia `ADDRESS.onion` por la dirección onion que obtuviste arriba |
| `onlynet=onion` | Permite conexiones de salida solo por la red Tor |

5. Reinicia Bitcoin Core: páralo y vuelve a arrancarlo como daemon (un
   proceso en segundo plano):

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. Comprueba las redes:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ En `networks` ves `ipv4`, `ipv6`, `onion`, `i2p` y `cjdns`. Solo
   `onion` tiene `reachable` en `true`; todas las demás están en
   `false`. En `localaddresses` ves solo tu dirección onion.

7. Comprueba los peers a los que te conectas, o que se conectan a ti:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ El campo `addr` tiene la dirección onion del peer y `network` dice
   `onion`.

⏳ Tor es lento: la primera sincronización tarda mucho más.

<details>
<summary><b>🔄 Cambiar la dirección onion de Bitcoin Core</b></summary>

1. Borra la carpeta `bitcoin-service`, que guarda la clave privada de la
   dirección onion:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Reinicia Tor, que vuelve a crear la carpeta con una clave pública y
   privada nuevas:

   ```bash
   systemctl restart tor
   ```

3. Comprueba la nueva dirección onion:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Reinicia Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 Paso 11: instala Rust

electrs es un servidor Electrum escrito en el lenguaje de programación
Rust, así que necesitas Rust en el aparato.

1. Instala primero `curl`:

   ```bash
   apt install curl
   ```

2. Instala Rust:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   Cuando pregunte por una opción de instalación, escribe **1** y pulsa
   **Enter** para la instalación por defecto.

3. Al terminar recomienda reiniciar la shell. Sal y vuelve a entrar (o
   vuelve a ser superusuario con `su`) para usar los comandos de Rust.

4. Instala `clang`, `cmake` y `build-essential`:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 Paso 12: descarga electrs

1. Mira la última versión de electrs en
   <https://github.com/romanz/electrs>. Aquí es la 0.10.7:

   ```bash
   VERSION="0.10.7"
   ```

   (Si la última versión es, por ejemplo, 0.10.9, cambia el valor entre
   comillas.)

2. Descarga esa versión:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. Entra en la nueva carpeta `electrs` e importa la clave del
   desarrollador:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. Comprueba la firma del desarrollador:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ La firma es válida si ves `gpg: Good signature from "...."`.

## 🏗️ Paso 13: compila e instala electrs

1. Dentro de la carpeta `electrs`, empieza la compilación. Tarda
   bastante, así que paciencia 😄

   ```bash
   cargo build --locked --release
   ```

2. Instala electrs:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. Comprueba que se instaló mostrando su versión:

   ```bash
   electrs --version
   ```

## 📝 Paso 14: ajustes de bitcoin.conf para electrs

1. Para Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

2. Abre `bitcoin.conf` con `nano ~/.bitcoin/bitcoin.conf` y añade:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

| Ajuste | Qué hace |
|---|---|
| `server=1` | Hace que Bitcoin Core funcione como servidor RPC |
| `rpcuser=USER` | El nombre de usuario (lo eliges tú) que las apps tienen que dar para conectarse al nodo por RPC |
| `rpcpassword=PASSWORD` | La contraseña (la eliges tú) de ese usuario |
| `rpcallowip=127.0.0.1` | Qué direcciones IP pueden usar el RPC del nodo: aquí solo los programas de la misma máquina |
| `whitelist=download@127.0.0.1` | Los programas de la misma máquina pueden descargar bloques y transacciones sin límites, aunque otras reglas lo impidan (por ejemplo `maxuploadtarget=500`). En resumen, electrs tiene acceso libre a los datos del nodo |

3. Vuelve a arrancar Bitcoin Core:

   ```bash
   bitcoind -daemon
   ```

## 📝 Paso 15: config.toml de electrs

1. Dentro de la carpeta donde instalaste electrs, crea el archivo de
   configuración:

   ```bash
   nano config.toml
   ```

2. Añade estas líneas:

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

   Pulsa **Ctrl + X**, **y** y **Enter**.

| Ajuste | Qué hace |
|---|---|
| `auth = "USER:PASSWORD"` | Cambia `USER` y `PASSWORD` por los mismos que pusiste en `bitcoin.conf`, para entrar por RPC |
| `daemon_rpc_addr = "127.0.0.1:8332"` | Dirección y puerto RPC del nodo: `127.0.0.1` es la misma máquina, `8332` es el puerto RPC por defecto de Bitcoin Core |
| `daemon_p2p_addr = "127.0.0.1:8333"` | Dirección y puerto P2P del nodo: `8333` es el puerto P2P por defecto de Bitcoin Core |
| `db_dir = ".electrum"` | La carpeta donde electrs guarda su base de datos |
| `network = "bitcoin"` | La red en la que funciona electrs: aquí la red principal de bitcoin |
| `electrum_rpc_addr = "127.0.0.1:50001"` | Dónde electrs ofrece su servicio a los clientes Electrum: `127.0.0.1` es solo desde la misma máquina, `50001` es el puerto por defecto para conexiones Electrum sin cifrar |
| `log_filters = "INFO"` | Cuánto detalle escribe electrs en su registro |

## ⏳ Paso 16: crea el índice de electrs

1. Dentro de la carpeta de electrs, empieza a indexar los bloques:

   ```bash
   electrs --conf config.toml
   ```

2. Deja el terminal abierto hasta que toda la red esté indexada. Puede
   tardar muchas horas: en la charla empezó el 5 de diciembre a las 6:21
   y terminó el 6 de diciembre a la 1:24, tras 19 horas y 3 minutos.

## 🧅 Paso 17: electrs por Tor

1. Abre la configuración de Tor con `nano /etc/tor/torrc` y añade al
   final:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   Pulsa **Ctrl + X**, **y** y **Enter**.

2. Reinicia Tor:

   ```bash
   systemctl restart tor
   ```

3. Muestra la dirección onion de electrs:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 Cambiar la dirección onion de electrs</b></summary>

1. Borra la carpeta `electrs_hidden_service`, que guarda la clave
   privada de la dirección onion:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Reinicia Tor, que vuelve a crear la carpeta con una clave pública y
   privada nuevas:

   ```bash
   systemctl restart tor
   ```

3. Comprueba la nueva dirección onion:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 Más

## ➕ Siguientes pasos

- **Core Lightning (CLN):** la Lightning Network para pagos, se
  configura en otras clases.
- **Mempool.space:** un explorador de bloques en tu propio servidor, con
  MariaDB, Node.js, un backend y un frontend, y `pm2` para gestionar los
  procesos.

## 🧪 Testnet

Para aprender: monedas sin valor y comisiones mínimas. El minado sube y
baja y la dificultad cambia.

- Carteras: Green, Electrum, Specter, Sparrow
- BTCBouncer: pagos en testnet
- BTCSigner: multisig en testnet

## ⌨️ bitcoin-cli

Formato: `bitcoin-cli [options] <command> [params]`, y `bitcoin-cli
help` lo lista todo.

- Blockchain: `getblockcount`, `getblockchaininfo`, `getblock`
- Red: `getnetworkinfo`, `getpeerinfo`
- Cartera: `getbalance`, `sendtoaddress`, `listunspent`
- Raw: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 Recursos

- [Clases de Officine Bitcoin](https://officinebitcoin.it/lezioni/fulhar/):
  hardware, Bitcoin Core, electrs, testnet, bitcoin-cli
- [Clase de Debian de Officine Bitcoin](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  incluidos [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)
  y [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md)
- [Bitcoin Core](https://bitcoincore.org/) y sus
  [notas sobre Tor](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) y su
  [documentación de configuración](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Generador de configuración de Bitcoin Core](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [Autoboot de Petitboot en el ODROID-M1](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)
  y el [hilo del foro de ODROID](https://forum.odroid.com/viewtopic.php?t=37109)

## 🧱 Otra guía

- [MiniBolt](https://minibolt.minibolt.info/): monta un nodo en un mini
  PC con un procesador Intel o AMD de más o menos 2010 en adelante.

💬 Encontrado en el chat de Telegram de Malaga 2140.

Los [requisitos de hardware](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)
de MiniBolt piden:

- CPU: procesador Intel/AMD de 2010 en adelante
- RAM: 2 GB o más (solo Bitcoin), 4 GB o más recomendado (Bitcoin,
  Lightning y extras)
- Almacenamiento: SSD SATA/NVMe de 2 TB o más recomendado
- Un pendrive USB o tarjeta SD de 4 GB o más para instalar
- Un monitor o televisor y un teclado USB o PS2, solo para la primera
  configuración

<a id="credits"></a>

## 🙏 Créditos

Traducido de estas charlas de [Satoshi Spritz](https://satoshispritz.it/)
(en italiano):

- [Nodo bitcoin: software, hardware y DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano, licencia
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
- [Nodo bitcoin en ODROID-M1, parte 1: instalar el sistema operativo](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  Satoshi Spritz Treviso, 29 de octubre de 2024
- [Nodo bitcoin en ODROID-M1, parte 2: instalar Bitcoin Core y empezar a sincronizar](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  Satoshi Spritz Treviso, 19 de noviembre de 2024
- [Nodo bitcoin en la red Tor](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  Satoshi Spritz Treviso, 14 de enero de 2025
- [Servidor Electrum: electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  Satoshi Spritz Treviso, 11 de febrero de 2025

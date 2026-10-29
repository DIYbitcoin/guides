# 🖥️ Il tuo nodo bitcoin

Il tuo nodo verifica da sé ogni regola di bitcoin, così non devi fidarti
del server di nessun altro. Queste guide ti aiutano a montarne uno a casa.

Ci sono due guide tra cui scegliere:

- 🛠️ [Nodo bitcoin su ODROID-M1](#odroid): una piccola scheda ARM con Debian 12, Bitcoin
  Core, Tor ed electrs, passo passo. Riprende le presentazioni sui nodi
  dei meetup bitcoin [Satoshi Spritz](https://satoshispritz.it/) (vedi
  [Crediti](#credits)).
- 🧱 [MiniBolt](#minibolt): monta un nodo su un mini PC con un processore
  Intel o AMD.

Prima c'è una panoramica valida per entrambe: perché un nodo, il
software e l'hardware.
## 🤔 Perché un nodo bitcoin?

- Validare l'intera catena di bitcoin
- Controllare il bilancio dei wallet senza terze parti
- Trasmettere le proprie transazioni
- Massima privacy, nessun trust in servizi esterni

## 📦 Software per il nodo

Soluzioni preconfezionate:

| Software | Prezzo | Caratteristiche |
|---|---|---|
| Umbrel | Software gratis, hardware 100 a 300 € | Più user friendly, app marketplace, stile Apple |
| MyNode | Free / 79 € premium / 399 a 599 € pre built | Node + Lightning + Electrum + block explorer |
| Start9 | 699 a 899 € plug and play / software gratis DIY | Open source, Tor integrato, no telemetry |

Requisiti comuni:

- Blockchain: circa 670+ GB (2025), crescita di circa 1 GB ogni pochi
  giorni
- Minimo 4 GB RAM, 1 TB SSD (consigliati 2 TB)
- Sync iniziale: 2 a 7 giorni

**Soluzioni preconfezionate:** setup veloce e interfaccia grafica, ma
Docker, script e check di sicurezza sono mascherati, e il software
aggiuntivo è di difficile controllo.

**Nodo DIY (manuale):** meno software vuol dire meno superficie di
attacco, controllo completo su ogni componente, apprendimento e
manutenibilità.

## 🔧 Selezione hardware

Requisiti minimi:

- Minimo 2 GB RAM (consigliati 4 GB)
- Disco di almeno 1 TB (consigliati 2 TB)
- CPU multi core (qualsiasi CPU recente)

Opzioni:

- **Vecchio PC:** costo zero; rimuovere la batteria se è un laptop
- **Raspberry Pi:** costoso, e il disco USB è meno stabile
- **ODROID-M1:** ARM, con slot interno per l'SSD
- **Thin client (30 a 100 €):** Fujitsu Futro S920, HP t620

Come sistema operativo usa **Debian 13**, Linux LTS con supporto
pluriennale. Aggiorna tutti i pacchetti, installa `tor` e `ssh` per la
manutenzione remota, e un gruppo di continuità (UPS) è consigliato.

<a id="minibolt"></a>

## 🧱 Un'altra opzione: MiniBolt

- [MiniBolt](https://minibolt.minibolt.info/): monta un nodo su un mini
  PC con un processore Intel o AMD dal 2010 circa in poi.

💬 Trovata nella chat Telegram di Malaga 2140.

I [requisiti hardware](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)
di MiniBolt chiedono:

- CPU: processore Intel/AMD dal 2010 in poi
- RAM: 2 GB o più (solo Bitcoin), 4 GB o più consigliati (Bitcoin,
  Lightning ed extra)
- Disco: SSD SATA/NVMe da 2 TB o più consigliato
- Una chiavetta USB o scheda SD da 4 GB o più per l'installazione
- Un monitor o TV e una tastiera USB o PS2, solo per la prima
  configurazione

<a id="odroid"></a>

# 🛠️ Nodo bitcoin su ODROID-M1

## 🧰 Materiale necessario

- ODROID-M1
- Memoria SSD NVMe (meglio da 2 TB)
- Router
- Cavo ethernet
- Monitor con cavo HDMI
- Mouse e tastiera USB

## 🔩 Passo 1: montaggio

1. Montiamo la memoria SSD NVMe nell'ODROID-M1.
2. Colleghiamo tutti i cavi: mouse e tastiera, monitor, cavo ethernet.
3. Colleghiamo il cavo di alimentazione per accendere l'ODROID-M1.

## 🐧 Passo 2: scarichiamo Debian 12

1. Dopo aver avviato l'ODROID-M1, in Petitboot andiamo sopra **Exit to
   shell** e premiamo **Invio**.
2. Digitiamo `udhcpc` e premiamo **Invio** (può aiutare a risolvere
   eventuali problemi di connettività).
3. Digitiamo `netboot_default` e premiamo **Invio**.

   ⌨️ Petitboot usa la tastiera americana, qualunque tastiera abbiate:
   per ottenere `_` bisogna tenere premuto **Shift** e premere il tasto a
   fianco dello **0**.

4. Digitiamo `exit` e premiamo **Invio**.
5. Ora nella schermata iniziale di Petitboot vedremo la lista delle
   immagini disponibili. Andiamo sopra **Debian 12 (Bookworm) Netboot
   installer** e premiamo **Invio**.

## 💿 Passo 3: installazione di Debian 12

1. **Lingua:** scegliamo **English** per una migliore comprensione (le
   traduzioni potrebbero non essere fatte bene).
2. **Posizione geografica:** scegliamo il paese in cui viviamo, che
   imposta il fuso orario. Se non è nel primo elenco, scegliamo
   **other**, poi il continente, poi il paese.
3. **Localizzazione:** se Debian lo chiede, possiamo continuare in
   inglese selezionando **United States** o **United Kingdom**.
4. **Tastiera:** scegliamo la disposizione della tastiera collegata.
5. **Hostname:** scegliamo un hostname. Il dominio può rimanere vuoto.
6. **Paese del mirror:** il paese da cui Debian scarica i pacchetti: il
   nostro, o il più vicino nell'elenco.
7. **Mirror:** scegliamo `deb.debian.org` se è nell'elenco, perché ci
   manda a un server veloce vicino a noi. Altrimenti va bene qualsiasi
   mirror dell'elenco.
8. **Proxy:** se non abbiamo un proxy lasciamo vuoto il campo e premiamo
   **Invio**.
9. **Password di root:** impostiamo la password per il superuser e
   premiamo **Invio**.
10. **Utente:** inventiamo un nome per l'utente non admin (non serve
    inserire nome e cognome in full name) e impostiamo la password.
11. **Disco:** selezioniamo **Guided: use entire disk**, poi il disco
    NVMe montato nell'ODROID.
12. **Partizioni:** lasciamo tutto in una singola partizione: **All files
    in one partition (recommended for new users)**.
13. Debian crea una tabella con la partizione di boot, la partizione di
    root e anche la partizione di swap, che non vogliamo. Andiamo sulla
    riga di swap, premiamo **Invio**, poi **Delete the partition** e
    **Invio**.
14. Salviamo con **Finish partitioning and write changes to disk**.
15. Debian ci ricorda che non abbiamo creato una partizione di swap.
    Andiamo su **No** per non tornare al menu delle partizioni.
16. Confermiamo le due partizioni (boot e root) con **Yes** per far
    partire l'installazione.
17. **Popularity contest** (statistiche anonime sui pacchetti Debian
    installati): andiamo su **No**.
18. **Programmi da installare:** non ci interessano ambienti grafici,
    quindi lasciamo spuntati solo **SSH server** e **standard system
    utilities** e premiamo **Invio**.
19. **Installazione completata:** andiamo su **Continue** per riavviare.

## 🔁 Passo 4: avvio automatico

1. In Petitboot andiamo su **System configuration** e premiamo **Invio**.
2. **Autoboot** deve essere **Enabled**.
3. In **Boot order** deve esserci il disco montato nell'ODROID oppure
   **Any device**. Per aggiungere il disco andiamo su **Add Device**,
   selezioniamo il disco e **OK**. In alternativa **Clear & Boot Any**
   imposta **Any device**.
4. **Timeout:** Debian parte dopo x secondi dall'accensione (10 secondi
   di default, non serve cambiarlo).
5. Andiamo su **OK**, premiamo **Invio** e riavviamo l'ODROID. Dopo il
   riavvio, in basso a sinistra parte il conto alla rovescia per
   l'avvio di Debian 12.

<details>
<summary><b>📵 Manca la connettività?</b></summary>

1. Accediamo come superuser `root` e facciamo `ping 8.8.8.8`. Se
   otteniamo **Network is unreachable**, il dispositivo non è connesso.
2. Eseguiamo `dhclient -v` per chiedere un indirizzo IP via DHCP. Così
   vediamo anche il nome dell'interfaccia di rete, per esempio `eth0`.
3. Rifacciamo `ping 8.8.8.8`: ora funziona, ma al primo riavvio il
   problema si ripresenta.
4. Perché `eth0` usi DHCP a ogni avvio, eseguiamo
   `nano /etc/network/interfaces` e aggiungiamo o modifichiamo così:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   **Ctrl + X** per uscire, **y** per salvare e **Invio** per
   sovrascrivere.

5. Riavviamo e verifichiamo di nuovo la connettività. Ora l'ODROID si
   connette da solo a ogni riavvio.

</details>

## 💻 Passo 5: collegamento ssh

1. Apriamo il terminale del nostro computer e colleghiamoci via ssh con
   l'utente creato durante l'installazione e l'IP che il router ha dato
   all'ODROID:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian non permette di collegarsi via ssh con le credenziali del
   superuser (`root`).

2. Dopo l'accesso digitiamo `su` e inseriamo la password del superuser
   per ottenerne i privilegi.

## 📥 Passo 6: aggiornare repository e sistema

1. Apriamo la lista dei repository:

   ```bash
   nano /etc/apt/sources.list
   ```

2. Cancelliamo tutte le righe e aggiungiamo queste:

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

   **Ctrl + X** per uscire, poi **y** per salvare e **Invio** per
   sovrascrivere il file.

3. Aggiorniamo l'elenco dei pacchetti e i pacchetti installati:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ `apt update` deve connettersi a `deb.debian.org`, i repository qui
   sopra.

## 🧅 Passo 7: installare Tor e ssh via Tor

1. Installiamo tor:

   ```bash
   apt install tor
   ```

2. Apriamo la configurazione con `nano /etc/tor/torrc` e in fondo
   aggiungiamo:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   **Ctrl + X**, **y** e **Invio**.

3. Riavviamo tor e vediamone lo stato (parte a ogni riavvio
   dell'ODROID):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. Vediamo il nostro indirizzo onion:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. Ora possiamo collegarci all'ODROID da ogni parte del mondo:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 Passo 8: download di Bitcoin Core

L'ultima versione di Bitcoin Core, l'hash e le firme si trovano su
<https://bitcoincore.org/en/download/>.

1. Scarichiamo il pacchetto di installazione, la checksum e le firme:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. Verifichiamo che la checksum del file scaricato corrisponda a quella
   in `SHA256SUMS`:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ Dopo il nome del file deve comparire **OK**:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Le chiavi pubbliche degli sviluppatori di Bitcoin Core sono nel
   repository `bitcoin-core/guix.sigs`. Probabilmente non abbiamo git,
   quindi prima installiamolo:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. Importiamo le chiavi pubbliche degli sviluppatori:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. Verifichiamo le firme del file `SHA256SUMS.asc`:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ Per ogni sviluppatore dovrebbe comparire `gpg: Good signature from "...."`,
   cioè la firma è valida. Possiamo ignorare eventuali warning.

🔒 Perché è importante:

- **Integrità:** la checksum assicura che il file non sia stato corrotto
  o modificato durante il download.
- **Autenticità:** le firme confermano che il file proviene davvero dagli
  sviluppatori di Bitcoin Core e non da una fonte malevola.

## ⚙️ Passo 9: installare Bitcoin Core e avviare la sincronizzazione

1. Scompattiamo il pacchetto:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Installiamo Bitcoin Core:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. Avviamo la sincronizzazione:

   ```bash
   bitcoind
   ```

4. Configurazione casalinga: apriamo `bitcoin.conf` con
   `nano ~/.bitcoin/bitcoin.conf` e aggiungiamo:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   **Ctrl + X**, **y** e **Invio**.

| Impostazione | Cosa fa |
|---|---|
| `daemon=1` | Esegue `bitcoind` come daemon in background: il processo continua anche dopo aver chiuso il terminale |
| `blocksonly=1` | Scarica solo i blocchi e non la mempool (transazioni non confermate) |
| `maxconnections=20` | Massimo 20 connessioni in entrata e in uscita |
| `maxuploadtarget=500` | Limite di upload giornaliero di 500 MB |
| `txindex=1` | Mantiene un indice completo delle transazioni (esplorazione completa della blockchain) |
| `blockfilterindex=1` | Crea e mantiene un indice dei filtri di blocco (verifica più efficiente delle transazioni) |

💡 Da Bitcoin Core 26, `v2transport` cifra le connessioni P2P. Per la
configurazione completa vedi il
[Bitcoin Core config generator](https://jlopp.github.io/bitcoin-core-config-generator/).

<details>
<summary><b>🔁 Come servizio systemd</b></summary>

Servizio `bitcoind`, utente dedicato `bitcoin`, restart automatico e log
in `~/.bitcoin/debug.log`:

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

## 🧅 Passo 10: Bitcoin Core solo su Tor

Configuriamo Bitcoin Core per connettersi ed essere raggiungibile solo
sotto rete Tor.

1. Apriamo la configurazione di tor con `nano /etc/tor/torrc` e alla fine
   aggiungiamo:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   **Ctrl + X**, **y** e **Invio**.

2. Riavviamo tor:

   ```bash
   systemctl restart tor
   ```

3. Vediamo l'indirizzo onion di Bitcoin Core:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Apriamo `bitcoin.conf` con `nano ~/.bitcoin/bitcoin.conf` e
   aggiungiamo:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   **Ctrl + X**, **y** e **Invio**.

| Impostazione | Cosa fa |
|---|---|
| `proxy=127.0.0.1:9050` | Bitcoin Core usa il proxy Tor |
| `listen=1` | Il nodo accetta connessioni in entrata |
| `bind=127.0.0.1:8333=onion` | Connessioni in entrata solo attraverso Tor |
| `externalip=ADDRESS.onion` | Sostituiamo `ADDRESS.onion` con l'indirizzo onion ottenuto prima |
| `onlynet=onion` | Connessioni in uscita solo sulla rete Tor |

5. Riavviamo Bitcoin Core: lo arrestiamo e lo riavviamo in modalità
   demone (processo in background):

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. Verifichiamo le reti:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ Sotto `networks` vediamo `ipv4`, `ipv6`, `onion`, `i2p` e `cjdns`.
   Solo `onion` ha `reachable` a `true`, tutte le altre sono `false`.
   Sotto `localaddresses` vediamo solo il nostro indirizzo onion.

7. Verifichiamo i peer a cui ci colleghiamo o che si collegano a noi:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ Nel campo `addr` c'è l'indirizzo onion del peer e `network` dice
   `onion`.

⏳ Tor è lento: la sync iniziale è molto più lunga.

<details>
<summary><b>🔄 Cambiare l'indirizzo onion di Bitcoin Core</b></summary>

1. Rimuoviamo la cartella `bitcoin-service`, che contiene la chiave
   privata che genera l'indirizzo onion:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Riavviamo tor, che rigenera la cartella con una nuova chiave pubblica
   e privata:

   ```bash
   systemctl restart tor
   ```

3. Verifichiamo il nuovo indirizzo onion:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Riavviamo Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 Passo 11: installazione di Rust

electrs è un electrum server scritto nel linguaggio di programmazione
Rust, quindi serve Rust sul dispositivo.

1. Prima installiamo `curl`:

   ```bash
   apt install curl
   ```

2. Installiamo Rust:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   Quando chiede un'opzione di installazione, digitiamo **1** e premiamo
   **Invio** per l'installazione predefinita.

3. Alla fine consiglia di riavviare la shell. Basta fare logout e
   rientrare (o tornare superuser con `su`) per usare i comandi di Rust.

4. Installiamo `clang`, `cmake` e `build-essential`:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 Passo 12: download di electrs

1. Verifichiamo l'ultima versione di electrs su
   <https://github.com/romanz/electrs>. In questo caso è la 0.10.7:

   ```bash
   VERSION="0.10.7"
   ```

   (Se l'ultima versione è per esempio 0.10.9, il valore tra virgolette
   va cambiato.)

2. Scarichiamo quella versione:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. Entriamo nella cartella `electrs` appena creata e importiamo la chiave
   dello sviluppatore:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. Verifichiamo la firma dello sviluppatore:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ La firma è valida se compare `gpg: Good signature from "...."`.

## 🏗️ Passo 13: compilare e installare electrs

1. Sempre dentro la cartella `electrs` avviamo la compilazione. Richiede
   parecchio tempo, quindi armarsi di pazienza 😄

   ```bash
   cargo build --locked --release
   ```

2. Installiamo electrs:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. Verifichiamo l'installazione controllando la versione:

   ```bash
   electrs --version
   ```

## 📝 Passo 14: impostazioni bitcoin.conf per electrs

1. Arrestiamo Bitcoin Core:

   ```bash
   bitcoin-cli stop
   ```

2. Apriamo `bitcoin.conf` con `nano ~/.bitcoin/bitcoin.conf` e
   aggiungiamo:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   **Ctrl + X**, **y** e **Invio**.

| Impostazione | Cosa fa |
|---|---|
| `server=1` | Abilita Bitcoin Core a funzionare come server RPC |
| `rpcuser=USER` | Il nome utente (lo scegli tu) che un'applicazione deve fornire per connettersi al nodo via RPC |
| `rpcpassword=PASSWORD` | La password (la scegli tu) associata al nome utente RPC |
| `rpcallowip=127.0.0.1` | Quali indirizzi IP possono usare l'RPC del nodo: qui solo i programmi sulla stessa macchina |
| `whitelist=download@127.0.0.1` | I programmi sulla stessa macchina possono scaricare blocchi e transazioni senza restrizioni, anche se altre regole del nodo li limiterebbero (es: `maxuploadtarget=500`). In sostanza electrs accede liberamente ai dati del nodo |

3. Riavviamo Bitcoin Core:

   ```bash
   bitcoind -daemon
   ```

## 📝 Passo 15: config.toml di electrs

1. Dentro la cartella dove è stato installato electrs creiamo il file di
   configurazione:

   ```bash
   nano config.toml
   ```

2. Aggiungiamo queste righe:

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

   **Ctrl + X**, **y** e **Invio**.

| Impostazione | Cosa fa |
|---|---|
| `auth = "USER:PASSWORD"` | Sostituiamo `USER` e `PASSWORD` con le stesse credenziali di `bitcoin.conf`, per autenticarci via RPC |
| `daemon_rpc_addr = "127.0.0.1:8332"` | Indirizzo e porta RPC del nodo: `127.0.0.1` è la stessa macchina, `8332` la porta RPC predefinita di Bitcoin Core |
| `daemon_p2p_addr = "127.0.0.1:8333"` | Indirizzo e porta P2P del nodo: `8333` è la porta P2P predefinita di Bitcoin Core |
| `db_dir = ".electrum"` | La cartella dove electrs salva il suo database |
| `network = "bitcoin"` | La rete su cui opera electrs: qui la rete principale di bitcoin |
| `electrum_rpc_addr = "127.0.0.1:50001"` | Dove electrs offre il servizio ai client Electrum: `127.0.0.1` solo dalla stessa macchina, `50001` è la porta predefinita per connessioni Electrum non cifrate |
| `log_filters = "INFO"` | Il livello di dettaglio dei log di electrs |

## ⏳ Passo 16: indicizzazione di electrs

1. Dentro la cartella di electrs avviamo l'indicizzazione dei blocchi:

   ```bash
   electrs --conf config.toml
   ```

2. Lasciamo il terminale aperto finché tutta la rete è indicizzata. Ci
   può impiegare molte ore: nella presentazione è partita il 5 dicembre
   alle 6:21 ed è finita il 6 dicembre all'1:24, dopo 19 ore e 3 minuti.

## 🧅 Passo 17: electrs su Tor

1. Apriamo la configurazione di tor con `nano /etc/tor/torrc` e alla fine
   aggiungiamo:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   **Ctrl + X**, **y** e **Invio**.

2. Riavviamo tor:

   ```bash
   systemctl restart tor
   ```

3. Vediamo l'indirizzo onion di electrs:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 Cambiare l'indirizzo onion di electrs</b></summary>

1. Rimuoviamo la cartella `electrs_hidden_service`, che contiene la
   chiave privata che genera l'indirizzo onion:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Riavviamo tor, che rigenera la cartella con una nuova chiave pubblica
   e privata:

   ```bash
   systemctl restart tor
   ```

3. Verifichiamo il nuovo indirizzo onion:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 Altro

## ➕ Prossimi passi

- **Core Lightning (CLN):** Lightning Network per i pagamenti,
  configurazione in lezioni separate.
- **Mempool.space:** block explorer self hosted con MariaDB, Node.js,
  backend e frontend, e `pm2` per gestire i processi.

## 🧪 Testnet

Per imparare: token senza valore e fee minime. Il mining fluttua e la
difficoltà varia.

- Wallet: Green, Electrum, Specter, Sparrow
- BTCBouncer: pagamenti testnet
- BTCSigner: multisig testnet

## ⌨️ bitcoin-cli

Formato: `bitcoin-cli [options] <command> [params]`, e `bitcoin-cli
help` elenca tutto.

- Blockchain: `getblockcount`, `getblockchaininfo`, `getblock`
- Rete: `getnetworkinfo`, `getpeerinfo`
- Wallet: `getbalance`, `sendtoaddress`, `listunspent`
- Raw: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 Risorse

- [Lezioni di Officine Bitcoin](https://officinebitcoin.it/lezioni/fulhar/):
  hardware, Bitcoin Core, electrs, testnet, bitcoin-cli
- [Lezione Debian di Officine Bitcoin](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  con [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)
  e [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md)
- [Bitcoin Core](https://bitcoincore.org/) e le sue
  [note su Tor](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs) e la sua
  [documentazione di configurazione](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core config generator](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [Autoboot di Petitboot su ODROID-M1](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)
  e la [discussione sul forum ODROID](https://forum.odroid.com/viewtopic.php?t=37109)

<a id="credits"></a>

## 🙏 Crediti

Dalle presentazioni di [Satoshi Spritz](https://satoshispritz.it/):

- [Nodo Bitcoin: Software, Hardware e DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano, licenza
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/)
- [Nodo Bitcoin su ODROID-M1, Parte 1: Installazione del sistema operativo](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  SatoshiSpritz Treviso, 29 ottobre 2024
- [Nodo Bitcoin su ODROID-M1, Parte 2: Installazione e avvio sincronizzazione di Bitcoin Core](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  SatoshiSpritz Treviso, 19 novembre 2024
- [Nodo Bitcoin sotto rete Tor](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  SatoshiSpritz Treviso, 14 gennaio 2025
- [Electrum Server: Electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  SatoshiSpritz Treviso, 11 febbraio 2025

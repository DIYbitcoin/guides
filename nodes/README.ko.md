# 🖥️ 내 비트코인 노드 직접 운영하기

내 노드는 모든 비트코인 규칙을 스스로 확인해요. 그래서 다른 누구의 서버도
믿을 필요가 없어요. 이 가이드들이 집에서 노드를 설치하도록 도와줘요.

이 가이드는 이탈리아 [Satoshi Spritz](https://satoshispritz.it/)의 노드
발표를 한국어로 옮긴 거예요([출처](#credits) 참고). 먼저 노드 소프트웨어와
하드웨어를 살펴보고, 그다음 ODROID-M1에 Bitcoin Core, Tor, electrs를
단계별로 설치해요.

## 🤔 왜 비트코인 노드를 운영할까요?

- 비트코인 블록체인 전체를 직접 검증해요
- 제3자 없이 지갑 잔액을 확인해요
- 내 거래를 직접 전송해요
- 최고의 프라이버시, 외부 서비스를 믿을 필요가 없어요

## 📦 노드 소프트웨어

완성형 솔루션:

| 소프트웨어 | 가격 | 특징 |
|---|---|---|
| Umbrel | 소프트웨어 무료, 하드웨어 100~300 € | 가장 쓰기 쉬움, 앱 마켓, Apple 스타일 |
| MyNode | 무료 / 79 € 프리미엄 / 완제품 399~599 € | 노드 + Lightning + Electrum + 블록 탐색기 |
| Start9 | 완제품 699~899 € / DIY는 소프트웨어 무료 | 오픈 소스, Tor 내장, 텔레메트리 없음 |

공통 요구 사항:

- 블록체인: 약 670GB 이상(2025년), 며칠마다 약 1GB씩 늘어나요
- 최소 4GB RAM, 1TB SSD(2TB 권장)
- 첫 동기화: 2~7일

**완성형:** 설치가 빠르고 그래픽 화면이 있지만, Docker, 스크립트, 보안
점검이 가려져 있고 추가 소프트웨어는 살펴보기 어려워요.

**DIY(수동):** 소프트웨어가 적을수록 공격 표면이 줄고, 모든 부분을 직접
제어하며, 관리하는 법을 배워요.

## 🔧 하드웨어 고르기

최소 사양:

- 최소 2GB RAM(4GB 권장)
- 최소 1TB 디스크(2TB 권장)
- 멀티코어 CPU(최근 CPU라면 무엇이든)

선택지:

- **오래된 PC:** 비용이 들지 않아요. 노트북이면 배터리를 빼요
- **Raspberry Pi:** 비싸고, USB 디스크는 덜 안정적이에요
- **ODROID-M1:** ARM, SSD용 내부 슬롯이 있어요
- **씬 클라이언트(30~100 €):** Fujitsu Futro S920, HP t620

운영체제는 여러 해 지원되는 장기 지원 Linux인 **Debian 13**을 써요. 모든
패키지를 업데이트하고, 원격 관리를 위해 `tor`와 `ssh`를 설치하고, UPS(무정전
전원 장치)를 권장해요.

# 🛠️ ODROID-M1으로 노드 만들기

## 🧰 준비물

- ODROID-M1
- NVMe SSD(2TB가 가장 좋아요)
- 공유기
- 이더넷 케이블
- HDMI 케이블이 있는 모니터
- USB 마우스와 키보드

## 🔩 1단계: 조립하기

1. NVMe SSD를 ODROID-M1에 끼워요.
2. 모든 케이블을 연결해요: 마우스와 키보드, 모니터, 이더넷 케이블.
3. 전원 케이블을 꽂아 ODROID-M1을 켜요.

## 🐧 2단계: Debian 12 내려받기

1. ODROID-M1이 켜지면 Petitboot에서 **Exit to shell**로 가서 **Enter**를
   눌러요.
2. `udhcpc`를 입력하고 **Enter**를 눌러요(연결 문제를 해결하는 데 도움이
   될 수 있어요).
3. `netboot_default`를 입력하고 **Enter**를 눌러요.

   ⌨️ Petitboot는 어떤 키보드를 쓰든 미국식 배열을 써요. `_`를
   입력하려면 **Shift**를 누른 채 **0** 옆의 키를 눌러요.

4. `exit`를 입력하고 **Enter**를 눌러요.
5. 이제 Petitboot 첫 화면에 쓸 수 있는 이미지 목록이 보여요. **Debian 12
   (Bookworm) Netboot installer**로 가서 **Enter**를 눌러요.

## 💿 3단계: Debian 12 설치하기

1. **언어:** **English**를 골라요. 따라가기 더 쉬워요(번역이 잘 안 되어
   있을 수 있어요).
2. **위치:** 사는 나라를 골라요. 이걸로 시간대가 정해져요. 첫 목록에
   없으면 **other**를 고른 다음 대륙, 나라 순서로 골라요.
3. **로캘:** Debian이 물어보면 **United States**나 **United Kingdom**을
   골라 영어로 둬도 돼요.
4. **키보드:** 연결한 키보드의 배열을 골라요.
5. **Hostname:** 호스트 이름을 정해요. 도메인은 비워 둬도 돼요.
6. **미러 국가:** Debian을 내려받을 나라(내 나라나 목록에서 가장
   가까운 나라).
7. **미러:** 목록에 `deb.debian.org`가 있으면 그걸 골라요. 가까운 빠른
   서버로 연결해 줘요. 없으면 목록의 아무 미러나 괜찮아요.
8. **프록시:** 프록시가 없으면 비워 두고 **Enter**를 눌러요.
9. **Root 비밀번호:** 슈퍼유저 비밀번호를 정하고 **Enter**를 눌러요.
10. **사용자:** 일반(관리자가 아닌) 사용자 이름을 정하고(전체 이름은
    안 적어도 돼요) 비밀번호를 정해요.
11. **디스크:** **Guided: use entire disk**를 고르고, 끼워 둔 NVMe 디스크를
    골라요.
12. **파티션:** 모두 한 파티션에 둬요: **All files in one partition
    (recommended for new users)**.
13. Debian이 boot, root, swap 파티션이 있는 표를 만드는데, swap은 필요
    없어요. swap 줄로 가서 **Enter**를 누르고 **Delete the partition**을
    골라 **Enter**를 눌러요.
14. **Finish partitioning and write changes to disk**를 골라요.
15. Debian이 swap 파티션이 없다고 알려 줘요. 파티션 메뉴로 돌아가지 않도록
    **No**를 골라요.
16. 두 파티션(boot와 root)을 **Yes**로 확인해 설치를 시작해요.
17. **Popularity contest**(쓰는 Debian 패키지에 대한 익명 통계): **No**를
    골라요.
18. **소프트웨어:** 그래픽 데스크톱은 필요 없으니 **SSH server**와
    **standard system utilities**만 체크해 두고 **Enter**를 눌러요.
19. **설치 완료:** **Continue**를 골라 다시 시작해요.

## 🔁 4단계: Debian 자동으로 시작하기

1. Petitboot에서 **System configuration**으로 가서 **Enter**를 눌러요.
2. **Autoboot**는 **Enabled**여야 해요.
3. **Boot order**에는 ODROID-M1에 끼운 디스크나 **Any device**가 있어야
   해요. 디스크를 추가하려면 **Add Device**로 가서 디스크를 고르고
   **OK**를 눌러요. 아니면 **Clear & Boot Any**를 골라 **Any device**로
   해요.
4. **Timeout**은 전원을 켠 뒤 Debian이 시작되기까지의 초예요(기본값
   10초, 바꿀 필요 없어요).
5. **OK**로 가서 **Enter**를 누르고 ODROID-M1을 다시 시작해요. 다시
   시작하면 왼쪽 아래 카운트다운 뒤에 Debian 12가 시작돼요.

<details>
<summary><b>📵 인터넷이 안 되나요?</b></summary>

1. 슈퍼유저 `root`로 로그인하고 `ping 8.8.8.8`을 실행해요.
   **Network is unreachable**이 나오면 기기가 연결되지 않은 거예요.
2. `dhclient -v`를 실행해 공유기에 DHCP로 IP 주소를 요청해요. 네트워크
   인터페이스 이름도 보여 줘요(예: `eth0`).
3. `ping 8.8.8.8`을 다시 실행해요. 이제 되지만, 다음에 다시 시작하면
   문제가 또 생겨요.
4. 시작할 때마다 `eth0`이 DHCP를 쓰도록 `nano /etc/network/interfaces`를
   실행하고 이 줄을 추가하거나 고쳐요:

   ```
   auto eth0
   iface eth0 inet dhcp
   ```

   **Ctrl + X**로 나가고, **y**로 저장하고, **Enter**로 덮어써요.

5. 다시 시작하고 연결을 다시 확인해요. 이제 ODROID-M1이 다시 시작할 때마다
   알아서 연결돼요.

</details>

## 💻 5단계: SSH로 접속하기

1. 컴퓨터에서 터미널을 열고, 설치할 때 만든 사용자와 공유기가 ODROID-M1에
   준 IP 주소로 SSH 접속해요:

   ```bash
   ssh USERNAME@IP_ADDRESS
   ```

   Debian은 슈퍼유저(`root`)로 SSH 로그인하는 걸 막아요.

2. 로그인한 뒤 `su`를 입력하고 슈퍼유저 비밀번호를 넣어 권한을 얻어요.

## 📥 6단계: 저장소와 시스템 업데이트하기

1. 저장소 목록을 열어요:

   ```bash
   nano /etc/apt/sources.list
   ```

2. 모든 줄을 지우고 이 줄들을 넣어요:

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

   **Ctrl + X**를 누르고, **y**로 저장하고, **Enter**로 파일을 덮어써요.

3. 패키지 목록과 설치된 패키지를 업데이트해요:

   ```bash
   apt update
   ```

   ```bash
   apt upgrade
   ```

   ✅ 이제 `apt update`가 위의 저장소인 `deb.debian.org`에 연결돼야
   해요.

## 🧅 7단계: Tor 설치하고 Tor로 SSH 접속하기

1. Tor를 설치해요:

   ```bash
   apt install tor
   ```

2. `nano /etc/tor/torrc`로 설정을 열고 맨 아래에 추가해요:

   ```
   HiddenServiceDir /var/lib/tor/hidden_service/
   HiddenServicePort 22 127.0.0.1:22
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

3. Tor를 다시 시작하고 상태를 확인해요(ODROID-M1이 켜질 때마다 시작돼요):

   ```bash
   systemctl restart tor
   ```

   ```bash
   systemctl status tor
   ```

4. 내 onion 주소를 확인해요:

   ```bash
   cat /var/lib/tor/hidden_service/hostname
   ```

5. 이제 세계 어디서든 ODROID-M1에 접속할 수 있어요:

   ```bash
   torify ssh USERNAME@YOUR_ADDRESS.onion
   ```

## 🟠 8단계: Bitcoin Core 내려받기

Bitcoin Core 최신 버전과 해시, 서명은
<https://bitcoincore.org/en/download/>에 있어요.

1. 설치 패키지, 체크섬, 서명을 내려받아요:

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS
   ```

   ```bash
   wget https://bitcoincore.org/bin/bitcoin-core-28.0/SHA256SUMS.asc
   ```

2. 내려받은 파일의 체크섬이 `SHA256SUMS`에 있는 것과 같은지 확인해요:

   ```bash
   sha256sum --ignore-missing --check SHA256SUMS
   ```

   ✅ 파일 이름 뒤에 **OK**가 나와야 해요:
   `bitcoin-28.0-aarch64-linux-gnu.tar.gz: OK`

3. Bitcoin Core 개발자들의 공개 키는 `bitcoin-core/guix.sigs` 저장소에
   있어요. 아직 git이 없을 테니 먼저 설치해요:

   ```bash
   apt-get install git
   ```

   ```bash
   git clone https://github.com/bitcoin-core/guix.sigs
   ```

4. 개발자들의 공개 키를 가져와요:

   ```bash
   gpg --import guix.sigs/builder-keys/*
   ```

5. `SHA256SUMS.asc`의 서명을 확인해요:

   ```bash
   gpg --verify SHA256SUMS.asc
   ```

   ✅ 개발자마다 `gpg: Good signature from "...."`가 보여야 해요. 서명이
   유효하다는 뜻이에요. 경고는 무시해도 돼요.

🔒 이게 중요한 이유:

- **무결성:** 체크섬은 내려받는 동안 파일이 손상되거나 바뀌지 않았는지
  확인해 줘요.
- **진위:** 서명은 파일이 정말 Bitcoin Core 개발자들이 만든 것이고 악의적인
  출처에서 온 게 아니라는 걸 확인해 줘요.

## ⚙️ 9단계: Bitcoin Core 설치하고 동기화 시작하기

1. 패키지 압축을 풀어요:

   ```bash
   tar xzvf bitcoin-28.0-aarch64-linux-gnu.tar.gz
   ```

2. Bitcoin Core를 설치해요:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin bitcoin-28.0/bin/*
   ```

3. 동기화를 시작해요:

   ```bash
   bitcoind
   ```

4. 가정용 설정: `nano ~/.bitcoin/bitcoin.conf`로 `bitcoin.conf`를 열고
   추가해요:

   ```ini
   daemon=1
   blocksonly=1
   maxconnections=20
   maxuploadtarget=500
   txindex=1
   blockfilterindex=1
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

| 설정 | 하는 일 |
|---|---|
| `daemon=1` | `bitcoind`를 백그라운드 데몬으로 실행해서 터미널을 닫아도 계속 돌아가요 |
| `blocksonly=1` | 블록만 내려받고 mempool(확인되지 않은 거래)은 받지 않아요 |
| `maxconnections=20` | 들어오고 나가는 연결을 최대 20개로 제한해요 |
| `maxuploadtarget=500` | 하루 업로드를 최대 500MB로 제한해요 |
| `txindex=1` | 거래 전체 색인을 유지해요(블록체인 전체 탐색) |
| `blockfilterindex=1` | 블록 필터 색인을 만들고 유지해요(거래를 더 효율적으로 확인) |

💡 Bitcoin Core 26부터 `v2transport`가 노드 사이 연결을 암호화해요. 모든
설정은 [Bitcoin Core 설정 생성기](https://jlopp.github.io/bitcoin-core-config-generator/)를
참고해요.

<details>
<summary><b>🔁 systemd 서비스로 실행하기</b></summary>

`bitcoind` 서비스, 전용 `bitcoin` 사용자, 자동 재시작, 그리고
`~/.bitcoin/debug.log`의 로그:

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

## 🧅 10단계: Bitcoin Core를 Tor로만 쓰기

Bitcoin Core가 Tor로만 연결하고, Tor로만 접속받도록 설정해요.

1. `nano /etc/tor/torrc`로 Tor 설정을 열고 맨 끝에 추가해요:

   ```
   HiddenServiceDir /var/lib/tor/bitcoin-service/
   HiddenServicePort 8333 127.0.0.1:8333
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

2. Tor를 다시 시작해요:

   ```bash
   systemctl restart tor
   ```

3. Bitcoin Core의 onion 주소를 확인해요:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. `nano ~/.bitcoin/bitcoin.conf`로 `bitcoin.conf`를 열고 추가해요:

   ```ini
   proxy=127.0.0.1:9050
   listen=1
   bind=127.0.0.1:8333=onion
   externalip=ADDRESS.onion
   onlynet=onion
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

| 설정 | 하는 일 |
|---|---|
| `proxy=127.0.0.1:9050` | Bitcoin Core가 Tor 프록시를 쓰게 해요 |
| `listen=1` | 노드가 들어오는 연결을 받게 해요 |
| `bind=127.0.0.1:8333=onion` | 들어오는 연결을 Tor로만 받아요 |
| `externalip=ADDRESS.onion` | `ADDRESS.onion`을 위에서 얻은 onion 주소로 바꿔요 |
| `onlynet=onion` | 나가는 연결을 Tor 네트워크로만 해요 |

5. Bitcoin Core를 다시 시작해요. 멈춘 다음 데몬(백그라운드 프로세스)으로
   다시 실행해요:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

6. 네트워크를 확인해요:

   ```bash
   bitcoin-cli getnetworkinfo
   ```

   ✅ `networks` 아래에 `ipv4`, `ipv6`, `onion`, `i2p`, `cjdns`가 보여요.
   `onion`만 `reachable`이 `true`이고 나머지는 모두 `false`예요.
   `localaddresses` 아래에는 내 onion 주소만 보여요.

7. 내가 연결하거나 나에게 연결한 피어를 확인해요:

   ```bash
   bitcoin-cli getpeerinfo
   ```

   ✅ `addr` 칸에 피어의 onion 주소가 있고 `network`는 `onion`이에요.

⏳ Tor는 느려요. 첫 동기화가 훨씬 오래 걸려요.

<details>
<summary><b>🔄 Bitcoin Core의 onion 주소 바꾸기</b></summary>

1. onion 주소를 만드는 개인 키가 든 `bitcoin-service` 폴더를 지워요:

   ```bash
   rm -r /var/lib/tor/bitcoin-service/
   ```

2. Tor를 다시 시작하면 새 공개 키와 개인 키로 폴더를 다시 만들어요:

   ```bash
   systemctl restart tor
   ```

3. 새 onion 주소를 확인해요:

   ```bash
   cat /var/lib/tor/bitcoin-service/hostname
   ```

4. Bitcoin Core를 다시 시작해요:

   ```bash
   bitcoin-cli stop
   ```

   ```bash
   bitcoind -daemon
   ```

</details>

## 🦀 11단계: Rust 설치하기

electrs는 Rust 프로그래밍 언어로 만든 Electrum 서버라서 기기에 Rust가
필요해요.

1. 먼저 `curl`을 설치해요:

   ```bash
   apt install curl
   ```

2. Rust를 설치해요:

   ```bash
   curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
   ```

   설치 옵션을 물으면 **1**을 입력하고 **Enter**를 눌러 기본 설치를 해요.

3. 끝나면 셸을 다시 시작하라고 해요. 로그아웃했다가 다시 들어오거나
   (`su`로 다시 슈퍼유저가 되어도 돼요) Rust 명령을 써요.

4. `clang`, `cmake`, `build-essential`을 설치해요:

   ```bash
   apt install clang cmake build-essential -y
   ```

## 🔎 12단계: electrs 내려받기

1. <https://github.com/romanz/electrs>에서 electrs 최신 버전을 확인해요.
   여기서는 0.10.7이에요:

   ```bash
   VERSION="0.10.7"
   ```

   (최신 버전이 예를 들어 0.10.9라면 따옴표 안의 값을 바꿔요.)

2. 그 버전을 내려받아요:

   ```bash
   git clone --branch v$VERSION https://github.com/romanz/electrs.git
   ```

3. 새로 생긴 `electrs` 폴더로 들어가서 개발자의 키를 가져와요:

   ```bash
   cd electrs
   ```

   ```bash
   curl https://romanzey.de/pgp.txt | gpg --import
   ```

4. 개발자의 서명을 확인해요:

   ```bash
   git verify-tag v$VERSION
   ```

   ✅ `gpg: Good signature from "...."`가 보이면 서명이 유효해요.

## 🏗️ 13단계: electrs 빌드하고 설치하기

1. `electrs` 폴더 안에서 빌드를 시작해요. 꽤 오래 걸리니 느긋하게
   기다려요 😄

   ```bash
   cargo build --locked --release
   ```

2. electrs를 설치해요:

   ```bash
   install -m 0755 -o root -g root -t /usr/local/bin ./target/release/electrs
   ```

3. 버전을 출력해 설치됐는지 확인해요:

   ```bash
   electrs --version
   ```

## 📝 14단계: electrs를 위한 bitcoin.conf 설정

1. Bitcoin Core를 멈춰요:

   ```bash
   bitcoin-cli stop
   ```

2. `nano ~/.bitcoin/bitcoin.conf`로 `bitcoin.conf`를 열고 추가해요:

   ```ini
   server=1
   rpcuser=USER
   rpcpassword=PASSWORD
   rpcallowip=127.0.0.1
   whitelist=download@127.0.0.1
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

| 설정 | 하는 일 |
|---|---|
| `server=1` | Bitcoin Core가 RPC 서버로 동작하게 해요 |
| `rpcuser=USER` | 앱이 RPC로 노드에 연결할 때 내야 하는 사용자 이름(직접 정해요) |
| `rpcpassword=PASSWORD` | 그 사용자 이름의 비밀번호(직접 정해요) |
| `rpcallowip=127.0.0.1` | 노드의 RPC를 쓸 수 있는 IP 주소: 여기서는 같은 기기의 프로그램만 |
| `whitelist=download@127.0.0.1` | 같은 기기의 프로그램은 다른 규칙이 막더라도(예: `maxuploadtarget=500`) 제한 없이 블록과 거래를 내려받을 수 있어요. 즉 electrs가 노드의 데이터에 자유롭게 접근해요 |

3. Bitcoin Core를 다시 시작해요:

   ```bash
   bitcoind -daemon
   ```

## 📝 15단계: electrs config.toml

1. electrs를 설치한 폴더 안에서 설정 파일을 만들어요:

   ```bash
   nano config.toml
   ```

2. 이 줄들을 넣어요:

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

   **Ctrl + X**, **y**, **Enter**를 눌러요.

| 설정 | 하는 일 |
|---|---|
| `auth = "USER:PASSWORD"` | `USER`와 `PASSWORD`를 `bitcoin.conf`에 넣은 것과 똑같이 바꿔 RPC로 로그인해요 |
| `daemon_rpc_addr = "127.0.0.1:8332"` | 노드의 RPC 주소와 포트: `127.0.0.1`은 같은 기기, `8332`는 Bitcoin Core의 기본 RPC 포트 |
| `daemon_p2p_addr = "127.0.0.1:8333"` | 노드의 P2P 주소와 포트: `8333`은 Bitcoin Core의 기본 P2P 포트 |
| `db_dir = ".electrum"` | electrs가 데이터베이스를 저장하는 폴더 |
| `network = "bitcoin"` | electrs가 쓰는 네트워크: 여기서는 비트코인 메인 네트워크 |
| `electrum_rpc_addr = "127.0.0.1:50001"` | electrs가 Electrum 클라이언트에 서비스를 여는 곳: `127.0.0.1`은 같은 기기에서만, `50001`은 암호화하지 않은 Electrum 연결의 기본 포트 |
| `log_filters = "INFO"` | electrs가 로그에 쓰는 자세함의 정도 |

## ⏳ 16단계: electrs 색인 만들기

1. electrs 폴더 안에서 블록 색인을 시작해요:

   ```bash
   electrs --conf config.toml
   ```

2. 네트워크 전체 색인이 끝날 때까지 터미널을 열어 둬요. 여러 시간이 걸릴
   수 있어요. 발표에서는 12월 5일 6시 21분에 시작해 12월 6일 1시 24분에,
   19시간 3분 만에 끝났어요.

## 🧅 17단계: electrs를 Tor로 쓰기

1. `nano /etc/tor/torrc`로 Tor 설정을 열고 맨 끝에 추가해요:

   ```
   HiddenServiceDir /var/lib/tor/electrs_hidden_service/
   HiddenServiceVersion 3
   HiddenServicePort 50001 127.0.0.1:50001
   ```

   **Ctrl + X**, **y**, **Enter**를 눌러요.

2. Tor를 다시 시작해요:

   ```bash
   systemctl restart tor
   ```

3. electrs의 onion 주소를 확인해요:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

<details>
<summary><b>🔄 electrs의 onion 주소 바꾸기</b></summary>

1. onion 주소를 만드는 개인 키가 든 `electrs_hidden_service` 폴더를
   지워요:

   ```bash
   rm -r /var/lib/tor/electrs_hidden_service/
   ```

2. Tor를 다시 시작하면 새 공개 키와 개인 키로 폴더를 다시 만들어요:

   ```bash
   systemctl restart tor
   ```

3. 새 onion 주소를 확인해요:

   ```bash
   cat /var/lib/tor/electrs_hidden_service/hostname
   ```

</details>

# 📚 더 보기

## ➕ 다음 단계

- **Core Lightning (CLN):** 결제를 위한 라이트닝 네트워크, 설정은 별도
  강의에서 다뤄요.
- **Mempool.space:** 직접 호스팅하는 블록 탐색기. MariaDB, Node.js,
  백엔드와 프런트엔드를 쓰고 `pm2`로 프로세스를 관리해요.

## 🧪 테스트넷

배우기용: 가치가 없는 코인과 아주 적은 수수료. 채굴은 오르락내리락하고
난이도도 바뀌어요.

- 지갑: Green, Electrum, Specter, Sparrow
- BTCBouncer: 테스트넷 결제
- BTCSigner: 테스트넷 멀티시그

## ⌨️ bitcoin-cli

형식: `bitcoin-cli [options] <command> [params]`, 그리고 `bitcoin-cli
help`가 모든 명령을 보여 줘요.

- 블록체인: `getblockcount`, `getblockchaininfo`, `getblock`
- 네트워크: `getnetworkinfo`, `getpeerinfo`
- 지갑: `getbalance`, `sendtoaddress`, `listunspent`
- Raw: `createpsbt`, `decodepsbt`, `signrawtransactionwithwallet`

## 🔗 자료

- [Officine Bitcoin 강의](https://officinebitcoin.it/lezioni/fulhar/):
  하드웨어, Bitcoin Core, electrs, 테스트넷, bitcoin-cli
- [Officine Bitcoin Debian 강의](https://officinebitcoin.it/lezioni/debian/)
- [Learning Bitcoin from the Command Line](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line),
  [Using Tor](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_0_Using_Tor.md)와
  [Changing Your Bitcoin Hidden Services](https://github.com/BlockchainCommons/Learning-Bitcoin-from-the-Command-Line/blob/master/14_2_Changing_Your_Bitcoin_Hidden_Services.md) 포함
- [Bitcoin Core](https://bitcoincore.org/)와
  [Tor 문서](https://github.com/bitcoin/bitcoin/blob/master/doc/tor.md)
- [Setting up a Tor hidden service](https://en.bitcoin.it/wiki/Setting_up_a_Tor_hidden_service)
- [electrs](https://github.com/romanz/electrs)와
  [설정 문서](https://github.com/romanz/electrs/blob/master/doc/config.md)
- [Bitcoin Core 설정 생성기](https://jlopp.github.io/bitcoin-core-config-generator/)
- [Mempool](https://mempool.space/)
- [ODROID-M1 Petitboot 자동 부팅](https://wiki.odroid.com/getting_started/petitboot/os_installation_using_otg?s[]=autoboot#set_autoboot)과
  [ODROID 포럼 글](https://forum.odroid.com/viewtopic.php?t=37109)

## 🧱 다른 가이드

- [MiniBolt](https://minibolt.minibolt.info/): 대략 2010년 이후에 나온
  Intel이나 AMD 프로세서가 달린 미니 PC로 노드를 만들어요.

💬 Malaga 2140 텔레그램 채팅방에서 찾았어요.

MiniBolt가 직접 정한 [하드웨어 요구 사항](https://github.com/minibolt-guide/minibolt/blob/main/index-1/preparations.md#hardware-requirements)은
이래요:

- CPU: 2010년 이후 세대의 Intel/AMD 프로세서
- RAM: 2GB 이상(비트코인만), 4GB 이상 권장(비트코인,
  라이트닝, 추가 기능)
- 저장 공간: SATA/NVMe SSD 2TB 이상 권장
- 설치에 쓸 4GB 이상의 USB 메모리나 SD 카드
- 처음 설정할 때 쓸 임시 모니터나 TV, 그리고 USB 또는 PS2 키보드

<a id="credits"></a>

## 🙏 출처

이 [Satoshi Spritz](https://satoshispritz.it/) 발표들(이탈리아어)을
옮겼어요:

- [비트코인 노드: 소프트웨어, 하드웨어, DIY](https://satoshispritz.it/presentazioni/260311-bitcoin-node.pdf),
  Valerio Vaccaro, Satoshi Spritz Milano,
  [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) 라이선스
- [ODROID-M1 비트코인 노드, 1부: 운영체제 설치](https://satoshispritz.it/presentazioni/241031-odroid.pdf),
  Satoshi Spritz Treviso, 2024년 10월 29일
- [ODROID-M1 비트코인 노드, 2부: Bitcoin Core 설치와 동기화 시작](https://satoshispritz.it/presentazioni/241031-odroid2.pdf),
  Satoshi Spritz Treviso, 2024년 11월 19일
- [Tor 네트워크의 비트코인 노드](https://satoshispritz.it/presentazioni/241031-tor.pdf),
  Satoshi Spritz Treviso, 2025년 1월 14일
- [Electrum 서버: electrs](https://satoshispritz.it/presentazioni/241031-electrs.pdf),
  Satoshi Spritz Treviso, 2025년 2월 11일

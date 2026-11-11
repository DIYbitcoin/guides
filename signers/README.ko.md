# 📟 DIY 서명 장치: SeedSigner, Kern, KISS Signer

서명 장치는 비트코인 키를 보관하고 거래에 서명하는 작은 오프라인 장치예요.
그래서 키가 컴퓨터나 휴대폰에 절대 닿지 않아요. 이 가이드에서는 오픈 소스
서명 장치를
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)에
설치("플래싱")해요. 카메라와 microSD 슬롯이 있는 3.5인치 터치스크린 보드예요. 납땜은 필요 없어요.

📷 **사용 방법:** 서명 장치는 인터넷에 절대 연결되지 않아요. 장치 카메라로
QR 코드를 스캔해서 Sparrow 같은 지갑 앱과 연결하고, 거래 서명도
같은 방식으로 해요.

⚠️ **세 가지 모두 이 보드에서는 초기 버전이에요. 연습용 시드 단어와
테스트넷(가치가 없는 연습용 비트코인) 또는 아주 적은 금액으로만 쓰고,
진짜 저축에는 절대 쓰지 마세요.**

### 🧰 필요한 것

- 📟 Waveshare 3.5인치 보드
- 🔌 데이터 전송이 되는 USB-C 케이블. 충전 전용 케이블은 보드에 전원은
  들어가지만 컴퓨터가 보드를 전혀 인식하지 못해요.
- 💻 컴퓨터: SeedSigner는 Mac이나 Linux, Kern과 KISS Signer는
  **Chrome**이나 **Brave**가 있는 컴퓨터면 다 돼요

### 👉 서명 장치 고르기

각각 보드를 꽂는 것부터 서명 장치의 첫 화면까지 이어지는 완전한
경로예요. 고른 것 하나만 따라 하세요.

- 🌐 **[Kern](#kern):** 가장 쉬워요. Chrome이나 Brave에서 몇 번 클릭하면
  플래싱돼요.
- 🌐 **[KISS Signer](#kiss):** 이것도 Chrome이나 Brave에서 플래싱해요.
- 💻 **[SeedSigner](#seedsigner):** 터미널에 명령어 몇 개를 입력해서
  플래싱해요. 짧은 페이지 네 개예요.

<a id="kern"></a>

## 🌐 Kern

**Chrome**이나 **Brave**가 필요해요. Safari와 Firefox로는 플래싱할 수
없어요.

1. USB-C 케이블을 보드의 **UART** 포트에 꽂고, 반대쪽을 컴퓨터에
   꽂아요.

   🐧 **Linux에서는** 아래 [Linux 단계](#linux-kern)를 먼저 하세요.

2. [Kern 웹 플래셔](https://odudex.github.io/Kern/flash/)를 열어요.
3. **📦 Latest CI Build**는 선택된 그대로 두세요.
4. **Board**에서 **Waveshare ESP32-P4 3.5" (320×480 SPI)**를 골라요.
   목록이 다른 보드로 시작하니까 꼭 확인하세요.
5. **🔌 Connect**를 클릭해요. 작은 창이 열리면 내 보드를 고르고
   (🍎 이름에 `usbmodem`, 🐧 `ttyACM`이 들어 있어요) **연결**을
   클릭해요.

   ✅ 버튼 아래 검은 상자에 `Connected to: ESP32-P4`가 나와요.
   플래셔가 칩을 읽어서 v1.x나 v3.x 빌드를 알아서 골라요.

6. **⚡ Flash**를 클릭하고, 케이블을 뽑지 말고 기다려요.

   ✅ `Device reset. Flash successful! ✓`가 나와요.

7. 보드를 뽑고 3초 기다린 다음 다시 꽂아요.

   ✅ 보드에서 Kern이 시작돼요. 🎉

<details id="linux-kern">
<summary><b>🐧 Linux 단계: 브라우저가 보드와 통신하게 하기</b></summary>

1. **터미널**을 열어요: **Ctrl + Alt + T**를 눌러요.
2. 실행해요:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 컴퓨터 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는 게
   정상이니까 그냥 입력하고 **Enter**를 누르세요.

3. **컴퓨터에서 로그아웃했다가 다시 로그인**한 다음, 위의 2단계로
   돌아가요.

</details>

📦 공식 [Kern 릴리스](https://github.com/odudex/Kern/releases)는
**v1.x** 칩만 지원하니까 웹 플래셔를 쓰세요.

다음: [🔋 배터리 넣기](#battery), 문제가 생겼다면
[🩺 문제 해결](#troubleshooting).

<a id="kiss"></a>

## 🌐 KISS Signer

**Chrome**이나 **Brave**가 필요해요. Safari와 Firefox로는 플래싱할 수
없어요.

1. USB-C 케이블을 보드의 **UART** 포트에 꽂고, 반대쪽을 컴퓨터에
   꽂아요.

   🐧 **Linux에서는** 아래 [Linux 단계](#linux-kiss)를 먼저 하세요.

2. [KISS Signer 설치 페이지](https://diybitcoin.github.io/kiss-signer/)를
   열어요.
3. **Which board is it?**에서 **Waveshare 3.5in**을 골라요.
4. **This erases the device. I'm starting with keys that hold nothing.**
   상자에 체크해요.
5. **Connect and install**을 클릭해요. 작은 창이 열리면 내 보드를
   고르고 (🍎 이름에 `usbmodem`, 🐧 `ttyACM`이 들어 있어요) **연결**을
   클릭해요.
6. 다음 창에서 **Install KISS Signer (Waveshare 3.5in)**을 클릭해요.
   **Erase device**에 체크하고 **Next**, 그다음 **Install**을 클릭해요.
   케이블을 뽑지 말고 기다려요.

   ✅ **Installation complete!**가 나와요.

7. 보드를 뽑고 3초 기다린 다음 다시 꽂아요.

   ✅ 보드가 KISS Signer의 첫 화면인 게임 **FRUIT ISLAND**로 시작해요. 🎉

8. 화면이 계속 검은가요? 보드에 새 **v3.x** 칩이 들어 있는데 이
   릴리스는 v1.x용이에요.
   [테스트 설치 페이지](https://diybitcoin.github.io/kiss-signer/test/)에서
   다시 플래싱하세요: **1. Your board**에서 보드를 고르고
   **Connect and install**을 클릭해요. 칩을 읽어서 맞는 빌드를 골라요.

   ⚠️ 테스트 설치 페이지에는 서명되지 않은 테스트 버전이 있고, 실제
   v3.x 보드에서는 아직 시험해 보지 않았어요.

<details id="linux-kiss">
<summary><b>🐧 Linux 단계: 브라우저가 보드와 통신하게 하기</b></summary>

1. **터미널**을 열어요: **Ctrl + Alt + T**를 눌러요.
2. 실행해요:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 컴퓨터 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는 게
   정상이니까 그냥 입력하고 **Enter**를 누르세요.

3. **컴퓨터에서 로그아웃했다가 다시 로그인**한 다음, 위의 2단계로
   돌아가요.

</details>

다음: [🔋 배터리 넣기](#battery), 문제가 생겼다면
[🩺 문제 해결](#troubleshooting).

<a id="seedsigner"></a>

## 💻 SeedSigner 1: 컴퓨터 준비하기

이 보드용 SeedSigner는 Keith(kdmukai)가 만든 초기 버전이에요. 칩
제조사가 만든 도구인 `esptool`로 **터미널**에 명령어를 입력해서
플래싱해요. 아래 명령어마다 **복사** 버튼이 있어요: 복사해서 터미널에
붙여 넣고 **Enter**를 누르세요.

<details>
<summary><b>🍎 Mac을 써요</b></summary>

1. **터미널**을 열어요: **Cmd + Space**를 누르고 `Terminal`을 입력한
   다음 **Enter**를 눌러요.

2. 터미널용 Mac 앱 설치 도구인 **Homebrew**를 설치해요. 이미 있다면
   건너뛰세요 (`brew --version`을 실행하면 버전이 나와요).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는 게
   정상이니까 그냥 입력하고 **Enter**를 누르세요.

   ⚠️ 설치가 끝나면 명령어 두세 개가 담긴 **Next steps**가 나와요.
   그것도 복사해서 실행하세요. 안 그러면 Mac이 `brew`를 찾지 못해요.

3. esptool을 설치해요:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Linux를 써요</b></summary>

1. **터미널**을 열어요: **Ctrl + Alt + T**를 눌러요.

2. 내 사용자가 보드와 통신하게 해요:

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 컴퓨터 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는 게
   정상이니까 그냥 입력하고 **Enter**를 누르세요.

3. `esptool`과 `unzip`을 설치해요. 한 줄씩 실행하세요:

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

4. **컴퓨터에서 로그아웃했다가 다시 로그인**한 다음, 터미널을 다시
   열어요.

</details>

✅ esptool이 잘 되는지 확인해요:

```bash
esptool version
```

**5.3 이상**이 나와야 해요. 더 오래된 버전은 새 칩을 플래싱하지 못해요:
🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`.

다음: [SeedSigner 2: 보드 꽂고 칩 확인하기](#ss-chip).

<a id="ss-chip"></a>

## 💻 SeedSigner 2: 보드 꽂고 칩 확인하기

이 보드에는 두 가지 칩 버전 중 하나가 들어 있어요. 예전 보드는
**v1.x**, 새 보드는 **v3.x**이고, 버전마다 SeedSigner 빌드가 따로
필요해요. 이 페이지에서 내 칩이 어느 쪽인지 알 수 있어요.

1. USB-C 케이블을 보드의 **UART** 포트에 꽂고, 반대쪽을 컴퓨터에
   꽂아요.

2. 내 컴퓨터에 맞는 줄을 실행해요:

   🍎 Mac:

   ```bash
   esptool -p $(ls /dev/cu.usbmodem* | head -n 1) chip-id
   ```

   🐧 Linux:

   ```bash
   esptool -p $(ls /dev/ttyACM* | head -n 1) chip-id
   ```

3. `ESP32-P4 (revision ...)`라고 나온 줄을 찾아요:

   - `revision v1.3`처럼 **v1**이면: **v1.x** 칩이에요.
   - `revision v3.0`, `v3.2`처럼 **v3**이면: **v3.x** 칩이에요.

   적어 두세요: 다음 페이지에서 필요해요.

다음: [SeedSigner 3: 내 칩에 맞는 빌드 내려받기](#ss-download).

<a id="ss-download"></a>

## 💻 SeedSigner 3: 내 칩에 맞는 빌드 내려받기

SeedSigner 2에서 확인한 **내 칩에 맞는 빌드만** 쓰세요. 다른 빌드는
시작되지 않아요.

<details open>
<summary><b>1️⃣ 내 칩은 v1.x예요</b></summary>

클릭해서
[seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)을
**다운로드** 폴더에 내려받아요.

밀어서 넘기면 버튼이 눌리는 대신 메뉴가 스크롤되도록 하는 **터치
수정**이 들어 있어요. 이 수정은 아직
[열린 풀 리퀘스트](https://github.com/kdmukai/esp-board-common/pull/21)이고,
SeedSigner에 정식으로 들어가지 않았어요. v1.3 보드에서 시험했어요.

zip 파일의 SHA256:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<details open>
<summary><b>3️⃣ 내 칩은 v3.x예요</b></summary>

Keith의 빌드는 GitHub에서 나와서, 내려받으려면 무료 GitHub 계정이
필요해요.

1. [github.com/login](https://github.com/login)에서 로그인하거나, 먼저
   계정을 만들어요.
2. [SeedSigner 최신 빌드](https://github.com/kdmukai/seedsigner-micropython-builder/actions/workflows/build-firmware.yml?query=branch%3Amain+is%3Asuccess)를
   열어요.
3. 목록 **맨 위**의 빌드를 클릭해요. 그게 가장 최신이에요.
4. **Artifacts**까지 내려가서
   **flashable-dist-waveshare-esp32-p4-35**를 클릭해 **다운로드**
   폴더에 내려받아요.

   ⚠️ 이름이 **35**로 끝나는지 꼭 확인하세요. `43`은 4.3인치 보드용이고,
   Keith의 Releases 페이지에 있는 파일도 4.3인치용이에요. 둘 다 이
   보드에서는 시작되지 않아요.

</details>

다음: [SeedSigner 4: 압축 풀고 플래싱하기](#ss-flash).

<a id="ss-flash"></a>

## 💻 SeedSigner 4: 압축 풀고 플래싱하기

1. 보드는 **UART** 포트에 꽂아 둔 채로 두세요. 내 컴퓨터와 칩에 맞는 줄
   **하나**만 실행해요. 다운로드 폴더로 가서 빌드 압축을 풀고(브라우저가
   이미 풀었다면 그 폴더를 써요) 플래싱해요.

   🍎 Mac, v1.x 칩:

   ```bash
   cd ~/Downloads && { [ -f seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip ] && unzip -oq seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip; cd seedsigner-waveshare-p4-35-pre-rev3-touchfix; } && esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🍎 Mac, v3.x 칩:

   ```bash
   cd ~/Downloads && { [ -f flashable-dist-waveshare-esp32-p4-35.zip ] && unzip -oq flashable-dist-waveshare-esp32-p4-35.zip -d flashable-dist-waveshare-esp32-p4-35; cd flashable-dist-waveshare-esp32-p4-35; } && esptool --chip esp32p4 -p $(ls /dev/cu.usbmodem* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux, v1.x 칩:

   ```bash
   cd ~/Downloads && { [ -f seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip ] && unzip -oq seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip; cd seedsigner-waveshare-p4-35-pre-rev3-touchfix; } && esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   🐧 Linux, v3.x 칩:

   ```bash
   cd ~/Downloads && { [ -f flashable-dist-waveshare-esp32-p4-35.zip ] && unzip -oq flashable-dist-waveshare-esp32-p4-35.zip -d flashable-dist-waveshare-esp32-p4-35; cd flashable-dist-waveshare-esp32-p4-35; } && esptool --chip esp32p4 -p $(ls /dev/ttyACM* | head -n 1) write-flash @flash_args
   ```

   4분쯤 걸리니까 보드를 뽑지 마세요. 처음에 나오는 `Deprecated`
   경고 몇 개는 괜찮아요.

   ✅ 끝나면 보드가 다시 시작되면서 SeedSigner가 켜져요. 🎉

2. 테스트 데이터로 써 보세요: 컴퓨터에서
   [testqrs.com](https://testqrs.com)을 열고 보드 카메라로 QR 코드를
   비춰요. 그 시드 단어는 공개된 것이니까 연습용으로만 쓰세요.

다음: [🔋 배터리 넣기](#battery), 문제가 생겼다면
[🩺 문제 해결](#troubleshooting).

<a id="battery"></a>

## 🔋 배터리 넣기

보드에는 작은 배터리와 작은 드라이버가 들어 있어서 케이블 없이도 쓸 수
있어요.

1. 케이스 뒷면에 있는 작은 덮개 네 개를 손톱으로 떼어 내요. 그 아래에
   나사가 있어요.
2. 드라이버로 나사 네 개를 풀고 케이스 뒷면을 들어 올려요.
3. 배터리 플러그를 보드 오른쪽 아래의 배터리 소켓에 꽂아요. 한 방향으로만
   들어가니까 안 들어가면 뒤집어 보세요. 억지로 밀어 넣지 마세요.
4. ✅ 전원 버튼 옆에 초록색 불이 켜져야 해요.
5. 케이스 뒷면을 다시 덮고 나사를 조여요. 배터리를 넣어도 케이스는
   평소처럼 닫혀요.

![케이스를 열고 배터리를 꽂은 Waveshare 3.5인치 보드](images/battery.jpg)

<a id="troubleshooting"></a>

## 🩺 문제 해결

| 이런 게 보이면 | 이렇게 하세요 |
|---|---|
| 브라우저 창에 고를 보드가 없어요 | **Chrome**이나 **Brave**, **UART** 포트, 데이터 케이블을 쓰세요. 🐧 [Kern](#linux-kern)이나 [KISS Signer](#linux-kiss) 페이지의 Linux 단계를 하세요. |
| `port is busy` 또는 `could not open port` | 웹 플래셔 탭과 보드를 쓰는 다른 터미널을 닫고 다시 해 보세요. |
| 🍎 `no matches found: /dev/cu.usbmodem*` 또는 🐧 `No such file or directory` | 컴퓨터가 보드를 못 찾고 있어요: **UART** 포트와 데이터 전송이 되는 케이블을 쓰세요. |
| 🐧 `Permission denied`, 또는 브라우저가 보드를 열지 못해요 | 로그아웃했다가 다시 로그인하는 것까지 포함해서 Linux 단계를 하세요. |
| `Failed to connect` | 보드를 쓰는 다른 프로그램을 닫고, 보드를 뽑았다가 다시 꽂은 다음 다시 해 보세요. |
| 🍎 `brew: command not found` | Homebrew 설치가 끝날 때 나온 **Next steps** 명령어를 실행하세요. |
| 🐧 `esptool: command not found` | 로그아웃했다가 다시 로그인한 다음, 새 터미널을 여세요. |
| `requires chip revision in range` | 빌드가 칩과 맞지 않아요: [SeedSigner 2](#ss-chip)를 다시 하고 다른 빌드를 내려받으세요. |
| `cd: no such file or directory: …` or `cd: …: No such file or directory` | 빌드가 다운로드 폴더에 없거나 다른 칩용 빌드예요: [SeedSigner 3](#ss-download)을 다시 하세요. |
| 플래싱 후 화면에 아무것도 안 나와요 | 뽑고 3초 기다린 다음 다시 꽂으세요. 빌드가 칩과 맞는지 확인하세요. |
| `esptool`이 칩을 플래싱하지 못해요 | 5.3 이상이어야 해요: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 감사의 말

- [Kern](https://github.com/odudex/Kern), odudex.
- [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder), kdmukai.
- [KISS Signer](https://github.com/DIYbitcoin/kiss-signer), DIYbitcoin.

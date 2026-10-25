# 📟 DIY 서명 기기: SeedSigner, Kern, KISS Signer

서명 기기는 비트코인 키를 보관하고 거래에 서명하는 작은 오프라인 기기예요.
그래서 키가 컴퓨터나 휴대폰에 절대 닿지 않아요. 이 가이드에서는 오픈 소스
서명 기기를
[Waveshare ESP32-P4-WIFI6-Touch-LCD-3.5](https://www.waveshare.com/esp32-p4-wifi6-touch-lcd-3.5.htm)에
설치("플래싱")해요. 카메라와 microSD 슬롯이 있는 3.5인치 터치스크린 보드예요. 납땜은 필요 없어요.

| 서명 기기 | 플래싱 방법 | 상태 |
|---|---|---|
| [Kern](https://github.com/odudex/Kern) | 웹 브라우저에서 | 테스트 버전 |
| [KISS Signer](https://github.com/DIYbitcoin/kiss-signer) | 웹 브라우저에서 | 베타 |
| [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder) | 터미널에서 | 테스트 버전 |

📷 **사용 방법:** 서명 기기는 인터넷에 절대 연결되지 않아요. 기기 카메라로
QR 코드를 스캔해서 Sparrow 같은 지갑 앱과 연결하고, 거래 서명도
같은 방식으로 해요.

⚠️ **세 가지 모두 이 보드에서는 초기 버전이에요. 연습용 시드 단어와
테스트넷(가치가 없는 연습용 비트코인) 또는 아주 적은 금액으로만 쓰고,
진짜 저축에는 절대 쓰지 마세요.**

## 🔌 1단계: 보드 연결하기

**UART**라고 표시된 포트에 USB-C 케이블을 꽂으세요.

💡 데이터가 통하는 게 확실한 케이블을 쓰세요. 충전 전용 케이블을 쓰면 보드에
전원은 들어가지만, 컴퓨터는 아무것도 알아보지 못해요.

<a id="step-2"></a>

## 💻 2단계: 컴퓨터 준비하기

🌐 **Kern**과 **KISS Signer**는 **Chrome**이나 **Brave**에서 웹 설치 도구로
플래싱해요. 그래서 이 둘에는 그 브라우저만 있으면 돼요(Linux라면 아래
Linux 항목의 2번도 하세요).

💻 **SeedSigner**는 터미널에서 `esptool`로 플래싱해요: 이 도구는 아래에서
설치하세요.

<details>
<summary><b>🍎 Mac을 써요</b></summary>

1. **터미널**을 여세요: **Cmd + Space**를 누르고 `Terminal`을 입력한 뒤
   **Enter**를 누르세요.

2. 터미널에서 쓰는 Mac 앱 설치 도구인 **Homebrew**를 설치하세요. 이미
   있다면 건너뛰세요(`brew --version`을 실행하면 버전이 나와요).

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

   🔒 Mac 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는데,
   정상이에요. 그냥 입력하고 **Enter**를 누르세요.

   ⚠️ 설치가 끝나면 명령어 두세 개와 함께 **Next steps**가 나와요.
   그 명령어도 복사해서 실행하세요. 안 그러면 Mac이 `brew`를 찾지 못해요.

3. esptool을 설치하세요:

   ```bash
   brew install esptool
   ```

</details>

<details>
<summary><b>🐧 Linux를 써요</b></summary>

1. **터미널**을 여세요: **Ctrl + Alt + T**를 누르거나, 앱 목록에서
   **터미널**을 찾으세요.

2. 내 사용자 계정이 보드와 통신할 수 있게 하세요(브라우저에서 쓰는 Kern을
   포함해 세 서명 기기 모두에 필요해요):

   ```bash
   sudo usermod -aG dialout $USER
   ```

   🔒 컴퓨터 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는데,
   정상이에요. 그냥 입력하고 **Enter**를 누르세요.

3. 변경 사항이 적용되도록 **컴퓨터에서 로그아웃했다가 다시 로그인하세요**.

4. SeedSigner에 필요한 `esptool`과 `unzip`을 설치하세요. 터미널을 다시 열고
   실행하세요:

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

5. `esptool`을 찾을 수 있도록 **터미널을 닫고 새로 여세요**.

</details>

✅ SeedSigner를 쓸 거라면 esptool이 작동하는지 확인하세요:

```bash
esptool version
```

**5.3 이상**이 나와야 해요.

<a id="step-3"></a>

## 🧬 3단계: 내 칩 확인하기

3.5인치 보드에는 ESP32-P4 칩 두 버전 중 하나가 들어 있어요: 예전 보드에는
**v1.x**, 새 보드에는 **v3.x**가 들어 있어서, 최근에 산 보드라면 v3.x일
가능성이 높아요. 서명 기기마다 내 칩에 맞게 만든 빌드가 필요해요:
v1.x 빌드는 v3.x 칩에서 켜지지 않고, 그 반대도 마찬가지예요.

| 서명 기기 | v1.x 칩 | v3.x 칩 |
|---|---|---|
| Kern | 웹 설치 도구 또는 공식 릴리스 | 웹 설치 도구만(테스트 버전) |
| KISS Signer | 설치 페이지(서명된 베타) | 테스트 설치 도구(테스트 버전) |
| SeedSigner | 터치 수정 빌드 | 빌더의 테스트 빌드 |

🌐 **Kern**과 **KISS Signer:** 확인할 필요 없어요. 아래 각 부분에서
방법을 알려 줘요.

💻 **SeedSigner:** 맞는 빌드를 내려받을 수 있도록 지금 `esptool`([2단계](#step-2))로
확인하세요:

1. 보드가 어디 있는지 터미널에 알려 주세요. 이 명령은 그 위치를 `$PORT`로 저장해요.

   🍎 Mac:

   ```bash
   PORT=$(ls /dev/cu.usbmodem* | head -n 1)
   ```

   🐧 Linux:

   ```bash
   PORT=$(ls /dev/ttyACM* | head -n 1)
   ```

2. 칩을 확인하세요:

   ```bash
   esptool -p $PORT chip-id
   ```

   ✅ `ESP32-P4 (revision v1.3)` 같은 게 나오면 **v1.x** 칩이에요.
   `v3.0` 이상이면 **v3.x** 칩이에요.

## Kern

1. **Chrome**이나 **Brave**에서 [Kern 웹 설치 도구](https://odudex.github.io/Kern/flash/)를
   여세요(Safari와 Firefox로는 플래싱할 수 없어요).
2. 보드를 연결하고 **Latest CI Build**를 플래싱하세요. 설치 도구가 칩을
   읽고 v1.x나 v3.x 빌드를 알아서 골라요.
3. ✅ 뽑고 3초 기다린 다음 다시 꽂으세요.

📦 공식 [Kern 릴리스](https://github.com/odudex/Kern/releases)는
**v1.x** 칩만 지원해요.

## KISS Signer

1. **Chrome**이나 **Brave**에서 [설치 페이지](https://diybitcoin.github.io/kiss-signer/)를
   열고, 보드를 연결해서 플래싱하세요.
2. ✅ 뽑고 3초 기다린 다음 다시 꽂으세요.
3. 화면이 계속 검은가요? 보드에 새 v3.x 칩이 들어 있는 거예요: 대신
   [테스트 설치 도구](https://diybitcoin.github.io/kiss-signer/test/)에서
   다시 플래싱하세요.

⚠️ 테스트 설치 도구에는 서명되지 않은 테스트 버전이 들어 있고, 실제
v3.x 보드에서는 아직 해 보지 않았어요.

## SeedSigner

이 보드용 SeedSigner는 초기 버전이고, 터미널에서
`esptool`([2단계](#step-2))로 플래싱해요.

### 1️⃣ 내 칩에 맞는 빌드 내려받기

**다운로드** 폴더에 저장하세요:

- **v1.x**:
  [seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip](https://github.com/DIYbitcoin/guides/releases/download/seedsigner-p4-35-touchfix/seedsigner-waveshare-p4-35-pre-rev3-touchfix.zip)
- **v3.x**: `flashable-dist-waveshare-esp32-p4-35`, 빌더의
  [Actions 빌드](https://github.com/kdmukai/seedsigner-micropython-builder/actions)에서 받아요
  (GitHub 로그인 필요)

<details>
<summary><b>👆 v1.x 빌드에 대해</b></summary>

화면을 밀었을 때 버튼이 눌리지 않고 메뉴가 스크롤되도록 하는 **터치 수정**이
들어 있어요. 이 수정은 아직
[열려 있는 pull request](https://github.com/kdmukai/esp-board-common/pull/21)라서,
SeedSigner에는 들어가지 않았어요. v1.3 보드에서 테스트했어요.

zip 파일의 SHA256:
`d372f345a1895f2e90ed5d86cf473136bd69948ec2384de7c8dd9313eea169e5`

</details>

<a id="seedsigner-unzip"></a>

### 2️⃣ 압축 풀기

다운로드 폴더로 이동하세요:

```bash
cd ~/Downloads
```

그다음 **내** 빌드에 해당하는 두 줄을 실행하세요.

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

### 3️⃣ 플래싱하기

💡 [3단계](#step-3) 이후에 터미널을 새로 열었다면, 먼저 3단계의 `PORT=`
줄을 다시 실행하세요.

```bash
esptool --chip esp32p4 -p $PORT write-flash @flash_args
```

✅ 끝나면 보드가 SeedSigner로 다시 시작돼요. 🎉

<a id="battery"></a>

## 🔋 배터리 넣기

보드에는 작은 배터리와 작은 드라이버가 딸려 와서, 케이블 없이도
쓸 수 있어요.

1. 케이스 뒷면의 작은 덮개 네 개를 손톱으로 떼어 내세요. 그 밑에 나사가
   있어요.
2. 드라이버로 나사 네 개를 풀고 케이스 뒷면을 들어 올려 떼어 내세요.
3. 배터리 플러그를 보드 오른쪽 아래에 있는 배터리 소켓에 꽂으세요.
   한 방향으로만 들어가니까, 안 들어가면 뒤집어 보세요. 억지로 밀어 넣지
   마세요.
4. ✅ 전원 버튼 옆에 초록색 불이 켜져야 해요.
5. 케이스 뒷면을 다시 덮고 나사를 조여 닫으세요. 배터리를 넣은 채로도
   케이스가 평소처럼 닫혀요.

![케이스를 열고 배터리를 연결한 Waveshare 3.5인치 보드](images/battery.jpg)

## 🩺 문제 해결

| 이런 게 보이면 | 이렇게 하세요 |
|---|---|
| 브라우저 설치 도구에 고를 보드가 안 나와요 | **Chrome**이나 **Brave**, **UART** 포트, 데이터 케이블을 쓰세요. 🐧 Linux에서는 [2단계](#step-2)를 하세요. |
| `port is busy` 또는 `could not open port` | 브라우저 설치 도구 탭과 보드를 쓰고 있는 다른 터미널을 모두 닫고 다시 해 보세요. |
| 컴퓨터가 보드를 못 알아보거나, `PORT=` 줄 다음에 🍎 `no matches found: /dev/cu.usbmodem*` / 🐧 `No such file or directory`가 나와요 | **UART** 포트와 데이터가 통하는 게 확실한 케이블을 쓰세요. |
| 🐧 `Permission denied`, 또는 브라우저가 보드를 못 열어요 | Linux에서 로그아웃했다가 다시 로그인하는 것까지 포함해 [2단계](#step-2)를 하세요. |
| 🍎 `brew: command not found` | Homebrew 설치가 끝날 때 나온 **Next steps** 명령어를 실행하세요. |
| 🐧 `esptool: command not found` | 터미널을 닫고 새로 여세요. |
| `flash_args`를 찾을 수 없어요 | 압축을 푼 폴더에 있지 않은 거예요: [SeedSigner 2번](#seedsigner-unzip)을 다시 하세요. |
| 플래싱한 뒤 화면에 아무것도 안 나와요 | 뽑고 3초 기다린 다음 다시 꽂으세요. 빌드가 내 칩에 맞는지 확인하세요. |
| `esptool`이 칩을 플래싱하지 못해요 | 5.3 이상이어야 해요: 🍎 `brew upgrade esptool`, 🐧 `pipx upgrade esptool`. |

## 🙏 감사의 말

- odudex의 [Kern](https://github.com/odudex/Kern).
- kdmukai의 [SeedSigner](https://github.com/kdmukai/seedsigner-micropython-builder).
- DIYbitcoin의 [KISS Signer](https://github.com/DIYbitcoin/kiss-signer).

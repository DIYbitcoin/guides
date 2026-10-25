# 💳 자바카드 플래싱하기: Satochip, Satodime, SeedKeeper, Keycard

자바카드에는 **보안 요소**가 있어요: 누가 카드를 손에 넣더라도
비트코인 키 같은 비밀을 안전하게 지키도록 만든 칩이에요. 여기에
**애플릿**이라는 작은 앱을 설치할 수 있어요. 이 가이드에서는 오픈 소스
비트코인 애플릿 네 가지 중 하나를 빈 J3H145 자바카드에 설치해요:

- **Satochip:** Sparrow Wallet과 함께 쓰는 DIY 비트코인 지갑
- **Satodime:** 현금처럼 카드를 가진 사람이 그 안에 든 비트코인의
  주인이 되는 무기명 카드
- **SeedKeeper:** 시드 단어와 패스프레이즈, 비밀번호, 지갑 디스크립터 등
  여러 비밀을 PIN으로 잠가 두는 암호화 백업
- **Keycard:** Sparrow Wallet과 함께 쓰는 또 다른 DIY 비트코인 지갑

애플릿은 [Satochip](https://satochip.io)과
[Keycard](https://keycard.tech)에서 나왔고, 이 가이드는
[Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) 가이드를 따라가요.

![컴퓨터, 리더기, 카드와 애플릿 네 가지](images/00-overview.svg)

## 🧰 준비물

- 💳 **J3H145** 자바카드. 사고 싶다면 이 가이드에서 쓴 카드는 이거예요:
  [Alibaba의 J3H145](https://www.alibaba.com/x/B2cDpJ?ck=pdp)
- 🔌 **USB 스마트카드 리더기**: 컴퓨터에 꽂고 카드를 칩 쪽부터 밀어 넣는
  작은 상자예요. 카드를 갖다 대는 NFC 리더기인 **ACS ACR122U**는 쓰지
  마세요: [Satochip-DIY 가이드](https://github.com/3rdIteration/Satochip-DIY#smartcard-readers)에서
  애플릿을 설치할 때 잘 안 될 수 있고 카드가 망가질 수도 있다고 경고해요.
  애플릿을 설치한 뒤 카드를 쓸 때는 괜찮아요.
- 🛡️ [ShieldSigner](https://github.com/3rdIteration/seedsigner)가 있나요?
  컴퓨터와 리더기 대신 그걸로도 이 애플릿들을 카드에 설치할 수
  있어요.
- 💻 **Mac**, **Linux**(Debian, Ubuntu, Raspberry Pi OS) 또는 **Windows**
  컴퓨터
- 🌐 다운로드에 쓸 인터넷 연결

## 🛠️ 1단계: 컴퓨터 준비하기 (한 번만)

여기서는 **Java**와, 카드에 애플릿을 넣는 `gp` 명령어인
**GlobalPlatformPro**를 설치해요.

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

3. Java를 설치하세요:

   ```bash
   brew install --cask temurin
   ```

4. GlobalPlatformPro를 설치하세요:

   ```bash
   brew install martinpaljak/brew/gppro
   ```

</details>

<details>
<summary><b>🐧 Linux를 써요</b></summary>

1. **터미널**을 여세요: **Ctrl + Alt + T**를 누르거나, 앱 목록에서
   **터미널**을 찾으세요.

2. Java, git, 스마트카드 서비스(`pcscd`)를 설치하세요:

   ```bash
   sudo apt update
   ```

   🔒 컴퓨터 비밀번호를 물어봐요. 입력하는 동안 아무것도 안 보이는데,
   정상이에요. 그냥 입력하고 **Enter**를 누르세요.

   ```bash
   sudo apt install default-jre pcscd git
   ```

3. GlobalPlatformPro가 `gp.jar`로 들어 있는
   [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY) 파일을 내려받으세요:

   ```bash
   git clone https://github.com/3rdIteration/Satochip-DIY.git ~/Satochip-DIY
   ```

4. 이 가이드의 모든 명령어가 적힌 그대로 작동하도록 짧은 이름 `gp`를
   붙여 주세요:

   ```bash
   echo "alias gp='java -jar ~/Satochip-DIY/gp.jar'" >> ~/.bashrc
   ```

5. 새 이름이 적용되도록 **터미널을 닫고 새로 여세요**.

</details>

<details>
<summary><b>🪟 Windows를 써요</b></summary>

⚠️ Windows에서는 테스트하지 않았어요.

1. Java를 설치하세요: <https://adoptium.net>에서 **Temurin**을 내려받아
   기본 옵션으로 설치하세요.

2. [gp.exe](https://github.com/3rdIteration/Satochip-DIY/raw/main/gp.exe)를
   [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY)에서
   **다운로드** 폴더로 내려받으세요.

3. **명령 프롬프트**를 여세요: **Windows** 키를 누르고 `cmd`를 입력한 뒤
   **Enter**를 누르세요. 관리자 권한으로 실행하지 않아도 돼요.

4. 다운로드 폴더로 이동하세요:

   ```bat
   cd %USERPROFILE%\Downloads
   ```

5. 이 가이드에 `gp`라고 나오는 곳에는 대신 `gp.exe`를 입력하세요. 예를 들면
   `gp.exe --info`처럼요. `cd ~/Downloads`라고 나오는 곳에는 4번에 나온
   줄을 쓰세요.

</details>

✅ 잘 됐는지 확인하세요:

```bash
gp --version
```

`#`으로 시작하는 줄이 몇 개 나오고 `command not found`가 없으면 잘 된
거예요.

💡 `gp`를 실행할 때마다 Java가 `WARNING:`으로 시작하는 줄을 몇 개 보여 줘요.
정상이니 무시해도 돼요.

## 🔌 2단계: 리더기가 카드를 알아보는지 확인하기

리더기를 꽂고 카드를 (칩 쪽부터) 넣은 뒤 실행하세요:

```bash
gp --info
```

✅ `CPLC` 줄과 `Global Platform card`가 보이면 리더기와 카드가 서로
잘 통하고 있는 거예요:

![gp --info 출력](images/01-gp-info.svg)

이제 카드에 이미 뭐가 들어 있는지 보세요:

```bash
gp --list
```

빈 카드에는 공장에서 넣어 둔 것만 보여요: 카드 관리자(`ISD`)와
패키지 하나(`A0000001515350`)예요. 이건 그대로 두세요.

![빈 카드의 gp --list](images/02-gp-list-blank.svg)

### 🛑 "Card cryptogram invalid"가 보이면 멈추세요

![Card cryptogram invalid 경고](images/07-gp-cryptogram-stop.svg)

⚠️ 카드가 내가 갖고 있지 않은 키로 잠겨 있다는 뜻이에요. **명령어를 다시
실행하지 마세요**: 몇 번 잘못 시도하면 카드가 스스로 영원히 잠겨요.
카드를 판 사람에게 키를 달라고 하세요.

## 📥 3단계: 애플릿 파일 내려받기

원하는 애플릿의 파일을 **다운로드** 폴더에 내려받으세요.
각 페이지에서 **Assets** 아래에 있는 파일 이름을 눌러 내려받으세요.

| 애플릿 | 내려받을 파일 | 설정 도구 |
|---|---|---|
| Satochip | [SatoChip-v0.15-0.1.cap](https://github.com/Toporin/SatochipApplet/releases/tag/v0.15-0.1) | Sparrow Wallet |
| Satodime | [Satodime-0.1-0.2.cap](https://github.com/Toporin/Satodime-Applet/releases/tag/v0.1-0.2) | Sato-Tools |
| SeedKeeper | [SeedKeeper-v0.2-0.1.cap](https://github.com/Toporin/Seedkeeper-Applet/releases/tag/v0.2-0.1) | Sato-Tools |
| Keycard | [keycard_v3.2.cap](https://github.com/keycard-tech/status-keycard/releases/tag/3.2) | Sparrow Wallet |

<details>
<summary><b>💡 왜 최신 버전이 아니라 이 버전을 쓰나요?</b></summary>

- **v0.16이 아니라 Satochip v0.15:** v0.16(2026년 10월)은 카드가 taproot
  키를 조정하는 방식을 바꿨는데, Sparrow 2.5.5는 아직 예전 형식을 보내요.
  그래서 Sparrow의 taproot 지갑은 v0.16 카드로 서명할 수 없어요. Native
  SegWit은 두 버전 모두에서 작동해요.
- **Satodime v0.1-0.2**가 최신 안정 버전이에요. v0.2 빌드는 아직 베타로
  표시돼 있어요.
- **4.0이 아니라 Keycard 3.2:** Keycard 4.0(2026년 9월)은 카드와 컴퓨터가
  암호화된 연결을 맺는 방식을 바꿨는데, Sparrow 2.5.5는 아직 예전 방식을
  써요.

Sparrow가 새 버전을 지원하게 되면 그때 새 버전을 쓰세요.

</details>

<details>
<summary><b>🔍 선택: 파일이 변조되지 않았는지 확인하기</b></summary>

다운로드 폴더로 이동하세요:

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

나오는 긴 코드가 릴리스 페이지에 있는 코드와 같아야 해요:

- `SatoChip-v0.15-0.1.cap`:
  `83b117ebe047acdb1a171196c709016329ae0d221f9c4d0c606076a634ecdc2a`
- `keycard_v3.2.cap`:
  `9ed83c56e64cfb00d732cab4705e6f339fa1f039507ad86de7028bb3056675e8`

</details>

<a id="step-4"></a>

## ⚡ 4단계: 카드에 애플릿 넣기

🔄 카드의 애플릿을 다른 애플릿으로 바꾸고 싶나요? 먼저 기존 애플릿을 지우고
새 애플릿을 설치하세요: 아래
[다른 애플릿으로 바꾸기](#switching)를 보세요.

파일이 있는 다운로드 폴더로 이동하세요:

```bash
cd ~/Downloads
```

그다음 **내 애플릿에 해당하는 줄만** 실행하세요:

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

Keycard(추가 설정을 넣어야 Sparrow가 찾는 지갑이 돼요):

```bash
gp --install keycard_v3.2.cap --applet A000000804000101 --create A00000080400010101
```

✅ `loaded:` 줄로 끝나요:

![gp --install 출력](images/03-gp-install-satochip.svg)

![Keycard용 gp --install](images/09-gp-install-keycard.svg)

✅ `gp --list`를 다시 실행하세요. `APP`으로 시작하고 `SELECTABLE`로 끝나는
새 줄이 있으면 애플릿이 설치되어 쓸 준비가 된 거예요:

![Satochip이 설치된 gp --list](images/04-gp-list-satochip.svg)

이제 내 애플릿의 설정 단계로 가세요:

- Satochip: [5a단계](#step-5a)
- SeedKeeper 또는 Satodime: [5b단계](#step-5b)
- Keycard: [5c단계](#step-5c)

<a id="step-5a"></a>

## 5a단계: Sparrow에서 Satochip 설정하기

👛 <https://sparrowwallet.com/download/>에서 내 컴퓨터에 맞는
Sparrow Wallet을 받으세요.

PIN 설정과 시드 단어 12개 입력은 Sparrow에서 바로 해요. Satochip 앱은
필요 없어요. 카드는 처음부터 끝까지 리더기에 꽂아 두세요.

1. Sparrow에서 새 지갑을 만들고 이름을 붙이세요. **Keystores** 아래에서
   **Connected Hardware Wallet**을 고르고 **Scan...**을 누르세요.

   ![Connected Hardware Wallet, Scan](images/sparrow-1-scan.webp)

2. Sparrow가 **Satochip**을 찾아요. **Import Keystore**를 누르세요.

   ![Sparrow가 Satochip을 찾음](images/sparrow-2-found.webp)

3. Sparrow가 **Choose a PIN code**라고 물어봐요. PIN을 입력하세요.

   ![Choose a PIN code](images/sparrow-3-choose-pin.webp)

4. Sparrow에 **Card not initialized**라고 나와요. 칸에 같은 PIN을 한 번 더
   입력하고 **Initialize**를 누르세요.

   ![Card not initialized, Initialize](images/sparrow-4-initialize.webp)

5. **Satochip Seed Words** 창이 열려요. 새 시드 단어 12개를 만들려면
   **Generate New**를 누르고, 이미 가진 시드 단어 12개가 있으면 입력하세요.
   ✍️ **시드 단어 12개를 종이에 적으세요**. 그다음 **OK**를 누르세요. Sparrow가
   패스프레이즈 없이 시드 단어를 카드에 넣어요.

   💡 Sparrow는 이 시드 단어 12개를 내 컴퓨터에서 만들어요. 그러니 진짜
   저축에 쓸 거라면 믿을 수 있는 컴퓨터를 쓰세요.

   ![Satochip Seed Words](images/sparrow-5-seed-words.webp)

6. ✅ **Card Initialized**. **OK**를 누르세요.

   ![Card Initialized](images/sparrow-6-initialized.webp)

7. 다시 Satochip 줄로 돌아와서 **Import Keystore**를 한 번 더 누르세요.

   ![다시 Import Keystore](images/sparrow-7-import-keystore.webp)

8. 🎉 이제 지갑에 Satochip이 핑거프린트와 함께 보여요. 핑거프린트는 내
   키를 가리키는 짧은 코드예요. **Apply**를 눌러 지갑을 저장하세요.

   ![Sparrow에서 완성된 지갑](images/sparrow-8-wallet.webp)

🔢 Sparrow는 카드에 **PIN 시도 기회 5번**을 줘요. 나중에 키스토어에서
**Change Pin**으로 PIN을 바꿀 수도 있어요.

<a id="step-5b"></a>

## 5b단계: Sato-Tools에서 SeedKeeper 또는 Satodime 설정하기

🧰 <https://github.com/Toporin/Satochip-Utils/releases>에서 내 컴퓨터에 맞는
Sato-Tools를 받으세요(이 가이드에서는
0.4.0-beta를 썼어요).

카드를 리더기에 넣은 채로 Sato-Tools를 열고 **Let's go!**를 누르세요.

![Sato-Tools 시작 화면](images/satotools-0-welcome.webp)

💡 **방금 플래싱한 카드를 Sato-Tools가 못 알아보면 앱을 종료했다가 다시
여세요.**

### SeedKeeper

1. Sato-Tools에 **Your SeedKeeper is connected**라고 나와요.
   **Setup my card**를 누르세요.

   ![Your SeedKeeper is connected](images/satotools-seedkeeper-1-connected.png)

2. **New PIN code**와 **Confirm PIN code**에 PIN을 똑같이 입력하고(4자에서
   16자까지), **Save PIN**을 누르세요.

   ![Setup my card](images/satotools-seedkeeper-2-setup-pin.png)

3. ✅ **Your card is now setup!** **Ok**를 누르세요.

   ![Your card is now setup](images/satotools-seedkeeper-3-success.webp)

4. 🎉 이제 메뉴에 **My secrets**, **Generate secret**, **Import secret**,
   **Backup card**, **Card logs**가 생겨요. SeedKeeper에 시드 단어,
   패스프레이즈, 비밀번호, 지갑 디스크립터를 보관할 준비가 됐어요.

   ![SeedKeeper 준비 완료](images/satotools-seedkeeper-4-ready.png)

### Satodime

1. Sato-Tools에 **Your Satodime is connected**라고 나오고, 금고 세 개가
   보여요: **Vault #0**, **Vault #1**, **Vault #2**.

   ![Your Satodime is connected](images/satotools-satodime-1-connected.webp)

2. **Overview**에서는 금고 세 개가 모두 **uninitialized**로 나와요: 아직
   아무것도 봉인되지 않았다는 뜻이에요.

   ![내 금고](images/satotools-satodime-2-vaults.png)

3. **About**에는 애플릿 버전과 **Card has no owner**가 나와요.
   **Take ownership**은 이 컴퓨터를 카드의 주인으로 만들어요. 금고를
   봉인하려면 먼저 이게 필요해요.

   ![About, Take ownership](images/satotools-satodime-3-about.png)

4. 🎉 **Take ownership**을 누르세요. 이제 About에 **You are the owner**라고
   나오고, 버튼이 **Transfer ownership**으로 바뀌어요. 카드를 다른 사람에게
   넘길 때 쓰는 버튼이에요.

💡 금고를 **봉인**하면 그 금고의 비트코인 주소가 만들어지고, 개인 키는 카드
안에 숨겨져요. **봉인 해제**하면 개인 키가 보여서 비트코인을 옮길 수 있어요.
Satodime을 결제 수단으로 받기 전에 금고가 아직 봉인돼 있는지
확인하세요.

### 📱 휴대폰에서

SeedKeeper와 Satodime은 휴대폰 앱도 있어요. NFC가 되는 휴대폰 뒷면에
카드를 대면 휴대폰에서 쓸 수 있어요.

### ❓ 왜 "Genuine: NO"라고 나오나요?

Satochip에서 산 카드에는 공장에서 넣은 Satochip 인증서가 들어 있고,
**Genuine**은 이 인증서를 확인해요. 직접 플래싱한 DIY 카드에는 그런
인증서가 없어서 항상 **Genuine: [NO]**로 나와요. 애플릿은 완전히 똑같이
작동해요.

<a id="step-5c"></a>

## 5c단계: Sparrow에서 Keycard 설정하기

👛 아직 없다면 <https://sparrowwallet.com/download/>에서 내 컴퓨터에 맞는
Sparrow Wallet을 받으세요.

Keycard는 Satochip과 똑같이 설정하는데, 두 가지가 달라요: PIN은
**정확히 숫자 6자리**이고, 카드는 **PIN을 3번까지만 틀릴 수** 있어요.

1. 새 지갑을 만들고 **Connected Hardware Wallet**을 고른 뒤
   **Scan...**을 누르세요. Sparrow가 **Keycard**를 찾아요. **Import Keystore**를 누르세요.

   ![Sparrow가 Keycard를 찾음](images/keycard-1-found.webp)

2. **정확히 숫자 6자리**로 PIN을 정하세요. 더 짧거나 길면
   **PIN length must be between 6 and 6 characters**라고 나와요.

   ![PIN은 숫자 6자리여야 해요](images/keycard-2-pin-error.webp)

3. 같은 PIN을 한 번 더 입력하고 **Initialize**를 누르세요. Satochip과 같은
   시드 단어 창이 열려요([5a단계](#step-5a)의 5번): **Generate New**를 누르거나
   직접 입력하고, ✍️ **시드 단어 12개를 적으세요**. 그다음 **OK**를 누르세요.
   ✅ **Card Initialized**: **OK**를 누르세요.

   ![Card Initialized](images/keycard-3-initialized.webp)

4. 🎉 **Import Keystore**를 한 번 더 누르세요. 이제 지갑에 Keycard가
   핑거프린트와 함께 보여요. **Apply**를 눌러 지갑을 저장하세요.

   ![Sparrow에서 완성된 Keycard 지갑](images/keycard-4-wallet.webp)

⚠️ **시드 단어 12개가 유일한 복구 방법이에요.** PIN을 3번 틀리면
Keycard가 잠기고, Sparrow에는 잠금을 풀 방법이 없어요. 그렇게 되면
Keycard를 지우고 다시 설치한 다음, Sparrow에서 시드 단어 12개를 다시
입력하세요.

<a id="switching"></a>

## 🔄 다른 애플릿으로 바꾸기

⚠️ **애플릿을 제거하면 그 안에 저장된 모든 것이 지워져요**. Satochip이나
Keycard의 시드, SeedKeeper의 비밀도 포함돼요. 시드 단어 12개를 적어 두었거나
연습용 지갑일 때만 하세요. 카드 자체는 망가지지 않고, 원하는 만큼
다시 플래싱할 수 있어요.

![제거하면 지워지고, 설치하면 새로 시작해요](images/08-swap.svg)

1. 애플릿 파일이 있는 다운로드 폴더로 이동하세요:

   ```bash
   cd ~/Downloads
   ```

2. 카드에 뭐가 있는지 모르겠나요? `gp --list`를 실행하세요: `APP` 줄에 나와요.

3. 지금 카드에 있는 애플릿을 지우세요. **그 애플릿의 줄만** 실행하세요:

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

4. ✅ `gp --list`를 다시 실행하세요. 카드 관리자(`ISD`)와 공장
   패키지(`A0000001515350`)만 남아 있어야 해요.

5. 새 애플릿을 설치하고([4단계](#step-4)) 설정하세요
   ([5a단계](#step-5a), [5b](#step-5b) 또는 [5c](#step-5c)).

예를 들면:

![Satochip을 Satodime으로 바꾸기](images/05-gp-swap-to-satodime.svg)

![Satodime을 SeedKeeper로 바꾸기](images/06-gp-swap-to-seedkeeper.svg)

## 🩺 문제 해결

| 이런 게 보이면 | 이렇게 하세요 |
|---|---|
| 🍎 `brew: command not found` | Homebrew 설치가 끝날 때 나온 **Next steps** 명령어를 실행하세요. |
| 🐧 `gp: command not found` | 터미널을 닫고 새로 여세요. |
| `No such file` 또는 `does not exist` | 먼저 `cd ~/Downloads`를 실행하고, 파일이 다운로드 폴더에 있는지 확인하세요. |
| `Card cryptogram invalid` | 카드가 다른 키로 잠겨 있어요. 멈추고, 다시 시도하지 마세요. |
| `LOAD failed: 0x6438` | 애플릿이 카드에 있는 것보다 새로운 Java Card 버전용으로 만들어졌어요. |
| `INSTALL ... failed: 0x6444` 또는 `0x6F00` | 애플릿에 필요한 기능이 카드에 없어요. |
| 이미 애플릿이 있는 카드에서 `gp --install`이 실패해요 | `gp --list`를 실행하세요. `APP` 줄이 있으면 먼저 그 애플릿을 지우세요([다른 애플릿으로 바꾸기](#switching) 참고). |
| Sparrow의 **Scan...**이 아무것도 못 찾아요 | Sato-Tools와 다른 카드 앱을 모두 종료하고, 카드를 뺐다가 다시 넣은 다음 **Scan...**을 다시 누르세요. |
| PIN을 잊어버렸거나, PIN을 너무 많이 틀려서 카드가 잠겼어요 | 애플릿을 지우고 다시 설치한 다음(카드가 지워져요), 시드 단어 12개로 복원하세요. |
| Sato-Tools가 카드를 못 알아봐요 | Sato-Tools를 종료했다가 다시 여세요. |
| `gp`가 리더기를 못 찾거나, 카드가 없다고 해요 | 카드를 칩 쪽부터 넣으세요. 그래도 카드가 없다고 하면 리더기가 멈춘 거예요: 컴퓨터에서 뽑았다가 카드를 넣은 채로 다시 꽂은 뒤 한 번 더 해 보세요. 🐧 그다음 `sudo systemctl restart pcscd`를 실행하세요. |

## 🚀 더 알아보기

- 🎥 [영상: 이 카드에 서로 다른 애플릿 7개 플래싱하기](https://www.youtube.com/watch?v=j13XrvPFZk0)
- 🔒 다른 누구도 카드에 설치된 것을 바꿀 수 없도록 내 키로 카드를
  잠그세요:
  [Satochip-DIY: Locking Javacards](https://github.com/3rdIteration/Satochip-DIY#locking-javacards-optional)
- 🏗️ 애플릿을 직접 빌드하세요. 위의 파일은 다른 사람이 빌드한 것이라서,
  공개된 소스 코드와 같다고 믿고 쓰는 거예요. 소스 코드에서 직접 빌드하면
  카드에 들어가는 것이 누구나 읽을 수 있는 바로 그 코드가
  돼요:
  [Satochip-DIY: Building](https://github.com/3rdIteration/Satochip-DIY#harder-but-better-method---building-on-your-own-pc)

## 🙏 감사의 말

- 🙌 [Toporin](https://github.com/Toporin):
  [Satochip](https://satochip.io)을 세운 분으로, Satochip, Satodime,
  SeedKeeper 애플릿과 Sato-Tools를 만들었어요.
- 🛠️ 3rdIteration의 [Satochip-DIY](https://github.com/3rdIteration/Satochip-DIY):
  이 가이드가 따라가는 DIY 가이드로, 테스트한 카드와
  리더기 목록, `gp.jar`를 제공해요.
- [Keycard](https://github.com/keycard-tech/status-keycard): Keycard
  애플릿을 만들었어요.
- 💳 Martin Paljak의 [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro):
  카드에 애플릿을 올리는 데 써요.

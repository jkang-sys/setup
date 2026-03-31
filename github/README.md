# GIT 사용방법


## 1. Git의 기본 작업 흐름
Git을 이해하는 가장 중요한 개념은 파일이 이동하는 **3가지 공간**입니다.

1.  **Working Directory:** 내가 현재 작업 중인 실제 폴더
2.  **Staging Area:** 저장소에 기록하기 전, "이 파일들을 저장할 거야"라고 선택해 올리는 장바구니
3.  **Repository (Local):** 내 컴퓨터에 최종 저장된 버전들의 기록

---

## 2. 필수 기초 명령어 (Local)

코딩을 시작하고 내 컴퓨터에 저장할 때 사용하는 명령어들입니다.

* **`git init`**: 현재 폴더를 Git 저장소로 초기화합니다. (처음 한 번만 실행)
* **`git status`**: 현재 파일들의 상태(수정됨, 추적 중 아님 등)를 확인합니다.
* **`git add <파일명>`**: 파일을 **Staging Area**로 보냅니다. (모든 파일은 `git add .`)
* **`git commit -m "메시지"`**: Staging Area에 있는 파일들을 버전으로 기록합니다.
* **`git log`**: 지금까지 만든 버전(커밋)들의 목록을 확인합니다.

---

## 3. 원격 저장소와 협업하기 (GitHub 등)

내 컴퓨터(Local)에 있는 내용을 서버(Remote)에 올리거나 가져올 때 사용합니다.

| 명령어 | 설명 |
| :--- | :--- |
| **`git clone <URL>`** | 서버에 있는 기존 프로젝트를 내 컴퓨터로 복사해옵니다. |
| **`git remote add origin <URL>`** | 내 로컬 저장소를 원격 서버 주소와 연결합니다. |
| **`git push origin <브랜치명>`** | 내가 커밋한 내용들을 서버에 업로드합니다. |
| **`git pull origin <브랜치명>`** | 서버의 최신 변경 내용을 내 컴퓨터로 가져와 합칩니다. |

---

## 4. 가지 치기 (Branch & Merge)

여러 기능의 작업을 동시에 진행할 때 사용합니다.

* **`git branch <이름>`**: 새로운 가지(Branch)를 만듭니다.
* **`git checkout <이름>`**: 해당 가지로 이동합니다.
* **`git merge <이름>`**: 다른 가지에서 작업한 내용을 현재 가지로 합칩니다.

---

### 💡 팁: 처음 설정하기
Git을 처음 설치했다면 사용자 정보를 등록해야 커밋이 가능합니다.
```bash
git config --global user.name "내이름"
git config --global user.email "내이메일@example.com"
```

> **주의:** `git add`를 하고 나서 반드시 `git commit`을 해야 실제로 "저장"이 됩니다. 장바구니에 담기만 하고 계산(결제)을 안 하면 물건이 내 것이 안 되는 것과 비슷해요!

혹시 지금 바로 실습해보고 싶은 프로젝트가 있나요? **가장 먼저 `git init`부터 시작해보고 싶으시다면, 폴더를 만드는 방법부터 차근차근 안내해 드릴까요?**

## …or create a new repository on the command line
```bash
echo "# setup" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:jkang-sys/setup.git
git push -u origin main
```
## …or push an existing repository from the command line
```bash
git remote add origin git@github.com:jkang-sys/setup.git
git branch -M main
git push -u origin main
```
---

## 1. SSH 키 생성하기 (내 컴퓨터)

SSH(Secure Shell) 키를 등록하면 매번 아이디와 비밀번호(또는 토큰)를 입력할 필요 없이 안전하게 GitHub과 통신할 수 있습니다. 과정은 **키 생성 -> 공개키 복사 -> GitHub 등록** 순으로 진행됩니다.


먼저 터미널(Git Bash, 터미널, 또는 명령 프롬프트)을 열고 아래 명령어를 입력합니다.

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```
* **`-t ed25519`**: 최신 보안 표준 방식인 ed25519 알고리즘을 사용합니다.
* **`-C`**: 본인의 GitHub 이메일 주소를 입력합니다 (단순 주석용입니다).

**엔터를 세 번 치세요:**
1.  파일 저장 위치 (기본값 엔터)
2.  비밀번호 설정 (추가 보안을 원하면 입력, 없으면 그냥 엔터)
3.  비밀번호 확인 (엔터)


---

## 2. 생성된 공개키(Public Key) 복사하기

키는 쌍으로 생성됩니다. `.pub` 확장자가 붙은 **공개키**의 내용을 복사해야 합니다.

* **Windows (Git Bash):**
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
* **Mac / Linux:**
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
화면에 출력되는 `ssh-ed25519 ...`으로 시작하는 긴 문자열을 모두 드래그해서 **복사(Ctrl+C)** 하세요.

---

## 3. GitHub에 키 등록하기

1.  [GitHub](https://github.com)에 로그인합니다.
2.  우측 상단 프로필 클릭 -> **Settings** 이동.
3.  왼쪽 메뉴에서 **SSH and GPG keys**를 클릭합니다.
4.  초록색 **New SSH key** 버튼을 누릅니다.
5.  **Title:** 내 컴퓨터 이름 (예: My Laptop, Office PC)
6.  **Key:** 아까 복사한 공개키 전체 내용을 붙여넣습니다.
7.  **Add SSH key**를 눌러 저장합니다.

---

## 4. 연결 확인하기

설정이 잘 되었는지 확인하려면 터미널에 다음을 입력하세요.

```bash
ssh -T git@github.com
```
> "Hi [사용자명]! You've successfully authenticated..." 라는 메시지가 나오면 성공입니다!

---

### 💡 주의사항: SSH 주소 사용하기
SSH 키를 등록했다면, 앞으로 원격 저장소를 연결할 때 `HTTPS` 주소가 아닌 **`SSH` 주소**를 사용해야 합니다.

* **기존 HTTPS를 SSH로 바꾸기:**
    ```bash
    git remote set-url origin git@github.com:계정명/저장소명.git
    ```


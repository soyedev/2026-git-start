# 2026 Git Start

GitHub에서 README 수정

오늘의 학습 목표: Git 협업과 충돌 해결 실습


# Git 협업 및 충돌 해결 실습 시퀀스 다이어그램

## 1단계. 전체 실습 구조

```mermaid
%%{init: {
  "theme": "base",
  "sequence": {
    "showSequenceNumbers": true
  },
  "themeVariables": {
    "fontSize": "22px",
    "actorFontSize": "22px",
    "messageFontSize": "21px",
    "noteFontSize": "21px",

    "actorBkg": "#E8F1FF",
    "actorBorder": "#2563EB",
    "actorTextColor": "#0F172A",

    "signalColor": "#334155",
    "signalTextColor": "#0F172A",

    "noteBkgColor": "#FEF3C7",
    "noteBorderColor": "#F59E0B",
    "noteTextColor": "#111827"
  }
}}%%

sequenceDiagram
    autonumber

    participant A as 👤 작업자 A<br/>/c/2026-git-start
    participant G as ☁️ GitHub<br/>origin/main
    participant B as 👤 작업자 B<br/>/c/2026-git-start-b

    rect rgb(232, 241, 255)
        Note over A,B: 1. 같은 GitHub 저장소를 두 작업자가 각각 사용

        A->>G: 기존 로컬 저장소 사용
        G->>B: 같은 저장소를 B 폴더에 Clone

        Note over A,B: A와 B는 같은 GitHub 저장소를 바라보지만<br/>각자의 로컬 저장소는 서로 독립적
    end

    rect rgb(240, 253, 244)
        A->>A: Worker A 작성자 설정
        B->>B: Worker B 작성자 설정
    end

    Note over A,B: 이후 정상 협업 실습과 충돌 해결 실습을 진행
```

---

## 2단계. 충돌 없는 협업 흐름

```mermaid
%%{init: {
  "theme": "base",
  "sequence": {
    "showSequenceNumbers": true
  },
  "themeVariables": {
    "fontSize": "22px",
    "actorFontSize": "22px",
    "messageFontSize": "21px",
    "noteFontSize": "21px",

    "actorBkg": "#E8F1FF",
    "actorBorder": "#2563EB",
    "actorTextColor": "#0F172A",

    "signalColor": "#334155",
    "signalTextColor": "#0F172A",

    "noteBkgColor": "#ECFDF5",
    "noteBorderColor": "#22C55E",
    "noteTextColor": "#111827"
  }
}}%%

sequenceDiagram
    autonumber

    participant A as 👤 작업자 A
    participant G as ☁️ GitHub
    participant B as 👤 작업자 B

    rect rgb(240, 253, 244)
        Note over A,G: 1. 작업자 A가 새 파일 작성 후 Push

        A->>A: worker-a.md 생성
        A->>A: git add worker-a.md
        A->>A: git commit -m "A: 작업자 A 문서 추가"
        A->>G: 🟩 git push

        G-->>A: ✅ GitHub에 A 커밋 반영
    end

    rect rgb(239, 246, 255)
        Note over G,B: 2. 작업자 B가 A의 변경을 가져옴

        B->>G: 🟦 git fetch origin
        G-->>B: A의 새 커밋 정보 전달

        Note right of B: fetch는 정보만 가져오며<br/>작업 파일은 아직 변경되지 않음

        B->>B: 🟪 git merge origin/main

        Note right of B: worker-a.md가 B 로컬에 반영됨
    end

    rect rgb(240, 253, 244)
        Note over B,G: 3. 작업자 B가 새 파일 작성 후 Push

        B->>B: worker-b.md 생성
        B->>B: git add worker-b.md
        B->>B: git commit -m "B: 작업자 B 문서 추가"
        B->>G: 🟩 git push

        G-->>B: ✅ GitHub에 B 커밋 반영
    end

    rect rgb(239, 246, 255)
        Note over A,G: 4. 작업자 A가 B의 변경을 가져옴

        A->>G: 🟦 git fetch origin
        G-->>A: B의 새 커밋 정보 전달
        A->>A: 🟪 git merge origin/main

        Note over A,B: ✅ A와 B 모두 worker-a.md / worker-b.md를 가진 상태
    end
```

실습 파일에서도 충돌 없는 협업은 작업자 A가 `worker-a.md`를 만들고 Push한 뒤, 작업자 B가 `git fetch origin`과 `git merge origin/main`으로 반영하는 순서로 진행됩니다. 이후 작업자 B가 `worker-b.md`를 Push하고 작업자 A가 다시 가져옵니다.  

---

## 3단계. 같은 README.md 수정과 Push 거절

```mermaid
%%{init: {
  "theme": "base",
  "sequence": {
    "showSequenceNumbers": true
  },
  "themeVariables": {
    "fontSize": "22px",
    "actorFontSize": "22px",
    "messageFontSize": "21px",
    "noteFontSize": "21px",

    "actorBkg": "#E8F1FF",
    "actorBorder": "#2563EB",
    "actorTextColor": "#0F172A",

    "signalColor": "#334155",
    "signalTextColor": "#0F172A",

    "noteBkgColor": "#FFF7ED",
    "noteBorderColor": "#FB923C",
    "noteTextColor": "#111827"
  }
}}%%

sequenceDiagram
    autonumber

    participant A as 👤 작업자 A
    participant G as ☁️ GitHub
    participant B as 👤 작업자 B

    rect rgb(255, 247, 237)
        Note over A,B: 1. 두 작업자가 README.md의 같은 문장을 서로 다르게 수정

        A->>A: README.md 수정
        Note right of A: 오늘의 학습 목표:<br/>작업자 A의 Git 협업 실습

        A->>A: git add README.md
        A->>A: git commit -m "A: README 학습 목표 수정"
        A->>G: 🟩 git push

        G-->>A: ✅ A의 변경 내용이 GitHub에 먼저 반영
    end

    rect rgb(255, 247, 237)
        B->>B: README.md 같은 문장을 다르게 수정
        Note right of B: 오늘의 학습 목표:<br/>작업자 B의 Merge 충돌 실습

        B->>B: git add README.md
        B->>B: git commit -m "B: README 학습 목표 수정"
        B->>G: 🟩 git push
    end

    rect rgb(254, 226, 226)
        G--xB: 🟥 Push 거절<br/>fetch first

        Note over B,G: 이유: GitHub에는 A의 새 커밋이 있지만<br/>B 로컬에는 아직 A의 커밋이 없음
    end

    Note over A,B: 이 상태를 브랜치가 서로 갈라진 diverged 상태라고 볼 수 있음
```

작업자 B는 A의 변경을 가져오지 않은 상태에서 같은 `README.md` 문장을 수정하고 Push를 시도하므로, GitHub가 `fetch first` 오류로 Push를 거절합니다. 

---

## 4단계. Fetch, Merge, 충돌 해결 및 최종 동기화

```mermaid
%%{init: {
  "theme": "base",
  "sequence": {
    "showSequenceNumbers": true
  },
  "themeVariables": {
    "fontSize": "22px",
    "actorFontSize": "22px",
    "messageFontSize": "21px",
    "noteFontSize": "21px",

    "actorBkg": "#E8F1FF",
    "actorBorder": "#2563EB",
    "actorTextColor": "#0F172A",

    "signalColor": "#334155",
    "signalTextColor": "#0F172A",

    "noteBkgColor": "#FEF3C7",
    "noteBorderColor": "#F59E0B",
    "noteTextColor": "#111827"
  }
}}%%

sequenceDiagram
    autonumber

    participant A as 👤 작업자 A
    participant G as ☁️ GitHub
    participant B as 👤 작업자 B

    rect rgb(239, 246, 255)
        Note over B,G: 1. 작업자 B가 원격 변경 확인

        B->>G: 🟦 git fetch origin
        G-->>B: A의 최신 커밋 정보 전달
    end

    rect rgb(254, 226, 226)
        Note over B: 2. Merge 중 README.md 충돌 발생

        B->>B: 🟪 git merge origin/main

        critical 🟥 README.md 충돌 발생
            Note right of B: CONFLICT(content)<br/>both modified: README.md
            Note right of B: 상태 표시 예시<br/>main | MERGING
        end
    end

    rect rgb(255, 237, 213)
        Note over B: 3. 충돌 파일 직접 수정

        B->>B: README.md 열기

        Note right of B: HEAD = B의 로컬 수정<br/>origin/main = A가 Push한 원격 수정

        B->>B: 두 내용을 검토해 최종 문장 결정

        Note right of B: 오늘의 학습 목표:<br/>작업자 A·B의 Git 협업 및 Merge 충돌 해결

        B->>B: 충돌 표시 제거
        Note right of B: <<<<<<< HEAD<br/>=======<br/>>>>>>> origin/main 삭제
    end

    rect rgb(240, 253, 244)
        Note over B,G: 4. Merge Commit 생성 후 Push

        B->>B: git add README.md
        B->>B: git commit -m "B: A의 변경과 README 충돌 해결"

        Note right of B: ✅ Merge Commit 생성

        B->>G: 🟩 git push
        G-->>B: ✅ 충돌 해결 결과 GitHub에 반영
    end

    rect rgb(236, 254, 255)
        Note over A,G: 5. 작업자 A도 최종 결과 동기화

        A->>G: 🟦 git fetch origin
        G-->>A: B의 Merge Commit 전달

        A->>A: 🟪 git merge origin/main

        Note over A,G: ✅ A 로컬에도 최종 README.md 반영
    end

    Note over A,B: 🎉 최종 상태<br/>작업자 A = 작업자 B = GitHub
```

작업자 B는 `git fetch origin` 후 `git merge origin/main`을 실행하면서 `README.md` 충돌을 확인합니다. 이후 충돌 표시를 제거하고 최종 문장을 정한 뒤 `git add README.md`, `git commit`, `git push` 순서로 해결합니다. 마지막으로 작업자 A가 다시 `fetch → merge`하여 최종 결과를 동기화합니다.   

---

## 색상 의미

```text
🟦 파랑: fetch, 원격 변경 확인
🟪 보라: merge, 원격 변경을 로컬에 반영
🟩 초록: push, 로컬 커밋을 GitHub에 업로드
🟥 빨강: Push 거절 또는 충돌 발생
🟧 주황: 충돌 파일 직접 수정
🩵 하늘색: 최종 동기화
```

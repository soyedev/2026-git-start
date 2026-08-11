# 2026 Git Start

GitHub에서 README 수정

오늘의 학습 목표: Git 협업과 충돌 해결 실습

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
    "actorLineColor": "#CBD5E1",

    "signalColor": "#334155",
    "signalTextColor": "#0F172A",

    "noteBkgColor": "#FEF3C7",
    "noteBorderColor": "#F59E0B",
    "noteTextColor": "#111827",

    "activationBkgColor": "#DCFCE7",
    "activationBorderColor": "#22C55E"
  }
}}%%

sequenceDiagram
    autonumber

    participant A as 👤 작업자 A<br/>/c/2026-git-start
    participant G as ☁️ GitHub<br/>origin/main
    participant B as 👤 작업자 B<br/>/c/2026-git-start-b

    Note over A,B: 🧭 읽는 방법<br/>🟦 가져오기(fetch) → 🟪 반영하기(merge) → 🟩 올리기(push) → 🟥 문제 발생 → 🟧 직접 해결

    rect rgb(232, 241, 255)
        Note over A,B: 1단계. 같은 GitHub 저장소를 두 작업자가 각각 사용

        A->>G: 기존 GitHub 저장소와 연결된 로컬 저장소 사용
        G->>B: 같은 저장소를 작업자 B 폴더에 Clone

        Note over A,B: A와 B는 같은 GitHub 저장소를 바라보지만<br/>각자의 로컬 저장소는 서로 독립적
    end

    rect rgb(240, 253, 244)
        Note over A,B: 2단계. 충돌 없는 협업 ① 작업자 A가 새 파일을 올림

        A->>A: worker-a.md 생성
        A->>A: git add worker-a.md
        A->>A: git commit -m "A: 작업자 A 문서 추가"
        A->>G: 🟩 git push

        G-->>A: ✅ GitHub에 A의 커밋 반영

        Note over G,B: 아직 B 로컬에는 worker-a.md가 없음
    end

    rect rgb(239, 246, 255)
        Note over A,B: 3단계. 작업자 B가 A의 변경을 가져와 반영

        B->>G: 🟦 git fetch origin
        G-->>B: A의 새 커밋 정보 전달

        Note right of B: fetch는 정보만 가져옴<br/>파일은 아직 바뀌지 않음

        B->>B: 🟪 git merge origin/main

        Note right of B: 이제 worker-a.md가<br/>B 로컬에 반영됨
    end

    rect rgb(240, 253, 244)
        Note over A,B: 4단계. 작업자 B가 새 파일을 올리고 A가 다시 동기화

        B->>B: worker-b.md 생성
        B->>B: git add worker-b.md
        B->>B: git commit -m "B: 작업자 B 문서 추가"
        B->>G: 🟩 git push

        G-->>B: ✅ GitHub에 B의 커밋 반영

        A->>G: 🟦 git fetch origin
        G-->>A: B의 새 커밋 정보 전달
        A->>A: 🟪 git merge origin/main

        Note over A,B: ✅ A와 B 모두 worker-a.md / worker-b.md를 가진 상태
    end

    rect rgb(255, 247, 237)
        Note over A,B: 5단계. 같은 README.md 문장을 서로 다르게 수정

        A->>A: README.md 같은 문장 수정
        Note right of A: 오늘의 학습 목표:<br/>작업자 A의 Git 협업 실습

        A->>A: git add README.md
        A->>A: git commit -m "A: README 학습 목표 수정"
        A->>G: 🟩 git push

        G-->>A: ✅ A의 README 수정 내용 반영

        B->>B: README.md 같은 문장을 다르게 수정
        Note right of B: 오늘의 학습 목표:<br/>작업자 B의 Merge 충돌 실습

        B->>B: git add README.md
        B->>B: git commit -m "B: README 학습 목표 수정"
        B->>G: 🟩 git push

        G--xB: 🟥 Push 거절<br/>fetch first

        Note over B,G: 이유: GitHub에는 A의 새 커밋이 있는데<br/>B 로컬에는 아직 그 커밋이 없음
    end

    rect rgb(254, 226, 226)
        Note over A,B: 6단계. 작업자 B가 원격 변경을 가져오다가 충돌 발생

        B->>G: 🟦 git fetch origin
        G-->>B: A의 최신 커밋 정보 전달

        B->>B: 🟪 git merge origin/main

        critical 🟥 README.md 충돌 발생
            Note right of B: CONFLICT(content)<br/>both modified: README.md
            Note right of B: 상태 표시 예시<br/>main | MERGING
        end
    end

    rect rgb(255, 237, 213)
        Note over A,B: 7단계. 충돌 파일을 직접 수정하고 Merge Commit 생성

        B->>B: README.md 열기
        Note right of B: HEAD = B의 로컬 수정<br/>origin/main = A가 Push한 원격 수정

        B->>B: 두 내용을 검토해 최종 문장 결정
        Note right of B: 오늘의 학습 목표:<br/>작업자 A·B의 Git 협업 및 Merge 충돌 해결

        B->>B: 충돌 표시 제거
        Note right of B: <<<<<<< HEAD<br/>=======<br/>>>>>>> origin/main 삭제

        B->>B: git add README.md
        B->>B: git commit -m "B: A의 변경과 README 충돌 해결"

        Note right of B: ✅ Merge Commit 생성

        B->>G: 🟩 git push
        G-->>B: ✅ 충돌 해결 결과 GitHub에 반영
    end

    rect rgb(236, 254, 255)
        Note over A,B: 8단계. 작업자 A도 최종 결과를 가져와 동기화

        A->>G: 🟦 git fetch origin
        G-->>A: B의 Merge Commit 전달

        A->>A: 🟪 git merge origin/main

        Note over A,G: ✅ A 로컬에도 최종 README.md 반영

        Note over A,B: 🎉 최종 상태<br/>작업자 A = 작업자 B = GitHub
    end
```

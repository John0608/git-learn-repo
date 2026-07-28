# Learn Git Repository

## Git 저장소로 만드는 법

```bash
cd '대상 프로젝트 Path'
git init
```

### 실행 후

- `.git` 폴더가 생성되고, 이 시점부터 변경사항이 추적된다.
- 자동으로 main 브랜치가 생성된다.

---

## Git에 파일 제외가 필요한 경우

- 프로젝트 최상단에 `.gitignore` 파일 생성 한 후, 수정한 뒤 커밋을 한다.
- 필자의 경우, 현재 사용중인 `Codex`의 플러그인이 생성하는 md파일을 제외할 것이다.

```md
# .gitignore 파일 내용

.memsearch
```

그럼 설정한 폴더/파일에 따라 Git 추적에서 제외되어 기록되지 않는다.

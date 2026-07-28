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

### .gitignore 파일 내용

```markdown
.memsearch
```

그럼 설정한 폴더/파일에 따라 Git 추적에서 제외되어 기록되지 않는다.

### Git에 저장하는 방법

- `commit`은 고른 변경을 Git에 기록으로 저장하는 것
- `staging`은 Git에 기록으로 저장할 파일을 고르는 것 (commit 대기상태)

```bash
# 파일 하나만 스테이징에 올릴 때
git add <filename>
```

```bash
# 현재 폴더의 변경 사항을 모두 추가할 때
git add .
```

```bash
# 추가 후 스테이징 확인
git status
```

```bash
# 실수로 추가한 경우, 스테이징에서 내릴 때
git restore --staged <filename>
```

```bash
# Git에 올릴 때
git commit                  # 커밋
git commit -m "<message>"   # 메시지 포함 커밋
```

### Commit 취소

```bash
# 마지막 커밋을 취소하고, 파일 수정 내용은 유지
git reset --soft HEAD~1
```

```bash
# 마지막 커밋을 취소하고, 파일 수정 내용도 제거
git reset --hard HEAD~1
```

```bash
# 스테이징 취소 및 파일 수정만 남김 (스테이징 X)
git reset HEAD~1
```

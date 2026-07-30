# Git과 GitHub 실습

실습은 연습용 폴더에서 진행합니다. 실제 작업 저장소와는 분리합니다. 각 단계를 마칠 때 `git status`와 `git log --oneline --all --graph`로 상태를 확인합니다.

> [!WARNING]
> 기존 프로젝트 폴더나 공유 저장소에서 실행하지 마세요. 아래에서 만드는 `git-practice` 폴더에서만 실습합니다.

## 준비

```bash
cd ~/Documents
mkdir git-practice
cd git-practice
git init -b main
git config user.name "연습 사용자"
git config user.email "practice@example.com"
```

## 1. 첫 파일과 커밋 만들기

1. 편집기로 `README.md`를 만들고 `# Git 연습`을 적습니다.
2. 다음 명령어를 실행합니다.

```bash
git status
git add README.md
git diff --staged
git commit -m "docs: 연습 저장소 초기화"
git log --oneline
```

**확인:** `git status`에 `working tree clean`이 표시됩니다.

## 2. 스테이징과 복원 연습

1. `README.md`에 한 줄을 추가합니다.
2. 변경 내용을 확인하고 스테이징합니다.

```bash
git diff
git add README.md
git diff --staged
```

3. 스테이징만 취소합니다.

```bash
git restore --staged README.md
git status
```

4. 수정 내용까지 버리고 싶을 때만 아래 명령어를 실행합니다.

```bash
git restore README.md
```

## 3. 브랜치와 merge 연습

```bash
git switch -c feature/greeting
```

1. `greeting.txt`를 만들고 `안녕하세요`를 적습니다.
2. 커밋을 만듭니다.

```bash
git add greeting.txt
git commit -m "feat: 인사말 파일 추가"
git switch main
git merge feature/greeting
```

**확인:** `main` 브랜치에 `greeting.txt`가 있습니다.

## 4. 충돌 해결 연습

먼저 `message.txt`에 `기본 문구`를 적고 커밋합니다.

```bash
git add message.txt
git commit -m "docs: 기본 문구 추가"
git switch -c feature/conflict
```

`feature/conflict`에서 `message.txt`의 내용을 `기능 브랜치 문구`로 바꾸고 커밋합니다.

```bash
git add message.txt
git commit -m "docs: 기능 브랜치 문구 수정"
git switch main
```

`main`에서 같은 파일 내용을 `main 브랜치 문구`로 바꾸고 커밋한 뒤 병합합니다.

```bash
git add message.txt
git commit -m "docs: main 브랜치 문구 수정"
git merge feature/conflict
```

충돌 표시를 지우고 원하는 문구를 남긴 뒤 마무리합니다.

```bash
git add message.txt
git commit -m "merge: 문구 충돌 해결"
```

## 5. stash와 amend 연습

1. `README.md`를 수정하고 커밋하지 않은 상태에서 보관합니다.

```bash
git stash
git stash list
git stash pop
```

2. 수정 내용을 커밋한 뒤 메시지를 고칩니다.

```bash
git add README.md
git commit -m "docs: 임시 메시지"
git commit --amend -m "docs: README 내용 보완"
```

## 6. revert와 reflog 연습

되돌릴 연습용 커밋을 하나 만듭니다.

```bash
git commit --allow-empty -m "test: 되돌릴 커밋"
git revert HEAD
git log --oneline -3
git reflog -5
```

**확인:** 원래 커밋은 남아 있습니다. 취소 내용을 기록한 revert 커밋이 하나 더 생깁니다.

## 7. cherry-pick과 rebase 연습

```bash
git switch -c feature/pick-source
```

1. `picked.txt`를 만들고 커밋합니다. 커밋 ID를 기록합니다.

```bash
git add picked.txt
git commit -m "feat: cherry-pick 연습 파일 추가"
git log --oneline -1
git switch main
git cherry-pick <방금-기록한-커밋ID>
```

2. rebase는 별도 브랜치에서만 연습합니다.

```bash
git switch -c feature/rebase-practice
git commit --allow-empty -m "test: 첫 번째 커밋"
git commit --allow-empty -m "test: 두 번째 커밋"
git rebase -i HEAD~2
```

편집기가 열리면 첫 줄의 `pick`을 `reword`로 바꾸고 저장한 뒤 메시지를 수정해 봅니다. 완료 후 `git log --oneline -3`으로 확인합니다.

## 8. bisect, worktree, clean 연습

`bisect`는 실제 버그를 재현할 수 있을 때 사용합니다. 이 연습에서는 흐름만 확인하고 반드시 종료합니다.

```bash
git bisect start
git bisect bad
git bisect good HEAD~3
git bisect reset
```

다른 브랜치를 별도 폴더에서 엽니다.

```bash
git branch hotfix/practice
git worktree add ../git-practice-hotfix hotfix/practice
git worktree list
git worktree remove ../git-practice-hotfix
```

`clean`은 먼저 미리보기만 실행합니다.

```bash
touch untracked.tmp
git clean -nd
```

## 9. GitHub 연동 연습

1. [github.md](github.md#2-github-계정과-인증)의 HTTPS 또는 SSH 인증을 먼저 마칩니다.
2. GitHub에서 비어 있는 `git-practice` 저장소를 만듭니다. README, `.gitignore`, 라이선스는 만들지 않습니다.
3. 저장소 주소를 복사해 연결하고 첫 push를 실행합니다.

```bash
git remote add origin <GitHub-저장소-주소>
git push -u origin main
```

4. `main`을 기준으로 브랜치를 만들고 GitHub에서 Pull Request를 만듭니다.

```bash
git switch main
git switch -c feature/github-practice
git commit --allow-empty -m "test: GitHub PR 연습"
git push -u origin feature/github-practice
```

5. GitHub에서 PR을 만든 뒤 변경 사항과 병합 방식을 확인합니다. 혼자 연습하는 저장소라면 병합한 뒤 로컬 `main`을 갱신합니다.

```bash
git switch main
git pull --ff-only
```

## 10. Issue와 Project 연습

1. GitHub 저장소의 **Issues → New issue**에서 아래 Issue를 만듭니다.

   ```md
   제목: README에 실습 목적 추가

   ## 완료 기준
   - README에 이 저장소가 Git 연습용이라는 설명이 있다.
   ```

2. Issue에 `documentation` Label을 붙이고 본인을 Assignee로 지정합니다.
3. 개인 프로필 또는 조직의 **Projects → New project**에서 **Table** 프로젝트를 만듭니다.
4. Project에 `Status`와 `Priority` 필드를 추가하고 Issue를 프로젝트에 넣습니다.
5. Issue의 Status를 `In progress`로 바꿉니다.

## 11. Issue를 연결한 PR 연습

1. `main`에서 새 브랜치를 만듭니다.

   ```bash
   git switch main
   git switch -c docs/readme-purpose
   ```

2. `README.md`에 `이 저장소는 Git과 GitHub를 연습하기 위한 저장소입니다.`를 추가합니다.
3. 커밋과 push를 실행합니다.

   ```bash
   git add README.md
   git commit -m "docs: README에 실습 목적 추가"
   git push -u origin docs/readme-purpose
   ```

4. GitHub에서 PR을 만들고 본문에 `Closes #<Issue번호>`를 적습니다.
5. 병합 후 Issue가 자동으로 닫히는지, Project의 Status를 `Done`으로 바꿀 수 있는지 확인합니다.
6. 로컬 `main`을 갱신합니다.

   ```bash
   git switch main
   git pull --ff-only
   ```

## 12. GitHub Actions 검사 연습

1. `.github/workflows/check.yml` 파일을 만들고 아래 내용을 저장합니다.

   ```yaml
   name: 기본 검사

   on:
     pull_request:
     push:
       branches: [main]

   permissions:
     contents: read

   jobs:
     check:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v6
         - run: test -f README.md
   ```

2. workflow를 커밋하고 push합니다.

   ```bash
   git add .github/workflows/check.yml
   git commit -m "ci: README 존재 여부 검사 추가"
   git push
   ```

3. GitHub 저장소의 **Actions** 탭에서 `기본 검사` workflow가 성공했는지 확인합니다.
4. 실패한 실행을 열어 job과 step 로그를 확인해 봅니다.

## 13. GitHub Pages 배포 연습

정적 HTML, CSS, JavaScript 파일은 GitHub Pages로 공개 URL에 배포합니다.

1. 프로젝트 최상단에 `index.html`을 만들고 아래 내용을 저장합니다.

   ```html
   <!doctype html>
   <html lang="ko">
     <head>
       <meta charset="utf-8">
       <title>Git 연습</title>
     </head>
     <body>
       <h1>GitHub Pages 배포 연습</h1>
     </body>
   </html>
   ```

2. GitHub 저장소의 **Settings → Pages**에서 배포 원본으로 **GitHub Actions**를 선택합니다.
3. `.github/workflows/deploy-pages.yml` 파일을 만들고 아래 내용을 저장합니다.

   ```yaml
   name: GitHub Pages 배포

   on:
     push:
       branches: [main]

   permissions:
     contents: read
     pages: write
     id-token: write

   jobs:
     deploy:
       environment:
         name: github-pages
         url: ${{ steps.deployment.outputs.page_url }}
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v6
         - uses: actions/configure-pages@v5
         - uses: actions/upload-pages-artifact@v4
           with:
             path: .
         - id: deployment
           uses: actions/deploy-pages@v4
   ```

4. 파일을 커밋하고 `main`에 push합니다.

   ```bash
   git add index.html .github/workflows/deploy-pages.yml
   git commit -m "docs: GitHub Pages 배포 추가"
   git push
   ```

5. Actions에서 `GitHub Pages 배포` workflow가 성공하면 deploy job의 URL을 열어 웹페이지가 보이는지 확인합니다.

## 14. 태그·Release 연습

```bash
git tag -a v0.1.0 -m "v0.1.0 연습"
git push origin v0.1.0
git show v0.1.0
```

1. GitHub 저장소의 **Releases → Draft a new release**를 선택합니다.
2. `v0.1.0` 태그를 선택하고 제목과 배포 노트를 작성합니다.
3. **Publish release**를 선택하고 Release 페이지의 태그·다운로드 링크를 확인합니다.

연습 태그와 Release를 삭제할 때는 GitHub에서 Release를 먼저 삭제한 뒤 아래 명령어를 실행합니다.

```bash
git tag -d v0.1.0
git push origin --delete v0.1.0
```

## 실습 완료 확인

```bash
git status
git log --oneline --all --graph
git branch -a
git tag
```

> [!TIP]
> 각 명령어를 실행하기 전에는 [init.md](init.md) 또는 [github.md](github.md)의 해당 설명을 먼저 읽고, 실행 후 `git status`로 결과를 확인합니다.

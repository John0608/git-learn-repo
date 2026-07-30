# GitHub 연동과 협업

Git은 내 컴퓨터에서 코드와 이력을 관리합니다. GitHub는 Git 저장소를 온라인에서 공유하고 협업하는 서비스입니다. 로컬 Git 명령어는 [init.md](init.md), 실제 연습은 [practice.md](practice.md)에서 다룹니다.

## 1. 시작 전 알아둘 용어

| 용어 | 뜻 |
| --- | --- |
| Repository(저장소) | 코드와 Git 기록을 보관하는 공간입니다. |
| Remote(원격 저장소) | GitHub에 있는 저장소입니다. 로컬 저장소와 연결해 동기화합니다. |
| Issue | 버그, 작업, 질문을 기록하고 추적하는 카드입니다. |
| Pull Request(PR) | 브랜치 변경 사항을 검토하고 병합해 달라고 요청하는 공간입니다. |
| Project | Issue와 PR의 진행 상태를 표·보드·로드맵으로 관리하는 공간입니다. |
| Action | 테스트, 배포 같은 반복 작업을 자동화하는 워크플로입니다. |
| Release | 특정 태그를 기준으로 배포 버전과 배포 노트를 공개하는 기능입니다. |

## 2. GitHub 계정과 인증

GitHub 웹사이트에서 계정을 먼저 만듭니다. 터미널에서 GitHub 저장소에 접근하려면 HTTPS 또는 SSH 중 하나로 인증합니다.

| 방식 | 특징 | 처음 사용할 때 |
| --- | --- | --- |
| HTTPS | 방화벽·프록시 환경에서도 쉽게 연결합니다. | GitHub CLI 또는 Personal Access Token(PAT)을 사용합니다. |
| SSH | 매번 토큰을 입력하지 않고 키로 인증합니다. | SSH 키를 만들고 공개 키를 GitHub 계정에 등록합니다. |

> [!WARNING]
> GitHub 계정 비밀번호는 HTTPS Git 인증에 사용할 수 없습니다. PAT와 SSH 개인 키는 비밀번호처럼 취급하고 채팅·코드·Issue·Actions 로그에 넣지 않습니다.

처음에는 GitHub CLI의 브라우저 로그인을 쓰면 편합니다.

```bash
gh auth login
```

GitHub CLI를 사용하지 않고 HTTPS로 접근할 때 비밀번호 입력을 요구하면 계정 비밀번호 대신 PAT를 입력합니다.

## 3. 원격 저장소란

내 컴퓨터 밖에 있는 Git 저장소를 원격 저장소라고 부릅니다. 코드 공유와 백업, PR 기반 검토와 병합의 기준점이 됩니다.

```text
로컬 저장소 ── push ──> GitHub 원격 저장소
로컬 저장소 <── pull ── GitHub 원격 저장소
```

## 4. GitHub 저장소 만들기와 복제

### 새 저장소 만들기

1. GitHub 오른쪽 위의 **+** 메뉴에서 **New repository**를 선택합니다.
2. 저장소 이름과 공개 범위를 정합니다.
3. 이미 로컬에서 `git init`한 프로젝트를 연결할 예정이라면 README, `.gitignore`, 라이선스는 만들지 않고 비어 있는 저장소로 생성합니다.
4. 생성 후 HTTPS 또는 SSH 주소를 복사합니다.

### 기존 저장소 복제하기

GitHub에 이미 있는 저장소를 내 컴퓨터에 처음 가져옵니다.

```bash
git clone <저장소-주소>
cd <저장소-폴더>
```

예시:

```bash
git clone https://github.com/organization/project.git
cd project
```

## 5. 기존 로컬 저장소 연결하기

이미 `git init`한 프로젝트를 GitHub의 비어 있는 저장소와 연결합니다.

```bash
git remote add origin <저장소-주소>
git remote -v
```

원격 주소를 바꿔야 하면 아래 명령어를 사용합니다.

```bash
git remote set-url origin <새-저장소-주소>
```

## 6. 원격 변경 가져오기와 올리기

| 명령어 | 설명 |
| --- | --- |
| `git fetch origin` | 원격 변경을 가져오되 현재 브랜치에는 반영하지 않습니다. |
| `git pull --ff-only` | 원격 변경을 가져와 fast-forward가 가능할 때만 현재 브랜치에 반영합니다. |
| `git push` | 현재 브랜치의 커밋을 연결된 원격 브랜치에 올립니다. |
| `git push -u origin <브랜치이름>` | 브랜치를 처음 원격 저장소에 올리고 추적 관계를 설정합니다. |

처음 `main` 브랜치를 올리는 예시입니다.

```bash
git push -u origin main
```

원격 변경을 확인만 하려면 fetch 후 비교합니다.

```bash
git fetch origin
git log HEAD..origin/main --oneline
```

> [!WARNING]
> 공유 브랜치에는 `git push --force`를 사용하지 않습니다. 이력이 바뀐 개인 브랜치를 올려야 한다면 `git push --force-with-lease`를 사용하고 먼저 팀에 알립니다.

## 7. 원격 브랜치와 태그 관리

| 명령어 | 설명 |
| --- | --- |
| `git branch -r` | 원격 브랜치 목록을 확인합니다. |
| `git branch -a` | 로컬과 원격 브랜치를 함께 확인합니다. |
| `git fetch --prune` | 원격에서 삭제된 브랜치 참조를 로컬에서도 정리합니다. |
| `git push origin --delete <브랜치이름>` | 원격 브랜치를 삭제합니다. |

원격에 있는 브랜치에서 로컬 작업 브랜치를 만듭니다.

```bash
git switch -c feature/login --track origin/feature/login
```

로컬에서 만든 태그를 GitHub에 올리거나 삭제합니다.

```bash
git push origin v1.0.0
git push origin --delete v1.0.0
```

## 8. Issue로 작업 기록하기

Issue에는 버그와 개선 요청, 작업 단위를 기록합니다. 코드를 수정하기 전 Issue를 만들면 “왜 이 작업을 하는지”가 남습니다.

### Issue 만들기

1. 저장소의 **Issues** 탭에서 **New issue**를 선택합니다.
2. 제목은 한눈에 이해되게 쓰고 본문에는 재현·완료 기준을 담습니다.
3. 필요하면 Label, Assignee, Milestone, Project를 지정합니다.
4. **Submit new issue**를 눌러 만듭니다.

버그 Issue에는 아래 내용을 적으면 됩니다.

```md
## 문제

로그인 버튼을 누르면 오류가 발생합니다.

## 재현 방법

1. 로그인 페이지를 엽니다.
2. 이메일과 비밀번호를 입력합니다.
3. 로그인 버튼을 누릅니다.

## 기대 결과

로그인 후 대시보드로 이동합니다.

## 실제 결과

오류 메시지가 표시됩니다.
```

### Issue와 PR 연결하기

PR 본문에 아래처럼 작성하면 기본 브랜치에 병합될 때 해당 Issue가 자동으로 닫힙니다.

```text
Closes #123
```

`#123`은 Issue 번호입니다. 병합 전에는 Issue를 닫지 말고 PR과 연결해 진행 상태를 추적합니다.

## 9. Projects로 작업 흐름 관리하기

GitHub Projects에서는 여러 저장소의 Issue와 PR을 한 곳에서 관리합니다.

### Project 만들기

1. 개인 프로필 또는 조직의 **Projects** 탭에서 **New project**를 선택합니다.
2. 처음에는 **Table** 템플릿으로 시작합니다.
3. 프로젝트 이름을 정하고 아래 필드를 추가합니다.

| 필드 | 예시 값 | 목적 |
| --- | --- | --- |
| Status | Todo, In progress, Done | 작업 상태 |
| Priority | High, Medium, Low | 우선순위 |
| Assignees | 담당자 | 담당자 확인 |
| Iteration 또는 날짜 | 이번 주, 다음 주 | 일정 관리 |

4. Issue 또는 PR의 오른쪽 사이드바 **Projects**에서 만든 Project를 선택해 항목으로 추가합니다.
5. 진행 상황을 보고 싶을 때는 같은 데이터를 **Board** 보기로 엽니다. 일정은 날짜 필드가 있을 때 **Roadmap** 보기로 확인합니다.

> [!TIP]
> Project는 작업의 상태를 보는 곳이고, Issue와 PR은 작업의 근거와 변경 내용을 남기는 곳입니다.

## 10. Pull Request로 변경 사항 검토하기

브랜치의 변경 사항을 대상 브랜치에 병합하기 전에 PR로 검토를 요청합니다.

```text
Issue 생성 → 브랜치 생성 → 작업·커밋 → push → PR 생성 → 리뷰 → 병합
```

1. 작업 브랜치를 원격 저장소에 올립니다.
2. GitHub 저장소의 **Compare & pull request** 또는 **Pull requests**에서 PR을 만듭니다.
3. 대상 브랜치, 제목, 설명, 연결할 Issue, 검토자를 지정합니다.
4. Files changed에서 변경 내용을 확인하고 리뷰를 반영합니다.
5. 자동 검사와 리뷰가 끝난 뒤 병합합니다.

### PR 병합 방식

| 방식 | GitHub에서의 결과 | 사용할 때 |
| --- | --- | --- |
| Create a merge commit | 병합 커밋을 추가해 두 브랜치의 이력을 모두 남깁니다. | 브랜치 병합 사실과 작업 이력을 보존할 때 |
| Squash and merge | PR의 커밋을 하나로 묶어 대상 브랜치에 남깁니다. | 기능 단위로 깔끔한 이력을 남길 때 |
| Rebase and merge | PR 커밋을 대상 브랜치 뒤에 일렬로 다시 적용합니다. | 병합 커밋 없이 선형 이력을 유지할 때 |

## 11. GitHub Actions로 자동 검사하기

GitHub Actions는 자동화 기능입니다. push, PR 생성, 일정 같은 이벤트가 발생하면 테스트·빌드·배포를 자동 실행합니다.

| 용어 | 뜻 |
| --- | --- |
| Workflow | 자동화 전체를 정의한 YAML 파일입니다. |
| Event | workflow를 시작하는 조건입니다. 예: `push`, `pull_request` |
| Job | runner에서 실행되는 작업 묶음입니다. |
| Step | job 안에서 순서대로 실행되는 명령 또는 Action입니다. |
| Runner | GitHub가 제공하거나 직접 운영하는 실행 환경입니다. |

workflow 파일은 반드시 `.github/workflows/` 폴더에 `.yml` 또는 `.yaml` 확장자로 저장합니다.

아래는 `main` push와 PR 때 README 존재 여부를 검사하는 가장 작은 예시입니다.

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

파일을 커밋하고 push하면 저장소의 **Actions** 탭에서 실행 결과와 로그를 볼 수 있습니다.

> [!WARNING]
> API 키와 비밀번호는 YAML 파일에 쓰지 않습니다. 저장소의 **Settings → Secrets and variables → Actions**에 Secret으로 등록하고 workflow에서만 참조합니다.

## 12. Release 만들기와 배포의 차이

GitHub Release는 특정 태그를 기준으로 버전, 배포 노트, 다운로드 파일을 공개합니다. Release 자체는 서버에 서비스를 배포하지 않습니다.

### Release 만들기

1. 로컬에서 주석 태그를 만들고 GitHub에 올립니다.

   ```bash
   git tag -a v1.0.0 -m "v1.0.0 배포"
   git push origin v1.0.0
   ```

2. 저장소의 **Releases**에서 **Draft a new release**를 선택합니다.
3. 기존 태그를 선택하거나 새 태그를 만듭니다.
4. 제목과 배포 노트를 작성하고, 필요하면 빌드 파일을 첨부합니다.
5. 초안·사전 배포 여부를 확인한 뒤 **Publish release**를 선택합니다.

Release에 연결한 태그 시점의 ZIP·tarball 다운로드도 함께 제공됩니다.

### 정적 웹사이트를 GitHub Pages로 배포하기

정적 HTML, CSS, JavaScript 사이트는 GitHub Pages와 Actions로 실제 공개 URL에 배포할 수 있습니다.

1. 저장소 **Settings → Pages**에서 배포 원본으로 **GitHub Actions**를 선택합니다.
2. `.github/workflows/deploy-pages.yml`을 만들고 아래 내용을 저장합니다.

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

3. `main`에 workflow를 병합하면 Actions가 실행됩니다. 성공한 배포 job의 URL 또는 **Settings → Pages**에서 사이트 주소를 확인할 수 있습니다.

> [!WARNING]
> 일반 웹 서비스, API, 데이터베이스가 있는 서비스의 배포는 GitHub Pages 대상이 아닙니다. 배포 대상(Vercel, AWS, 사내 서버 등)에 맞는 Actions workflow와 Secret을 별도로 설계해야 합니다.

## 13. 협업 규칙과 추가 기능

저장소의 **Settings → Branches**에서 기본 브랜치 보호 규칙을 설정할 수 있습니다.

- `main` 직접 push 제한
- PR 리뷰 승인 요구
- 테스트·정적 검사 같은 상태 검사 통과 요구
- 오래된 리뷰 승인 무효화

| 기능 | 용도 |
| --- | --- |
| Milestones | 여러 Issue와 PR을 버전·기간 단위로 묶어 진행률 관리 |
| Discussions | 코드 변경 없이 질문과 아이디어를 논의 |
| Releases | 태그 기준 배포 노트와 배포 파일 제공 |
| Actions | 테스트, 배포, 정기 작업 자동화 |

## 공식 참고 문서

- [GitHub 인증](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/about-authentication-to-github)
- [Issues 만들기](https://docs.github.com/en/issues/tracking-your-work-with-issues/using-issues/creating-an-issue)
- [Projects에 항목 추가하기](https://docs.github.com/en/issues/planning-and-tracking-with-projects/managing-items-in-your-project/adding-items-to-your-project)
- [GitHub Actions 빠른 시작](https://docs.github.com/en/actions/get-started/quickstart)
- [Release 관리](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)
- [GitHub Pages workflow](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)

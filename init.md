# 로컬 Git 사용법

이 문서는 내 컴퓨터에서 Git 저장소, 파일, 커밋, 브랜치를 관리하는 방법을 다룹니다. GitHub 연결과 협업은 [github.md](github.md), 따라 하기 실습은 [practice.md](practice.md)를 참고합니다.

## 1. 사용자 설정

Git은 커밋을 만들 때 작성자의 이름과 이메일을 기록합니다.

```bash
git config --global user.name "이름"
git config --global user.email "이메일"
```

현재 설정을 확인합니다.

```bash
git config --global user.name
git config --global user.email
```

회사 저장소처럼 현재 저장소에서만 다른 정보를 쓰려면 `--global` 없이 설정합니다.

```bash
git config user.name "회사 이름"
git config user.email "company@example.com"
```

> [!TIP]
> 저장소별 설정은 전역 설정보다 우선합니다.

## 2. 저장소 초기화와 제외 규칙

프로젝트 폴더에서 Git 저장소를 만듭니다.

```bash
cd "프로젝트 경로"
git init
```

`.gitignore`에는 Git이 추적하지 않을 파일과 폴더를 적습니다.

```gitignore
.env
node_modules/
```

이미 커밋된 파일은 `.gitignore`에 추가해도 계속 추적됩니다. 이때는 먼저 추적 대상에서 제거해야 합니다.

```bash
git rm --cached <파일명>
```

## 3. 상태와 변경 내용 확인

| 명령어 | 설명 |
| --- | --- |
| `git status` | 파일의 수정·스테이징·추적 상태를 확인합니다. |
| `git diff` | 아직 스테이징하지 않은 변경 내용을 확인합니다. |
| `git diff --staged` | 스테이징한 변경 내용을 확인합니다. |
| `git diff HEAD` | 마지막 커밋 이후의 모든 변경 내용을 확인합니다. |
| `git diff <파일명>` | 특정 파일의 변경 내용을 확인합니다. |
| `git diff main...<브랜치이름>` | `main`에서 갈라진 뒤 해당 브랜치에 추가된 변경 내용을 확인합니다. |
| `git diff --check` | 줄 끝 공백 등 공백 오류를 확인합니다. |

## 4. 스테이징과 커밋

스테이징에서는 다음 커밋에 포함할 변경을 고릅니다. 커밋은 고른 변경을 Git 기록으로 저장하는 단계입니다.

```text
파일 수정 → git add → git commit
```

```bash
# 파일 하나 스테이징
git add <파일명>

# 현재 폴더의 변경 사항 모두 스테이징
git add .

# 커밋 만들기
git commit -m "로그인 오류 수정"
```

스테이징만 취소하고 수정 내용은 남기려면 아래 명령어를 사용합니다.

```bash
git restore --staged <파일명>
```

### 마지막 커밋 수정

커밋 메시지를 바꾸거나 빠진 파일을 마지막 커밋에 넣을 수 있습니다.

```bash
# 메시지 수정
git commit --amend -m "수정한 커밋 메시지"

# 스테이징한 파일을 마지막 커밋에 추가하고 메시지는 유지
git add <빠뜨린-파일>
git commit --amend --no-edit
```

> [!WARNING]
> `--amend`는 커밋 ID를 바꿉니다. 이미 공유한 커밋은 수정하지 않는 편이 안전합니다.

## 5. 변경과 커밋 되돌리기

| 명령어 | 설명 |
| --- | --- |
| `git restore <파일명>` | 스테이징하지 않은 파일을 마지막 커밋 상태로 되돌립니다. |
| `git reset --soft HEAD~1` | 마지막 커밋만 취소하고 변경 사항은 스테이징 상태로 남깁니다. |
| `git reset HEAD~1` | 마지막 커밋과 스테이징을 취소하고 수정 내용만 남깁니다. |
| `git reset --hard HEAD~1` | 마지막 커밋과 수정 내용을 모두 버립니다. |
| `git revert <커밋ID>` | 지정한 커밋의 변경을 취소하는 새 커밋을 만듭니다. |

`git restore`와 `git reset --hard`는 수정 내용을 지웁니다. 실행 전 `git diff`로 확인합니다.

`git revert`는 기존 커밋을 지우지 않아 공유된 커밋을 되돌릴 때 적합합니다.

```bash
# 가장 최근 커밋을 되돌리는 새 커밋 만들기
git revert HEAD
```

revert 중 충돌이 나면 수정 후 계속 진행하거나 중단합니다.

```bash
git add <파일명>
git revert --continue

git revert --abort
```

## 6. 커밋 기록과 코드 이력 확인

| 명령어 | 설명 |
| --- | --- |
| `git log` | 커밋 기록을 자세히 확인합니다. |
| `git log --oneline` | 커밋 기록을 한 줄씩 간단히 확인합니다. |
| `git show HEAD` | 가장 최근 커밋의 변경 내용을 확인합니다. |
| `git reflog` | HEAD와 브랜치가 가리킨 이전 위치를 확인합니다. |
| `git blame <파일명>` | 파일의 각 줄을 마지막으로 수정한 커밋과 작성자를 확인합니다. |
| `git grep "검색어"` | 추적 중인 파일에서 문자열을 검색합니다. |

`git reflog`는 reset, rebase, 브랜치 이동 뒤에 이전 커밋을 찾을 때 유용합니다.

## 7. 브랜치 만들기와 이동

브랜치는 독립된 작업 흐름입니다. 하나의 저장소에서 작업을 나누어 진행할 때 만듭니다.

| 명령어 | 설명 |
| --- | --- |
| `git branch` | 로컬 브랜치 목록을 확인합니다. |
| `git branch <브랜치이름>` | 브랜치를 만듭니다. |
| `git switch <브랜치이름>` | 브랜치로 이동합니다. |
| `git switch -c <브랜치이름>` | 브랜치를 만들고 바로 이동합니다. |
| `git branch -m <새이름>` | 현재 브랜치의 이름을 바꿉니다. |
| `git branch -d <브랜치이름>` | 병합된 브랜치를 삭제합니다. |
| `git branch -D <브랜치이름>` | 병합하지 않은 브랜치도 강제로 삭제합니다. |

## 8. 브랜치 병합과 이력 정리

변경 내용을 받는 쪽은 현재 브랜치입니다.

```bash
# main에 feature/login의 변경 사항 병합
git switch main
git merge feature/login
```

| 명령어 | 설명 |
| --- | --- |
| `git merge <브랜치이름>` | 선택한 브랜치의 변경 사항을 현재 브랜치에 병합합니다. |
| `git merge --squash <브랜치이름>` | 선택한 브랜치의 변경을 하나로 스테이징합니다. 이후 `git commit`이 필요합니다. |
| `git rebase <브랜치이름>` | 현재 브랜치에서 만든 커밋을 선택한 브랜치의 최신 커밋 뒤에 다시 적용합니다. |
| `git rebase -i HEAD~3` | 최근 3개 커밋의 순서·메시지·병합 여부를 대화형으로 정리합니다. |

> [!WARNING]
> 이미 공유한 브랜치에서 rebase나 대화형 rebase를 실행하면 커밋 ID가 바뀝니다.

## 9. 병합 충돌 해결

두 브랜치가 같은 파일의 같은 부분을 다르게 수정하면 충돌이 발생합니다.

```text
충돌 시작: <<<<<<< HEAD
현재 브랜치의 내용
분리선: =======
병합하려는 브랜치의 내용
충돌 끝: >>>>>>> feature/login
```

필요한 내용을 남기고 충돌 표시 줄을 모두 지운 뒤 아래 명령어를 실행합니다.

```bash
git add <파일명>

# merge 중이었다면
git commit

# rebase 중이었다면
git rebase --continue

# cherry-pick 중이었다면
git cherry-pick --continue
```

중단하고 시작 전 상태로 돌아가려면 작업 방식에 맞는 명령어를 사용합니다.

```bash
git merge --abort
git rebase --abort
git cherry-pick --abort
git revert --abort

# squash merge를 중단할 때
git reset --merge
```

## 10. 작업 내용 임시 보관

| 명령어 | 설명 |
| --- | --- |
| `git stash` | 추적 중인 파일의 변경 사항을 임시 보관합니다. |
| `git stash -u` | 추적하지 않는 새 파일까지 함께 보관합니다. |
| `git stash list` | 보관한 작업 목록을 확인합니다. |
| `git stash pop` | 가장 최근 작업을 적용하고 보관 목록에서 제거합니다. |
| `git stash apply` | 가장 최근 작업을 적용하지만 보관 목록에는 남깁니다. |
| `git stash drop` | 가장 최근 보관 작업을 삭제합니다. |

## 11. 특정 커밋만 가져오기

다른 브랜치의 특정 커밋 하나만 현재 브랜치에 적용할 때 쓰는 명령어입니다.

```bash
git cherry-pick <커밋ID>
```

충돌이 나면 [병합 충돌 해결](#9-병합-충돌-해결)의 cherry-pick 절차를 따릅니다.

## 12. 버그가 생긴 커밋 찾기

`git bisect`는 정상 커밋과 오류 커밋 사이를 이분 탐색해 버그가 들어온 커밋을 찾습니다.

```bash
git bisect start
git bisect bad                 # 현재 커밋에 오류가 있음
git bisect good <정상-커밋ID>  # 정상 동작한 커밋 지정
```

Git이 이동시킨 커밋에서 테스트한 뒤 결과를 표시합니다.

```bash
git bisect good
git bisect bad

# 탐색 종료 및 원래 브랜치로 복귀
git bisect reset
```

## 13. 여러 브랜치 동시 작업

`git worktree`를 쓰면 브랜치를 전환하지 않고 다른 폴더에서 별도 브랜치를 함께 작업합니다.

```bash
git worktree add -b hotfix/login ../프로젝트-hotfix
git worktree list
git worktree remove ../프로젝트-hotfix
```

## 14. 태그 관리

태그는 특정 커밋에 버전 이름표를 붙일 때 사용합니다. 배포 버전에는 메시지가 남는 주석 태그를 씁니다.

```bash
git tag
git tag -a v1.0.0 -m "v1.0.0 배포"
git show v1.0.0
git tag -d v1.0.0
```

태그를 원격 저장소에 올리거나 삭제하는 방법은 [github.md](github.md#6-원격-태그-관리)를 참고합니다.

## 15. 추적하지 않는 파일 정리

`git clean`은 추적하지 않는 파일을 삭제합니다. 먼저 삭제 대상을 확인합니다.

```bash
git clean -nd  # 삭제될 파일과 폴더를 미리 확인
git clean -fd  # 확인한 파일과 폴더를 실제로 삭제
```

> [!WARNING]
> `git clean -fd`는 삭제한 파일을 Git으로 복구할 수 없습니다.

## 16. 저장소 규칙과 구성 요소

### `.gitattributes`

파일별 줄바꿈, diff, 병합 규칙을 저장소 전체에 적용합니다.

```gitattributes
* text=auto
*.sh text eol=lf
```

### Git hooks

`.git/hooks/`의 hook은 커밋이나 push 전 자동 검사를 실행합니다. 예를 들어 `pre-commit` hook은 커밋 전에 검사 명령어를 돌릴 수 있습니다.

> [!NOTE]
> `.git/hooks/`은 기본적으로 Git이 추적하지 않습니다. 팀과 공유할 hook은 저장소 안의 별도 폴더에 스크립트와 설치 방법을 함께 관리합니다.

### Submodule

다른 Git 저장소를 현재 저장소의 하위 경로에 특정 커밋으로 포함할 때 사용합니다.

```bash
git submodule add <저장소-주소> <경로>
git submodule update --init --recursive
```

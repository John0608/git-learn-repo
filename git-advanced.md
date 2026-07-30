# 로컬 Git 심화

기초 명령어는 [init.md](init.md)를 먼저 익힙니다. 이력 복구, 협업 전 이력 정리, 큰 저장소 관리, 저장소 진단이 이 문서의 범위입니다.

## 1. Git이 관리하는 영역

```text
작업 디렉터리 → index(스테이징) → 로컬 저장소(커밋)
                 git add          git commit
```

`restore`, `reset`, `checkout`은 되돌리는 영역이 다릅니다. 실행 전 `git status`와 `git diff`를 확인합니다.

## 2. 이력 복구

| 상황 | 명령어 | 목적 |
| --- | --- | --- |
| 공유하지 않은 마지막 커밋 취소 | `git reset HEAD~1` | 수정 내용을 남긴 채 커밋만 취소합니다. |
| 공유한 커밋 취소 | `git revert <커밋ID>` | 기존 이력을 보존합니다. |
| 이전 위치 찾기 | `git reflog` | reset·rebase 전의 HEAD를 찾습니다. |
| 복구 후보 확인 | `git switch -c recover <커밋ID>` | 별도 브랜치에서 안전하게 확인합니다. |

저장소 손상이 의심될 때만 `git fsck --full`로 객체 무결성을 검사합니다.

## 3. 대화형 rebase

```bash
git rebase -i HEAD~3
```

| 명령 | 용도 |
| --- | --- |
| `pick` | 커밋 유지 |
| `reword` | 메시지 수정 |
| `edit` | 커밋 내용 수정 |
| `squash` | 이전 커밋과 합치고 메시지 편집 |
| `fixup` | 이전 커밋과 합치고 현재 메시지 제거 |
| `drop` | 커밋 제거 |

> [!WARNING]
> 공유된 브랜치에서 rebase하면 커밋 ID가 바뀝니다. 개인 브랜치에서 PR을 만들기 전에만 이력을 정리합니다.

## 4. 충돌 재사용과 선택적 이동

```bash
git config rerere.enabled true
git cherry-pick <커밋ID>
git format-patch -1 <커밋ID>
git am <patch파일>
git apply <patch파일>
git bundle create repo.bundle --all
```

`rerere`를 켜 두면 같은 충돌을 해결한 결과를 기억합니다. patch와 bundle은 네트워크 없이 변경을 전달할 때 씁니다. 적용 전 출처와 대상 브랜치를 확인합니다.

## 5. 버그 조사

```bash
git bisect start
git bisect bad
git bisect good <정상-커밋ID>
git bisect reset
```

| 명령어 | 용도 |
| --- | --- |
| `git blame <파일명>` | 각 줄의 마지막 변경 커밋을 확인합니다. |
| `git log -S "문자열" -- <파일명>` | 문자열이 추가·삭제된 커밋을 찾습니다. |
| `git log -G "정규식" -- <파일명>` | diff가 정규식과 일치하는 커밋을 찾습니다. |
| `git range-diff A...B A...C` | 두 patch series의 차이를 비교합니다. |

`blame`은 책임 추궁이 아니라 변경 맥락 조사에 사용합니다.

## 6. 큰 저장소와 여러 작업 공간

| 기능 | 사용 시점 |
| --- | --- |
| `git worktree` | 브랜치를 바꾸지 않고 여러 작업을 동시에 할 때 |
| `git sparse-checkout` | monorepo에서 필요한 경로만 체크아웃할 때 |
| `git submodule` | 다른 저장소의 특정 커밋을 하위 경로에 고정할 때 |
| Git LFS | 대용량 바이너리를 별도 LFS 저장소에 관리할 때 |

Submodule은 하위 저장소의 브랜치가 아니라 특정 커밋 ID를 기록합니다. 갱신 뒤 상위 저장소의 참조도 커밋해야 합니다.

## 7. 저장소 관리와 팀 규칙

```bash
git count-objects -vH
git gc
git maintenance run
```

| 파일 | 역할 |
| --- | --- |
| `.gitignore` | 추적하지 않을 파일 |
| `.gitattributes` | 줄바꿈·diff·merge·LFS 규칙 |
| `.mailmap` | 작성자 이름·이메일 통합 |
| `.git/hooks/` | 로컬 자동 검사 |
| `CODEOWNERS` | 경로별 PR 검토자(GitHub) |

hooks는 기본으로 공유되지 않습니다. 팀 hook은 버전 관리되는 스크립트와 설치 방법을 함께 제공합니다.

## 8. 서명

```bash
git commit -S -m "서명한 커밋"
git tag -s v1.0.0 -m "서명한 태그"
git verify-commit <커밋ID>
git verify-tag v1.0.0
```

서명은 키를 신뢰할 수 있을 때만 의미가 있습니다. 조직의 키 관리 정책을 먼저 확인합니다.

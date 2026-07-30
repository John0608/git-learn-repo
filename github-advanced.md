# GitHub 협업 심화

기초 원격 작업과 Issue·PR은 [github.md](github.md)를 먼저 익힙니다. 여러 사람과 여러 저장소가 참여하는 협업에서 지킬 운영 규칙을 정리합니다.

## 1. 권한과 최소 권한 원칙

조직 저장소 역할은 Read, Triage, Write, Maintain, Admin 순으로 권한이 커집니다.

| 역할 | 주 사용처 |
| --- | --- |
| Read | 코드·Issue·PR을 읽고 의견을 남기는 사람 |
| Triage | Issue·PR을 분류하고 관리하는 사람 |
| Write | 브랜치에 작업을 올리는 개발자 |
| Maintain | 저장소 운영을 돕는 사람 |
| Admin | 권한·보안·규칙·삭제를 관리하는 사람 |

권한은 업무에 필요한 최소 수준으로 부여합니다. 외부 협력자, PAT, SSH 키, deploy key는 정기적으로 검토하고 퇴사·계약 종료 시 즉시 회수합니다.

## 2. Fork와 upstream

원본 저장소에 쓰기 권한이 없으면 fork를 씁니다. 내 계정에 복사본을 만들어 기여하는 방식입니다.

```bash
git clone <내-fork-주소>
cd <저장소>
git remote add upstream <원본-저장소-주소>
git remote -v
```

| 원격 | 역할 |
| --- | --- |
| `origin` | 내 fork. 작업 브랜치를 push합니다. |
| `upstream` | 원본 저장소. 변경을 가져옵니다. |

```bash
git fetch upstream
git switch main
git merge --ff-only upstream/main
git push origin main
```

## 3. Issue 운영

- Label은 유형(`bug`, `feature`, `documentation`)과 상태를 구분합니다.
- Milestone은 릴리스·기간 단위 목표를 묶습니다.
- 큰 작업은 sub-issue로 나누고 선행 작업은 dependency로 연결합니다.
- 재현 방법·기대 결과·완료 기준이 없는 Issue는 구현하지 않습니다.

반복되는 요청은 `.github/ISSUE_TEMPLATE/`에 Markdown 템플릿 또는 Issue Form을 둡니다. 보안 취약점은 일반 Issue에 공개하지 않고 저장소의 보안 보고 절차를 따릅니다.

## 4. PR 품질과 검토

```md
## 변경 내용
- 로그인 실패 시 오류 메시지를 표시합니다.

## 연결 Issue
Closes #123

## 검증
- `npm test` 통과

## 영향 범위
- 로그인 화면과 인증 API 오류 처리
```

좋은 PR은 목적이 하나이고 변경 이유·검증 결과·영향 범위를 담습니다. `CODEOWNERS`를 사용하면 경로별 담당 리뷰어를 자동 요청할 수 있습니다. Draft PR은 아직 병합 준비가 안 된 작업을 공유할 때 사용합니다.

## 5. Project 운영

| 보기 | 목적 |
| --- | --- |
| Table | 담당자·우선순위·완료일을 관리합니다. |
| Board | Status별 병목을 확인합니다. |
| Roadmap | 날짜·iteration 기반 일정을 확인합니다. |
| Insights | 완료량과 상태 분포를 확인합니다. |

필드는 `Status`, `Priority`, `Assignee`, `Iteration` 또는 `Target date`부터 시작합니다. 자동화는 새 Issue·PR 추가처럼 반복되는 작업에서만 추가합니다.

## 6. 보호 규칙과 보안

기본 브랜치에는 다음을 권장합니다.

- 직접 push 제한
- PR 리뷰 승인 요구
- Actions 상태 검사 통과 요구
- force push와 branch 삭제 제한
- 최신 대상 브랜치 반영 요구 여부 결정

Secret은 Settings의 Secret 저장소에만 두고 코드·Issue·PR·로그에 기록하지 않습니다. Dependabot, secret scanning, 코드 스캔은 조직의 정책과 요금제를 확인해 활성화합니다.

## 7. Release 운영

Release에는 태그, 배포 노트, 파일을 묶습니다. 버전 정책, 사전 배포 여부, 변경 기록 형식, 롤백 기준을 팀에서 먼저 합의합니다.

릴리스 전 확인 목록:

- 기본 브랜치 검사와 리뷰가 통과했는가
- 태그가 정확한 커밋을 가리키는가
- 비밀값과 디버그 파일이 포함되지 않았는가
- 롤백할 이전 버전과 담당자가 정해졌는가

## 공식 참고 문서

- [저장소 역할](https://docs.github.com/en/organizations/managing-user-access-to-your-organizations-repositories/managing-repository-roles/repository-roles-for-an-organization)
- [fork의 upstream 설정](https://docs.github.com/en/pull-requests/how-tos/work-with-forks/configuring-a-remote-repository-for-a-fork)
- [fork 동기화](https://docs.github.com/en/pull-requests/how-tos/work-with-forks/syncing-a-fork)

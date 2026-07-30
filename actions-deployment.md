# GitHub Actions와 배포 심화

기본 workflow와 GitHub Pages 실습은 [github.md](github.md), [practice.md](practice.md)를 먼저 봅니다.

## 1. workflow 설계 순서

1. 자동화할 목표를 하나 정합니다. 예: 테스트, 빌드, 정적 사이트 배포.
2. 실행 시점(event)과 대상 브랜치·경로를 정합니다.
3. 최소 권한을 `permissions`에 적습니다.
4. Secret·Environment·승인 규칙을 설정합니다.
5. 실패 시 로그, artifact, 롤백 절차를 정합니다.

## 2. event와 기본 문법

```yaml
on:
  pull_request:
  push:
    branches: [main]
  workflow_dispatch:
  schedule:
    - cron: "0 0 * * 1"
```

`workflow_dispatch`를 넣으면 화면에서 직접 실행합니다. `schedule`의 cron은 UTC 기준입니다.

## 3. 권한·Secret·Environment

```yaml
permissions:
  contents: read
```

배포처럼 쓰기 권한이 필요한 job에만 권한을 추가합니다. Secret은 Settings의 repository·environment·organization Secret에 등록하고 환경 변수로 전달합니다.

```yaml
env:
  DEPLOY_TOKEN: ${{ secrets.DEPLOY_TOKEN }}
```

Secret 값을 출력하거나 `set -x`를 사용하지 않습니다. 외부 fork의 PR에는 Secret이 기본적으로 전달되지 않는다는 전제로 workflow를 설계합니다.

## 4. 검사, artifact, cache, matrix

| 기능 | 목적 |
| --- | --- |
| 테스트 | PR에서 회귀를 찾습니다. |
| artifact | 빌드 파일·테스트 결과·스크린샷을 실행 뒤에도 보관합니다. |
| cache | 의존성처럼 다시 내려받을 수 있는 데이터를 재사용합니다. |
| matrix | 여러 운영체제·언어 버전을 검사합니다. |

artifact는 결과물을 보관하거나 job 사이에 전달할 때, cache는 다시 생성 가능한 의존성을 빠르게 재사용할 때 씁니다. cache가 없어도 workflow는 정상 동작해야 합니다.

## 5. 조건, 동시성, 재사용

```yaml
concurrency:
  group: deploy-${{ github.ref }}
  cancel-in-progress: true
```

같은 브랜치의 오래된 배포를 취소할 수 있습니다. `if:`는 event·브랜치·변수에 따라 job 또는 step을 제한합니다. 여러 저장소에서 반복하는 workflow는 `workflow_call`로 재사용하고 필요한 Secret만 명시적으로 전달합니다.

## 6. 배포 환경과 승인

`development`, `staging`, `production` Environment를 나누면 환경별 Secret과 보호 규칙을 적용할 수 있습니다.

```text
PR 검사 → main 병합 → staging 배포·확인 → production 승인 → production 배포
```

production은 승인자, 대기 시간, 배포 가능 브랜치를 설정합니다. 일반 웹 서비스와 데이터베이스 배포에는 플랫폼별 배포 명령, 백업, 마이그레이션, 롤백 절차가 필요합니다.

## 7. Release와 롤백

Release는 버전과 파일을 공개합니다. 서비스 배포는 실행 중인 환경을 바꿉니다. 실패한 배포를 새 코드로 즉시 덮어쓰지 말고 이전 태그의 artifact·이미지로 되돌리는 롤백 절차를 문서화합니다.

## 8. Actions 보안

- 외부 Action은 버전, 가능하면 커밋 SHA로 고정합니다.
- PR이 제공한 코드에 쓰기 권한 Secret을 넘기지 않습니다.
- `pull_request_target`는 권한 모델을 이해한 뒤에만 사용합니다.
- 토큰 권한을 최소화하고 정기적으로 교체합니다.
- 로그와 artifact에 개인정보·비밀값이 없는지 확인합니다.

## 공식 참고 문서

- [Workflow 문법](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [Secrets](https://docs.github.com/en/actions/concepts/security/secrets)
- [Artifact와 cache의 차이](https://docs.github.com/en/actions/concepts/workflows-and-actions/workflow-artifacts)
- [GitHub Pages workflow](https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages)

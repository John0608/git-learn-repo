# Git과 GitHub 학습 지도

이 저장소는 Git과 GitHub를 처음 접하는 사람을 위한 교재입니다. 로컬 작업, 팀 협업, 자동화와 배포까지 순서대로 익힙니다.

## 시작 전

- 터미널은 명령어를 입력하는 프로그램입니다. `pwd`는 현재 폴더, `cd <경로>`는 폴더 이동입니다.
- Git은 내 컴퓨터에서 코드와 이력을 관리합니다.
- GitHub는 Git 저장소를 온라인에서 공유하고 협업하는 서비스입니다.

```bash
git --version
```

## 학습 순서

| 단계 | 문서 | 목표 |
| --- | --- | --- |
| 1 | [로컬 Git 기초](init.md) | 저장소·커밋·브랜치·복구의 기본 흐름을 익힙니다. |
| 2 | [GitHub 기초](github.md) | 원격 저장소·Issue·Project·PR·기본 자동화를 익힙니다. |
| 3 | [로컬 Git 심화](git-advanced.md) | 이력 정리·복구·대규모 저장소·검증을 익힙니다. |
| 4 | [GitHub 협업 심화](github-advanced.md) | 권한·fork·템플릿·보안·운영 규칙을 익힙니다. |
| 5 | [Actions와 배포 심화](actions-deployment.md) | CI, Secret, artifact, 환경별 배포와 롤백을 익힙니다. |
| 실습 | [단계별 실습](practice.md) | 별도 연습 저장소에서 직접 실행합니다. |

## 기본 원칙

1. 실행 전 `git status`와 `git diff`로 상태를 확인합니다.
2. `reset --hard`, `clean -fd`, 강제 push는 영향 범위를 확인한 뒤 사용합니다.
3. 공유 브랜치의 실수는 `reset`보다 `revert`로 되돌립니다.
4. 비밀번호, PAT, SSH 개인 키, API 키는 코드·Issue·PR·로그에 기록하지 않습니다.

## 기본 과정 완료 기준

- 브랜치에서 작업하고 PR로 병합한다.
- 충돌을 해결하거나 안전하게 중단한다.
- 이미 push한 잘못된 커밋을 `revert`로 취소한다.
- Issue를 Project에서 관리하고 PR과 연결한다.
- Actions 검사와 GitHub Pages 배포 결과를 확인한다.

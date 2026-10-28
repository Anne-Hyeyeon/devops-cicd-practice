# devops-cicd-practice

GitHub Actions로 구성한 Nest.js 서버의 CI/CD 파이프라인 실습 저장소입니다.

## 파이프라인

```
push / PR → Checkout → npm ci → Lint → Unit test → E2E test → Build → Artifact 업로드
                                                                     ↓ (main push일 때만)
                                     Artifact 다운로드 → scp로 EC2 전송 → pm2 재시작 → Health check
```

워크플로우 파일: [`.github/workflows/cicd.yml`](.github/workflows/cicd.yml)

## Trigger 규칙

| 이벤트 | 실행 범위 |
|---|---|
| `feature/**` 브랜치 push | CI |
| `main` 대상 Pull Request | CI |
| `main` 브랜치 push | CI → CD |
| 수동 실행 (`workflow_dispatch`) | CI |
| `*.md` 파일만 변경 | 실행 안 함 |

## CD 설정

저장소 Settings → Secrets and variables → Actions 에 아래 값을 등록하면 `main` push 시 EC2로 자동 배포됩니다.
등록하지 않으면 배포 단계는 건너뛰고 CI만 실행됩니다.

| Secret | 값 |
|---|---|
| `EC2_HOST` | EC2 퍼블릭 IP |
| `EC2_USERNAME` | `ec2-user` |
| `EC2_SSH_PRIVATE_KEY` | `.pem` 키 파일 전체 내용 |

## 로컬 실행

```bash
npm ci
npm run start      # http://localhost:3000
npm test           # 단위 테스트
npm run test:e2e   # E2E 테스트
npm run build      # dist/ 생성
```

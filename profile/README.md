# 💊 ACS-Nutrients: Personal Supplement Intelligence

> **데이터 기반 개인 맞춤형 영양제 분석 및 추천 플랫폼**
> 사용자의 건강검진 기록, 처방약물, 현재 복용 중인 영양제를 분석하여 최적의 영양 상태를 가이드합니다.

---

## 🚀 Project Overview

단순한 영양제 추천을 넘어, **국가 건강검진 데이터**와 **실제 처방 이력**을 결합하고 **AWS AI 서비스**를 활용해 사용자에게 가장 필요한 영양 성분과 적정 함량을 계산해주는 MSA 기반 서비스입니다.

---

## 🏗️ Architecture Overview

```
[React Frontend] ──HTTPS──▶ [ALB] ──▶ ┌─────────────────────────────────────┐
                                       │         ECS Fargate (Private)        │
                                       │  users │ analysis │ chatbot │ history │
                                       └──────────────┬──────────────────────┘
                                                       │ boto3 invoke
                                       ┌──────────────▼──────────────────────┐
                                       │    AWS Bedrock AgentCore Runtime     │
                                       │  supervisor → analysis / question    │
                                       │             → summary                │
                                       └─────────────────────────────────────┘
```

---

## 📦 Repository Structure

```
code-total/
├── codecaine-react-frontend/        # React 18 + Vite 프론트엔드
├── codecaine-python-users/          # 사용자 인증 및 건강 데이터 수집
├── codecaine-python-analysis/       # 영양 분석 및 추천 엔진
├── codecaine-python-chatbot/        # 챗봇 상담 (WebSocket)
├── codecaine-python-history/        # 이력 저장 및 아카이빙
├── codecaine-python-supervisoragent/  # 에이전트 오케스트레이션
├── codecaine-python-analysisagent/    # 영양 분석 AI 에이전트
├── codecaine-python-questionagent/    # Q&A AI 에이전트
├── codecaine-python-summaryagent/     # 응답 포맷팅 에이전트
└── codecaine-terraform-infra/       # AWS 인프라 (Terraform IaC)
```

---

## 🧩 Microservices

### 백엔드 서비스 (ECS Fargate)

| 서비스 | 역할 | 핵심 기술 |
|--------|------|-----------|
| **users** | 인증, CODEF API 연동, OCR | FastAPI, Cognito, Textract, asyncpg |
| **analysis** | 영양 분석 파이프라인, 추천 | FastAPI, SQLAlchemy, AgentCore |
| **chatbot** | 실시간 상담, 세션 관리 | FastAPI, WebSocket, Redis |
| **history** | 이력 저장 및 아카이빙 | FastAPI, S3, Aurora PG |

### AI 에이전트 (Bedrock AgentCore Runtime)

| 에이전트 | 역할 |
|----------|------|
| **supervisor** | 요청 분류 및 라우팅 (Q&A vs 재분석) |
| **analysis** | KB 기반 영양 분석 (Cohere 임베딩) |
| **question** | 일반 영양 Q&A (DuckDuckGo 검색 포함) |
| **summary** | 분석 결과 자연어 변환 |

**요청 흐름:**
- **분석 경로:** Users → Analysis Service → AnalysisAgent (KB + Bedrock) → 결과 저장
- **상담 경로:** Users → Chatbot → SupervisorAgent → QuestionAgent or AnalysisAgent → SummaryAgent → 응답

---

## 🛠️ Tech Stack

| Category | Technology |
|----------|------------|
| **Frontend** | React 18.3, Vite 6.3, TypeScript, Tailwind CSS 4, Radix UI, Recharts |
| **Backend** | Python 3.11, FastAPI, SQLAlchemy 2.0, asyncpg, Alembic |
| **AI/LLM** | AWS Bedrock (Claude), LangGraph, LangChain, Cohere Embeddings |
| **Auth** | AWS Cognito (JWT RS256), Kakao OAuth |
| **Database** | Aurora PostgreSQL (Multi-AZ), ElastiCache Redis |
| **Storage** | S3 (아카이브, KB, 백업) |
| **Infrastructure** | AWS ECS Fargate, ALB, ECR, VPC, Secrets Manager |
| **IaC** | Terraform (14+ 모듈) |
| **CI/CD** | GitHub Actions → ECR → ECS (OIDC 기반 IAM) |
| **Observability** | AWS X-Ray, ADOT, CloudWatch, AWS Managed Grafana |
| **External API** | CODEF API (건강검진/처방), AWS Textract (OCR) |

---

## ☁️ Infrastructure (Terraform)

**Region:** ap-northeast-2 (Seoul) / Multi-AZ (2a, 2c)

| 모듈 | 내용 |
|------|------|
| `foundation` | VPC (10.0.0.0/16), 서브넷, IGW, 보안 그룹 |
| `compute` | ECS Fargate 클러스터, ALB, Bastion Host |
| `database-rds` | Aurora PostgreSQL (users/analysis/chatbot/history DB) |
| `security` | Route53, ACM, WAF, Cognito User Pool |
| `agentcore` | Bedrock AgentCore Runtime, Lambda |
| `monitoring` | Grafana Workspace, CloudWatch 대시보드 |
| `fis` | Chaos Engineering (14+ 실험 시나리오) |
| `dms` | Database Migration Service |
| `dr` | Disaster Recovery |

---

## 🔒 데이터 보안 정책

* **건강검진 데이터 / 처방약물 데이터:** 수집 후 **1개월** 뒤 자동 물리적 삭제
* **챗봇 상담 이력:** 상담 완료 후 **최대 1개월** 보관 후 물리적 삭제
* **최소 저장 원칙:** 분석에 필요한 핵심 데이터 외 서버 잔류 금지
* **암호화:** Secrets Manager 기반 DB 자격증명, TLS 통신

---

## 🚦 CI/CD

모든 서비스는 동일한 파이프라인을 따릅니다.

```
Push to deploy branch
  → GitHub Actions (OIDC → AWS IAM)
  → Docker Build & Push (ECR)
  → ECS Task Definition 업데이트
  → ECS Rolling Deploy
```

---

## 🔭 Observability

* **분산 추적:** AWS X-Ray + ADOT (OpenTelemetry) 사이드카
* **메트릭:** CloudWatch (ECS, ALB, AgentCore 커스텀 메트릭)
* **시각화:** AWS Managed Grafana (Service Health / Business Overview 대시보드)
* **카오스 테스트:** FIS 실험 (ElastiCache 재부팅, NAT 장애, RDS 격리 등)

---

## 👥 Members

* **Organization:** [ACS-Nutrients](https://github.com/ACS-Nutrients)

---

**ACS-Nutrients**는 데이터로 증명되는 건강한 삶을 지향합니다.

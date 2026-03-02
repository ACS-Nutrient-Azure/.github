## Hi there 👋


# 💊 ACS-Nutrients: Personal Supplement Intelligence

> **데이터 기반 개인 맞춤형 영양제 분석 및 추천 플랫폼** > 사용자의 건강검진 기록, 처방약물, 현재 복용 중인 영양제를 분석하여 최적의 영양 상태를 가이드합니다.

---

## 🚀 Project Overview

단순한 영양제 추천을 넘어, **국가 건강검진 데이터**와 **실제 처방 이력**을 결합하고 **AWS AI 서비스**를 활용해 사용자에게 가장 필요한 영양 성분과 적정 함량을 계산해주는 MSA 기반 서비스입니다.

---

## 🏗️ Microservices Architecture (MSA)

우리 서비스는 독립적인 확장이 가능하도록 4개의 핵심 서비스로 구성됩니다.

### 1. 👤 User Information Service (사용자 정보)

사용자의 민감한 건강 데이터를 안전하게 수집하고 관리합니다.

* **인증/인가:** 회원가입 및 로그인 관리
* **데이터 연동:** `CODEF API`를 통한 건강검진 결과 및 문진 데이터 수집
* **처방 정보:** `CODEF API` 기반 처방약명, 함량, 처방 기간 관리
* **OCR 분석:** `AWS Textract`를 활용하여 현재 복용 중인 영양제 라벨에서 성분 자동 추출
* **개인화:** 사용자의 주관적 현재 컨디션 상태 기록

### 2. 🧠 Recommendation & Analysis Service (추천/분석)

서비스의 핵심 로직이 작동하는 '브레인' 부문입니다.

* **복합 AI 분석:** 검진 데이터 + 처방약 + 컨디션을 종합하여 부족 영양군 도출
* **정밀 함량 계산:** * 필요 영양군 권장량 vs 현재 복용 중인 영양제 함량 비교 로직
* 부족분만큼의 최적 상품 추천


* **데이터베이스 구축:** * 영양제별 일일 적정/최대 섭취량(RDA/UL) 가이드라인 DB
* 실제 유통 영양제 상품 정보 및 성분 DB



### 3. 📅 Intake History Service (섭취 이력)

사용자의 꾸준한 건강 습관 형성을 돕습니다.

* **데일리 로그:** 매일 섭취한 영양제 리스트 관리
* **시각화:** 달력 형태의 UI를 통해 섭취 이력 및 달성도 확인

### 4. 💬 Chatbot Consultation Service (챗봇 상담)

LLM을 활용한 고도화된 상담 경험을 제공합니다.

* **맞춤형 상담:** 분석 결과와 사용자 정보를 종합하여 추가적인 건강 궁금증 해소
* **지식 기반:** 사용자 데이터를 기반으로 한 컨텍스트 유지 상담

---

## 🔒 Data Privacy & Security Policy

우리는 사용자의 민감 정보를 보호하기 위해 엄격한 데이터 관리 정책을 준수합니다.

* **물리적 삭제 정책:** * `건강검진 데이터` 및 `처방약물 데이터`: 수집 후 **1개월** 뒤 자동 물리적 삭제
* `챗봇 상담 이력`: 상담 완료 후 **최대 1개월** 보관 후 물리적 삭제


* **최소 저장 원칙:** 분석에 필요한 핵심 데이터 외에는 서버에 잔류시키지 않습니다.

---

## 🛠️ Tech Stack

| Category | Technology |
| --- | --- |
| **Cloud** | AWS (App Runner, S3, Firehose, Athena) |
| **AI/ML** | AWS Textract, Claude 3 (via Bedrock), Claude Code |
| **API Integration** | CODEF API (Health/Medical) |
| **Database** | PostgreSQL / DynamoDB (Session & Logs) |
| **Architecture** | Microservices Architecture (MSA) |

---

## 👥 Members

* **Organization:** [ACS-Nutrients](https://github.com/ACS-Nutrients)
* **Contact:** `your-email@example.com`

---

**ACS-Nutrients**는 데이터로 증명되는 건강한 삶을 지향합니다.

---

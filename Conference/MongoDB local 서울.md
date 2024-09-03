# Big Data Assistant - 생성형 AI를 활용한 데이터 업무 혁신
빅데이터 전문가 대체는 불가 but 조력자로 사용

## Wat id BD Assistant
AI Assistant를 통해 insight를 생성해 실제 사업에 기여할 수 있고, 전 임직원이 data를 기반한 의사결정을 할 수 있는 환경을 제공한다.

**BD Assistant Misson Statement**
단순 자연어 질의만으로 Data  분석 가능한 AI 기반 분석 자동화 서비스를 통해
임직원이 쉽고 빠르게 Data를 분석할 수 있는 서비스 제공

대부분의 데이터 분석은 단순 질의 or Data 추출. -> AI로 대체 가능

### 기존 Data 분석 프로세스
질의 의도 파악, Data 분석 전문가 배정, 수동 Data 분석(Table 분석, Mart 제작 등)
차트 제작, 분석 결과 전달, 원하는 결과가 아닌 경우 재협의, 리포트제작

**BD Assistant**
실시간 질의/답변 가능(자연어), 추가 질의 가능, 시각화 및 리포팅 자동 생성, 인사이트 생성 등 \

**프로세스**
- 의도 분석
- SQL 생성
- DATA 추출
- DATA 요약
- Charts 생성
- Charts 해석
- 피드백 등록

## MongoDB Operation
### MongoDB Use CCase
BD Assistant 서비스의 전체 DB 역할을 담당


## Next Steps
- Multi Agent 간 Communication Message
- Feedback & VoC
- Frontend Database

# MongoDB 8.0
Search와 Vector Search

완전 관리형 서비스를 원하면 atlas로 지원가능.

다양한 워크로드의 확장.

**8.0의 핵심 영역**
- 성능
  - 고가용성
  - 고확장성
- Workload Demand
- 유연한 샤딩과 수평적 확장
- 보안

# 서비스의 품질과 생산성 향상을 위한 MongoDB 생성형 AI 애플리케이션 아키텍처
## 생성형 AI 애플리케이션의 현재
대표적인 예: semantic text로 검색

가지고 있는 데이터셋이 아니더라도 쿼리 가능.

# Atlas Stream Processing으로 이벤트 기반 애플리케이션 구축하기
스트리밍 데이터는 최신 앱의 핵심입니다.

스트리밍 이벤트는 문서와 유사합니다.

Kafka는 Kafka대로 있어야 한다. 그것과 별개로 MongoDB가 필요.

MongoDB 어그리게이션 썼더니 너무 편하더라.

이벤트 데이터는 복잡하고, 이벤트가 중첩되거나, 필드가 누락되거나 할 수 있다.

데이터 스트림을 성공적으로 처리하려면 이러한 복잡한 문제를 해결할 수 있는 강력한 도구가 필요합니다.

## Atlas 스트림 처리 소개
- 문서 모델과 유연한 스키마를 중심으로 구축
- 연속 처리, 유효성 검사 및 상태 저장 창 처리

**Atlas의 Stream Processor** - 카프카는 그대로 존재하고 사용을 도움
- Aggregate
- Filter
- Route
- ALert
- Emit

Atlas 스트림 프로세싱의 장점
- 실시간 데이터 처리
- 강력한처리, 분석 기능
- 간편한 통합 및 연결성
- 손쉬운 인터페이스

# LG U+ Cloud Management Platform의 MongoDB 활용 사례
## Aggregation Pipeline
- 효율적인 Date Filtering, Grouping 및 Aggreagation
- 여러 Stage를 조합하여 원하는 결과를 생성
- Stage는 이전 Stage의 결과를 입력으로 사용

```
$match Stage를 통해 그 다음 Stage로 보내는 데이터의 양을 줄여주는 것이 중요
컬렉션 전체를 스캔하여 데이터를 집계하는 경우, 모든 Stage가 전체 데이터 수집 이후 시작하므로
$group이나 $sort stage가 쿼리 실행 시간 증가의 주요 원인이 될 수 있음
```

# Relational Migrator를 이용한 애플리케이션 현대화
## 애플리케이션 마이그레이션의 주요 도전 과제
**기회 분석**
- 수백 개의 애플리케이션 중 어떻게 선택할 것인가?
- 기술적 타당성을 어떻게 평가할 것인가?
- 내부와 외부의 어떤 전문 지식이 필요한가?
- 비즈니스 사레는 어떻게 구축할 것인가?

**데이터 모델**
- NoSQL 데이터 패턴을 어떻게 채택할 것인가?
- 누가 기본 데이터에 대해 이해하고 있는가?
- 유연성과 확장성을 위해 어떻게 설계할 것인가?

**마이그레이션 플랜**
- 프로덕션 전환 프로세스는 무엇인가?
- 마이그레이션 계획을 테스트하고 발전시킬 수 있는가?
- 데이터 무결성을 어떻게 검증할 것인가?
- 실제 프로토타입으로 리스크를 줄일 수 있는가?

**애플리케이션 리팩토링**
- ORM 또는 애플리케이션 데이터 계층을 어떻게 리포인팅 할 것인가?
- 다른 어떤 애플리케이션이 이 데이터에 접근하는가?
- 저장 프로시저나 트리거를 어떻게 마이그레이션할 것인가?

### MongoDB에서의 지원
- AI를 통해 RDB 스키마 -> 몽고DB 스키마 설계 지원.
- 소스 데이터를 마이그레이션하고 변환 지원
- 코드 아티팩트를 생성하고 SQl을 MQL로 변환 지원

# MongoDB Atlas, 데이터 관리 패러다임을 바꾸다.
**야놀자**
글로벌 톱티어 트래블 테크 기업? Next: 글로벌 여행 데이터 기업

**기능**
- 항공
- 교통
- 숙소예약
- 투어
- 레저티켓

## Why MongoDB Atlas?
as-is: public Cloud 환경의 MSA

NoSQL도 DB 팀에서 통합 관리하기 위해 (RDB처럼)

Copycat DB와 비교했지만 MongoDB가 여러 면에서 우수

# MongoDB Atlas로 해결한 주류(酒) 도메인의 문제들
개인화 추천
MongoDB로 개인화 추천 디비를 구성하면서 느낀 점
- Insert가 RDB보다 가벼움
- JSON 기반이라 데이터가 친숙함
- 쿼리 구성 시 선택할 수 있는
- ..

**지역 기반 서비스**
지역 데이터를 캐시로 저장 -> 값을 String으로 가져가므로 데이터 구조를 유연하게 가져갈 수 없음

Index type을 잘 설정할 것
- 2dsphere
- 2d
  - ..
  - ...
  - ..

### 고도화된 검색
기존에 사용하던 DB는 full-text search는 ...

자동 완성, 동의어 검색, 형태소 분리 등 내장된 기능 사용
- Analyzer(nori, standard, keyword)
- Synonyms
- Autocomplete

MongoDB Atlas Search를 구서앟면서 느낀 점
- 검색 쿼리를 실행할 수 있게 index를 만드는 과정은 간단
- 하지만 의도한 검색 결과를 쿼리하려면 analyzer와 analyzer 별 지원하는 옵션을 잘 튜닝해야 함
- Cluster 수정을 하면 search index를 빌드하는 과정에서 $search 쿼리를 할 때 error 발생
  - 이때는 MySQL full-text search로 임시 우회





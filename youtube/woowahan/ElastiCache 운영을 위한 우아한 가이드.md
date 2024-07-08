https://www.youtube.com/watch?v=JH07ABaRPWo

### 완전 관리형 서비스
- 1~2개의 샤드로도 운영 가능.
- 최대 500개 샤드, 한 개당 5개의 복제본 가능

### 가상 이슈 사례로 알아보는 ElastiCache 운영 방법
**확인 요소**
- CPU Utilization
- 메모리(Item Count, Eviction, Fragmentation, ...)
- 네트워크
- 커넥션
- 커맨드(latency, get/set type, slow log)
- 개발팀?

Redis Exporter를 이용해서 추가 모니터링

### Slow Log
실제로 어떤 쿼리가 수행됐는지 확인하는 용도

redis는 싱글 스레드로 처리하기 때문에
실제로 애플리케이션 응답에 오래 걸렸지만 slowlog에는 잡히지 않을 수 있음

**지연을 유발할 수 있는 커맨드 자제**
- Hash: HGETALL, HKEYS, HVALS
- Sorted Set, ZRANGE 0 -1, ZINTER, ZUNION, ZDIFF
- SET: SMEMERS, SINTER, SUNION, SDIFF
- common: KEYS, SORT, FLUSHDB, FLUSHALL

엔진 CPU 사용률과 CPU 사용률을 모니터링
CPU 4개 이상 -> 엔진 CPU 사용률 (EngineCPUUilization) 모디터링
2개 이하 -> CPU 사용률(CPUUtilization) 모니터링

### AWS 네트워크 I/O 크레딧 매커니즘
크레딧 버킷
- 네트워크 I/O 크레딧이 부족할 때, 크레딧 버킷에서 크레딧을 사용
- 크레딧이 부족하면 네트워크 I/O가 제한됨

버스트 대역폭 사용이 많아지면서 쿼리 응답속도가 전체적으로 느려진다면?
- 네트워크 성능 부족 의심
- 스케일 업

### 신규 레디스 메모리 분석 툴
실제 메모리를 까보고 싶다~ 해서 만듦

- 사용 메모리 급증
- 응답시간이 길어졌을 때
- 레디스의 데이터 파악이 필요할 때

**as-is**
기존엔 RMA(redis-memory-analyzer)를 사용했음. 10G 레디스 분석 시 5시간 이상 소요

**to-be**
- 매일 배치로 키 분석해서 그라파나 대시보드에 추가
- 10분 이내 분석

**intro.**
- key, type, size, size ratio, ttl 등을 확인
- prefix별 분석


### 성능 향상 시나리오
네트워크 I/O를 줄이기 위해 파이프라인 도입
- 파이프라인을 사용하면 네트워크 I/O를 줄일 수 있음
- 레디스 자체에서도 read/write가 줄어 성능이 크게 향상

클러스터를 쓸땐 파이프라인을 사용할 수 없음.  
-> 샤드별로 저장된 키를 조회해서 각각 파이프라인 방식 적용한 뒤 통합

하나의 노드에 저장된 데이터라도 같은 해시 슬롯이 아니라면 파이프라인 불가능
-> Lua script 도입
- 네트워크 I/O 최소화
- 레디스 내에서 복잡한 로직 처리
- 데이터 전송량 감소
- 원자적 연산

https://www.youtube.com/watch?v=92NizoBL4uA

## Redis 캐시로 사용하기
- 단순한 key-value 구조
- In-memory 데이터 저장소 -> 빠름

### 캐싱 전략

cache warming으로 미리 캐시에 올려둘 수 있음

- write around: 저장시 DB에만 저장
- write through: 저장시 캐시에도 저장

## 다양한 자료 구조
- String
- Bitmap
- List
- Hashes
- Set
- Sorted Set
- HyperLogLog
- Stream

### Counting
- String
- Bitmap
- HyperLogLog
  - 대용량 데이터를 카운팅할 때 적절

## Messaging
- List
  - Blocking 기능을 이용해 이벤트 큐로 사용
  - 캐시 키가 있을때만 데이터 저장 가능
- Stream
  - 로그를 저장하기 가장 적절
  - append-only

## 데이터 영구 저장
- AOF: 커맨드 그대로 저장
- RDB: 스냅샷

**백업은 필요하지만 어느 정도의 데이터 손실이 발생해도 괜찮은 경우**
- RDB 단독 사용
- redis.conf 파일에서 save 옵션을 적절히 사용

**장애 상황 직전까지의 모든 데이터가 보장되어야 할 경우**
- AOF 사용
- APPENDSYNC 옵션이 everysec인 경우 최대 1초 사이의 데이터 유실 가능

**제일 강력한 내구성이 필요한 경우**
RDB + AOF 사용

## 아키텍처
### Replication
Master + Replica  

**단순한 복제 연결**
- replicaof 커맨드를 이용해 간단하게 복제 연결
- 비동기식 복제
- HA 기능이 없으므로 장애 상황 시 수동 복구
  - replicaof no one
  - 애플리케이션에서 연결 정보 변경

### Sentinel
Master + Replica + Sentinel

**자동 페일오버 가능한 HA 구성**
- sentinel 노드가 다른 노드 감시
- 마스터가 비정상 상태일 때 자동으로 페일오버
- 연결 정보 변경 필요 없음
- sentinel 노드는 항상 3대 이상의 홀수 -> 과반수 이상의 sentinel이 동의해야 페일오버 진행

### Cluster
**스케일 아웃과 HA 구성**
- 키를 여러 노드에 자동으로 분할해서 저장(샤딩)
- 모든 노드가 서로를 감시하여, 마스터 비정상 상태일 때 자동 페일오버
- 최소 3대의 마스터 노드가 필요

## 장애를 막는 설정
**MAXMEMORY-POLICY = ALLKEYS-LRU**
- redis를 캐시로 사용할 때 expire time 설정 권장
- 키 관리 정책 
  - volatile-lru
  - allkeys-lru

## Cache Stampede
TTL을 너무 작게 설정한 경우
만료될때 DB에 부하가 늘어남

## Max Memory 값 설정
복제를 사용하면 메모리가 두배로 늘어나기때문에 주의

## Memory 관리
- used_memory: 논리적으로 redis가 사용하는 메모리
- used_memory_rss: os가 redis에 할당하기 위해 사용한 물리적 메모리 양
- 삭제되는 키가 많으면 fragmentation 증가
- CONFIG SET activedefrag yes: 단편화가 많이 발생했을때 사용

# 토스 증권 Kakfa 활용
- 각종 거래소 정보를 Kafka를 통해 실시간 전송.
- 서비스 간 통신이나 서비스 서버의 비동기 처리를 kafka 활용.
- 서버와 클라이언트의 모든 로그를 카프카로 입수.

Consumer가 용도에 맞는 저장소에 저장.

**Kafka는 안정적으로 운영돼야 한다.**
- 장애 발생 징후 감지
- 장애 시나리오를 갖춰놓기

## Kafka Cluster 내 일부 노드 장애

## IDC 전면 장애
치명적인 장애
-> IDC 이중화 구성으로 극복

**IDC가 한개라면?**
IDC에 장애가 발생하면 서비스가 중단됨.

증권사들은 이런 장애에 대응하기 위해 다른 IDC에 DR(Disaster Recovery) 시스템을 구성해야 함.

DR은 Active-Standby로 구성 -> Standby가 Active로 변경되어 서비스를 이어가는 방식.
그러나 Standby가 제대로 스탠바이되어있지 않을 수 있기에

토스에선 Active-Active로 구성.

Kafka는 Stateful한 시스템이라 이중화 구성이 어렵다.
어떻게 state 일관성을 유지해야 할까?

## Kafka Cluster의 이중화 구성
- 유저 트래픽이 IDC 두 곳으로 50%씩 분산되어 들어옴. (Active-Active)
- 각 IDC에 절반식 들어온 메시지는 전체 메시지를 가질 수 있도록 Kafka Connect를 이용해 상대 쪽 IDC로 실시간 메시지를 미러링.
- Offset Sync라는 데몬은 Consumer Group Offset을 실시간으로 양쪽 IDC간 Sync를 맞춰주고 있음.
- 컨슈머는 한 IDC에만 붙음(메시지 중복 소비를 막기 위해)

### Kafka Connect를 이용한 메시지 미러링
양방향 미러링.
이러한 잡이 라이브 중인 토픽 개수만큼 필요하기 때문에 사실상 수백 개가 된다. -> 운영 부담

Kafka Offset은 어디까지 읽었는지를 나타내는 값. 양쪽 클러스터가 모두 가지고 있어야 함. Offset Sync 작업으로 싱크를 맞춤.

## 운영
프로메테우스, ES로 모니터링

# Kafka Cluster의 이중화 구성 이후 장애 대응
최소한의 다운타임으로 서비스 재개.





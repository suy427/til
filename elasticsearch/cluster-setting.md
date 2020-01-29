# elasticsearch cluster set up

### 아니 ELK 왤케 업뎃 잦냐.. 죄다 적용할 수 없는 옛날자료만 나오고...ㅜㅜ(Official Document를 읽자..)

아마 5.x 이하 버전에서는 단일 인스턴스에서 노드 여러개를 구성할 수 있었던 것 같은데   
7.+ 부터 개별 인스턴스당 노드 한개만 구성할 수 있는 것 같다.   
대신 Cluster 이름을 맞춰서 묶어서 쓴다.
그래서 master, data, injest 등 여러 노드를 클러스터링 하려면 각 노드마다 개별 인스턴스를 따로 set-up해야한다.  

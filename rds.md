[목차](https://garamgaram5.github.io/blog)    
### [RDS] 데이터베이스 연결하기   
    
#### RDS란?    
관계형 데이터베이스 서비스    
    
MySQL, MariaDB 등 여러 관계형 데이터베이스 서비르르 AWS로부터 빌려서 사용하는 형태    
    
#### RDS를 사용하는 이유?    
- DB 배포에 용이    
로컬 환경(ex. 내 노트북)에서 개발할 때는 로컬 환경에 설치된 MySQL와 같은 DB를 연결해서 사용    
하지만 서버를 배포하고 나면 서버가 내 컴퓨터에 설치된 MySQL과 연결을 할 수가 없음     
DB도 외부 인터넷에서 접근할 수 있게 같이 배포가 필요함. AWS RDS라는 데이터베이스를 빌려서 사용    
- AWS RDS의 편리한 부가기능(자동 백업, 모니터링, 다중 AZ 등)    
    
#### EC2에 MySQL을 직접 설치해서 운영하면 안 되나요?    
EC2에 MySQL을 설치하면 별도의 RDS의 비용이 나오지 않기 때문에 비용을 절감 가능하나,    
백엔드 서버에 장애로 인해 EC2 컴퓨터가 죽을 경우, 애꿎은 MySQL도 같이 죽는 리스크..    
또한 RDS가 제공하는 부가적인 편리한 기능이 많아서 MySQL을 직접 설치해서 쓰지 않고 RDS를 쓰기도 함    
    
**현업에서는 EC2와 RDS를 분리해서 인프라를 구성하는 경우가 대부분**    
비용이 부담된다면, 하나의 EC2에 백엔드 서버와 MySQL을 직접 설치해 사용해도 무방    
    
#### EC2와 RDS 간의 아키텍처    
사용자 -> (ELD )-> EC2 -> RDS    
    
(실습)    
#### 1. RDS 생성    
리전 체크!    
RDS > 데이터베이스 생성    
> 표준 생성 > MySQL >     
템플릿: 프리티어 (프리티어라고 성능 문제 없음! 실제 현업의 운영에도 사용)    
설정> DB 인스턴스 식별자 (데이터 베이스 한 개의 이름) instagram-db (서비스 이름 포함)    
자격 증명 설정 > 마스터 사용자 이름 / 마스터 암호 (데이터베이스에 아무나 접근 못하도록 아이디와 비번) admin / 암호    
인스턴스 구성: 그대로 db.t3.micro    
스토리지: (하드디스크 같은 것. RDS도 DB 지만 결국 컴퓨터) 최근 버전 gp3 선택    
연결>     
VPC 등은 그대로    
**퍼블릭 엑세스**: 예 (나중에 배우고 아니오로 더욱 보안적으로 안전하게)    
나머지 디폴트    
    
예상 금액.. 프리티어는 이정도 안나옴    
    
데이터베이스 생성    
5~10분 정도 기다림    
    
#### 2. 보안 그룹 생성    
RDS도 하나의 컴퓨터 서비스!     
별도의 보안 그룹(울타리와 대문)가 필요    
EC2 > 네트워크 및 보안 > 보안 그룹 > 보안 그룹 생성    
보안 그룹 이름: instagram-db-security-group    
인바운드 규칙: MySQL 3306 Anywhere IPv4    
보안 그룹 생성    
    
RDS로 돌아가 생성한 데이터베이스 > 수정    
연결> 보안그룹 추가    
    
#### 3. 파라미터 그룹 추가    
파라미터 그룹이란? MySQL의 여러 옵션 설정값을 쉽게 설정 가능    
파라미터 그룹 > 파라미터 그룹 생성    
파라미터 그룹 패밀리: mysql8.0    
유형: (그대로) DB Parameter Group    
그룹 이름: instagram-db-parameter-group    
생성 후 편집 버튼 누름    
수정가능한 파라미터(394) 중 character 로 검색    
인코딩 = utf8mb4로 바꿈 (한글과 한자, 이모티콘을 포함한 모든 문자 지원)    
character_set_client    
character_set_connection    
character_set_database    
character_set_filesystem    
character_set_results    
character_set_server    
    
변경사항 저장    
collation으로 검색 (문자의 정렬 방식)    
utf8mb4_unicode_ci 값 입력    
collation_connection    
collation_server    
    
time_zone 검색    
Asia/Tokyo    
변경사항 저장    
    
RDS로 돌아감    
만든 DB 인스턴스 > 수정 >     
추가 구성>    
DB 파라미터 그룹: (디폴트)에서 변경     
> 계속 > 즉시적용 > DB 인스턴스 수정    
time_zone은 재부팅을 해야 함    
설정값 수정 완료 후에 재부팅 가능 > 재부팅    
    
#### 4. RDS에 접속하기    
RDS의 기본 세팅 끝남.     
데이터베이스를 만들면 가장 먼저 하는 것    
DB 관리 툴과 연결해서 DB가 잘 작동하는지 확인    
Workbench (옛날 느낌)    
Datagrip    
DBeaver <- 실습엔 이거 사용    
나는 A5 사용
    
**엔드포인트**    
포트 3306번    
server host: 엔드포인트 도메인으로 접속    
    
sys는 설정값의 데이터베이스라 그대로 두고    
instagram이라는 db 새로 만듦    
    
엔드포인트란? 특정 리소스(서버, DB 등)에 접근할 수 있도록 해주는 URL    
    
#### 5. Express 서버에 RDS 연결하기    
(생략)    
    
#### 6. 비용 나가지 않게 깔끔하게 RDS 종료하기    
db 인스턴스 > 삭제    
- 최종 스냅샷 생성 / 자동 백업 보존 체크해제!!! 해야지 확실히 비용 안나옴    
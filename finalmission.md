[목차](https://garamgaram5.github.io/blog)   
     
<최종 미션>    
가장 중요!!!    
스스로 처음부터 끝까지 실제로 구축하기!!!    
    
백엔드 서버란?    
웹 애플리케이션에서 사용자와 데이터베이스 간의 상호작용을 중개, 전체 시스템의 핵심 로직을 처리하는 주체    
주요 역할    
- 사용자 요청 처리    
- DB 관리    
- 비즈니스 로직 수행    
[참고](https://wntdev.tistory.com/62)    
    
웹 서버 (정적인 리소스) HTTP 프로토콜을 기반으로 클라이언트의 요청 받아, 정적인 콘텐츠 제공    
Apache, Nginx     
index.html    
-> EC2? S3?    
    
애플리케이션 서버 (동적인 콘텐츠) 웹서버 요청 <-> 데이터 베이스 조회 및 비즈니스 로직 수행 후 동적인 컨텐츠를 클라이언트에 반환    
Tomcat, Express.js    
-> S3, CloudFront    

데이터 베이스 서버    
데이터베이스에 데이터 저장 및 관리    
MySQL, PostgreSQL     
-> RDS    
    
보안 인프라    
    
------------    
<최종 미션>    
백엔드 서버 배포    
- DB 연동 로직    
- S3 활용해 파일 업로드 기능 구현    
RDS 사용    

(2024/1/12 이하 시도..)    
[깃헙에 올림] (https://github.com/garamgaram5/aws-practice)    
RDS: aws-practice-rds 생성    
EC2: 배포함. .env 수동 작성    
DATABASE_HOST=aws-practice-rds.cfndlqy6x7z8.ap-northeast-1.rds.amazonaws.com // 엔드포인트    
EC2에서 보안그룹 생성 / 파라미터 추가    
(A5 에서 접속 확인)    
    
EC2 생성 완료    
aws-practice-server    
보안 그룹 추가    
키페어 추가    
    
Tip     
RDS 데이터베이스 이름은 수정 가능    
보안 그룹 이름 못 바꿈    
    
S3: 버킷 만들기    
aws-practice-static-files    
버킷: 정책 추가, 리소스 포함     
 -> 불특정 다수 모두가 버킷에 객체 다운로드 가능    
IAM: aws-practice-server 사용자 추가 > 액세스 키 발급     
아무나가 아니라 aws-practice-server 사용자가 액세스키 가지고 S3 버킷에 접근 가능    
 -> 특정 사용자가 버킷에 객체 업로드 등 제어 가능    

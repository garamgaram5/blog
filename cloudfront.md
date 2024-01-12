[목차](https://garamgaram5.github.io/blog)    
### [S3, Cloudfront] 웹 페이지 배포하기    
    
#### 웹 서비스를 배포할 때 사용하는 S3, CloundFront    
S3는 파일 저장 서비스 이지 않나?    
S3의 부가 기능, 정적 웹사이트 호스팅 기능 !    
즉 웹서비스를 다른 사용자들도 쓸 수 있게 인터넷에 배포    
    
#### CloudFront란?    
컨텐츠(파일, 동영상 등) 빠르게 전송해주는 서비스, CDN 서비스    
CDN (Content Delivery Network)    
전세계 곳곳에 컨텐츠(파일, 동영상 등)의 복사본을 저장해 놓는 임시 저장소를 구축해    
오리진 서버가 아닌 가장 가까운 임시저장소로 부터 컨텐츠를 가져와서 속도가 훨씬 빨라짐    
이런 형태의 서비스가 CDN     
    
#### CloudFront를 왜 사용할까?    
S3만 사용해도 웹 서비스 배포 가능하나    
- CloudFront는 컨텐츠를 전송 받는 성능을 향상 시킴    
- HTTPS 적용하려면 CloudFront 사용 필요. S3는 HTTPS 적용 기능을 제공하지 않음    
- 보안을 한층 강화    
    
#### 현업에서 많이 사용할까?    
현업에서 웹 서비스를 배포할 때 S3와 CloudFront 많이 활용하니 잘 배워 두자.    
(S3, CloudFront 이외에 Netlify, Vercel, Cloudflare 등의 서비스 사용하기도 함)    
    
#### S3, CloudFront를 활용한 아키텍처 구성    
사용자 -> CloudFront -> S3    
CloudFront를 거쳐서 S3 로부터 컨테츠 다운로드 받음    
임시 저장되어 있다면 S3까지 안가고 CloudFront 에서 바로 보냄    
S3로 부터 직접 안 받음    
    
#### (실습) 1. S3 버킷 세팅    
버킷 새로 만들기    
instagram-web-page    
내용은 지난번과 동일    
    
#### 2. S3에 업로드하기 / 웹 호스팅 설정    
버킷 > 객체 > 업로드      
테스트용 index.html 만들어서 업로드    
> 객체 URL 누르면 잘 들어가 짐    
S3의 정적 웹사이트 호스팅 기능 사용!    
버킷 > 속성 > 정적 웹 사이트 호스팅 > 편집 에서 활성화    
> 인덱스 문서: index.html > 변경 사항 저장    
링크가 생김 - 웹사이트 배포 성공    
    
추가로 CloudFront 적용 (성능 향상, HTTPS 적용 위해)    
    
#### 3. CloudFront 생성    
CloudFront > 배포 생성    
원본 도메인: (실제 원본 파일이 어딨냐) 만든 S3 버킷 선택    
웹 사이트 엔드포인트 사용 버튼 클릭     
    
기본 캐시동작> 뷰어 프로토콜 정책    
**Redirect HTTP to HTTPS** 선택    
    
웹 애플리케이션 방화벽(WAF)     
보안 보호 비활성화 선택 (보안에 딱히 위험한 건 아님)    
    
설정
가격 분류    
북미, 유럽, 아시아, 중동 및 아프리카에서 사용 (아시아권 사용자만 대상)    
    
기본값 루트 객체- 선택사항    
index.html
    
배포 생성
완료되면    
    
배포 도메인 이름 으로 접속 가능!    
    
CloudFront 에 도메인 연결 + HTTPS 적용    
    
#### 4. 도메인 연결, HTTPS 적용    
(CloudFront 에 HTTPS 적용 위해) ACM 에서 인증서 먼저 발급    
단, CloudFront 의 인증서는 리전: 버지니아 북부 로 해야하는 룰    
ACM > 요청 > 도메인 입력(instagram.ahnlab.tokyo)    
(검증방법: DNS 검증)    
    
Route53에서 레코드 생성    
-> Route53에 CNAME 의 레코드가 생성 됨.    
시간지나면 ACM에서 CloudFront 의 인증서의 상태: 성공을 바뀜    
    
CloudFront > 일반 > 설정. 편집 >    
대체 도메인 이름(CNAME) > 항목 추가    
도메인 입력(instagram.ahnlab.tokyo)    
    
사용자 정의 SSL 인증서    
(인증서는 반드시 미국 동부(버지니아 북부) 리전(us-east-1)에 있어야 합니다)    
인증서 적용 후 > 변경사항 저장    
    
Route53에서 도메인 연결 필요!    
Route 53 > 호스팅 영역 > 레코드 생성    
A 주소, 별칭: CloudFront 배포에 대한 별칭 선택    
배포 선택: 선택    
    
#### 5. 비용 나가지 않게 S3, CloudFront 깔끔하게 종료    
- S3: 버킷 삭제 (객체 삭제 먼저)    
- CloudFront 삭제 (비활성화 > 사용중지 까지 기다린 후 삭제 버튼 눌러서 삭제)    
(그 외)    
- ACM: 인증서 삭제 (버지니아 북부!!)    
- Route53: 레코드 삭제    
    
    
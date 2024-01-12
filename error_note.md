[목차](https://garamgaram5.github.io/blog)   
   
### ec2 server refused our key   
[현상]   
Putty로 생성한 EC2 인스턴스에 접속이 안됨   
에러 메세지: server refused our key   
ppk (키페어) 는 정상 발급 완료   
   
[원인]   
EC2 인스턴스 시작 때, OS를 Amazon Linux 가 아닌 ubuntu 선택했는데 로그인 아이디 잘못 사용   
```markdown   
login as: ec2-user   
```   
   
[해결책]   
EC2 인스턴스에 연결할 때 AMI에 잘못된 사용자 이름을 사용하고 있습니다.    
일반 사용자 이름은 ec2-user, ubuntu, centos, root 또는 관리자입니다.   
```markdown   
login as: ubuntu   
```   
   
(참고)   
[SSH を使用して EC2 インスタンスに接続しようとすると、「サーバーがキーを拒否しました」というエラーが表示されるのはなぜですか?](https://repost.aws/ja/knowledge-center/ec2-server-refused-our-key)      
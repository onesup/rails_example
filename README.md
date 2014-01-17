# Capistrano + Rails4 샘플 프로젝트

## ssh-key

### 1. 서버 ssh 로그인

로컬 컴퓨터의 key를 서버에 전송해서 등록해야함

    ssh-copy-id -i deployer@host

-i 하면 기본으로 ~/.ssh/id_rsa.pub를 사용함
만약 없다면 ssh-keygen 으로 생성

    ssh-keygen -t rsa -C "your_email@example.com"

이후 패스워드를 입력하면 서버의 deployer 계정의 ~/.ssh/authorized_keys에 추가된다.


참고:  https://help.github.com/articles/generating-ssh-keys



### 2. 서버 agent forwarding

##### 1) 로컬에서

~/.ssh/config에 ForwardAgent 세팅

    Host *
        ForwardAgent yes

\* 말고 호스트나 IP를 하나하나 적어주는게 원래 맞음


/etc/ssh_config가 override하고 있는지 체크
희안하게 /etc/ssh_config가 더 늦게 로딩되어 사용자 설정을 override한다.

    cat /etc/ssh_config | grep ForwardAgent


ssh-agent에 identity가 설정되어있어야함

    ssh-add -L
    ssh-add

ssh-add하면 디폴트로 현재 유저의 id_rsa.pub가 등록된다.
이후 서버에서 ssh로 접속할때 ssh-agent에 등록된 identity를 사용하게 된다.



##### 2) 서버에서

별 설정 필요없는데 혹시나 /etc/ssh/sshd_config에 AllowAgentForwarding가 설정되어있는지 체크
AllowAgentForwarding는 디폴트로 yes이기 때문에 아예 AllowAgentForwarding가 없는게 맞음

    cat /etc/ssh/sshd_config | grep AllowAgentForwarding



##### 3) ssh-agent 체크

로컬과 서버에서 각각

    echo "$SSH_AUTH_SOCK"

에 대해 값이 들어있어야하고

    ssh -T git@github.com

가 정상동작해야함



참고:  https://help.github.com/articles/using-ssh-agent-forwarding




## capistrano

### 1. 소스코드 배포

ssh 설정이 완료되었다면 서버에 deploy 바로 가능

    cap production deploy


production, staging 두개의 스테이지를 디폴트로 생성함. 일반 테스트용은 staging으로 서비스용은 production으로 사용하면 될 듯.









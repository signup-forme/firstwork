# Workstation

## 1. 프로젝트 개요 (Project Overview)
본 프로젝트는 Linux, Git, Docker를 사용하기에 앞서 기본적인 명령어를 실행해 보고 환경을 구축 및 검증하는 것을 목표로 함.

---

## 2. 실행 환경 (Environment)
* **OS / Subsystem**: Windows 11 & WSL2 (Ubuntu 26.04 LTS)
* **Shell / Terminal**: Bash / Windows Terminal
* **Container Engine**: Docker Engine v29.7.1
* **Version Control**: Git v2.55 & GitHub

---

## 3. 수행 항목 체크리스트 (Checklist)
- [x] 터미널 기본 조작(파일/폴더 생성, 이동, 복사, 삭제 등)
- [x] 파일 권한 변경(chmod)
- [x] Docker
- [x] Docker 컨테이너
- [x] Docker 이미지
- [x] Git과 GitHub
- [x] 기타

---

## 4. 터미널 실습, 개념 정리 및 트러블슈팅 통합 로그

### 1. 터미널 기본 조작

<img width="3492" height="4288" alt="image" src="https://github.com/user-attachments/assets/ce918443-c8e6-4a67-bca8-fa5400b57077" />

* 위치 : pwd → 현재 경로 출력
* 조회 : ls -la → 숨김 파일 포함 전체 파일 목록 조회(LiSt, -l : 상세 정보 출력, -a : All을 의미하며 숨김 파일을 포함한 모든 항목 출력)
* 생성 : mkdir [폴더 이름] → 디렉토리 생성(MaKe DIRectory)
* 생성 : touch [파일 이름] → 빈 파일 생성
* 생성/작성 : echo "문자열" > [파일 이름] → echo로 출력한 문자열을 >(리다이렉션 - 다른 파일로 이동/호출) 인자로 파일에 저장
* 이동 : cd [경로] → 경로로 이동(cd ..은 상위 이동)
* 변경/이동 : mv [대상] [변경 이름/목적지] → 대상 파일이나 폴더를 이름을 변경하거나 목적지로 이동(맨 뒤에 / 필요)
* 복사 : cp [원본] [복사본] → 원본을 복사본으로 복사(폴더는 cp -r [원본] [복사본])
* 삭제 : rm [파일 이름] → 파일 삭제
* 삭제 : rm -rf [폴더 이름] → 폴더 및 하위 파일 전체 삭제(-r : 하위 디렉토리 재귀 삭제, -f : 강제 삭제)

### 2. 파일 권한 변경

<img width="3056" height="3296" alt="image" src="https://github.com/user-attachments/assets/ff032021-c0cd-4384-b960-acee55dc92c0" />

* 1번째 글자 : 파일 유형(-는 파일, d는 디렉토리)
* 2번째 글자 이후 : 3글자씩 끊어서 계산
  * 2번째 ~ 4번째 : 소유자/관리자 권한
  * 5번째 ~ 7번째 : 그룹 권한
  * 8번째 ~ 10번째 : 기타 사용자 권한
* 권한 기호와 숫자값
  * r : 읽기(Read) → 4
  * w : 쓰기(Write) → 2
  * x : 실행하기(eXecute) → 1
* chmod [숫자] [파일 이름]
* chmod -R [숫자] [폴더 이름] : Recursive에서 따왔으며, 폴더 내 파일이나 하위 폴더 전체의 권한을 변경

### 3. Docker

<img width="3640" height="2936" alt="image" src="https://github.com/user-attachments/assets/a92c431f-6743-4ec8-a4c4-33000eb604a5" />

### 4. Docker 컨테이너

<img width="3680" height="4804" alt="image" src="https://github.com/user-attachments/assets/65e47254-ffe3-4054-886f-472a005fea5c" />

* docker run -d --name [이름] nginx : Nginx 웹 서버 컨테이너를 background(-d)에서 [이름]이라는 이름(--name)으로 실행
* docker ps -a : 현재 존재하는 전체 컨테이너(실행 중 + 정지 상태) 목록 확인
* docker container prune : 정지 상태의 컨테이너 전체 삭제
* docker stop [이름] : 실행 중인 [이름] 컨테이너 정지
* docker rm [이름] : 정지 상태인 [이름] 컨테이너 삭제

#### 컨테이너의 삭제와 데이터 보존 ####
컨테이너는 데이터를 가상 파일 시스템에 생성하는데, 이 데이터는 컨테이너와 운명을 같이 함. 즉, 컨테이너가 삭제되면 그 내부에 저장되어 있던 파일이나 데이터도 함께 파기.
해결 방법 : Docker에서 제공하는 영구 저장 공간인 Volume에 파일과 데이터를 보관.

<img width="3680" height="4264" alt="image" src="https://github.com/user-attachments/assets/a05e6371-2e50-4093-92f2-cb537578898b" />

* Docker는 이미 생성되어 실행 중인 컨테이너에 볼륨을 연결하는 것은 지원하지 않으므로, 기존 컨테이너를 삭제한 후 볼륨을 생성하여 연결
* docker volume create [이름] : 볼륨 [이름] 생성
* docker run -d --name [컨테이너 이름] -p 80:80 -v [볼륨 이름]:[컨테이너 내부 경로] [이미지 종류]
  * nginx 웹서버를 사용 중일 경우 → docker run -d --name [컨테이너 이름] -p 80:80 -v [볼륨 이름]:/usr/share/nginx/html nginx
  * docker attach [컨테이너 이름] : 특정 컨테이너나 서브 프로세스를 메인 프로세스에 부착하여 메인 프로세스와 함께 실행되고 함께 종료
  * docker exec [컨테이너 이름] : 메인 프로세스에서 실행하기 곤란할 때 비슷한 동작을 하는 새로운 별개의 프로세스를 생성
  * sh : SHell을 의미하며, 사용자의 명령을 받아 시스템에 전달해주는 프로그램
  * -c : 뒤따라오는 큰따옴표로 묶인 문자열 전체를 하나의 명령어로 보고 실행
  * cat : Concatenate를 의미하며, 파일 안에 적힌 글자를 화면에 출력

### 5. Docker 이미지

<img width="100%" alt="image" src="https://github.com/user-attachments/assets/f58ccd7f-e97d-4f51-9717-ba402e6b056d" />

* docker images : 현재 존재하는 전체 이미지 목록 확인
* docker image prune : 더 이상 사용하지 않는 이미지 전체 삭제
* docker build -t [이름]:1.0 . : 현재 위치의 Dockerfile을 읽어서 -t로 지정된 태그 1.0이 붙은 [이름]이라는 새로운 Docker 이미지를 생성(맨 뒤의 .은 현재 위치의 디렉토리를 의미)
* docker run -d -p 8080:80 --name [컨테이너 이름] [이미지 이름]:1.0 : [이미지 이름]을 기반으로 -d를 통해 백그라운드에서 [컨테이너 이름]이라는 컨테이너를 --name으로 지정해 -p 8080:80으로 포트포워딩을 실행해 컴퓨터의 포트(8080)와 컨테이너의 포트(80)를 서로 연결

#### 이미지와 컨테이너의 차이 ####
이미지 : Dockerfile에 정의된 명령어를 바탕으로 만들어지는 정적 파일, 읽기 전용 파일이므로 실제로 동작할 수 없음, 한 번 만들어지면 수정이 불가능해 실수했거나 변경이 필요하다면 다시 만들어야 함
컨테이너 : 이미지를 바탕으로 실행과 동시에 생성되는 동적 개체, 이미지를 기반으로 메모리와 CPU 자원을 할당받아 실제로 동작, 실행 도중에도 상태 변화(생성/수정/삭제)를 주는 것이 가능(단, 데이터 보존을 위해서는 Volume 필요)

→ 쉽게 비유하면 이미지는 설계도, 컨테이너는 그 설계도를 바탕으로 만든 실제 건축물

#### 내부 포트 접근 불가 ####
Docker 컨테이너는 가상 네트워크라는 격리된 환경을 가지고 있으므로 컨테이너 내부의 포트는 컨테이너 자체의 가상 주소에만 열려 있다. 따라서 호스트 외부나 브라우저에서는 이곳에 직접 접근이 불가능하다. 이 때문에 호스트의 포트로 들어오려는 요청을 컨테이너 내부 포트로 전달해주는 포트 포워딩이 필요하다. 이렇게 하면 호스트 주소를 거쳐 컨테이너 내부로 접근할 수 있다.

#### 호스트 포트 점유로 인한 매핑 실패 ####
1) 해당 호스트 포트를 점유하고 있는 프로세스를 강제로 종료시킨다.
2) 해당 호스트 포트가 아닌 다른 포트로 접근하여 실행한다.(8081:80 등)

### 6. Git과 GitHub ###

<img width="3680" height="4352" alt="image" src="https://github.com/user-attachments/assets/0852a94e-402e-4721-8a7e-d1670f8300a3" />

* git config --global -l : 내 컴퓨터에 글로벌(--global)로 설정된 사용자 이름과 이메일을 출력(-l은 목록(List))
* ssh -T git@github.com : 내 컴퓨터에 등록된 SSH 키를 이용해 GitHub 서버와 연결이 성공적으로 이루어졌는지 확인
  * SSH는 Secure SHell로, 네트워크를 통해 원격 컴퓨터나 서버에 안전하게 접속하여 명령어를 실행할 수 있게 해주는 보안 프로토콜, 모든 통신 데이터를 암호화 처리하여 해커나 도청자가 중간에 낚아채도 내용을 알 수 없음.
  * 성공 시 환영 메시지 출력
  * 실패 시 권한 거절 메시지 출력
* ls -la ~/.ssh : 디렉토리 내부의 .ssh 디렉토리에 존재하는 전체 파일 출력
* ssh-keygen -t ed25519 -C "이메일 주소" : -C로 키를 식별할 라벨 역할을 할 이메일을 지정한 후, 보안성이 우수한 새로운 SSH 키 생성
  * ed25519 : 최신식 암호화 기술
* cat ~/.ssh/id_25519.pub : 생성된 SSH 키 중 공개키를 출력, 이 키를 복사해야 GitHub에 등록하여 컴퓨터와 통신이 가능

#### Git과 GitHub의 차이 ####
Git :  내 컴퓨터 안에서 코드의 변경 이력을 기록하고 관리하는 소프트웨어 프로그램
GitHub : 그 기록들을 인터넷에 올려 타인과 공유하고 협업하게 해주는 웹 사이트

쉽게 비유하면
   - Git : 내 컴퓨터 안에 설치된 카메라 → 코드의 순간순간을 사진 찍듯 기록하여 하드디스크에 저장
   - GitHub : 그 카메라로 찍은 사진을 올리는 SNS → 인터넷에 공유하여 사람들과 공유, 협업

### 7. 기타 ###

#### 절대 경로와 상대 경로 ####

절대 경로 : 파일의 최상위 기준점으로부터 목적지까지의 전체 주소
상대 경로 : 내가 위치한 디렉토리를 기준으로 목적지까지의 위치

쉽게 비유하면
   - 절대 경로 : 조상으로 올라가는 족보
   - 상대 경로 : 나를 기준으로 한 촌수

#### 트러블슈팅 ####

[Trouble 1: 기본 Welcome to Nginx 페이지 출력]
- 문제: Dockerfile 및 HTML 파일을 수정한 후 웹사이트 접속 시 여전히 Nginx 기본 페이지가 출력되는 현상 발생.
- 원인 가설: 웹 브라우저가 이전 8080 포트 접속 당시의 Nginx 기본 페이지 캐시를 보관하고 있어서 발생함.
- 확인 및 해결: 브라우저에서 F12(개발자 도구)를 열어 강력 새로고침 및 캐시 비우기를 진행하여 수정한 custom HTML이 정상 출력됨을 확인.

[Trouble 2: Git Push 인증 오류 (Password Authentication Error)]
- 문제: git push 실행 시 계정 비밀번호 인증 실패 (remote: Invalid username or token).
- 원인 가설: GitHub 보안 정책으로 비밀번호 인증 중단.
- 확인 및 해결: Developer Settings에서 Personal Access Token(classic) 발급 후 비밀번호 대신 입력하여 푸시 성공.

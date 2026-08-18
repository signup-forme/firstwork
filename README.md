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
- [x] 파일 권한 변경
- [x] Docker 설치 및 기본 점검 (`docker --version`, `docker info`)
- [x] 컨테이너 실행 실습 (`hello-world`, `ubuntu` 진입 및 종료 관찰)
- [x] Dockerfile 기반 Nginx 커스텀 웹 서버 이미지 빌드 및 포트 매핑 실행
- [x] 바인드 마운트(Bind Mount)를 통한 실시간 변경 사항 반영 검증
- [x] Docker 볼륨(Volume)을 통한 데이터 영속성(Persistence) 검증
- [x] Git 사용자 정보 설정 및 GitHub 원격 저장소 연동/푸시

---

## 4. 터미널 실습, 개념 정리 및 트러블슈팅 통합 로그

### 1. 터미널 기본 조작

<img width="3492" height="4288" alt="image" src="https://github.com/user-attachments/assets/ce918443-c8e6-4a67-bca8-fa5400b57077" />

* 위치 : pwd → 현재 경로 출력
* 조회 : ls -la → 숨김 파일 포함 전체 파일 목록 조
* 생성 : mkdir [폴더 이름] → 디렉토리 생성(MaKe DIRectory)
* 생성 : touch [파일 이름] → 빈 파일 생성
* 생성/작성 : echo "문자열" > [파일 이름] → echo로 출력한 문자열을 >(리다이렉션 - 다른 파일로 이동/호출) 인자로 파일에 저장
* 이동 : cd [경로] → 경로로 이동(cd ..은 상위 이동)
* 변경/이동 : mv [대상] [변경 이름/목적지] → 대상 파일이나 폴더를 이름을 변경하거나 목적지로 이동(맨 뒤에 / 필요)
* 복사 : cp [원본] [복사본] → 원본을 복사본으로 복사(폴더는 cp -r [원본] [복사본])
* 삭제 : rm [파일 이름] → 파일 삭제
* 삭제 : rm -rf [폴더 이름] → 폴더 및 하위 파일 전체 삭제

### 2. 파일 권한 변경



* 1번째 글자 : 파일 유형(-는 파일, d는 디렉토리)
* 2번째 글자 이후 : 3글자씩 끊어서 계산
  * 2번째 ~ 4번째 : 소유자/관리자 권한
  * 5번째 ~ 7번째 : 그룹 권한
  * 8번째 ~ 10번째 : 기타 사용자 권한
* 권한 기호와 숫자값
  * r : 읽기(Read) → 4
  * w : 쓰기(Write) → 2
  * x : 실행하기(eXecute) → 1

# ==========================================
# 3. Docker 설치 점검 및 기본 컨테이너 실행
# ==========================================
$ docker --version
Docker version 29.7.1, build 2ae97ce

$ docker info
Client: Docker Engine - Community
 Context: default

# hello-world 실행
$ docker run --rm hello-world
Hello from Docker!

# ubuntu 컨테이너 진입 및 테스트
$ docker run -it --name ubuntu-test ubuntu bash
user@DESKTOP:/# ls -la
user@DESKTOP:/# exit


# ==========================================
# 4. Dockerfile 이미지 빌드 및 포트 매핑
# ==========================================
# Dockerfile 작성 후 이미지 빌드
$ docker build -t my-web-server:1.0 .

# 포트 매핑 실행 (호스트 8080 -> 컨테이너 80)
$ docker run -d -p 8080:80 --name web-container my-web-server:1.0

# 로컬 접속 검증
$ curl http://localhost:8080


# ==========================================
# 5. 바인드 마운트 및 볼륨 영속성 검증
# ==========================================
# 바인드 마운트 실행 (실시간 파일 반영)
$docker run -d -p 8081:80 -v$(pwd):/usr/share/nginx/html --name bind-test my-web-server:1.0

# Docker 볼륨 생성 및 데이터 영속성 검증
$ docker volume create mydata

# 첫 번째 컨테이너에 연결 후 데이터 작성
$docker run -d --name vol-test1 -v mydata:/data ubuntu sleep infinity$ docker exec -it vol-test1 bash -c "echo 'Persistent Data Test' > /data/testcase.txt"

# 컨테이너 삭제
$ docker rm -f vol-test1

# 두 번째 새 컨테이너에 연결하여 데이터 유지 검증
$docker run -d --name vol-test2 -v mydata:/data ubuntu sleep infinity$ docker exec -it vol-test2 bash -c "cat /data/testcase.txt"
Persistent Data Test


# ==========================================
# 6. 핵심 개념 정리 (Learning Points)
# ==========================================
1. 절대 경로 vs 상대 경로
   - 절대 경로: 최상위 루트(/) 기준 전체 경로 (예: /home/user/storage/web_server)
   - 상대 경로: 현재 위치(.) 기준 경로 (예: ./Dockerfile)

2. 파일 권한 규칙 (r/w/x)
   - 755 (rwxr-xr-x): 소유자 전체 권한, 그룹/기타 사용자는 읽기 및 실행 가능
   - 644 (rw-r--r--): 소유자 읽기/쓰기, 그룹/기타 사용자는 읽기 전용

3. 포트 매핑 필요성
   - 격리된 컨테이너 내부 네트워크 포트를 호스트 OS 포트와 매핑하여 외부 접근 허용

4. Git vs GitHub
   - Git: 로컬 분산 버전 관리 시스템
   - GitHub: 원격 소스코드 저장 및 팀 협업 클라우드 플랫폼


# ==========================================
# 7. 트러블슈팅 (Troubleshooting)
# ==========================================
[Trouble 1: 기본 Welcome to Nginx 페이지 출력]
- 문제: Dockerfile 및 HTML 파일을 수정한 후 웹사이트 접속 시 여전히 Nginx 기본 페이지가 출력되는 현상 발생.
- 원인 가설: 웹 브라우저가 이전 8080 포트 접속 당시의 Nginx 기본 페이지 캐시를 보관하고 있어서 발생함.
- 확인 및 해결: 브라우저에서 F12(개발자 도구)를 열어 강력 새로고침 및 캐시 비우기를 진행하여 수정한 custom HTML이 정상 출력됨을 확인.

[Trouble 2: Windows CMD 환경 및 WSL 디렉터리 경로 혼선]
- 문제: CMD 환경에서 Linux 명령어 사용 및 default WSL 연결 경로 차이로 인해 Dockerfile 미발견.
- 원인 가설: CMD의 PowerShell/Linux 구문 미지원 및 Docker Desktop default WSL 경로 설정 문제.
- 확인 및 해결: WSL Ubuntu 환경으로 진입하여 실제 작업 경로(/home/user/storage/web_server)로 이동 후 해결.

[Trouble 3: Git Push 인증 오류 (Password Authentication Error)]
- 문제: git push 실행 시 계정 비밀번호 인증 실패 (remote: Invalid username or token).
- 원인 가설: GitHub 보안 정책으로 CLI 패스워드 인증 중단 (PAT 필수).
- 확인 및 해결: Developer Settings에서 Personal Access Token(classic) 발급 후 Password 대신 입력하여 푸시 성공.

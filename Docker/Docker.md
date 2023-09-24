#Docker 

# Docker
## 사용 이유

1. 인스톨러 내려받기
2. 인스톨러 실행
3. 설치 완료 
4. 
의 순서를 거치게 되는데 버전대가 다르거나 의존성이 다른 프로그램들을 다운 받으면 

1. 인스톨러 내려받기
2. 인스톨러 실행
3. **에러** 가 발생하는 경우가 있다.


## 도커란 무엇인가?

### 컨테이너
컨테이너를 사용하면 응용프로그램을 더 쉽게 만들고 배포하고 실행할 수 있도록 설게된 도구.
컨테이너 기반의 오픈소스 가상화 플랫폼이며 생태계이다.
#### 서버에서의 컨테이너
![[Pasted image 20230921151257.png]]
이런 식으로 다양한 프로그램, 실행환경을 컨테이너로 추상화하고 동일한 인터페이스를 제공하여 프로그램의 배포 및 관리를 단순하게 해준다.
AWS, Azure, Google Cloud 등 어디에서든 실행 가능하게 해준다.



## 항상 도커를 사용할 때는
1. 먼저 도커 CLI에 커맨드를 입력한다.
2. 그러면 도커 서버 (Docker Daemon)이 그 커맨드를 받아서 그것에 따라 이미지를 생성하든 컨테이너를 실행하든 모든 작업을 하게 된다.

```cli
docker run hello-world 
```

1. 도커 클라이언트에 커맨드를 입력하니 클라이언트에서 도커 서버로 요청을 보낸다.
2. 서버에서 hello-world 라는 이미지가 이미 로컬에 cache되어 있는지 확인
3. 현재는 없기에 *Unable to find image ~* 라는 문구가 2번째 줄에 표시
4. 그러면 Docker Hub라는 이미지가 저장되어 있는 곳에 가서 그 이미지를 가져오고, 로컬에 cache로 보관한다.
5. 그 후 이제는 이미지가 있으니 그 이미지를 이용해서 컨테이너를 생성한다.
6. 그리고 이미지로 생성된 컨테이너는 이미지에서 받은 설정이나 조건에 따라 프로그램을 실행한다.


### 시작하기 전에 기억해야 할 것
- [1] 이미지 안에는 프로그램을 실행하기 위한 **필요한 모든 것**을 포함하고 있다.

## 이미지
### **필요한 모든 것** 은 무엇인가요?
1. 컨테이너가 시작될 때 실행되는 명령어 `ex) run kakaotalk`
2. 파일 스냅샷 ex) 컨테이너에서 카카오톡을 실행하고 싶다면 카카오톡 파일 스냅샷 
	- [*] 스냅샷: 디렉토리나 파일을 카피한 것

![[Pasted image 20230921162805.png]]

(ㅋㅋ 처음에 이미지가 진짜 Img인 줄 알았다..푸핳 스냅샷+명령어의 조합이었다고한다고한닥~!)

### 이미지로 컨테이너 만드는 순서
1. Docker 클라이언트에 `docker run <이미지>`
2. 도커 이미지에 있는 파일 스냅샷을 컨테이너 하드 디스크에 옮겨 줍니다.
 ![[Pasted image 20230921164736.png]]
 


#### 중간 정리
이미지? 
	* 컨테이너를 만들기 위해 필요한 설정이나 종속성들을 갖고 있는 소프트웨어 패키지
	* Dockerhub에 이미 다른 사람들이 만들어 놓은 것을 이용할 수도 있으며, 직접 도커 이미지를 만들어서 사용할 수도 있다.
		`docker create <이미지이름>`

#### 도커 이미지 생성 순서
![[Pasted image 20230921165726.png]]
---
### 도커 파일 만들기
도커 이미지를 만들기 위한 설정 파일.
컨테이너의 행동을 해야하는지에 대한 설정 정의.

#### 도커 파일 만드는 순서 (도커 이미지가 필요한 것이 무엇인지 생각하기)
1. 베이스 이미지 명시 (파일 스냅샷에 해당)
2. 추가적으로 필요한 파일을 다운 받기 위한 몇 가지 명령어를 명시 (파일 스냅샷에 해당)
3. 컨테이너 시작 시 실행될 명령어를 명시 (시작 시 실행될 명령어에 해당)

#### 베이스 이미지?
도커 이미지는 레이어들로 되어져 있다. 그 중 베이스 이미지는 이 이미지의 기반이 된다.
![[Pasted image 20230921170119.png]]



### 도커 파일 만들기 부터 도커 클라이언트에 전달 -> 도커 서버 인식까지
#### 순서
1. 아무거나 원하는 폴더에 파일 생성
	![[Pasted image 20230921170602.png]]
	
2. vscode로 켜기
3. vscode 내부에서 파일 하나 생성. 이름은 dockerfile
4. 그 안에 어떻게 진행해 나갈지 기본적인 토대 명시.
	![[Pasted image 20230921170649.png]]
	* **FROM** 이미지 생성 시 기반이 되는 이미지 레이어
		- baseImage
		- alpine

	* **RUN** 도커 이미지가 생성되기 전에 수행할 쉘 명령어
		- alpine 만으로 출력 가능하므로 RUN 생략 가능

	* **CMD** 컨테이너가 시작되었을 때 실행할 실행 파일 또는 쉘 스크립트
			

5. **docker build ./**
	![[Pasted image 20230921172244.png]]
	![[Pasted image 20230921173311.png]]
6. 실행
	**docker run 2cdf** // sha256: 뒤에 몇 자리만 써주면 실행 됨.
	![[Pasted image 20230921173349.png]]


#### 리액트를 위한 도커 파일 작성하기
![[Pasted image 20230921174031.png]]

#### Dockerfile.dev
실제로는 Dockerfile을 개발단계를 위한 것과
실제 배포 후를 위한 것을 따로 작성하는 것이 좋다.

개발단계를 위해서 Dockerfile.dev를 아래와 같이 작성할 수 있다.
![[Pasted image 20230921191031.png]]

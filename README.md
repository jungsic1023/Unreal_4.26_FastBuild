# FastBuild
설치환경 Visual Studio 2019 pro
Unreal Engine - 4.26
Fast Build v1.04
Windows SDK - 10.0.22000.0

언리얼 빌드를 빠르게 하기 위하여 로컬 pc와 remote pc를 연결하여 분산 빌드 하는 시스템


# 기본 설정

![1648799929](https://user-images.githubusercontent.com/62869017/161221116-df056c97-b30a-4537-88a3-64ba4a4bc343.png)


빌드를 할 호스트 pc와 cpu를 빌려줄 remote pc 둘 다 네트워크 공유센터에서 허용해주며, 암호 보호 공유 또한 꺼준다.

![1648799957](https://user-images.githubusercontent.com/62869017/161221370-0646f9ab-f68b-4497-9885-8c2e695b525f.png)

Host와 Remote를 연결할 공유폴더를 생성 후 공유해주며 공유할 사람을 추가한 후 읽기/쓰기 권한을 준다. (여기서는 폴더명 : FASTBUILDShared)

빨간 줄이 그어진곳은 호스트pc의 ip주소이거나 이름이다. 공유된 폴더를 우클릭하여 공유파트를 보면 나온다.

![1648799781](https://user-images.githubusercontent.com/62869017/161221459-3b1f4383-55a3-416d-9b95-3d216b94740f.png)


환경변수에 FASTBUILD_BROKERAGE_PATH를 추가해준다. 공유된 폴더경로이며 로컬 경로가 아닌 네트워크 경로이다. 
EX) \\PC_IP\FASTBUILDShared
FASTBUILDShared폴더 밑에 Cache 폴더를 생성하고 환경변수에 FASTBUILD_CACHE_PATH로 추가해준다.

이작업은 host와 remote pc 둘다 해줘야하며 두 pc는 이 등록된 경로가 같아야한다. 

EX) Host pc - FASTBUILD_BROKERAGE_PATH : \\PC_IP\FASTBUILDShared
    Remote pc - FASTBUILD_BROKERAGE_PATH : \\PC_IP\FASTBUILDShared

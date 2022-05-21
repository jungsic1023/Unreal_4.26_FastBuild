# FastBuild

## 목차
### 1. 기본 설정
### 2. Fast Build 수정
### 3. Fast Build 활성화
### 4. Fast Build 캐싱기능
### 5. Fastbuild moniter
### 6. 주의사항











설치환경 Visual Studio 2019 pro

Unreal Engine - 4.26

Fast Build v1.04

Windows SDK - 10.0.22000.0




언리얼 빌드를 빠르게 하기 위하여 로컬 pc와 remote pc를 연결하여 분산 빌드 하는 시스템

windows와 mac은 서로연결불가능하다


# 기본 설정

![1648799929](https://user-images.githubusercontent.com/62869017/161221116-df056c97-b30a-4537-88a3-64ba4a4bc343.png)


빌드를 할 호스트 pc와 cpu를 빌려줄 remote pc 둘 다 네트워크 공유센터에서 허용해주며, 암호 보호 공유 또한 꺼준다.

![1648799957](https://user-images.githubusercontent.com/62869017/161221370-0646f9ab-f68b-4497-9885-8c2e695b525f.png)

Host와 Remote를 연결할 공유폴더를 생성 후 공유해주며 공유할 사람을 추가한 후 읽기/쓰기 권한을 준다. (여기서는 폴더명 : FASTBUILDShared)

빨간 줄이 그어진곳은 호스트pc의 ip주소이거나 이름이다. 공유된 폴더를 우클릭하여 공유파트를 보면 나온다.


![1648799781](https://user-images.githubusercontent.com/62869017/161226802-7a38c084-2d5b-4906-a3a8-8d577b974313.png)


환경변수에 FASTBUILD_BROKERAGE_PATH를 추가해준다. 공유된 폴더경로이며 로컬 경로가 아닌 네트워크 경로이다. 

EX) \\\PC_IP\FASTBUILDShared

FASTBUILDShared폴더 밑에 Cache 폴더를 생성하고 환경변수에 FASTBUILD_CACHE_PATH로 추가해준다.

이작업은 host와 remote pc 둘다 해줘야하며 두 pc는 이 등록된 경로가 같아야한다. 


EX) Host pc - FASTBUILD_BROKERAGE_PATH : \\\PC_IP\FASTBUILDShared

   Remote pc - FASTBUILD_BROKERAGE_PATH : \\\PC_IP\FASTBUILDShared

FASTBuild-Windows-x64-v1.04또한 환경변수 PATH에 등록하여 어디서든 fbuild를 사용할 수 있도록한다. 등록 후 cmd에 fbuild -version 입력 시 버전이 나오면 된것이다.



# Fast Build 수정

이제 Fast Build를 사용하기 위해서는 약간의 수정이 필요하다.
fastbuild-ue4.26.2-1.04-ue4\External\SDK\VisualStudio에서 자신이 사용중인 visual studio 버전에 따라 수정할 것이 다르다.
![화면 캡처 2022-04-01 171235](https://user-images.githubusercontent.com/62869017/161223282-4b0ac22c-a229-4cb0-893e-9961895f5347.png)

![화면 캡처 2022-04-01 171251](https://user-images.githubusercontent.com/62869017/161223291-9044c474-38b5-4641-9afa-3e90f58d6e15.png)

![화면 캡처 2022-04-01 171456](https://user-images.githubusercontent.com/62869017/161223590-7548195f-b544-4502-9666-ccfe8df6ef3c.png)
Windows Sdk는 옵션이지만 후에 fastbuild 활성화 시에 버전이 다르면 안되기에 windows sdk를 최신으로 설치하여 변경했다.

https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/




# Fast Build 활성화

cmd 창을 키고 fastbuild-ue4.26.2-1.04-ue4\Code 이곳으로 이동한 후 
FBuild.exe All-x64-Release -dist -clean 을 입력한다.

빌드가 정상적으로 완료되었으면 성공

fastbuild-ue4.26.2-1.04-ue4\UnrealEngine 의 Fastbuild.cs, ShaderCompilerFASTBuild.cs 파일을 열어 보면 F:\\Cache 가 있다. 이것을 아까 공유한 FASTBUILD_CACHE_PATH로 변경한다.

이제 fastbuild-ue4.26.2-1.04-ue4 폴더 안에 tmp 폴더가 생성됬다.

fastbuild-ue4.26.2-1.04-ue4\tmp\x64-Release\Tools\FBuild밑에 있는 FBuild/Fbuild.exe와 FBuildWorker/FBuildWorker.exe가 이제 중요하다.
![1648801360](https://user-images.githubusercontent.com/62869017/161224902-7ab2d78c-a552-4a9d-abb1-2e71b53d3a7a.png)

FBuildWorker.exe 파일은 Remote pc로 옮겨주고 실행시켜 준다.
 
host pc에서는 이 두 파일을 Engine\Extras\ThirdPartyNotUE\FASTBuild\Win64 로 옮겨 준다.
fastbuild-ue4.26.2-1.04-ue4\UnrealEngine의 4개의 스크립트 파일은 Engine\Source\Programs\UnrealBuildTool\System 밑으로 옮겨준다.



# Fast Build 캐싱기능
  캐싱 기능을 활용하면 빌드 시간을 대폭 단축시킬 수 있다.
  가끔 처음빌드 시에는 모든 cpu를 다 사용하다가 두번째 빌드부턴 로컬 cpu로만 빌드하는 경우 캐싱이 적용이 안됬는데 fastbuild는 캐싱을 했다고 생각하고 로컬 cpu로만 캐시 read 중인거라  매우 느리다.
  
Engine\Source\Programs\UnrealBuildTool\Executors\Experimental\FASTBuild.cs 파일을 수정해야한다.

![image](https://user-images.githubusercontent.com/62869017/169438731-d3a98901-9c8a-499a-a3a0-68c5a4b4af87.png)

저 부분을 true와 ReadWrite로 해야한다.
  
 
캐싱 기능을 키고 처음 빌드하면

![image](https://user-images.githubusercontent.com/62869017/169438949-39896ab2-e106-4609-a875-e51e1e3a26e2.png)


stores 즉 저장하고

재빌드하면 

![image](https://user-images.githubusercontent.com/62869017/169438988-d8ddfa63-4405-4bbf-9d9b-df4aa47ae606.png)

캐시된것을 가져온다.

![image](https://user-images.githubusercontent.com/62869017/169439025-80e771f3-6e99-4766-83ee-ad64ea5f8c88.png)


# fastbuild moniter

모든 작업이 끝난 후 FASTBUILD_BROKERAGE_PATH/main/**.windows/밑에 remote pc의 ip파일이 만들어지면 된다.
언리얼 엔진을 빌드해보면 

![1648801660](https://user-images.githubusercontent.com/62869017/161225960-2837ab88-02f4-4a0a-b7fb-4540494d7d0c.png)


![1648801616](https://user-images.githubusercontent.com/62869017/161225969-a87a79d0-1689-4805-ad80-b67841755a73.png)


이렇게 출력되며

FASTBuild-Dashboard-master\Source 의 FASTBuild.Dashboard.sln를 빌드한 후 Bin 밑의 실행파일을 실행시키면 현재 연결된 pc와 빌드 상황을 볼수 있다.

인크레디빌드보단 느리긴 한것같은데 안하는것보단 빠른듯하다




# 주의사항

1. 방화벽도 끄고 포트도 열어야한다.

2. 잘되다가 갑자기 안되면 Engine\Intermediate 제거하고 빌드하면 된다.

3. fastbuild 재빌드 하려면 싹다끄고 remote pc의 worker도 끄고 해야 빌드 에러안난다.


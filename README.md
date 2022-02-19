# Table of contents

- 개발환경
  - [Linux용 Windows 하위 시스템 구성](#Linux용-Windows-하위-시스템)
  - [Ubuntu 구성](#Ubntu-구성)
  - [VSCODE 구성](#VSCODE-구성)



# Linux용 Windows 하위 시스템 구성

WSL 설치 
-  https://docs.microsoft.com/ko-kr/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package

참고사항
1. 4단계 - Linux 커널 업데이트 패키지 다운로드
   최신 패키지를 다운로드합니다.
2. WSL2 를 기본으로 설정함.
3. 리눅스버전 : Ubuntu 20.04 LTS

# 개발 S/W 설치 (Utubntu)

Ubuntu 20.04 LTS 실행

1. apt 업데이트
  - sudo apt-get update && sudo apt-get upgrade
  ![image](https://user-images.githubusercontent.com/80744273/154805296-0d9975d5-6ff1-458b-b3f5-4f5aa0ebe9b7.png)

2. openjdk-11-jdk를 설치
  - sudo apt-get install openjdk-11-jdk
  ![image](https://user-images.githubusercontent.com/80744273/154805408-d4a41e79-5009-47ba-aaba-e9b7fcb4282e.png)

3.JAVA 환경 설정
 -  vim ~/.bashrc
 -  맨아래 아래 내용을 추가함   
    export JAVA_HOME=$(dirname $(dirname $(readlink -f $(which java))))  
    export PATH=$PATH:$JAVA_HOME/bin  
    ![image](https://user-images.githubusercontent.com/80744273/154805615-2ce20510-c686-43a1-a2e1-3e4a96c3568c.png)  

 - 다음 명령어로 변경한 설정을 현재 실행된 쉘에 적용할 수 있습니다. 또는 새로운 터미널 창을 실행시키면 됩니다  
    source ~/.bashrc

 - 다음과 같이 JAVA_HOME이 설정되었는지 확인할 수 있습니다.  
   echo $JAVA_HOME  
   /usr/lib/jvm/java-11-openjdk-amd64  
   ![image](https://user-images.githubusercontent.com/80744273/154805778-dfe70b1b-426e-4db5-a490-e71ee6f6e4e7.png)

 4. Maven 설치  
   sudo apt install maven  
   mvn -v  
   apt list maven  
  
# VSCODE 구성

VSCODE 다운로드 및 설치 (Windows용)
- https://code.visualstudio.com/download

EXTENTIONS 설치

1. Remote-WSL

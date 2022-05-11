
#### Table of contents
  - 사전준비
  - Repository 생성
  - Reference


#### 사전준비
  - #### Git 설치하기    
    ```
    sudo apt-get intsall git
    ```    
    
  - #### Git 자격증명
    AWS CodeCommit에 대한 HTTPS Git 자격 증명
    ![image](https://user-images.githubusercontent.com/80744273/167784514-239a2adf-9b1f-41ac-abfe-7b28432e996f.png)
 
  - #### 레파지토리 생성
    ![image](https://user-images.githubusercontent.com/80744273/167784016-6ee034cb-ed6f-49ca-9ecd-156adf1a15c2.png)


#### 첫 커밋 생성
  - #### 소스트리 다운로드 및 설치   
    https://www.sourcetreeapp.com/
   
  - #### 로컬 리포지토리 생성    
    ```
    git clone https://git-codecommit.ap-northeast-2.amazonaws.com/v1/repos/edp.emc.water.wte.ui
    cd edp.emc.water.wte.ui
    ```
  - #### 윈도우에서 복제된 로컬 리포지토리 경로 확인
    ![image](https://user-images.githubusercontent.com/80744273/167786451-f2ad3a7f-fc2d-4cdb-8cb0-bada4d4133bc.png)
    
  - #### 소스트리에 리포지토리 추가  
    ![image](https://user-images.githubusercontent.com/80744273/167785706-0422f452-802f-490f-bac9-92607e0b8bc7.png)
 
  - #### git config를 실행하여 사용자 이름 및 이메일 주소를 로컬 리포지토리에 추가합니다    
    ```    
    git config --local user.name "your-user-name"
    git config --local user.email your-email-address
    git config --list
    ```    
 
  - ### master 브랜치로 변경
    ```
    git checkout -b master    
    ```
  - ### 소스트리에서 소스코드 커밋 / 푸시
    ![image](https://user-images.githubusercontent.com/80744273/167794840-dbb68cc5-334f-40da-b864-f58f30edde2d.png)
    
### Git Flow 저장소 초기화
  - ### 소스트리에서 Git Flow 저장소 초기화
    ![image](https://user-images.githubusercontent.com/80744273/167791363-424b9b9c-d625-44b8-8789-732831b2b2bf.png)
    
  
### Git 명령어
  - #### 로컬 리포지토리 생성    
    ```
    git push -u origin main
    ```    
  - #### 로컬 브랜치 생성 및 체크아웃
    ```
    git checkout -b develop
    ```  
  - #### 기본 브랜치 이름을 설정함.    
    ```
    git config --local init.defaultBranch main
    ```    
    
  - #### 브랜치 목록 조회
    ```
    git branch  (로컬 브랜치 목록 조회)
    git branch -r  (원격 브랜치 목록 조회)
    git branch -a  (모든 브랜치 목록 조회)    
    ```
    
#### Reference   
https://docs.aws.amazon.com/ko_kr/codecommit/latest/userguide/getting-started.html

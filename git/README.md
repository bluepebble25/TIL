# git 사용하다 마주치는 문제들

## git 라이프사이클
> **add**는 스테이징 (커밋할 대상 목록에 올리기)  
**commit**은 스냅샷을 저장하기 (상태 박제하기)  
> *\*여기까지는 local 저장소(내 컴퓨터) 안에서 이루어지고 있는 과정*  

>**push**는 commit(박제한 상태)를 remote 저장소에 업로드하기

<br>

## 기본편
### remote
- remote를 등록하고 싶어요
  ```
  git remote add [등록할 remote 이름] [원격 저장소 주소]
  git remote add origin https://github.com/bluepebble25/TIL.git
  ```

### clone
- 원격 저장소를 내려받고 싶어요 (로컬에 아무것도 없을 때 init하고 다운로드 해준다)
  ```
  git clone [저장소 주소]
  ```

### pull
- 원격 저장소의 업데이트사항을 다운받고 싶어요
  ```
  git pull [원격 저장소 이름] [브랜치 이름]
  git pull origin main
  ```

### add
- 파일 여러개를 add하고 싶어요
  ```
  git add [file1] [file2]
  ```
- add를 취소하고 싶어요(스테이징 취소)
  ```
  git reset HEAD        // 전체 취소
  git reset HEAD [file] // 특정 파일 취소
  ```

### commit
- commit을 취소하고 싶어요
  ```
  git reset HEAD^
  ```
- 메세지를 변경하고 싶어요
  ```
  git commit --amend    //편집기가 나타난다.
  ```

### push
- push를 취소하고 싶어요
  ```
  git reset HEAD^         // 커밋 취소(한 칸 전의 커밋을 가리키기)
  git commit -m "message" // 커밋 다시 하기
  git push origin main -f // 강제로 push해서 덮어씌우기
  ```

- push 오류가 발생해요
  - repository를 생성할 때 README.md를 같이 생성해서 원격에는 README.md가 있는데 local에는 없기 때문에 발생한다. pull로 동기화해주자.
    ```
    git pull origin main
    ```

### log
- 최근 log를 n개만 보고 싶어요
  ```
  git log -숫자 // 빠져나오려면 'q' 입력
  ```
- commit 메세지로 검색하고 싶어요
  ```
  git log --grep "찾는 내용"
  ```
  띄어쓰기가 없으면 따옴표를 안붙여도 되지만 있는 경우 붙여야 한다.

### 파일 관리
로컬에서 직접 파일 삭제나 이름 변경을 해도 변경 사항을 staging 하지 않으면 버전관리에 반영되지 않는다. 따라서 이름 변경과 파일 삭제 후 staging까지 한번에 하기 위해서는 mv와 rm 명령어를 이용해야 한다.
- 파일 이름 변경
  ```
  git mv [대상 파일명] [새로운 이름]
  ```
  mv를 통해 변경하면 이름 변경 사항이 추적된다.

- 파일 삭제
  - 파일이 staging 되지 않은 경우
    ```
    git rm [삭제할 파일명]
    ```
  - 이미 staging 되어 있는 파일인 경우
    ```
    git rm -f [삭제할 파일명]
    ```
  - git 저장소에서만 삭제하고 로컬에는 남기고 싶은 경우
    ```
    git rm --cached [삭제할 파일명]
    ```
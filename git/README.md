# git 사용하다 마주치는 문제들

## git flow 이해하기
add -> commit -> push


> **add**는 스테이징 (감시 대상 목록에 올려놓기)  
**commit**은 스냅샷 찍기 (상태 박제하기)  
> *\*여기까지는 local 저장소(내 컴퓨터) 안에서 이루어지고 있는 과정*  

>**push**는 commit(박제한 상태)를 remote 저장소에 업로드하기

<br>

## 기본편

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


### log
- 최근 log를 n개만 보고 싶어요
  ```
  git log -숫자
- commit 메세지로 검색하고 싶어요
  ```
  git log --grep "찾는 내용"
  ```
  띄어쓰기가 없으면 따옴표를 안붙여도 되지만 있는 경우 붙여야 한다.


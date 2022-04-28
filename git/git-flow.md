# git-flow

[Vincent Driessen의 blog](https://nvie.com/posts/a-successful-git-branching-model/)에서 처음 소개되어 인기를 끌게 된 방법론이다. git이 활성화되기 시작하는 2010년대에 쓴 글이라 여러개의 버전으로 나뉘어 서비스해야 하는 로컬 기반 소프트웨어에서 웹에서 서비스되는 웹앱으로 패러다임이 바뀐 것을 유념하는게 좋다.

## 협업 시 git repository 구성

|협업 시 repository 구성|
|--------------|
|중앙 remote 저장소|  
|중앙 저장소를 fork해온 개인 remote 저장소|
|개인 Local 저장소|

1. 중앙 remote 저장소를 개인별로 fork해서 개인 remote 저장소를 생성한다.  
2. 로컬에서 작업한 다음 작업 브랜치를 개인 remote 저장소에 push한다.
3. push한 브랜치를 중앙 remote 저장소에 pull request한다.
4. 코드리뷰를 거친 후 중앙 remote 저장소가 pull request를 merge한다.

## git-flow 전략
용도별로 나뉘어진 5개의 브랜치로 작업을 관리하는 전략이다.  
항상 유지되는 메인 브랜치(master, develop)와 작업이 끝나면 merge되어 사라지는 보조 브랜치(feature, release, hotfix)가 있다.

- master: 기준이 되는 브랜치. 이 브랜치에서 제품을 배포한다.
- develop: develop 브랜치에서 개발을 진행한다. 배포해도 된다 싶으면 QA를 거쳐 master에 병합한다.
- feature: 단위 기능을 개발하는 브랜치. 기능 개발이 완료되면 develop 브랜치에 병합한다.
- release: develop에서 개발한 내용을 QA하는 브랜치. 검수가 완료되면 버그를 고치고 master와 develop 브랜치에 병합한다.
- hotfix: master 브랜치에 배포한 코드에 버그가 생겼을 때 긴급수정하는 브랜치

![git-flow](./img/git-model.png)

### 설명
1. develop 브런치는 master 브랜치에서 분기한 브랜치이다. 상시로 버그 수정을 하는 등의 커밋이 발생하고 기능개발이 완료된 feature 브랜치, 검수 완료가 된 release 브랜치와의 병합이 일어난다.
2. 만약 새로운 기능을 추가한다면 feature 브랜치를 develop으로부터 분기해서 생성하고 여기서 기능 개발이 이루어진다. 개발이 다 끝나면 반드시 develop 브런치에 합쳐져야 된다.
3. develop 브랜치를 배포하기 전 검수를 진행하기 위해 release 브랜치를 생성한다. QA를 한 다음, 발생한 버그를 여기서 수정한다. 완료되면 develop 브랜치와 master 브랜치에 merge한다.
4. release 브랜치와 master 브랜치에 merge되면 그게 최신 버전이다. 여기에 버전 태그를 붙인다.
5. 만약 master 브랜치에 있는 배포 버전에 버그가 발생한다면 hotfix 브랜치에서 버그를 수정하고 다시 master 브랜치에 병합한다.

## merge 전략
### 그래프가 복잡해지는 것을 방지하려면...
단순히 브랜치를 merge하기만 하면 커밋 히스토리를 그래프로 봤을 때 브랜치가 얽히고설켜 매우 복잡해보일 것이다. 따라서 아래와 같이 상황에 따라 merge하는 방법을 선택한다.

1) 커밋 내역을 모두 보존하면서 브랜치도 보존하고 싶다. - 일반 merge (두 개의 부모를 가진 한 개의 병합된 커밋을 생성한다)
2) 커밋 내역을 모두 보존할 필요가 없다. - squash and merge (commit 이력을 한 개의 commit으로 압축해 merge하고 브랜치를 삭제하면 깔끔해진다.)
3) commit 내역까지 다른 브랜치에 원래 있었던 것처럼 옮기고 싶다 - rebase

### git-flow에서 사용 예시
- **develop - feature** 브랜치 간 merge  
  Squash and Merge  
  feature의 복잡하고 지저분한 커밋 히스토리를 모두 묶어 완전 새로운 커밋으로 develop 브렌치에 추가한다. 일반적으로 merge 후에 feature 브렌치를 삭제해버리는걸 생각해봤을때 커밋 히스토리를 모두 남길 필요가 없기 때문이다.

- **master - release** 브랜치 간 merge  
  Merge  
  다른 병합 방법(squash merge, rebase)는 언제 병합되었는지 시점을 알 수 없고 원래 그랬던 것처럼 commit에 포함되어 있는데 반해, merge는 병합된 시점을 명확히 알 수 있다는 장점이 있다. 따라서 master에 있는 태그번호와 release의 특정 시점의 커밋을 연관짓기 쉽다.

- **hotfix - master** 브랜치간 merge  
  Merge 또는 Squash and Merge 모두 유용  
  hotfix 브랜치에서 작업한 커밋 히스토리가 모두 남아야 하는 경우 Merge, 필요 없는 경우 Squash and Merge를 사용하면 된다.

<br>

## *Reference*
- [git-flow가 처음 소개된 Vincent Driessen의 blog](https://nvie.com/posts/a-successful-git-branching-model/)
- [우아한 형제들 기술 블로그 | 우린 Git-flow를 사용하고 있어요](https://techblog.woowahan.com/2553/)
- [GitHub의 Merge, Squash and Merge, Rebase and Merge 정확히 이해하기](https://meetup.toast.com/posts/122)
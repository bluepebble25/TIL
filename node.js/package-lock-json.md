# package-lock.json
## package-lock.json은 왜 있는걸까?
package.json만을 보고 디펜던시 라이브러리를 다운받게 된다면 `^16.0.9`같은 version range로 인해 16.0.10이나 16.1.0을 다운받을 수도 있다. 이론상으로는 breaking change(엄청난 변경)이 일어날 때 메이저 버전이 업데이트되므로 마이너나 패치 버전이 다른 건 별 상관 없을 수 있다. 하지만 이론상으로나 그렇지 실제로는 마이너나 패치 버전이 바뀌면 프로그램에 문제가 생기는 경우가 많다.

package-lock.json은 처음 환경 구성을 위해 `npm install`를 하는 순간 생성되며 이후에 계속 내가 다운받은 라이브러리들과 버전을 기록한다. package.json과 다른 점은 package-lock.json은 version range가 아니라 내가 다운받은 정확한 버전을 기록한다는 점이다. 이를 두고 package-lock.json은 디펜던시 트리를 갖고 있다고 한다. 덕분에 동료가 내 패키지를 다운받으면 내가 구성한 환경과 정확히 같은 버전의 라이브러리를 다운받을 수 있다. 시간, 환경에 상관없이 여러 사람이 동일한 node_modules 폴더를 가질 수 있는 것이다.

## Reference
- [package-lock.json은 왜 필요할까?](https://hyunjun19.github.io/2018/03/23/package-lock-why-need/)
- [내 마음을 불편하게 만드는 package-lock.json](https://www.josephk.io/package-lock-json/)
# git flow (from 우아한형제들 기술 블로그)
    http://woowabros.github.io/experience/2017/10/30/baemin-mobile-git-branch-strategy.html  


## branch role
* master
- 말그대로 완성된 제품의 형상
* develop
- 지금 개발중인 버전의 root(다음버전을 위한거라고 할 수 있겠다)
* release
- develop 에서 목표하던 개발이 완료되면(구현되면) release를 위해 QA를 거치는 브랜치
* hotfix
- 예기치 못한 버그를 고치기 위한 브랜치. 완성하고 보니 예상치 못한게 나오는 경우기 때문에 master에서 나옴
* feature
- develop에서 부터 나오며 이번 개발 목표에 해당하는 개별 기능들을 위한 브랜치

![example_img](./img/git-flow.png)

# about rebase(merge) a branch into parent branch

* fast-forward :  
  갈라져 나온 브랜치가 원본 브랜치의 HEAD에 기반하고 별다른 conflict 상황이 없으면   
  갈라져나온 브랜치의 모든 변경사항이 차곡차곡 ```commit```이 쌓이듯이 합쳐지고 이걸 fast-forward라고함   
  (원본 브랜치의 HEAD가 이동하는 모양)   

* git branch --no-merged / git branch --merged :   
  git branch를 하면 현재 로컬에 존재하는 브랜치 list가 나오는데 뒤에 저런 옵션을 붙이면  
  머지가 돼있는 브랜치인지 아직 안 된 브랜치인지 태그가 표시되어 나온다.   

* git pull --rebase :  
  pull이란것도 현재 local의 내 코드에 remote의 코드를 'merge'하는 작업이다.  
  실제로 git pull을 하면 3-way-merge가 일어나며 이 때 merge가 아닌 rebase로 합칠 수가 있다.  
  git pull --rebase를 하게되면 remote의 코드를 내 코드에 합칠 때 merge가 아닌 rebase를 하게되고  
  remote의 최종 commit(HEAD)이후부터 내 커밋들이 차례차례 쌓인다.  

* git push --force-with-lease :  
  --force push하는 중에 충돌이나면 push 하지 않고 취소함  

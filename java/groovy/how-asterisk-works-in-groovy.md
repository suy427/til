# How Asterisk works in Groovy

## Asterisk(*)기호는 groovy list에서 2가지의 의미를 가진다(포인터랑 관련 없음)
1. list를 확장(뭐랄까 펼쳐놓는달까)하는 역할
2. list를 traverse(순회)하는 역할(list외에 다른 collection에서도 되는지 확인 해봐야지)

## EX)
1. list 펼치기 
```groovy
   def list1 = [1,2]
   def list2 = [3,4]
   def list3 = [*list1, *list2] // list3 = [1,2,3,4]
```

2. list의 element 하나하나를 traverse하기
```groovy
   def uppers = ['A','B','C','D','E']
   def lowers = uppers*.toLowerCase() // ['A'.toLowerCase(), 'B'.toLowerCase().....]
```

## RGRG??

## MultivalueMap

multivaluemap은 request의 query string이나 header 값을 담기 위한 자료구조이다.   
java에서 기본으로 제공하는 collection은 아니고, spring에서 제공한다.  
MultivalueMap은 key:value형태의 일반적인 Map과 약간 다르게 value가 무조건 List형이다.   
때문에 배열형태의 데이터도 담을 수 있다.   
예를들어 

```
storage: {
  id: "mv-001",
  type: "movie",
  items: [
    {
      title: "About time",
      genre: ["sf", "drama"],
      released: 2012
    },
    {
      title: "Interstella",
      genre: ["sf", "drama"],
      released: 2014
    }
  ],
  amount: 2
}
```
이런 형식의 데이터에서 어바웃 타임의 개봉년도를 표현하려면 어떻게 해야할까?   
```storage.items``` 가 배열이기 때문에 ```item[0].released```와 같이 표현해줘야 한다.

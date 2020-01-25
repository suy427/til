## json slurper can convert each other json string and map(object)

#### 대신 모든 quote는 single이 아니라 double로 해야한다. (single quote 하나라도 쓰면 그냥 String으로 반환됨)

```java
String json = '''
{
  "name": "john",
  "age": "28",
  "nation": "korea",
  "job": ["doctor", "singer", "designer"],
  "address": {
    "kyeongkido": {
      "seong-nam": {
        "pangyo": "uspace"
      }
    }
  },
  "date": "2020.01.24"
}
'''

def result = new JsonSlurper().parseText(json)

/////
println result.age // 28
println result.job // ["doctor", "singer", "designer"]

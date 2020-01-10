# Gradle 시스템 정복하기!!

## Gradle이란?
* gradle은 쉽게 말하면 프로젝트 빌드해주는 툴이다. 프로젝트 내에서 여러 라이브러리나 sdk, 프레임워크 등의 의존성을 관리하기가 힘드니깐 이 gradle이 이것까지 다 자동으로 맞춰서 빌드를 시켜준다.

## gradlew(grade.bat)
* Grade wrapper파일. 쉘파일이고 이걸로 gradle이 의존성 다 맞춰서 build할 수 있음
* Grade wrapper는 뭐냐면 gradle 자체도 버전 의존성이 있을 수 있으니까(내 local 컴퓨터에 설치된거랑, 내가 build할 프로젝트가 가진 gradle이랑) 이 wrapper파일이 그거까지 다 맞춰준다.
       심지어 Java나 Gradle을 설치할 필요도 없고 프로젝트 내에 Gradlew파일이 있으면 이게 다 알아서 해준다. 따라서 꼭 Wrapper파일을 쓰는게 좋다.
    * 빌드 방법 : ```./gradlew build```

## gradle-wrapper.jar
* Grade wrapper의 설정 파일.
* task가 실행될 때 이 파일에 명시된 여러 버전을 보고 필요에따라 새로운 버전의 wrapper 파일 등을 로컬 캐시에 다운받는다.

## Build.gradle
* 프로젝트 내에서 의존성, 플러그인 설정하는 스크립트. Groovy로 되어있다.
    * 하나의 build.gradle파일이 영향을 미치는 범위에 대해 알아볼것.

## settings.gradle
* 하나의 프로젝트 전체의 구성 정보를 기록하는 파일
* 어떤 하위 프로젝트들이 어떤 관계로 구성되어 있는지를 기술
* Gradle은 이 파일을 보고 프로젝트를 알아서 구성함. (IDE에서 직접 프로젝트, 모듈 생성 하지 않고도…?!)

## Gradle script분석
* gradle은 의존성관리를 위해 만들어진 DSL(Domain-Specific Language)이다. Groovy로 만들어졌지만 gradle DSL의 규칙이 조금 추가되어있다.
* Gradle script는 1개 이상의 projects로 구성되어있고, projects는 1개 이상의 task로 구성되어있다.
    * task는 쉽게말하면 메소드 같은거다.
    * task예시) task example1{ doLast{println ‘Hello world!!’} }
    * task example2(dependsOn: example1) { doLast{‘Hello world!!’} } 이렇게하면 example2는 example1에 의존하게되고 example1이 먼저 실행되고 나서 2가 실행된다.

## build.gradle 예시)
```groovy
allprojects { // 최 상위 단위..? 내가 가지고있는 프로젝트에서는 이건 없다.
    repositories { // yum이나 apt처럼 필요한 library를 가져오기 위한 웹 repository // maven, jcenter, ivy모두 repository 이름이다.
        mavenCentral()
        jcenter()
        maven {
            url "http://repo.mycompany.com/maven2"
        }
        ivy {
            url "../local-repo"
        }
    }

    dependencies { // 위의 repository{}에서 명시한 repository들에서 가져올 의존성들을 여기 적음. Compile, runtime 등의 가져올 시점과 함께 적음
        compile fileTree(dir: 'libs', include: '*.jar') // 
        compile project(':shared') // 
        compile 'com.google.guava', name: 'guava:23.0' // compile은 compile타임에 명시한 의존성을 받아온다는거다
        testCompile group: 'junit', name: 'junit', version: '4.+' // testCompile은 테스트시에만 명시한 의존성을 받아온다는거다. version지정을 저렇게 ‘4.+’로 해서 항상 4점대의 최신버전을 사용하게 할 수 있다.
        compileOnly 'org.projectlombok:lombok:1.16.18' // compileOnly는 말그대로 compile할때만 명시한 의존성을 쓰고 artifact를 만들때(.jar, .war)는 포함하지 않는다는거다. —> 롬복을 컴파일때만 쓴다는건 이거 포함해서 컴파일 해놓으면 그 뒤에 링크,로드(..?)할때 필요없다는건가..?, 그리고 artifact에대한 정확한 정의 확립하기
        runtime('org.hibernate:hibernate:3.0.5') // 이건 실행할 때 의존성을 받아온다는거다 (‘기본적으로 컴파일을 모두 포함’이라고 블로그에 돼있는데 이게 뭔뜻일까..?) —> 컴파일을 ‘포함’한다는건 각 빌드 타임마다 각각 이 의존성을 쓴다는건가..? 
    }
}
```

ext는 def처럼 변수 선언할 때 쓰는건데 def는 해당 스크리브의 지역변수인 반면 ext로 선언한건 프로젝트 전체와 서브 프로젝트에서도 접근 가능하다.
def var1 =1 이렇게 말고 ext.var2 = 1 이런식으로 쓰고 여러개를 한꺼번에 선언할 때는 ext{} 로 묶어서 여러개를 한꺼번에 선언할 수도 있다.
doLast는 해당 task가 실행되고 끝날 때 실행되는거, doFirst는 말 안해도 뭔지 RG?




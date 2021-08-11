# k8s 워크로드 & 스토리지

## 선언적인 관리??  
k8s는 ```선언적인``` 방식으로 자원(k8s object)를 관리한다. (Declarative Object Management). 마치 CSS처럼 클러스터상의 내 container들이 어떤 상태를 가져야하는지를 선언해놓는게 k8s object이다. k8s는 이 object에 선언된 상태로 컨테이너들을 유지시킨다.   

보통 하나의 어플리케이션은 여러 컴포넌트들이 연합한 형태인데, 의미상 맞다면 가능한 연관된 오브젝트들은 한 파일에 모아놓는게 관리하기 쉽다.   
(예시: [guest-book-all-in-one.yaml](https://github.com/kubernetes/examples/blob/master/guestbook/all-in-one/guestbook-all-in-one.yaml))

</br>
  
<img src="https://user-images.githubusercontent.com/36359175/129010423-bd65702f-cacb-4469-9ffd-dacf64f1d26c.png" width="70%" height="70%">

</br>
</br>
  
**💡 Object들의 관계?**
```
처음엔 ReplicaSet이나 Deployment가 Pod를 포함하는(가지는) 개념인줄 알았습니다.  
그러나 결국 물리적(이라고 해도 될지..?)으로 실제 존재하며 동작하는 자원은 Pod정도이고,   
나머지 워크로드들은 k8s api에 Pods들의 상태를 "요청"하는(RequestBody같은?)개념이 더 가깝지 않나 싶습니다.
```
<img src="https://user-images.githubusercontent.com/36359175/129008331-3825fc62-25a9-402e-a594-030fc5c76d1a.png" width="70%" height="70%">

</br>

## Storage

#### Volume
Pod들이 가지는 가지는 볼륨은 임시 볼륨이다. 컨테이너 프로세스 내에서 바라보는 격리화된 가상 파일시스템이기때문에 Pod가 내려가면 사라진다.  
이를 다른 Pod와 공유하거나 Pod가 종료되어도 유지되기 원한다면 볼륨을 사용해야한다.
**볼륨**은 당연히 논리적인 개념이 아니라 Node처럼 물리적인 자원의 개념이다. k8s클러스터 안에서 사용할 수 있도록 구성된 파일시스템이라고 생각하면 될것같다.

<img src="https://user-images.githubusercontent.com/36359175/129037537-c37598b0-5bb9-4e2e-8416-9ec2b7bc896c.png" width="70%" height="70%">  

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pd
spec:
  containers:
  - image: k8s.gcr.io/test-webserver
    name: test-container
    volumeMounts:
    - mountPath: /test-pd
      name: test-volume
  volumes:
  - name: test-volume
    hostPath:
      # 호스트의 디렉터리 위치
      path: /data
      # 이 필드는 선택 사항이다
      type: Directory
```


</br>

#### PV(Persistence Volume) & PVC(Persistence Volume Clame)
위 그림의 예시 외에도 k8s에서 지원하는 볼륨의 종류는 엄청나게 많다. 그리고 각각의 볼륨마다 pod에 마운트 하는 방법이 다 다르다. 이를 직접 쓴다면, 추후 볼륨이 변경되면 이를 사용하는 모든 Pod의 설정을 다 같이 변경하여 재배포 해야한다. PV는 이 볼륨을 추상화해서 사용될 볼륨과 사용하는 Pod간의 결합을 느슨하게 하는 역할을 한다. PVC는 말 그대로 PV에대한 요청이다.

<img src="https://user-images.githubusercontent.com/36359175/129038963-c6a5e9fe-25b0-4fa5-821c-79282e7ff7b9.png" width="70%" height="70%">

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: task-pv-volume
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi

---
apiVersion: v1
kind: Pod
metadata:
  name: task-pv-pod
spec:
  volumes:
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
```

#### Storage Class
그렇다면 PVC는 굳이 왜 필요할까? PVC가 그냥 PV를 요청하는 의미라면 PVC는 항상 자기가 요청할 PV를 알고있어야하고,   
물론 그 PV는 항상 클러스터 내에 미리 만들어져있어야한다. 너무 정적이고 뭔가 이상하다.  
파드들에 대해서 여러가지 워크로드로 프로비저닝이 가능했던것처럼 스토리지도 그런것이 가능해야한다.
   
위에 작성한 yaml파일을 보면 PVC가 필요한 PV에 대해서 기술하고있지 않고 ```storageClassName```을 지정해서 요청하고 있다.
Storage Class는 볼륨에대한 프로파일을 정의해두고, 이 볼륨을 k8s가 동적으로 프로비저닝할 수 있도록 하는 오브젝트이다.
이를 통해서 현재 PVC가 요청하는 storage class에 해당하는 PV가 있다면 그 PV를 사용하고 없다면 동적으로 생성하여 사용할 수 있게된다.

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: slow
provisioner: kubernetes.io/aws-ebs
parameters:
  type: io1
  iopsPerGB: "10"
  fsType: ext4
```
  
----  

## 다시 워크로드... (StatefulSet)
가장 기본적인 워크로드로 ReplicaSet이나 Deployment(사실상 ReplicaSet은 많이 사용하지 않는듯..?)를 봤는데, 이 외에도 몇가지 워크로드가 더 있고 그 중 StatefulSet이란게 있다.
처음엔 Deployment와 뭐가 다르지? Deployment는 왜 못하지? 이런 궁금증이 들었다.
  
### Deployment
일단 첫번째로 Deployment는 내부적으로 ReplicaSet을 생성하는데 ReplicaSet는 생성될때 Pod들을 병렬로 동시에 띄운다. 이는 master가 먼저 뜨고 slave가 떠야하는 식의 어플리케이션을 띄울 수 없다. ReplicaSet이 띄우는 Pod는 이름이 컨트롤러에 의해서 불규칙적으로 지어지고, 이로인해서 slave가 뜰때, master의 이름을 특정할 수도 없다.

```
바인딩
사용자는 특정한 크기와 접근 모드로 PVC를 선언할 수 있다. 컨트롤 루프는 새로운 PVC가 생성되는 것을 확인하고, 이에 맞는 PV를 매칭한다.   
당연히 PVC에 의해 새로 만들어진 PV는 요청한 PVC에 매칭된다.   
PVC가 PV를 새로 만들도록 요청하고 이미 있는 PV와 매칭되지는 않는다.(그러면 잉여 PV가 쌓인다.)    
PVC-PV관계는 일대일이며 배타적이다. PV가 사라져도 PVC는 남는다. 나중에 PV가 다시 available한 상태가 되면 다시 바인딩된다.   
예를들어 PVC가 100Gi를 요청했을 때, 50Gi짜리 PV와 1:2로 매칭될 일은 없다.
```
Deployment는 Pod의 템플릿을 작성하는 부분은 있지만 PV(PVC)의 템플릿을 작성하는 부분은 없다. 때문에 각각의 Replica가 같은 PV를 사용하려고 하는데, 이 때 제일 먼저 뜨는 Pod에 PV가 바인딩되고 나면 위와 같은 배제적인 바인딩 정책때문에 나머지 Pod들이 뜨는데에 실패하고만다.

### StatefulSet
StatefulSet의 Pod들은 ```${statefulset name}-{ordinal number from 0}``` 이렇게 sequencial한 이름으로 생성이 부여되며, 순차적으로 기동된다. 또한 yaml파일에서 PVC Template을 작성할 수 있도록 지원한다.
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```

<img src="https://user-images.githubusercontent.com/36359175/129060914-93c54f93-d798-4ed3-a20a-c88c0dd94d52.png" width="70%" height="70%">

**💡 나머지 워크로드**
```
이 외의 워크로드로 DemonSet, Job, CronJob 등이 있지만 깊히 다루지는 않겠습니다.
DemonSet은 모니터링 등의 용도로 클러스터 내의 모든 노드에 기동이 되는 Pod들의 Set입니다. 
Job은 웹 서버 같이 항상 떠있는 어플리케이션이 아니라 실행해서 완료가 되는 작업입니다. (like Batch)
CronJob으로 반복 일정에 따라 Job을 생성할 수 있습니다.
```
  
-----
__Reference__
> storage: https://m.blog.naver.com/freepsw/222005161870   
> statefulset:   
> * https://bcho.tistory.com/1306   
> * https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=isc0304&logNo=221885403537

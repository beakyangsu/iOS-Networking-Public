> ### reference : https://appstuff.teachable.com/courses/enrolled/2176637

<a href="https://github.com/beakyangsu/iOS-Networking/blob/main/README.en.md"><img alt="Static Badge" src="https://img.shields.io/badge/switch_to-readme_en-blue?style=plastic&link=https://github.com/beakyangsu/iOS-Networking/blob/main/README.en.md">

![iOS badge](https://img.shields.io/badge/iOS-15.0%2B-green)
![iOS badge](https://img.shields.io/badge/iOS-Swift_UI%2B-red)
![iOS badge](https://img.shields.io/badge/iOS-Async_Await%2B-yellow)


## 1. 네트워킹 : 핸들러 vs 컨커런시 
async await은 네트워킹을 하기에 핸들러보다 더 적합함
+ self? 같은 옵셔널 신경안써도되고,
+  핸들러안쓰니까 리테인 사이클 걱정안해도되고
+ 코드가 훨씬 깔끔, 가독성상승
+  UI-Thread 접근할때도 MainActor하나만쓰면되서 코드가 깔끔함


## 2. observedObject vs EnviromentObejct

 + EnviromentObejct랑observedObject 는 여러뷰에서 동일한 데이터를 인젝션을 통해 쉐어하게햊ㅁ
 observedObject는 파라미터로 계속 넘겨야해서 코드가 지저분한데

 + EnviromentObejct를 rootview에서 @StateObject를 선언하고 contentView()에 인젝션하면
 그뒤에 별도 코딩을 안해도 ContentViews내부에 있는 모든뷰가 StateObject에 접근할수있임

 + 하지만 반대로 EnviromentObejct로 하는 클래스는 모든뷰에서 접근하는것만 해야함
 EnviromentObejct으로 모든걸 해결하려고 하다보면 한 클래스안에 다른 뷰에서 필요한 로직을 모두 넣어야하기때문에
 하는일이 너무 많아져서 오히려 코드가 지저분해짐

 + observedObject 국지적, EnviromentObejct 글로벌로 상황에 맞게 적절한 사용 필요


## 3.디펜던시 인젝션

 서비스를 프로토콜로 정의해서 인젝션한다

 + API를 매 테스트마다 호출하는건 너무 남용임
 + 목업서비스와 실제 서비스 스위칭이 간편해야함
 + 목업서비스로 실제 API를 호출하지않고 테스트할수있는 서비스로 개발해야 효율적
 + 디펜던시 인젝션을 프로토콜로 하면 실제 수정은 root app에서 한번만 하면되고 다른부분은 건들지않아도됨 -> 수정이 쉬움
 + 프토토콜을 준수해놓고 그안에 프로토콜에 없는 다른 함수를 정의하면안됨
 + 이러면 프로토콜타입에 의핸 디펜던시 인젝션의 장점이 사라짐, 결국엔 그 함수 때문에 특정 클래스 타입에 의존성이생김, 서비스 스위칭이안됨




## 4. 유닛테스트

 + 퀄리티좋은 코드를 만들게해줌

 + 유닛테스트를 통해 예외케이스를 테스트하여 미리 예방가능
 구조가 잘잡힌 코드인지아닌지 확인가능
 테스트 코드 짜기 편한코드가 결국 well-organized코드고 유지보수 쉽다는 뜻이됨

 + 리팩토링을 하거나 비지니스 로직을 바꿀경우
 유닛테스트를 별도로 수정하지않고 그냥 빌드시에 제대로 동작하는지만 확인하면 제대로 짲는지 확인 가능

 + 새로운 코드가 수정됐을경우 기존에 문제없던 기능들이 여전히 문제없는지 유닛테스트로 확인 가능 (regression prevention -회귀 예방)

 + 문서역할을 함  - 유닛테스트를 보면 각각 요소들이 무슨일을 하는지 바로 이해할수 있고, 인풋, 아웃풋이 뭔지 이해가능

 + 이를 기반으로 협업을 쉽게 만들어줌

 + 유닛테스트는 지속가능한 통합(merge)(continuous intergration == CI)파이프라인을 자동화한다. -> 유닛테스트는 지속적이고, 반복적으로 돌면서 코드에 변화가있을대마다 이슈를 잡는걸 보장해준다






## 5. 페이지네이션
 + 한번에 모든 데이터를 서버에서 가져오지않는다
 + 리스트의 마지막에 도달하면 추가로 가져온다

 + 성능적으로도 한번에 다 가져올필요가없음
 + 유저한테 노출되는 시점에 가져오는게 더 유리함




## 6. async urlsession more 
 + https://www.wwdcnotes.com/notes/wwdc21/10095/

 + Upload data
````
func upload(for request: URLRequest, from data: Data) async throws -> (Data, URLResponse)
func upload(for request: URLRequest, fromFile url: URL) async throws -> (Data, URLResponse)
````


 + Example:

 데이터를 data 타입으로 인코딩
 ````
 let order = Order(customerId: "12345", items: ["Cheese pizza", "Diet soda"])
 guard let data = try? JSONEncoder().encode(order) else {
     return
 }
````

 URLRequest생성하고 POS로 지정
 ````
 var request = URLRequest(url: url)
 request.httpMethod = "POST"
 let (data, response) = try await URLSession.shared.upload(for: request, from data: data)
 guard let httpResponse = response as? HTTPURLResponse,
       httpResponse.statusCode == 201 /* Created */ else {
   throw MyNetworkingError.invalidServerResponse
 }
````





## 7. AsyncImage

 + Async로 이미지 다운받는걸 지원해줌 매우 편함
 + 하지만 caching은 지원안함 
````
 AsyncImage(url: URL(string: coin.image)) { image in
     image
         .resizable()
         .frame(width: 32, height: 32)
 } placeholder: {
     EmptyView()
 }
````

# 📏 Swift's downsides.

**"스위프트의 강점과 약점을 모두 인식하면, 언제 어떻게 사용할지 적절히 결정할 수 있습니다."**<br>

## 📏 Module stability(모듈 안정성).

스위프트는 여전히 빠르게 발전하고 있으며 모듈 안정성이 없어, 한 버전에서 제공되는 프레임워크는 새로운 스위프트 버전에 맞게 다시 컴파일해야 합니다.<br>

예를 들어, 애플리케이션용 프레임워크를 작성한다고 상상해 보세요.<br>

스위프트 6이 출시되면, 스위프트 6으로 작성된 애플리케이션은 당신의 프레임워크를 스위프트 6으로 업데이트할 때까지 사용할 수 없습니다.<br>
다행히 Xcode는 마이그레이션을 도와주는 많은 기능을 제공하므로, 이번 마이그레이션이 그리 고통스럽지 않을 것으로 기대합니다.🤣<br>

## 📏 Strictness(엄격)

스위프트는 엄격하고 경직된 언어로, 정적 언어에 대한 일반적인 비판이지만 스위프트를 사용할 때는 더욱 그렇습니다.<br>

스위프트에 익숙해지기 전에는 마치 수갑을 찬 채로 타이핑하는 것 같은 느낌을 받을 수 있습니다.<br>

실제로, 옵셔널을 사용하거나 컬렉션에서 값을 혼합하거나 열거형을 다룰 때와 같이 많은 타입을 컴파일 타임에 해결해야 합니다.<br>

## 📏 Protocols are tricky(프로토콜이 까다롭다).

스위프트의 큰 매력 포인트는 프로토콜입니다.<br>

하지만 프로토콜을 사용하기 시작하면 예리한 모서리에 부딪히고 때때로 상처를 입게 됩니다.<br>

"그냥 작동해야 할것들"이 어떤 이유에서인지 작동하지 않습니다.<br>

프로토콜은 현재 상태에서도 훌륭하며 이미 품질 좋은 소프트웨어를 만드는 데 충분하지만, 때로는 벽에 부딪히게 되고, 이 책에서 많이 공유하는 우회 방법을 사용해야 할 수도 있습니다.<br>

흔한 좌절의 원인: Equatable 타입을 함수에 전달하여 동등한지 확인하고 싶을 때, 막히게 됩니다.<br>

예를 들어, 배열 내의 모든 항목과 하나의 값을 비교하는 것을 단순히 시도할 수 있습니다.<br>

스위프트에서는 이 방법이 통하지 않는다는 것을 알게 될 것입니다.<br>

아래 코드를 봐봅시다<br>

```swift
// Error Code
import Foundation

areAllEqual(value: 2, values: [3, 3, 3, 3])

func areAllEqual(value: Equatable, values: [Equatable]) -> Bool {
	guard !values.isEmpty else {
		return false
	}
	
	for element in values {
		if element != value {
			return false
		}
	}
	return true
}
```

Swift는 무엇을해야하는지에 대한 모호한 제안과 함께 수수께끼 같은 오류를 반환합니다.<br>

아래 오류를 봐봅시다.<br>

```
error: protocol 'Equatable' can only be used as a generic constraint 
because it has Self or associated type requirements
```

추후에 왜 이런 일이 발생하고 이러한 문제를 피하는 방법을 알 수 있습니다.<br>

스위프트의 프로토콜 확장은 그성의 주요 판매 포인트 중 하나이며 제공하는 가장 강력한 기능 중 하나입니다.<br>

프로토콜은 인터페이스처럼 작동할 수 있으며, 프로토콜 확장은 타입에 기본 구현을 제공하여, 경직된 서브클래싱 트리를 피하는 데 도움을 줍니다.<br>

하지만 프로토콜은 보기보다 까다롭고, 심지어 경험 많은 개발자도 놀라게 할 수 있습니다.<br>

예를 들어, FlavorType이라는 프로토콜이 있다고 가정해보세요.<br>

이는 커피처럼 맛을 개선할 수 있는 음식이나 음료를 대표합니다.<br>

프로토콜 선언 내부에 없는 기본 구현으로 이 프로토콜을 확장하면 놀라운 결과를 얻을 수 있습니다!<br>

다음 예제에서 두 가지 커피 타입이 있지만, addFlavor를 호출할 때 각각 다른 결과를 얻는 것을 확인할 수 있습니다.<br>

이것은 미묘하지만 중요한 세부사항입니다.<br>

```swift
import Foundation

protocol FlavorType {
	// func addFlavor() // you get different results if this method doesn't exist.
}

extension FlavorType {
	func addFlavor() { // Create a default implementation
		print("Adding salt!")
	}
}
struct Coffee: FlavorType {
	func addFlavor() { // Coffee supplies its own implementation
		print("Adding cocoa powder")
	}
}

let tastyCoffee: Coffee = Coffee() // tastyCoffee is of type 'Coffee'
tastyCoffee.addFlavor() // Adding cocoa powder

let grossCoffee: FlavorType = tastyCoffee // grossCoffee is of type FlavorType
grossCoffee.addFlavor() // Adding salt!
```

비록 같은 커피 타입을 다루고 있지만, 먼저 코코아 파우더를 추가한 다음 실수로 소금은 넣게 되는데, 이는 아침에 누구도 일어나는 데 도움이 되지 않습니다.🤣<br>

프로토콜이 강력하긴 하지만, 때로는 미묘한 버그를 도입할 수 있습니다.<br>

## 📏 Concurrency

우리의 컴퓨터와 장치들은 동시에 여러 CPU를 사용하는 동시성 기계입니다.<br>

스위프트에서 작업할 때, 애플의 Grand Central Dispatch(GCD)를 통해 이미 동시성 코드를 표현할 수 있습니다.<br>

**"하지만 동시성은 스위프트 언어 기능으로 존재하지 않습니다."**<br>

이미 GCD를 사용할 수 있기 때문에, 적합한 동시성 모델에 조금 더 기다리는 것은 큰 문제가 아닙니다.<br>

그럼에도 불구하고, 스위프트와 결합된 GCD는 완벽하지 않습니다.<br>

첫째, GCD를 사용하는 것은 이른바 '피라미드 오브 둠(심하게 중첩된 코드)'을 만들 수 있으며, 다음 목록에 나와 있는 미완성 코드에 의해 증명됩니다.<br>

```swift
func loadMessages(completion: (result: [Message], error: Error?) -> Void) {
    loadResource("/user") { user, error in
        guard let data = data else {
            completion(nil, error)
            return
        }
        loadResource("/messages/", user.id) { messages, error in
            guard let messages = messages else {
                completion(nil, error)
                return
            }
            storeMessages(messages) { didSucceed, error in
                guard let error != nil else

{
                    completion(nil, error)
                    return
                }
                DispatchQueue.main.async { // Move code back to main queue
                    completion(messages)
                }
            }
        }
    }
}
```

두 번째, 비동기 코드가 어떤 큐에서 호출되는지 모릅니다.<br>
loadMessage를 호출한다면, 완료 블록이 백그라운드 큐로 이동하는 작은 변경으로 인해 문제가 발생할 수 있습니다.<br>
호출 위치에서 메인 큐에서 콜백을 완료하도록 주의를 기울일 수도 있지만, 어느 쪽이든 타협이 필요합니다.<br>

세 번째, 오류 처리는 최적이 아니며 Swift 모델에 맞지 않습니다.<br>
반환된 데이터와 오류 모두 이론적으로 채워지거나 nil일 수 있습니다.<br>
코드는 하나 또는 다른 하나만 될 수 있다는 것을 표현하지 않습니다.<br>
이것에 대해서는 추후에 다룹니다.<br>

Swift의 나중 버전, 아무도 7 또는 8에서 async/await 모델을 기대할 수 있습니다.<br>
이는 이 문제들이 해결될 때까지 기다림을 의미합니다.<br>
다행히도, GCD는 대부분의 필요에 충분하며, 반응형 프로그래밍 대안으로 RxSwift 또는 Apple의 Combine 프레임워크를 사용하게 될 수도 있습니다.<br>

## 📏 Venturing away from Apple's platforms(애플의 플랫폼에서 벗어나기)

Swift는 웹과 시스템 언어로서 새로운 영역으로 진출하고 있으며, 특히 웹 서버 Kitura의 형태로 IBM의 지원과 클라우드로 Swift 도입을 통해 두드러지게 나타납니다.<br>

Apple의 플랫폼에서 벗어나는 것은 흥미로운 기회를 제공하지만, 필요한 패키지가 부족할 수도 있다는 점을 인지해야 합니다.<br>

iOS, watchOS, tvOS, macOS와 같은 xOS 프레임워크의 경우 Xcode, Cocoapods 또는 Carthage를 사용하여 의존성을 관리할 수 있습니다.<br>

xOS 계열을 벗어난 경우, Apple이 제공하는 Swift Package Manager를 사용할 수 있습니다.<br>

그러나 많은 Swift 개발자들이 iOS에 집중하고 있으며, Linux 대상에 대한 지원이 부족한 매력적인 패키지를 마추질 수도 있습니다<br>

기존의 생태계, 예를 들어 Python의 수천 개 패키지, Node.js의 npm, Ruby의 gems와 같은 것들과 경쟁하기는 어렵지만, 이것은 또한 당신의 관점에 달려 있습니다.<br>

패키지가 부족하다는 것은 Swift를 배우는 동안 커뮤니티와 생태계에 기여할 수 있는 신호일 수도 있습니다.<br>

Swift가 오픈 소스임에도 불구하고, Apple이 운전대를 잡고 있습니다.<br>

Apple이 Swift 지원을 중단할 걱정은 없지만, Swift 업데이트의 방향이나 속도에 항상 동의하지는 않을 수 있습니다.<br>

## 📏 Compile times

Swift는 성능이 높은 언어입니다.<br>

그러나 컴파일 과정은 상당히 느릴 수 있으며 개발자의 많은 시간을 소비할 수 있습니다.<br>

Swift는 LLVM 컴파일러를 통해 여러 단계로 컴파일됩니다.<br>

이것은 포드를 실행 할 때 최적화를 제공하지만, 일상적인 프로그래밍에서 느린 빌드 시간에 직면할 수 있으며, 실행 중인 코드를 빠르게 테스트하려고 할 때는 조금 지루할 수 있습니다<br>

모든 Swift 프로젝트가 단일 프로젝트는 아니며, 여러 프레임워크를 통합하게 되면 많은 코드를 컴파일하여 빌드를 생성해야 하므로 프로세스가 느려집니다.<br>

결국, 모든 프로그래밍 언어에는 장단점이 있으며, 일을 처리하기 위한 적절한 도구를 선택하는 문제입니다.<br>

저는 Swift가 깨끗하고 간결하며 고성능 코드로 아름다운 소프트웨어를 만드는 데 도움이 될 밝을 미래를 가지고 있다고 믿습니다!<br>

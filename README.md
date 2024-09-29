# Rules

# Руководство по оформлению кода для языка Swift

При написании руководства использованы следующие материалы:

- [Google's Swift Style Guide](https://google.github.io/swift/)
- [Raywenderlich Swift Style Guide](https://github.com/raywenderlich/swift-style-guide)
- [Airbnb Swift Style Guide](https://github.com/airbnb/swift)



## Основные моменты

- при написании кода рекомендуется руководствоваться [*Protocol-Oriented Programming*](https://habr.com/ru/post/358804/);
- код должен соответствовать *Apple Swift* [*API Design Guidelines*](https://swift.org/documentation/api-design-guidelines/#naming);
- длина строки не должна превышать **120 символов**;
- длина класса или структуры не должна превышать **400 строк**;
- длина функции или метода не должна превышать **40 строк**;
- длина файла не должна превышать **500 строк** (исключение - ресурсы, локализация); 
- кодировка, используемая в текстовых файлах - **UTF8**;
- отступ слева устанавливается только с помощью **табов**.

![[SSG-1678115567371.png|900]]
Для настройки указанных выше параметров перейдите в **Xcode -> Preferences -> Text Editing -> Indentation**.

![[SSG-1678115634961.png|900]]
Кроме того в **Xcode → Preferences → Text Editing → Editing** необходимо установить флаги для параметра: **Automatically trim trailing whitespace**

## Предупреждения компилятора

Код должен компилироваться без предупреждений, когда это возможно. Любые предупреждения, которые могут быть легко удалены автором, должны быть удалены.

**Исключения:** 
- `// TODO:`, `// FIXME:`;
- `deprecated`, если:
	- невозможна немедленная миграция на *API* замены;
	- невозможна немедленная миграция на *API* новой версии.

Максимально допустимое количество предупреждений - **120 warnings**.

## Организация .swift файла

### Копирайт

В начале каждого нового файла должна стоять шапка согласованного в проекте формата, пример:

``` swift
//
//	FileName.swift
//	Project name
//
//	Created by UserName on 01.01.2023.
//
```

### Структура файла

Как правило, большинство исходных файлов содержат только один тип верхнего уровня, особенно когда объявление типа большое. В одном файле с типом могут находиться расширения текущего типа. Модель, используемая только одним типом, определяется внутри него.

При описании протокола в первую очередь описываются требуемые свойства, потом инициализаторы, а после остальные необходимые функции. В классах/структурах, подписанных на протокол, требуемые свойства/методы следует описывать в том же порядке. 

Следует избегать неиспользуемый код. Мёртвый код, включая код шаблона *Xcode* и комментарии к заполнителям, должен быть удалён. 
Любой метод, реализация которого просто вызывает суперкласс, также должен быть удалён. Под этот критерий попадают любые пустые/неиспользуемые методы `UIApplicationDelegate`.

Каждый файл разделяется на смысловые блоки в следующем порядке:

```swift
class TaskViewController: UIViewController {

	// MARK: - Dependencies

	// внешние зависимости (например, service('s), manager('s))

	// MARK: - Outlets

	// MARK: - Public properties

	// public свойства используются только во фреймворках

	// MARK: - Open properties

	// open свойства используются только во фреймворках

	// MARK: - Internal properties

	// MARK: - Constants

	// константы, используемые внутри объекта

	// MARK: - Private properties

	// MARK: - Lifecycle

	// системные методы инициализации и деинициализации (например, init, init?(coder: NSCoder), deinit)
	// системные методы и свойства (например, viewDidLoad или viewDidLayoutSubviews)

	// MARK: - Public methods

	// public методы используются только во фреймворках

	// MARK: - Open methods

	// open методы используются только во фреймворкаx

	// MARK: - Internal methods

	// MARK: - Private methods

	// MARK: - Actions

	// действия c UI компонентами из Storyboard и Xib
}

// MARK: - <# NameProtocol #>

extension TaskViewController: NameProtocol {
	// реализация протокола <# NameProtocol #>
	// свойства и методы, которые описаны в протоколе
}
```

**Примечание:**
- под меткой `Lifecycle` в первую очередь описываются методы инициализации, если они есть.

### Импорты

- импорты модулей/подмодулей/деклараций не дублируются;
- импорт минимально достаточен, то есть все импортированные модули должны быть необходимы для компиляции файла, например, нет смысла импортировать `UIKit`, если достаточно `Foundation`;
- импорт подмодулей разрешён, если функция подмодуля недоступна при импорте модуля верхнего уровня, например, `UIKit.UIGestureRecognizerSubclass` необходимо явно импортировать, чтобы предоставить методы, которые позволяют клиентскому коду `UIGestureRecognizer` наследовать подклассы - они не видны при импорте `UIKit`.

**Рекомендуемый порядок импортов**:

- импорт модулей/подмодулей;
- индивидуальный импорт декларации (`class`, `enum`, `func`, `struct`, `var`);
- импорт с `@testable` (только в тестах).

```swift
import CoreLocation
import MyThirdPartyModule
import SpriteKit
import UIKit
import func Darwin.C.func
@testable import MyModuleUnderTest
```

### Расширения

Расширения `extension` могут использоваться для:
- дополнения системных классов и протоколов;
- определения стандартного поведения метода/функции;
- ограничения доступности методов для определенного типа;
- обеспечения соответствия существующего типа протоколу.

Каждый `extension` помечается меткой `// MARK: -` для придания большей читаемости кода.

```swift
// ✅ Принимается

extension String {

	var isUppercase: Bool {
		// ...
	}

	var isLowercase: Bool {
		// ...
	}
}

// MARK: - UITableViewDataSource

extension LoginViewController: UITableViewDataSource {
	// table view data source methods
}

// MARK: - UITableViewDelegate

extension LoginViewController: UITableViewDelegate {
	// table view delegate methods
}
```

```swift
// ❌ Не принимается

class LoginViewController: UITableViewDataSource, UITableViewDelegate {
	// ...

	// MARK: - UITableViewDataSource
	// table view data source methods

	// MARK: - UITableViewDelegate
	// table view delegate methods
}

// MARK: - UITableViewDataSource & UITableViewDelegate implementation
extension LoginViewController: UITableViewDataSource, UITableViewDelegate {

// table view data source methods

// table view delegate methods
}
```

**Примечания:** 
- расширения обеспечивают соответствие существующего типа только **одному** протоколу, при необходимости соответствия более чем одному протоколу следует объявить `typealias`;
- в расширении протокола **не допускается** определения стандартного поведения метода/функции, без выполнения какого-либо функционала;
- `extension` **не должны** использоваться для создания *mock*-заглушек в классах с тестами, для этого необходимо объявить самостоятельный *mock*-класс.

## Форматирование кода

### Запятые

Конечные запятые не используются в литералах массива и словаря. 

```swift
✅ let configurationKeys = ["bufferSize", "compression", "encoding"]

❌ let configurationKeys = ["bufferSize", "compression", "encoding", ]
```

### Точки с запятой

Точки с запятой `;` не используются ни для завершения строки, ни для разделения операторов. 

```swift
✅ let a = 0

❌ let a = 0;
```

### Круглые скобки

Круглые скобки не используются вокруг самого верхнего выражения, которое следует за `if`, `guard`, `while` или `switch`.

```swift
// ✅ Принимается

if x == 0 {
	print("x is zero")
}
```

```swift
// ❌ Не принимается

if (x == 0 || y == 1) {
	print("x is zero or y is one")
}
```

Круглые скобки используются при объявлении аргументов замыкания, только если в качестве аргумента передается кортеж.

```swift
// ✅ Принимается

let foo = { (bar: Int) in }

let foo = { bar in }
```

```swift
// ❌ Не принимается

let foo = { (bar) -> Bool in }

foo.bar { (x, y) in }
```

### Выравнивания

Выравнивание по вертикали применяется, если в нескольких строках объявления находятся:
- параметры функции;
- аргументы функции;
- элементы в литерале коллекции.

```swift
// ✅ Принимается

func validateFunction(
	_ file: SwiftLintFile,
	kind: SwiftDeclarationKind,
	dictionary: SourceKittenDictionary
) -> [StyleViolation] {
	// ...
}

object.foo(param1: 1, param2: bar, param3: false)

let abc = [
	"alpha": "a",
	"beta": "b",
	"gamma": "g",
	"delta": "d",
	"epsilon": "e"
]
```

```swift
// ❌ Не принимается

func validateFunction(
	_ file: SwiftLintFile, kind: SwiftDeclarationKind,
		dictionary: SourceKittenDictionary
) -> [StyleViolation] {
	// ...
}

object.foo(param1: 1, param2: bar
	param3: false)

let abc = ["alpha": "a",
	"beta": "b",
	"gamma": "g",
	"delta": "d",
	"epsilon": "e"
]
```

Замыкающая скобка имеет тот же отступ, что и начальная строка, в которой присутствует открывающая скобка, для:
- литералов массива и словаря;
- замыканий.

```swift
// ✅ Принимается

let x = [
	1,
	2
]

SignalProducer(values: [1, 2, 3])
	.startWithNext { number in
		print(number)
	}
```

```swift
// ❌ Не принимается

let x = [
	1,
	2
	]

SignalProducer(values: [1, 2, 3])
	.startWithNext { number in
		print(number)
}
```

### Горизонтальные отступы

Пробелы в конце строки не используются, также как и отступы в пустых строках.

```swift
✅ let name: String

❌ let name: String··
❌ ··
```

После запятой используется пробел, перед запятой пробела быть не должно.

```swift
✅ func abc(a: String, b: String) { }

❌ func abc(a: String ,b: String) { }
```

Между названием метода и круглыми скобками не используются пробелы.

```swift
✅ object.foo()

❌ object.foo ()
```

Стрелка возврата и тип возврата отделяются одним пробелом или находятся на отдельной строке.

```swift
✅ func abc() -> (Int, Int) {}

❌ func abc()-> (Int, Int) {}
❌ func abc() ->(Int, Int) {}
```

Открывающейся фигурной скобке предшествует один пробел в той же строке, что и объявление. 

```swift
✅ [].map() { $0 * scale }

❌ [].map(){ $0 }
```

Замыкающие выражения содержат по одному пробелу после открывающей и перед закрывающей фигурными скобками

```swift
✅ [].map ({ $0.description })

❌ [].map ({$0.description})
```

Между закрывающимися фигурной и круглой скобками `})` не используется пробел.

```swift
✅ [].map ({ $0.description })

❌ [].map ({ $0.description } )
```

Операторы заключаются в пробелы с обеих сторон:
- при определении;
- при использовании.

```swift
✅ func <|< <A>(lhs: A, rhs: A) -> A {}
✅ let foo = 1 > 2
✅ let foo = !false

❌ func <|<<A>(lhs: A, rhs: A) -> A {}
❌ let foo = 1+2
❌ let foo = 1   + 2
```

При объявлении классов/моделей с реализацией протоколов после имени объекта используются двоеточие и пробел `: `.

```swift
// ✅ Принимается

class BiometricAccessViewController: UIViewController {
	// ...
}
```

```swift
// ❌ Не принимается

class BiometricAccessViewController : UIViewController {
	// ...
}
```

Между именем и типом константы/переменной используется только один пробел.

```swift
✅ var isAuthorized: Bool

❌ var isAuthorized:   Bool
```

Двоеточие используется рядом **(без пробела)** с идентификатором при указании типа и рядом с ключом в словарных литералах.

```swift
// ✅ Принимается

let smartCities: [String: Point] = [
	"Moscow": Point(latitiude: 55.751244, longitude: 37.618423),
	"Hong Kong": Point(latitiude: 22.28552, longitude: 114.15769)
]
```

```swift
// ❌ Не принимается

let smartCities: [String : Point] = [
	"Moscow" : Point(latitiude: 55.751244, longitude: 37.618423),
	"Hong Kong": Point(latitiude: 22.28552, longitude: 114.15769)
]
```


### Вертикальные отступы

Вертикальный отступ присутствует в следующих местах:
1. между последовательными членами типа: инициализаторами, методами и вложенными типами;
2. между последовательно хранимыми свойствами или двумя случаями перечисления при использовании документации, а также если их объявления не помещаются целиком в одну строку, за исключением того, что:
	- пустая строка необязательна между двумя тесно связанными свойствами (например, частное хранимое свойство и связанное общедоступное вычисляемое свойство);
3. только по мере необходимости между операторами, чтобы организовать код в логические подразделы.

```swift
// ✅ Принимается

// Пример, иллюстрирующий п.1.

class TaskViewController: UIViewController {
	override func viewDidLoad() {
		// ...
	}

	override func viewWillAppear(_ animated: Bool) {
		// ...
	}
}

// Пример, иллюстрирующий п.2.

public enum IMQTTClientConnnectionAck: UInt8 {

	/// Соединение установлено.
	case accept = 0

	/// В соединении отказано, сервер не поддерживает протокол MQTT, запрошенный клиентом.
	case unacceptableProtocolVersion = 1

	/// В соединении отказано, идентификатор клиента не разрешен сервером.
	case identifierRejected = 2
}

final class TitleTableViewHeaderView: UITableViewHeaderFooterView {

	private lazy var titleLabel: UILabel = {
		let label = UILabel()
		label.numberOfLines = 0
		label.set(style: .header, weight: .bold)
		return label
	}()

	private lazy var subtitleLabel: UILabel = {
		let label = UILabel()
		label.numberOfLines = 1
		label.set(style: .header, weight: .medium)
		return label
	}()
}

enum Number {
	case one
	case two
	case three
}

struct ResultModel {
	var resultCode: Int
	var message: String
}

final class MQTTClientFrameworkAdapter: NSObject, IMQTTClient {

	// MARK: - Private properties

	private let transport: MQTTCFSocketTransport
	private let session: MQTTSession
}

final class LineaCase {

	var isConnected: Bool {
		return _isConnected
	}
	private var _isConnected: Bool = false
}

// Пример, иллюстрирующий п.3.

enum HTTPStatus: Int {
	case ok = 200

	case badRequest = 400
	case notAuthorized = 401
	case paymentRequired = 402
	case forbidden = 403
	case notFound = 404

	case internalServerError = 500
}
```

```swift
// ❌ Не принимается

public enum IMQTTClientConnnectionAck: UInt8 {
	/// Соединение установлено.
	case accept = 0
	/// В соединении отказано, сервер не поддерживает протокол MQTT, запрошенный клиентом.
	case unacceptableProtocolVersion = 1
	/// В соединении отказано, идентификатор клиента не разрешен сервером.
	case identifierRejected = 2
}

class TaskViewController: UIViewController {
	override func viewDidLoad() {
		// ...
	}
	override func viewWillAppear(_ animated: Bool) {
		// ...
	}
}
```

**Примечания:**
- внутри функции допускаются вертикальные отступы для разделения блоков функциональности, слишком большое количество блоков указывает на то, что метод должен быть разделён на несколько;
- файлы имеют одну завершающую новую строку;
- не используются нескольких пустых строк подряд;
- вертикальные отступы не используются перед закрывающими фигурными скобками.

```swift
// ✅ Принимается

SignalProducer(values: [1, 2, 3])
	.startWithNext { number in
		print(number)
	}

let numbers = [
	1,
	2,
	3
]
```

```swift
// ❌ Не принимается

SignalProducer(values: [1, 2, 3])
	.startWithNext { number in
		print(number)

	}

let numbers = [
	1,
	2,
	3

]
```

### Переносы строк

Операторы `else` и `catch` определяются в одной строке через один пробел после предыдущего объявления. 
```swift 
// ✅ Принимается 

guard
	let taskView = taskView,
	let appDelegate = appDelegate,
	let userInfo = notification.userInfo,
	let message = userInfo["message"] as? String else { return }
	
do {
	initialRequest = try convertible.asURLRequest()
	try initialRequest.validate()
} catch {
	rootQueue.async { request.didFailToCreateURLRequest(with: error) }
	return
}
```

```swift
// ❌ Не принимается

guard
	let taskView = taskView,
	let appDelegate = appDelegate,
	let userInfo = notification.userInfo,
	let message = userInfo["message"] as? String 
else { return }

guard
	let taskView = taskView,
	let appDelegate = appDelegate,
	let userInfo = notification.userInfo,
	let message = userInfo["message"] as? String 
	else { return }

do {
	initialRequest = try convertible.asURLRequest()
	try initialRequest.validate()
}
catch {
	rootQueue.async { request.didFailToCreateURLRequest(with: error) }
	return
}
```

При объявлении метода/функции с числом параметров большим чем **3** или превышающим [**лимит ширины строки**](#line-width), используется перенос строки:
- после круглой открывающейся скобки `(`;
- перед каждым параметром метода;
- перед круглой закрывающейся скобкой `)`.

```swift
// ✅ Принимается

public func index<Elements: Collection, Element>(
	of element: Element, 
	in collection: Elements
) -> Elements.Index?
	where Elements.Element == Element, Element: Equatable { 
	// Превышение числа символов в строке
}

func presentUpdateViewController(
	open: @escaping (URL) -> Void,
	present: @escaping (UIViewController) -> Void,
	onResponse: @escaping () -> Void,
	onError: @escaping (Error) -> Void,
	onDismiss: @escaping () -> Void
) -> (_ bundleIdentifier: String, _ current: AppVersion) -> Void {
	// Количество аргументов функции > 3
}
```

```swift
// ❌ Не принимается

public func index<Elements: Collection, Element>(of element: Element, in collection: Elements) -> Elements.Index? where Elements.Element == Element, Element: Equatable {
	// Превышение числа символов в строке
}
```

При использовании метода/функции с числом параметров большим чем **3** или превышающим [**лимит ширины строки**](#line-width), используется перенос строки:
- после круглой открывающейся скобки `(`;
- перед каждым аргументом метода; 
- перед круглой закрывающейся скобкой `)`.

```swift
// ✅ Принимается

UIView.animateKeyframes(
	withDuration: 0.3,
	delay: 0,
	options: .layoutSubviews,
	animations: {
		self.someLabel.alpha = 1.0
		// ...
	},
	completion: { isFinished in
		if isFinished {
			// ...
		 }
	}
)
```

```swift
// ❌ Не принимается

UIView.animateKeyframes(withDuration: 0.3,
						delay: 0,
						options: .layoutSubviews,
						animations: { [weak self] in
							guard let self = self else { return }
							// ...
						}, completion: { isFinished in
							if isFinished {
								// ...
							}
						})
```

При создании массива/словаря с числом элементов большим чем **3**, или превышающим [**лимит ширины строки**](#line-width) используется перенос строки:
- после открывающейся квадратной скобки `[`;
- перед каждым элементом;
- перед закрывающейся квадратной скобкой `]`.

```swift
// ✅ Принимается

let array2 = [
	element1,
	element2,
	element3,
	element4,
	element5
]
```

```swift
// ❌ Не принимается

let array2 = [element1,
				element2,
				element3,
				element4,
				element5]
```

Отключение или включение правил *SwiftLint* описываются в одну строку через пробел. Команды *SwiftLint* `disable` излишни, если отключенное правило не вызвало бы нарушения в отключенной области.

```swift
// ✅ Принимается

// swiftlint:disable cyclomatic_complexity function_body_length
func update(with dict: [String: Any]) {
	// ...
}
```

```swift
// ❌ Не принимается

// swiftlint:disable cyclomatic_complexity 
// swiftlint:disable function_body_length
func update(with dict: [String: Any]) {
	// ...
}
```

Связанные вызовы функций описываются либо в одной строке, либо по одному на строку, если не достигнут [**лимит ширины строки**](#line-width).

```swift
// ✅ Принимается

let evenSquaresSum = [20, 17, 35, 4].filter { $0 % 2 == 0 }.map { $0 * $0 }.reduce(0, +)

let chain = a
	.b(1, 2, 3)
	.c { blah in
		print(blah)
	}
	.d()
```

```swift
// ❌ Не принимается

let evenSquaresSum = a.b(1, 2, 3)
	.c(2, 3, 4).d()

let evenSquaresSum = a.b(1, 2, 3).c { blah in
		print(blah)
	}
	.d()
```

## Организация кода

### Числовые литералы

При написании длинных числовых литералов используются разделители `_`, это необходимо для группировки цифр, чтобы облегчить их читаемость.

**Рекомендуемые группировки:**
- три цифры для десятичного числа (разделители тысяч), четыре цифры для шестнадцатеричного числа;
- четыре цифры для двоичных литералов.

```swift
// ✅ Принимается

let foo = 1_000_000.000_000_1
let binary = 0b1000_0001
```

```swift
// ❌ Не принимается

let foo = 1000000.000_000_1
let foo = 1000
let foo = 10_00
```

### Объявление типа

Тип переменной не указывается в явном виде в объявлении, кроме случаев, когда:
- литерал используется для инициализации значения типа, отличного от его значения по умолчанию;
- тип не может быть выведен из контекста иначе;
- время разрешения типа занимает более **100 мс** (для ускорении времени сборки проекта).

```swift
// ✅ Принимается

let y: Character = "a"
let shortLongitude: Float = 0.3242689
let maximumWidth: CGFloat = 106.5
let message = "Click the button"
```

```swift
// ❌ Не принимается

let b = Character("a")
let shortLatitude = Float(-1.579968)
let maximumWidth: Double = 106.5
let message: String = "Click the button"
```

Предпочтительно использование сокращенных версий объявлений типов над полным синтаксисом обобщений. Длинные формы `Array<Element>`, `Dictionary<Key, Value>` и `Optional<Wrapped>` пишутся только тогда, когда этого требует компилятор.

```swift
// ✅ Принимается

var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?

func enumeratedDictionary<Element>(
	from values: [Element],
	start: Array<Element>.Index? = nil
) -> [Int: Element] {
	// ...
}
```

```swift
// ❌ Не принимается

var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>

func enumeratedDictionary<Element>(
	from values: Array<Element>,
	start: Optional<Array<Element>.Index> = nil
) -> Dictionary<Int, Element> {
	// ...
}
```

В объявлениях типов функций (таких как замыкания или переменные, содержащие ссылку на функцию) возвращаемый тип всегда записывается как `Void` вместо `()`.

```swift
// ✅ Принимается

let abc: () -> Void = {}

func foo(completion: () -> Void)

let foo: (ConfigurationTests) -> () throws -> Void
```

```swift
// ❌ Не принимается

let abc: () -> () = {}

let abc: () -> (Void) = {}

func foo(completion: () -> ())

func foo(completion: () -> (Void))

let foo: (ConfigurationTests) -> () throws -> ()
```

В функциях, объявленных с `func` и ключевым словом, возвращаемый тип `Void` полностью опускается.

```swift
// ✅ Принимается

func foo() {}

func foo() -> Int {}

func foo() -> Int -> Void {}

let foo: (Int) -> Void
```

```swift
// ❌ Не принимается

func foo() -> Void {}

protocol Foo {
	func foo() -> Void
}

func foo() -> () {}
```

Вместо конструкции `Void ->` используется конструкция `() ->`.

```swift
// ✅ Принимается

let abc: () -> Void = {}

func foo(completion: () -> Void)

func foo(completion: () thows -> Void)
```

```swift
// ❌ Не принимается

let abc: (Void) -> Void = {}

func foo(completion: (Void) -> Void)

func foo(completion: (Void) throws -> Void)
```

### Атрибуты

Атрибуты (такие как `@availability` или `@objc`) записываются в отдельной строке непосредственно перед объявлением, к которому они применяются.

```swift
// ✅ Принимается

@available(iOS 13.0, *)
public func coolNewFeature() {}

@objc
func doSomeThing() {}
```

``` swift
// ❌ Не принимается

@available(iOS 12.0, *) public func coolNewFeature() {}

@objc func doSomeThing() {}
```

`@IBOutlet` размещаются в одной строке с объявлением.

```swift
// ✅ Принимается

@IBOutlet private var tableView: UITableView!
```

``` swift
// ❌ Не принимается

@IBOutlet
private var tableView: UITableView!
```

Атрибут `Objective-C (@objc)` не добавляется в объявлении, если он избыточен.

```swift
✅ @objc private var foo: String? {}
✅ @IBOutlet private var tableView: UITableView!

❌ @objc @IBAction private func foo(_ sender: Any) {}
```

Атрибуты следует располагать в следующем порядке:

- уровень доступа: `open`, `public`, `internal`, `fileprivate`, `private`;
- уровень доступа к установщику;
- `override`;
- `lazy`;
- `final`;
- `required`, `convenience`;
- `static`, `class`;
- `weak`, `unowned`.

```swift
// ✅ Принимается

@objc
public private(set) weak var foo: Bar?

public final class Foo {}

class Foo {
	internal lazy var bar: String = "foo"
}

public override static func foo() -> Int {}
```

``` swift
// ❌ Не принимается

private(set) public weak var foo: Foo?

override static public func foo() -> Int {}

weak open var weakBar: NSString?
```

Проверки доступности или атрибуты не используют более старые версии, удовлетворяющие цели развертывания.

```swift
✅ if #available(iOS 13, *) {}

❌ if #available(iOS 10, *) {}
```

Уровень доступа установщика не указывается явно, если он совпадает с уровнем доступа к переменной.

```swift
// ✅ Принимается

public private(set) var foo: Int

public var foo: Int
```

``` swift
// ❌ Не принимается

private private(set) var foo: Int

class A {
	internal(set) var value: Int
}
```

### Свойства

Доступы пишутся в согласованном порядке (сначала `get` потом `set`):
- в вычисляемых свойствах и индексах; 
- при объявлении свойств в протоколах.

```swift
// ✅ Принимается

var totalCost: Int {
	get { totalCost }
	set { totalCost = newValue }
}

var bar: String { get set }
```

```swift
// ❌ Не принимается

var totalCost: Int {
	set { totalCost = newValue }
	get { totalCost }
}

var bar: String { set get }
```

Если для `get` и/или `set` выражение простое и не превышает [**лимит ширины строки**](#line-width), то оно записывается в одну строку.

```swift
// ✅ Принимается

var totalCost: Int {
	get { totalCost }
	set { totalCost = newValue }
}
```

```swift
// ❌ Не принимается

var totalCost: Int {
	get { 
		totalCost
	}
	set {
		totalCost = newValue
	}
}
```

Для свойств, доступных только для чтения, ключевое слово `get` опускается.

```swift
// ✅ Принимается

var foo: Int {
	return 20
}
```

```swift
// ❌ Не принимается

var foo: Int {
	get { return 20 }
}
```

При переопределении `set` для установки нового значения используется `newValue`.

```swift
// ✅ Принимается

var aValue: String {
	get { Persister.shared.aValue }
	set { Persister.shared.aValue = newValue }
}

var aValue: String {
	get { Persister.shared.aValue }
	set { _ = newValue }
}
```

```swift
// ❌ Не принимается

var aValue: String {
	get { Persister.shared.aValue }
	set { Persister.shared.aValue = aValue }
}

var aValue: String {
	get { Persister.shared.aValue }
	set { }
}
```

### Замыкания

Тело замыкания не превышает **20 строк**.
Неиспользуемые ссылки в списке захвата удаляются.

```swift
// ✅ Принимается

service.processStatus(ware: fullWare, status: status) { [weak self] _ in
	guard let self = self else { return }
	self.openWare(ware: WareInfoModel(from: fullWare), readPrice: fullWare.normPrice)
}
```

```swift
// ❌ Не принимается

service.processStatus(ware: fullWare, status: status) { [weak self] success in
	print(success)
}
```

Неиспользуемые параметры в замыкании заменяются на `_`.

```swift
// ✅ Принимается

service.something { number, idx in
	return number * idx
}

service.something { number, _ in
	return number
}
```

```swift
// ❌ Не принимается

service.something { number, idx in
	return number
}
```

Параметры замыкания находятся на той же строке, что и открывающая фигурная скобка `{`.

```swift
// ✅ Принимается

[1, 2].map { number in
	number + 1 
}
```

```swift
// ❌ Не принимается

[1, 2].map {
	number in
		number + 1
}
```

Для замыканий с одним аргументом и вычисляемых свойств, где контекст ясен, используется неявный `return`.

```swift
// ✅ Принимается

var plainCount: Int { 120 }

foo.map { $0 + 1 }
```

```swift
// ❌ Не принимается

var plainCount: Int { 
	return 120
}

foo.map { return $0 + 1 }
```

Если функция в качестве аргумента принимает единственное замыкание, которое является последним, то она вызывается с использованием конечного синтаксиса замыкания.

```swift
// ✅ Принимается

Timer.scheduledTimer(timeInterval: 30, repeats: false) { _ in
	print("Timer done!")
}
```

```swift
// ❌ Не принимается

Timer.scheduledTimer(timeInterval: 30, repeats: false, block: { timer in
	print("Timer done!")
})
```

Конечный синтаксис замыкания не используется при передаче в функцию двух и более замыканий.

```swift
// ✅ Принимается

foo.something(param1: { $0 }, param2: { $0 + 1 })

UIView.animate(withDuration: 1.0) {
	someView.alpha = 0.0
}
```

```swift
// ❌ Не принимается

foo.something(param1: { $0 }) { $0 + 1 }

UIView.animate(withDuration: 1.0, animations: {
	someView.alpha = 0.0
}) { _ in
	someView.removeFromSuperview()
}
```

Когда функция, вызываемая с использованием конечного синтаксиса замыкания, не принимает других аргументов, пустые скобки `()` после имени функции не используются.

```swift
✅ let squares = [1, 2, 3].map { $0 * $0 }
✅ [1, 2].reduce(0) { $0 + $1 }

❌ let squares = [1, 2, 3].map() { $0 * $0 }
```

### Перечисления

Случаи перечисления следуют в логическом порядке, который автор может объяснить. Если нет очевидного логического порядка, используется лексикографический порядок, основанный на названиях `case`. Случай по умолчанию (`unknown`, `undefined` и тп.) располагается либо первым, либо последним.

```swift
// ✅ Принимается

public enum HTTPStatus: Int {
	case ok = 200

	case badRequest = 400
	case notAuthorized = 401
	case paymentRequired = 402
	case forbidden = 403
	case notFound = 404

	case internalServerError = 500
}
```

```swift
// ❌ Не принимается

public enum HTTPStatus: Int {
	case badRequest = 400
	case forbidden = 403
	case internalServerError = 500
	case notAuthorized = 401
	case notFound = 404
	case ok = 200
	case paymentRequired = 402
}
```

Перед **каждым элементом** перечисления пишется ключевое слово `case` независимо от количества элементов.

```swift
// ✅ Принимается

public enum Token {
	case comma
	case semicolon
	case identifier
}
```

```swift
// ❌ Не принимается

public enum Token {
	case comma, semicolon, identifier
}
```

В `enum` не может содержаться несколько случаев с одним и тем же именем.

```swift
// ✅ Принимается

enum PictureImport {
	case addImage(image: UIImage)
	case addData(data: Data)
}
enum A {
	case add(image: UIImage)
}
enum B {
	case add(image: UIImage)
}
```

```swift
// ❌ Не принимается

enum PictureImport {
	case add(image: UIImage)
	case addURL(url: URL)
	case add(data: Data)
}
```

Строковое значение перечисления опускается, если оно совпадает с именем перечисления.

```swift
// ✅ Принимается

enum Numbers: String {
	case one
	case two
}
```

```swift
// ❌ Не принимается

enum Numbers: String {
	case one = "one"
	case two = "two"
}
```

Когда все `case` должны быть `indirect`, перечисление само объявляется `indirect`, ключевое слово опускается лишь в отдельных случаях.

```swift
// ✅ Принимается

public indirect enum DependencyGraphNode {
	case userDefined(dependencies: [DependencyGraphNode])
	case synthesized(dependencies: [DependencyGraphNode])
}

public enum DependencyGraphNode {
	case indirect userDefined(dependencies: [DependencyGraphNode])
	case synthesized(dependencies: [DependencyGraphNode])
}
```

```swift
// ❌ Не принимается

public enum DependencyGraphNode {
	case indirect userDefined(dependencies: [DependencyGraphNode])
	case indirect synthesized(dependencies: [DependencyGraphNode])
}
```

### Операторы

Вместо выполнения операции и последующего присваивания используются сокращенные операторы `+=`, `-=`, `*=`, `/=`. 

```swift
✅ foo += variable
✅ self.foo = foo * variable

❌ foo = foo - variable
❌ self.aProperty = self.aProperty - variable
```

Вместо присваивания обратного значения `someBool = !someBool` используется функция `.toggle()`.

```swift
✅ someBool.toggle()

❌ someBool = !someBool
```

При использовании оператора сравнения (и других) переменная всегда располагается слева, а константа - справа.

```swift
✅ guard foo >= 42 else { return }

❌ guard 42 <= foo else { return }
```

Во всех случаях проверки условий для инструкций `if` и `guard` используется `,` вместо `&&`.

```swift
// ✅ Принимается

if let item = items?.first, item.name == "taskID" {
	// ...
}

guard let end = to, end > 0, length > 0 else {
	return ""
}
```

```swift
// ❌ Не принимается

if let item = items?.first, item.name == "taskID" && item.plan == 2 {
	// ...
}

guard let end = to, end > 0 && length > 0 else {
	return ""
}
```

#### Оператор `switch`

- для `switch` используется стандартное форматирование строк *IDE Xcode*, т.е. операторы `case` имеют отступ на том же уровне, что и оператор `switch`, к которому они принадлежат;
- операторы внутри блоков `case` всегда переносятся на новую строку и имеют отступ в `tab` от уровня `case`;
- ненужные операторы прерывания опускаются;

```swift
// ✅ Принимается

switch order {
case .ascending:
	print("Ascending")
case .descending:
	print("Descending")
case .same:
	print("Same")
}
```

```swift
// ❌ Не принимается

switch order {
	case .ascending:
		print("Ascending")
	case .descending:
		print("Descending")
		break
	case .same: break
}
```

- аргументы не указываются при сопоставлении перечислений со связанными значениями, если они не используются;

```swift
// ✅ Принимается

switch foo {
case .bar: 
	print("Hello World")
}

switch foo {
case .bar(let x): 
	print(x)
}
```

```swift
// ❌ Не принимается

switch foo {
case .bar(_):
	print("Hello World")
}

switch foo {
case .bar():
	print("Hello World")
}
```

- внутри `case` не используется оператор `switch`, если это необходимо, то его следует вынести в отдельную функцию;
- когда несколько `case` в `switch` выполняют одни и те же операторы, шаблоны объединяются в диапазоны или списки, разделённые запятыми;
- **не рекомендуется** использовать `fallthrough`, если это необходимо, то `fallthrough` используется, только если `case` содержит хотя бы одно другое утверждение.

```swift
// ✅ Принимается

switch value {
case 1:
	print("one")
case 2...4:
	print("two to four")
	fallthrough
case 5, 7:
	print("five or seven")
default:
	break
}
```

```swift
// ❌ Не принимается

switch value {
case 1:
	print("one")
case 2:
	fallthrough
case 3:
	fallthrough
case 4:
	print("two to four")
default:
	break
}
```

#### Тернарный оператор

Тернарный оператор используется только тогда, когда он увеличивает ясность и чистоту кода по сравнению с `if`. Если достигнут [**лимит ширины строки**](#line-width), то перед `?` и `:` ставить **перенос строки**. При использовании тернарного оператора условие выражается переменной типа `Bool`.

```swift
// ✅ Принимается

let isShowScanScreen = getBoolValue(forKey: Key.isShowScanScreen.rawValue)

let switchMessageText = isShowScanScreen ? "Отключен ❌" : "Включен ✅"

let switchMessageText = isShowScanScreen
	? L10n.MainTaskPresenter.SwitchMessage.Disabled.text
	: L10n.MainTaskPresenter.SwitchMessage.Enabled.text
```

```swift
// ❌ Не принимается

let result = a > b ? x = c > d ? c : d : y

let switchMessageText = getBoolValue(forKey: Key.isShowScanScreen.rawValue) ? "Отключен ❌" : "Включен ✅"

let switchMessageText = isShowScanScreen ? L10n.MainTaskPresenter.SwitchMessage.IsShowScanScreen.text 
	: L10n.MainTaskPresenter.SwitchMessage.IsNotShowScanScreen.text
```

### Комментарии

- комментарии используют формат двойной косой черты `//`;
- метка `MARK` пишется в определенном формате: `// MARK: -`;
- для комментариев используется один пробел после косой черты;
- после каждого использования `// MARK: -` добавляется пустая строка.

```swift
✅ // Something

❌ //Something
❌ /// Triple slash comment
```

**Примечание:**
- закомментированный код удаляется, если это невозможно сразу, то такой код помечается маркой `// FIXME: -`.

## Именование

Для именования типов/классов/переменных не используются:
- `main`;
- эмодзи;
- цифры, кроме случаев использования общепринятых наименований/аббревиатур, в которых присутствуют цифры;
- кириллица. 

### Именование файлов исходного кода

- все исходные файлы *Swift* имеют расширение *.swift*;
- имя исходного файла лучше всего описывает основной объект, который он содержит;
- файл, который содержит один тип, имеет имя этого типа;
- имя протокола имеет префикс `I`;
- в названии протокола не используется суффикс `Protocol`;
- файл, который расширяет существующий тип с помощью соответствия протоколу, именуется комбинацией имени типа и протокола, соединенных знаком плюс `+`;
- файл, который расширяет существующий тип и добавляет метод/вычисляемую переменную, именуется комбинацией имени типа и основного предназначения метода/вычисляемой переменной, соединенных знаком плюс `+`;
- файл, который расширяет примитивы типа языка (`String`, `Int`, ...) именуется комбинацией имени типа и названия метода/переменной, которой дополняется, соединенных знаком плюс `+`;
- для более сложных ситуаций, используется лучшее суждение.

```swift
✅ IWareInfoPresenter
❌ WareInfoPresenterProtocol

extension UILabel {
	func set(style: FontStyle, weight: UIFont.Weight? = nil, color: UIColor? = .black) {
		// ...
	}
}

✅ UILabel+Style.swift
❌ UILabel+Extension.swift
❌ UILabel+FontWeight.swift
```

### Именование типов

- для именования типов используются ёмкие краткие имена без сокращений;
- заглавные буквы используются в начале имени типа (класс, протокол и т.п.), строчные буквы для всего остального;
- общепринятые сокращения/аббревиатуры (*ID*, *EAN13*, *HTTP*, *ADSL*, *ASCII*, *RGB*, *BMP*) в имени типа пишутся заглавными буквами;
- имена типов имеют длину от **3** до **40 символов**.

```swift
// ✅ Принимается

var barcodeEAN13 = "1234567890123"
var connectToMQTTBroker = true

class MQTTProvider: IMQTTProvider {
	// ...
}
```

```swift
// ❌ Не принимается

var barcodeEan13 = "1234567890123"
var connectToMqttBroker = true

class MqttProvider: IMqttProvider {
	// ...
}
```

#### Универсальные типы

- для универсальных типов используются описательные названия;
- если имя типа не имеет значимых отношений или роли, используется традиционная одиночная прописная буква (`T`, `U`, или `V`) или `Element`: `struct Stack<Element> { ... }`;
- имя универсального типа содержит только буквенно-цифровые символы, начинается с символа верхнего регистра и имеет длину от **1** до **20 символов**. 

```swift
✅ func foo<T>() -> T {}
✅ func configureWith(data: Either<MessageThread, (project: Project, backing: Backing)>)

❌ func foo<T, U_Foo>(param: U_Foo) -> T {}
❌ class Foo<type> {}
```

#### Кортежи

- параметры кортежей `tuples` именуются;
- если в кортеже больше двух `tuple members`, то объявляется `typealias` с именами его элементов.

```swift
✅ let foo: (start: Int, end: Int)

❌ let foo: (Int, Int, Int)
❌ let foo: (start: Int, end: Int, value: String)
```

### Статические и классовые свойства

Статические свойства и свойства класса, которые возвращают экземпляры объявленного типа, не имеют суффикса с именем типа.

```swift
// ✅ Принимается

public class UIColor {
	public class var red: UIColor {
		// ...
	}
}

public class URLSession {
	public class var shared: URLSession {
		// ...
	}
}
```

```swift
// ❌ Не принимается

public class UIColor {
	public class var redColor: UIColor {
		// ...
	}
}

public class URLSession {
	public class var sharedSession: URLSession {
		// ...
	}
}
```

### Именование переменных

Имена переменных/констант/параметров имеют длину от **3** до **40 символов**.

**Исключения:**
- `x`, `y`, `id`.

```swift
// ✅ Принимается

var index: Int
var y: Int

func getValue(array: [String], for index: Int) -> String

func findTask(with id: UInt) -> IDrivenTask?

func isTransitionSupported(task: Task, to status: TaskStatuses) -> Bool
```

```swift
// ❌ Не принимается

let i: Int
func getValue(array: [String], for i: Int) -> String 
```

- в каждой строке объявляется только одна переменная, объявление нескольких переменных, разделенных запятой, не допускается;
- имена констант и переменных соответствуют стилю **lowerCamelCase**;
- для именования переменных используются ёмкие краткие имена без сокращений;
- сокращения используются только для общепринятых аббревиатур, например, `url`;
- общепринятые аббревиатуры/сокращения в именах переменных пишутся заглавными буквами, например: `wareID`, `rawZPLData`, кроме случаев, когда они используются в начале имени переменной, тогда аббревиатуры/сокращения пишутся строчными буквами (`ipAddress`, `ean13Barcode`, `httpSession`, `asciiCode`, `bmpImage`);
- булевы типы и методы их возвращающие читаются как утверждения, например: `line.isEmpty` или `line1.intersects(line2)`;
- `outlets` объекты именуются из двух частей: "Название" + "Класс объекта без префикса `UI`".

```swift
// ✅ Принимается

var isActiveLabel: Bool
let smile = "😊"
let deltaX = newX - previousX

func didChangeUserName() {
	// ...
}

var ean13Barcode = "1234567890123"
var ean13 = "1234567890123"
let mqttProvider: IMQTTProvider?
var wareID: String

@IBOutlet private var priceImageView: UIImageView!
```

```swift
// ❌ Не принимается

var name = "Tom", surname = "Smith"

var actLabel: Bool
let diff = newX - previousX

func didChangUName() {
	// ...
}

var EAN13Barcode = "1234567890123"
var Ean13 = "1234567890123"
var wareId: String

@IBOutlet private var priceUIImageView: UIImageView!
```

### Именование функций и инициализаторов

- допускается не более **5 параметров** функции, если это условие не выполняется, то следует пересмотреть функцию, выделить `typealias`, дополнительный тип и т.д.;
- функции именуются соответственно их действию:
- если метод мутирующий, то используется глагол: `sequence.sort()`, то есть метод меняет саму *sequence*;
- если метод не мутирующий, то используется причастие: `sequence.sorted()`, метод возвращает отсортированную последовательность, но сама исходная *sequence* при этом не изменяется;
- параметры в функциях именуются так, чтобы вместе с именем метода они читались как документация к методу;
- фабричные методы начинаются со слова `make`, к которому добавляется название создаваемого объекта;
- методы, предназначенные для пересоздания объекта с определенной конфигурацией, начинаются с `remake`, к которому добавляется название пересоздаваемого объекта; 
- метод, в котором настраивается *UI*, именуется `setup()`, если настройка занимает большое число строк и/или необходимо логическое деление, то код разбивается на методы, названия которых имеют префикс `setup`, а сами они вызываются из метода `setup()`;
- методы получения каких-либо данных начинаются с `get`, вычисляемые свойства именуются без `get`;
- для ясности аргументы инициализатора, которые непосредственно соответствуют сохранённому свойству, имеют то же имя, что и свойство, явное `self` используется во время присваивания для устранения неоднозначности;
- допускается использование `_` перед именами первых параметров инициализаторов и методов, а также перед замыканиями;
- аргументы по умолчанию располагаются ближе к концу сигнатуры.

```swift
// ✅ Принимается

func makeModule() -> Module {}

func setup() {
	setupTableView()
	setupSearchView()
}

func getStatus(with plainCount: Int)

func foo(baz: String, bar: Int = 0) {}

public struct Person {
	public let name: String
	public let phoneNumber: String

	public init(_ name: String, phoneNumber: String) {
		self.name = name
		self.phoneNumber = phoneNumber
	}
}

let person = Person("Jo", phoneNumber: "9260000000")
```

```swift
// ❌ Не принимается

func createModule() -> Module {}

func configureSubviews() {
	func updateUI()
}

func status(with plainCount: Int)

func foo(bar: Int = 0, baz: String) {}

public struct Person {
	public let name: String
	public let phone: String

	public init(_ name: String, _ phoneNumber: String) {
		self.name = name
		self.phone = phoneNumber
	}
}

let person = Person.init("Jo", "9260000000")
let person = Person("Jo", "9260000000")
```

#### Перегрузки

Из-за неоднозначности в контексте использования не используются перегрузки названий функций:
- по возвращаемому типу;
- по имени их аргумента замыкания (это предотвращает использование конечного синтаксиса замыкания - когда метка отсутствует).

Перегрузки объявляются последовательно в одном блоке кода без вставок других полей между ними, если:
- тип имеет несколько инициализаторов;
- файл/тип имеет несколько функций с одним и тем же базовым именем.

**Исключение:**
- инициализаторы/функции переносятся в соответствующее расширение, если они определены в протоколе.

```swift
// ✅ Принимается

protocol Greeting {
	func greetFriendly(_ nameProvider: () -> String)
}

class Person {
	func greetEnthusiastically(_ nameProvider: () -> String) {
		// ...
	}

	func greetApathetically(_ nameProvider: () -> String) {
		// ...
	}
}

extension Person: Greeting {
	func greetFriendly(_ nameProvider: () -> String) {
		// ...
	}
}

greetEnthusiastically { "John" }
greetApathetically { "not John" }
greetFriendly { "John" }
```

```swift
// ❌ Не принимается

protocol Greeting {
	func greet(friendly nameProvider: () -> String)
}

class Person: Greeting {
	func greet(enthusiastically nameProvider: () -> String) {
		// ...
	}

	func greet(friendly nameProvider: () -> String) {
		// ...
	}

	func greet(apathetically nameProvider: () -> String) {
		// ...
	}
}

greet { "John" }
```

## Практики программирования

### Контроль доступа

- ограниченный уровень доступа `internal`, `fileprivate` или `private` используется для сокрытия информации;
- `internal` пишется только по необходимости, в остальных случаях оно используется как значение по умолчанию;
- определения имеют такой же или более низкий уровень доступа, чем их родительский элемент;
- в расширении не указывается явный уровень доступа, за исключением расширения с уровнем доступа `private`;
- каждый член расширения имеет свой уровень доступа, который указывается явно, если он отличается от уровня по умолчанию;
- чтобы избежать утечки `UIKit` на более высокие уровни, `IBOutlets` имеют `private` доступ, допускается ограничение доступа только к `setter`;
- `IBActions` имеют `private` доступ.

```swift
// ✅ Принимается

extension String {
	public var isUppercase: Bool {
		// ...
	}

	public var isLowercase: Bool {
		// ...
	}
}

private extension String {
	var isUppercase: Bool {
		// ...
	}

	var isLowercase: Bool {
		// ...
	}
}

@IBAction private func barButtonTapped(_ sender: UIButton) {}

@IBOutlet private(set) var label: UILabel!
```

``` swift
// ❌ Не принимается

public extension String {
	var isUppercase: Bool {
		// ...
	}

	var isLowercase: Bool {
		// ...
	}
}

@IBAction func barButtonTapped(_ sender: UIButton) {}

@IBOutlet var label: UILabel!
```

### Пространство имен

- глобальные константы не используются, они всегда находятся внутри пространства имён *namespace*, для реализации подобного *namespace* используется `enum` без значений;
- типы, используемые для размещения только статических членов, реализуются как перечисления без значений, для предотвращения создания экземпляров;
- если при создании модели (структуры, перечисления) предполагается ее использование только одним модулем, то она создается внутри него.

```swift
// ✅ Принимается

enum SomeEnum {
	static func foo1() {}
	static func foo2() {}
}

class Parser {
	enum Error: Swift.Error {
		case invalidToken(String)
		case unexpectedEOF
	}

	func parse(text: String) throws {
		// ...
	}
}
```

```swift
// ❌ Не принимается

class SomeClass {
	static func foo1() {}
	static func foo2() {}
}

class Parser {
	func parse(text: String) throws {
		// ...
	}
}

enum ParseError: Error {
	case invalidToken(String)
	case unexpectedEOF
}
```

### Константы

- глобальные константы создаются внутри модели, при этом имя модели соответствует конкретной категории констант `{ModelCategory}Constants`, а имя константы позволяет однозначно идентифицировать ее использование;
- если не предполагается использование константы сторонними сущностями (классами, структурами, перечислениями), то она создается внутри сущности;
- "магические" числа не используются с `case` и `return`;
- перед объявлением констант через строку ставится `// MARK: - Constants`;
- технические строки (пути к файлам, имена ресурсов и подобное) выносятся в константы.

**Исключение:**
- кастомные отступы/переходы для дизайна макетов.

```swift
// ✅ Принимается

// MARK: - Constants

enum TimeConstants {
	static let secondsPerMinute = 60
	static let secondsPerHour = 3_600
}
var seconds = 5 * TimeConstant.secondsPerMinute

enum Constant {
	static let filePath = "text.txt"
}

do {
	let document = try Document(path: filePath)
} catch {
	// ...
}
```

```swift
// ❌ Не принимается

let SecondsPerMinute = 60
let kSecondsPerMinute = 60
let gSecondsPerMinute = 60
let SECONDS_PER_MINUTE = 60

do {
	let document = try Document(path: "important.data")
} catch {
	// ...
}
```

### Опциональные типы

Неявное извлечение опционалов `!` используется **только** для старых классов и модулей, в которых невозможно передать зависимость через инициализатор. При этом следует обращать внимание на проблему *цикла сильных ссылок* и следить, чтобы экземпляры классов не создавали сильные связи друг с другом.

**Не используются:** 
- принудительное разворачивание *force unwrap* - `!`;
- принудительное преобразование *force cast* - `as!`.

**Исключения:**
- принудительное разворачивание разрешается в модульных тестах и только для тестового кода, стоит обязательно учитывать, что это может привести к падению теста, а не к невыполнению;
- неявно развёрнутые свойства разрешаются в модульных тестах.

```swift
// ✅ Принимается

class MainViewController: UIViewController, IMainViewController {

	// MARK: - Dependencies

	var presenter: IMainViewPresenter!
	var appContext: IAppContext!
}

// Presenter не хранит сильную ссылку на экземпляр MainViewController, чтобы избежать цикла сильных ссылок

class MainViewPresenter: IMainViewPresenter {

	// MARK: - Dependencies

	weak var view: IMainViewController?
}
```

```swift
// ❌ Не принимается

struct Document {
	init?(name: String) {
		// ...
	}
}

let document = Document(name: "someName.txt")!

let value = otherValue as! Int
```

Перед использованием опциональные константы/переменные разворачиваются. Если не предполагается преобразования типа и/или значения, при "разворачивании" опциональной константы/переменной сохраняется ее оригинальное имя. Для опционального `self` и для вновь созданной константы используется тоже имя `self`.

```swift
// ✅ Принимается

registrationProvider.confirm(phone: msisdn) { [weak self] (result) in
	guard let self = self else { return }
	self.sendInfo = result.value
	completion(result.error)
}

guard let value = value else { return }
```

```swift
// ❌ Не принимается

registrationProvider.confirm(phone: msisdn) { [weak self] (result) in
	self?.sendInfo = result.value
	completion(result.error)
}

guard let someValue = value else { return }
```

Необязательные логические значения не используются, вместо этого значение устанавливается в `false`. 

**Исключение:**
- модели API, приходящие с сервера.

```swift
✅ let foo: Bool = false

❌ var foo: Bool?
```

Вместо опциональной привязки `let _ =` используется сравнение с `nil`.

```swift
// ✅ Принимается

if optionalValue != nil {
	print("Hello World")
}
```

```swift
// ❌ Не принимается

if let _ = optionalValue {
	print("Hello World")
}
```

Оператор `nil coalescing` используется только в том случае, если выражение в правой части не является `nil`, использование `nil` в `rhs` избыточно.

```swift
✅ myVar ?? 0

❌ myVar ?? nil
```

### Инициализация объектов

Явный вызов `.init(...)` используется только тогда, когда получатель вызова является переменной метатипа, или когда используется ссылка на инициализатор напрямую с использованием `MyType.init` синтаксиса для преобразования его в замыкание. При прямых вызовах инициализатора, использующего буквенное имя типа, `.init` опускается.

```swift
// ✅ Принимается

let x = MyType(arguments)

let type = lookupType(context)
let x = type.init(arguments)

let x = makeValue(factory: MyType.init)
```

```swift
// ❌ Не принимается

let x = MyType.init(arguments)
```

Инициализация опциональной переменной с помощью `nil` является избыточной.

```swift
✅ var myVar: Int?

❌ var myVar: Int? = nil
```

Для инициализации картинок не используется прямое обращение через имя ресурса.
Для инициализации не используются литералы:
- цветов;
- картинок.

```swift
// ❌ Не принимается

let image = #imageLiteral(resourceName: "image.jpg")

let color = #colorLiteral(red: 0.9607843161, green: 0.7058823705, blue: 0.200000003, alpha: 1)
```

Прямая инициализация типов, таких как *Bundle* и *UIDevice* не используется.

```swift
✅ let foo = Bundle.main

❌ let foo = UIDevice()
```

### Сложность и вложенность

- следует избегать лишней вложенности и как можно раньше прерывать выполнение метода в случае ошибки;
- если это возможно используется конструкция `guard` вместо `if`;
- использование `guard` - `continue` может быть полезным в цикле, когда всё тело цикла должно быть выполнено только в некоторых случаях;
- максимальный уровень вложенности:
	- для типов - **2 уровеня**;
	- для функции - **2 уровня**;
- следует ограничивать сложность функций.

```swift
// ✅ Принимается

func discombobulate(_ values: [Int]) throws -> Int {

	guard let first = values.first else {
		throw DiscombobulationError.arrayWasEmpty
	}
	guard first >= 0 else {
		throw DiscombobulationError.negativeEnergy
	}

	var result = 0
	for value in values {
		result += invertedCombobulatoryFactory(of: value)
	}

	return result
}
```

```swift
// ❌ Не принимается

func discombobulate(_ values: [Int]) throws -> Int {
	if let first = values.first {
		if first >= 0 {
			var result = 0
			for value in values {
				result += invertedCombobulatoryFactor(of: value)
			}
			return result
		} else {
			throw DiscombobulationError.negativeEnergy
		}
	} else {
		throw DiscombobulationError.arrayWasEmpty
	}
}
```

### Обращение к `self` и `super`

Везде, где программа компилируется без `self`, его следует убирать, `self` обязателен только в некоторых случаях, например, внутри замыканий или в инициализаторах.
Некоторые переопределенные методы всегда должны вызывать `super`. 
А некоторые - нет, в таких методах обращение к `super` следует удалить.

```swift
// ✅ Принимается

class VC: UIViewController {
	override func loadView() {}

	override func viewWillAppear(_ animated: Bool) {
		super.viewWillAppear(animated)
	}
}
```

```swift
// ❌ Не принимается

class VC: UIViewController {
	override func loadView() {
		super.loadView()
	}

	override func viewWillAppear(_ animated: Bool) {↓
		// not calling to super
		method()
	}
}
```

### Разделители

Явное указание разделителя по умолчанию не используется.

```swift
✅ let foo = bar.joined(separator: ",")

❌ let foo = bar.joined(separator: "")
```

### Неиспользуемые значения

Если возвращаемое значение функции игнорируется, то используется запись  `_ = foo()` вместо `let _ = foo()`.

```swift
✅ _ = foo()
✅ let _: ExplicitType = foo()

❌ let _ = foo()
❌ if _ = foo() { let _ = bar() }
```

### Устаревшие функции

- вызов `print()` не используется для вывода сообщений, так как печать снижает производительность приложения;
- следует избегать создания представлений с помощью *Interface Builder*; 
- вместо устаревших функций используется функция рандомизации `type.random(in:)`;
- вместо оператора остатка `%` используется функция `isMultiple(of:)`;
- когда индекс или элемент не используются, `.enumerated()` удаляется.

```swift
// ✅ Принимается

Int.random(in: 0..<10)

guard let i = reversedNumbers.firstIndex(where: { $0.isMultiple(of: 2) }) else { return }

for (idx, foo) in bar.enumerated() { }
```

```swift
// ❌ Не принимается

print("some message")

arc4random(10)

guard let i = reversedNumbers.firstIndex(where: { $0 % 2 == 0 }) else { return }

for (_, foo) in bar.enumerated() { }
```

### Хеш-функции

Следует тщательно выбирать описание хеш-функции для протокола `Hashable`, так как при ее описании важно распределение битового шаблона, а не фактическое числовое значение. Для нерационально подобранной функции выполнение будет происходить произвольно и непредсказуемо в зависимости от значений хеша отдельных терминов.

```swift
✅ var hashValue: Int { foo.hashValue &+ 31 * (bar.hashValue &+ 31 &* baz.hashValue) }

❌ var hashValue: Int { return foo.hashValue + 31 * (bar.hashValue + 31 * baz.hashValue) }
```

**Примечание:**
- для расчёта хеша используется `func hash(into hasher: inout Hasher)`, а не реализация `hashValue`.

### `CGGeometry`

- вместо устаревших функций используются:
	- свойства и методы расширений;
	- конструкторы *Swift*;
- вместо устаревших глобальных констант используются константы с областью видимости структуры;
- вместо явной инициализации с нулевыми параметрами используется значение `.zero`.

```swift
// ✅ Принимается

rect.width
CGPoint(x: 10, y: 10)

CGRect.infinite
CGSize(width: 2, height: 4)
CGSize.zero
```

```swift
// ❌ Не принимается

CGRectGetWidth(rect)
CGPointMake(10, 10)

CGRectInfinite
CGSize(width: 0, height: 0)
```

### Работа с последовательностями

Вместо `seq.map { $0 }` для преобразования последовательности в массив используется `Array(seq)`.

```swift
✅ foo.map { ((), $0) }

❌ foo.map { $0 }
```

Если всё тело цикла представляет собой отдельный `if` блок, проверяющий состояние элемента, то условие помещается вместо `if` в `where` на уровне оператора `for`.

```swift
// ✅ Принимается

for item in collection where item.hasProperty {
	// ...
}
```

```swift
// ❌ Не принимается

for item in collection {
	if item.hasProperty {
		// ...
	}
}
```

Вместо цикла `while` используется конструкция `for-in`.

```swift
// ✅ Принимается

for _ in 0..<3 {
	print("Hello three times")
}
```

```swift
// ❌ Не принимается

var i = 0
while i < 3 {
	print("Hello three times")
	i += 1
}
```

Для последовательностей используются проверки `isEmpty` в случаях сравнения:
- с нулем `0`;
- с пустыми литералами коллекций `[]` и `[:]`;
- с литералом пустой строки `""`.

```swift
// ✅ Принимается

myArray.count > 1
myArray.isEmpty
myString.isEmpty
```

```swift
// ❌ Не принимается

myArray.count == 0
myArray == []
myString == ""
```

Вместо комбинаций с функцией `.filter(where:)` используются другие функции высшего порядка для следующих случаев: 
- `.first(where:)` вместо `.filter(where:).first`;
- `.last(where:)` вместо `.filter(where:).last`;
- `.contains` вместо `.filter(where:).count == 0`;
- `.contains` вместо `.filter(where:).isEmpty`.

```swift
// ✅ Принимается

myList.first { $0 % 2 == 0 }
myList.last { $0 % 2 == 0 }

myList.contains(where: { $0 % 2 == 0 })
myList.filter { $0 % 2 == 0 }.count > 1
```

```swift
// ❌ Не принимается

myList.filter { $0 % 2 == 0 }.first
myList.filter { $0 % 2 == 0 }.last

myList.filter { $0 % 2 == 0 }.count == 0
myList.filter(where: { $0 % 2 == 0 }).isEmpty
```

Другие рекомендации при работе с коллекциями:
- `.contains` используется вместо `first(where:) != nil`;
- `.contains` используется вместо `range(of:) != nil`;
- `min`/`max` используются вместо `sorted().first`/`sorted().last`. 

```swift
// ✅ Принимается

myList.first { $0 % 2 == 0 }

myString.range(of: "Test")

myList.min(by: >)
```

```swift
// ❌ Не принимается

myList.first { $0 % 2 == 0 } != nil

myString.range(of: "Test") != nil

myList.sorted(by: >).first
```

## Работа с ошибками

### Ошибочные значения

`Optional` используется для передачи результата без ошибок, который является либо значением, либо отсутствием значения. Например, при поиске значения в коллекции отсутствие значения все ещё является действительным и ожидаемым результатом, а не ошибкой. `Optional` также используется для сценариев ошибок, когда существует одно явное состояние отказа, то есть, когда операция может завершиться неудачей по одной конкретной причине, понятной клиенту.

```swift
// ✅ Принимается

func index(of thing: Thing, in things: [Thing]) -> Int? {
	// ...
}

if let index = index(of: thing, in: lotsOfThings) {
		// Found it.
} else {
		// Didn't find it.
}

struct Int17 {
	init?(_ string: String) {
		// ...
	}
}
```

```swift
// ❌ Не принимается

func index(of thing: Thing, in things: [Thing]) -> Int {
	// ...
}

let index = index(of: thing, in: lotsOfThings)

if index != -1 {
		// Found it.
} else {
		// Didn't find it.
}
```

### Типы ошибок

Типы ошибок используются, когда существует несколько возможных состояний ошибок, они описываются в модели ошибок. Рекомендуется добавление новых типов ошибок в процессе выявления таковых.

```swift

struct Document {
	enum ReadError: Error {
		case notFound
		case permissionDenied
		case malformedHeader
	}
}
```

### Обработка ошибок

Неверные входные данные и недопустимое состояние обрабатываются как ошибки с помощью:
- конструкций `do-catch`, `try`;
- объявления функции, в которой выполняется вызов, `throws` и передачи ошибки выше уровнем;
- конструкции `try?` когда конкретная причина сбоя не имеет значения.

При использовании конструкции `try/catch` обрабатываются все случаи ошибок.

**Запрещено** использование *force try* - `try!`.

```swift
// ✅ Принимается

struct Document {
	enum ReadError: Error {
		case notFound
		case permissionDenied
		case malformedHeader
	}

	init(path: String) throws {
		// ...
	}
}

do {
	let document = try Document(path: "important.data")
} catch Document.ReadError.notFound {
	// ...
} catch Document.ReadError.permissionDenied {
	// ...
} catch {
	// ...
}
```

``` swift
// ❌ Не принимается

struct Document {
	enum ReadError: Error {
		case notFound
		case permissionDenied
		case malformedHeader
	}

	init(path: String) throws {
		// ...
	}
}

guard let value = try? Document(path: "important.data") else { return }

let value = try! Document(path: "important.data")
```

**Исключение:** 
- `try!` разрешён в модульных тестах и в коде только для тестирования, при использовании следует учитывать, что это может привести падению теста, а не к невыполнению.

### Прерывание работы

Если в `release` дальнейшая работа пользователя с приложением возможна при наличии ошибки, то рекомендуется использовать `assert(_:_:file:line:)`/`assertionFailure(_:file:line:)` для прерывания работы программы в отладочном режиме.

Использование `fatalError` допустимо для:
- `init(coder:)`;
- участков инициализации представлений из `.xib`/`.nib`;
- других случаев, когда дальнейшая работа пользователя с приложением невозможна.

Не следует использовать `fatalError` без необходимости, `fatalError` всегда сопровождается сообщением. 

## Управление памятью

Код не должен приводить к неуправляемым утечкам памяти из-за некорректного зацикливания связей объектов *retain cycle*, чтобы избавиться от *retain cycle*, следует использовать:
- поля типа `weak` и `unowned`;
- структуры и перечисления вместо классов;
- `weak var` для `delegate`.

Чтобы продлить время жизни объекта, можно использовать связку: 
- создание ссылки или копирование в контекст `[weak self]`; 
- разворачивание перед использованием `guard let self = self else { return }`.

Если сразу известно, что объект будет жить дольше, чем замыкание, то в замыкании следует использовать `[unowned self]`, в остальных случаях лучше использовать `[weak self]`.

```swift
// ✅ Принимается

request.onComplete { [weak self] response in
	guard let self = self else { return }
	let model = self.updateModel(response)
	self.updateUI(model)
}
```

``` swift
// ❌ Не принимается

// Объект self может быть удалён из памяти перед вызовом замыкания, в этом случае обращение self.updateModel() упадёт
request.onComplete { [unowned self] response in
	let model = self.updateModel(response)
	self.updateUI(model)
}

// Объект self может быть удалён из памяти в произвольный момент, поэтому такой код может привести к тому, что updateModel() сработает, а updateUI() — нет.
request.onComplete { [weak self] response in
	let model = self?.updateModel(response)
	self?.updateUI(model)
}
```

**Примечания:**
- делегаты удерживаются слабыми ссылками, чтобы избежать циклов;
- протоколы делегатов относятся только к классам, чтобы на них можно было слабо ссылаться;
- в протоколах, определяемых только для классов, используется `AnyObject` вместо `class`.

```swift
// ✅ Принимается

weak var delegate: SomeProtocol?

protocol FooDelegate: AnyObject {}

protocol SomeClassOnlyProtocol: AnyObject {}
```

```swift
// ❌ Не принимается

var delegate: SomeProtocol?

protocol FooDelegate {}

protocol SomeClassOnlyProtocol: class {}
```

## Рефакторинг

`TODO` и `FIXME` должны быть решены. 
Код, который требует доработок, помечается одной из меток:
- `// FIXME:` используется в тех случаях, когда необходимы доработки и исправления;
- `// TODO:` используется для нового кода (например, создать *service/manager* для *presenter*).

```swift
// ✅ Принимается

protocol ITaskViewControllerDelegate: AnyObject {
	// TODO: добавить свойства/методы контроллера
}

// FIXME: Вынести реализацию протоколов для всех инстансов в extension's
final class JobsMqttProvider: IPublishableProvider, SMMQTTSessionManagerConnectionDelegate, SMMQTTSessionManagerDelegate {
	// ...
}
```

```swift
// ❌ Не принимается

public func setSoundAtScan(enabled: Bool, completion: @escaping (Result<Void, LineCaseError>) -> Void) { // FIXME:
	// ...
}

switch barcode.type {
	case .datamatrix:
		checkDatamatrixBarcode(barcode: barcode)
		// TODO: 
	default:
		getFoundWareBy(barcode: barcode)
}
```

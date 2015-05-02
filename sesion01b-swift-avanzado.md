# Sesión 1b: <br/> Swift <br/> avanzado

### Servicios de las plataformas móviles - iOS

<small>Domingo Gallardo - domingo.gallardo@ua.es  
Departamento Ciencia de la Computación e Inteligencia Artificial  
Master Programación de Dispositivos Móviles</small>

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Protocolos

- El concepto de protocolo en Swift es similar al de Objective-C o al concepto de _interface_ en Java.
- Un _protocolo_ proporciona una plantilla de métodos, propiedades y otros requisitos que definen una tarea o funcionalidad particular.
- Un protocolo no proporciona ninguna implementación, sino que debe ser _adoptada_ por una clase, un _struct_ o una enumeración.
- Un protocolo proporciona también un _tipo_ y se puede usar en muchos sitios donde se permiten usar tipos:
    - Como el tipo de un parámetro o de un valor devuelto por una función, un método o un inicializador.
    - Como el tipo de una constante, variable o propiedad.
    - Como el tipo de los ítems de un array, diccionario, o otros contenedores.


<!-- Tres líneas en blanco para la siguiente transparencia -->



### Definición de un protocolo


- La forma de definir un protocolo es muy similar a la de clases, estructuras y enumeraciones.

```swift
protocol SomeProtocol {
    // la definición del protocolo viene aquí
}
```

- Un protocolo puede requerir que se defina una propiedad con un nombre y un tipo determinado. También define si la propiedad debe ser solo _gettable_ o _gettable_ y _settable_.

<!-- .element: class="fragment" data-fragment-index="1" -->

```swift
protocol SomeProtocol {
    var mustBeSettable: Int { get set }
    var doesNotNeedToBeSettable: Int { get }
}
```

<!-- .element: class="fragment" data-fragment-index="1" -->

- Por ejemplo:

<!-- .element: class="fragment" data-fragment-index="2" -->

```swift
protocol FullyNamed {
    var fullName: String { get }
}
```
<!-- .element: class="fragment" data-fragment-index="2" -->


---

El protocolo `FullyNamed` requiere al tipo que lo adopte que proporcione una propiedad `fullName` de tipo `String` que sea _getteable_.


<!-- Tres líneas en blanco para la siguiente transparencia -->



### Definición de una clase que _adopta_ un protocolo

- Para indicar que una clase, struct o enumeración adopta un protocolo escribimos el nombre del protocolo tras el nombre del tipo, separado por una coma. Se pueden listar múltiples protocolos, todos separadas por comas.

```swift
struct SomeStructure: FirstProtocol, AnotherProtocol {
    // structure definition goes here
}
```

- Si una clase tiene una superclase, hay que escribir el nombre de la superclase antes de cualquiera de los protocolos que se adoptan.

<!-- .element: class="fragment" data-fragment-index="1" -->

```swift
class SomeClass: SomeSuperclass, FirstProtocol, AnotherProtocol {
    // class definition goes here
}
```

<!-- .element: class="fragment" data-fragment-index="1" -->

- Un ejemplo con el protocolo anterior `FullyNamed`:

<!-- .element: class="fragment" data-fragment-index="2" -->

```swift
struct Person: FullyNamed {
   var fullName: String
}
let john = Person(fullName: "John Appleseed")
```

<!-- .element: class="fragment" data-fragment-index="2" -->

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Ejemplo

```swift
class Starship: FullyNamed {
    var prefix: String?
    var name: String
    init(name: String, prefix: String? = nil) {
        self.name = name
        self.prefix = prefix
    }
    var fullName: String {
        return (prefix != nil ? prefix! + " " : "") + name
    }
}
var ncc1701 = Starship(name: "Enterprise", prefix: "USS")
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Requerimiento de métodos

- Los protocolos pueden requerir que se implementen métodos específicos en los tipos que lo adopten. Estos métodos se escriben en la declaración del protocolo, de la misma forma que si fueran métodos de un tipo específico, pero sin las llaves ni el cuerpo de la implementación.


```swift
protocol SomeProtocol {
   class func someTypeMethod()
}
```

- Por ejemplo:

<!-- .element: class="fragment" data-fragment-index="1" -->

```swift
protocol RandomNumberGenerator {
    func random() -> Double
}
```
<!-- .element: class="fragment" data-fragment-index="1" -->

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Implementación de métodos

- La implementación del método se debe realizar en la clase que adopta el protocolo.

```swift
class LinearCongruentialGenerator: RandomNumberGenerator {
    var lastRandom = 42.0
    let m = 139968.0
    let a = 3877.0
    let c = 29573.0
    func random() -> Double {
        lastRandom = ((lastRandom * a + c) % m)
        return lastRandom / m
    }
}
```
<!-- .element: class="fragment" data-fragment-index="1" -->

```swift
let generator = LinearCongruentialGenerator()
println("Here's a random number: \(generator.random())")
// prints "Here's a random number: 0.37464991998171"
println("And another one: \(generator.random())")
// prints "And another one: 0.729023776863283"
```

<!-- .element: class="fragment" data-fragment-index="2" -->

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Ejemplo completo

- Veamos un ejemplo sencillo en el que se define un clase `Dice` que representa un dado de _n_ caras que puede usarse en un juego de mesa.

```swift
class Dice {
    let sides: Int
    let generator: RandomNumberGenerator
    init(sides: Int, generator: RandomNumberGenerator) {
        self.sides = sides
        self.generator = generator
    }
    func roll() -> Int {
        return Int(generator.random() * Double(sides)) + 1
    }
}
```

- La propiedad `generator` es del tipo `RandomNumberGenerator` y se inicializa en el constructor, pasándole una instancia de una clase que haya adoptado el protocolo.

<!-- .element: class="fragment" data-fragment-index="1" -->

```
var d6 = Dice(sides: 6, generator: LinearCongruentialGenerator())
for _ in 1...5 { println("Random dice roll is \(d6.roll())") }
```

<!-- .element: class="fragment" data-fragment-index="1" -->

<!-- Tres líneas en blanco para la siguiente transparencia -->



### El patrón _delegación_ 

- *Delegación* es un patrón de diseño que permite a una clase o estructura pasar (o delegar) alguna de sus responsabilidades a una instancia de otro tipo.
- Una forma de implementarlo es definiendo un protocolo que encapsula las responsabilidades delegadas y definiendo en la clase un método que actualiza el delegado con una instancia proporcionada por el programador que usa la clase.
- Permite extender funcionalidades de un _framework_ con código proporcionado por nosotros, los programadores que usamos el _framework_.
- Cocoa define propiedades y protocolos _delegados_ en muchas clases. Por ejemplo, [`UIScrollViewDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScrollViewDelegate_Protocol/) es el protocolo que debe cumplir el objeto delegado del [`UIScrollView`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIScrollView_Class/index.html#//apple_ref/occ/instp/UIScrollView/delegate) y que nos permite definir ciertos comportamientos cuando el usuario realiza un scroll.
- Veremos que para gestionar las notificaciones se utiliza el objeto delegado [`UIApplicationDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/#//apple_ref/occ/intfm/UIApplicationDelegate/application:willFinishLaunchingWithOptions:) de la clase [`UIApplication`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/swift/cl/UIApplication).


<!-- Tres líneas en blanco para la siguiente transparencia -->



### Cómo usar la delegación

1. Debemos crear una clase que adopte el protocolo delegado (por ejemplo `UIApplicationDelegate`). La clase puede ser de cualquier tipo y tener las propiedades y métodos añadidos que nos interesen.
2. Implementamos los métodos del delegado que nos interesen. Esos métodos representan eventos particulares detectados por la clase delegadora (por ejemplo `UIApplication`) en los que va a llamar a nuestro código. En todos esos métodos aparece como parámetro la clase delegadora y otros parámetros adicionales con información del evento. Usaremos estos parámetros en nuestro código para poder acceder a la clase delegadora o a la información del evento.
3. Hay que actualizar en algún momento la propiedad correspondiente de la clase delegadora (en este caso `UIApplication`). La propiedad suele llamarse `delegate`.
4. Cuando en tiempo de ejecución suceda el evento detectado por la clase delegadora se llamará a nuestro código.

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Un ejemplo de delegación

- Veamos una implementación del juego _Snake and ladders_ en el que se usa delegación para poder ampliar el comportamiento del juego cuando suceden determinados eventos.

<img src="images/snakesAndLadders.png" width=600px/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



### El estado del juego: tablero y variables

```swift
let finalSquare = 25
var board = [Int](count: finalSquare + 1, repeatedValue: 0)
board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
var square = 0
var diceRoll = 0
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



### El juego sin delegación

```swift
gameLoop: while square != finalSquare {
    let diceRoll = dice.roll()
    switch square + diceRoll {
    case finalSquare:
        // diceRoll will move us to the final square, so the game is over
        break gameLoop
    case let newSquare where newSquare > finalSquare:
        // diceRoll will move us beyond the final square, so roll again
        continue gameLoop
    default:
        // this is a valid move, so find out its effect
        square += diceRoll
        square += board[square]
    }
}
println("Game over!")
```

---

Se tira el dado al comienzo de cada bucle. En lugar de mover al jugador inmediatamente, se usa una sentencia switch en la que se considera el resultado del movimiento y se comprueba si es válido: 

- Si el dado mueve al jugador al cuadrado final, el juego termina. La sentencia `break` transfiere el control a la primera línea de código del bucle y finaliza el juego.
- Si el dado mueve al jugador más allá del cuadrado final, el movimiento es inválido, y el jugador necesita tirar de nuevo. La sentencia `continue` finaliza la iteración actual y comienza la siguiente iteración.
- En todos los demás casos el movimiento es válido. El jugador se mueve hacia adelante tantos cuadrados como la tirada del dado y la lógica del juego comprueba si hay alguna serpiente o escalera. El bucle termina y el control vuelve a la condición del `while` para decidir si se requiere otro turno.

<!-- Tres líneas en blanco para la siguiente transparencia -->



### Definimos el protocolo del juego y su delegado

- Definimos dos protocolos, uno para definir un juego de dados genérico y otro para definir su delegado.

```swift
protocol DiceGame {
    var dice: Dice { get }
    func play()
}
protocol DiceGameDelegate {
    func gameDidStart(game: DiceGame)
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int)
    func gameDidEnd(game: DiceGame)
}
```

- En todos los métodos del protocolo delegado se nos va a pasar como parámetro el propio juego. Los métodos `gameDidStart` y `gameDidEnd` se llamarán al comienzo y al final del juego. El método `game` se llama en cada tirada y nos van a pasar como parámetro adicional el valor obtenido en el dado.


<!-- Tres líneas en blanco para la siguiente transparencia -->



### Juego completo

```swift
class SnakesAndLadders: DiceGame {
    let finalSquare = 25
    let dice = Dice(sides: 6, generator: LinearCongruentialGenerator())
    var square = 0
    var board: [Int]
    init() {
        // Inicializamos el tablero, mismo código que antes
    }
    var delegate: DiceGameDelegate?
    func play() {
        // bucle del juego completo
    }
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



### El bucle de juego con delegación


```swift
func play() {
    square = 0
    delegate?.gameDidStart(self)
    gameLoop: while square != finalSquare {
        let diceRoll = dice.roll()
        delegate?.game(self, didStartNewTurnWithDiceRoll: diceRoll)
        switch square + diceRoll {
        case finalSquare:
            break gameLoop
        case let newSquare where newSquare > finalSquare:
            continue gameLoop
        default:
            square += diceRoll
            square += board[square]
        }
    }
    delegate?.gameDidEnd(self)
}
```


<!-- Tres líneas en blanco para la siguiente transparencia -->



### La clase que adopta el delegado

```swift
class DiceGameTracker: DiceGameDelegate {
    var numberOfTurns = 0
    func gameDidStart(game: DiceGame) {
        numberOfTurns = 0
        if game is SnakesAndLadders {
            println("Started a new game of Snakes and Ladders")
        }
        println("The game is using a \(game.dice.sides)-sided dice")
    }
    func game(game: DiceGame, didStartNewTurnWithDiceRoll diceRoll: Int) {
        ++numberOfTurns
        println("Rolled a \(diceRoll)")
    }
    func gameDidEnd(game: DiceGame) {
        println("The game lasted for \(numberOfTurns) turns")
    }
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



### El juego en acción

```swift
let tracker = DiceGameTracker()
let game = SnakesAndLadders()
game.delegate = tracker
game.play()
// Started a new game of Snakes and Ladders
// The game is using a 6-sided dice
// Rolled a 3
// Rolled a 5
// Rolled a 4
// Rolled a 5
// The game lasted for 4 turns
```


<!-- Tres líneas en blanco para la siguiente transparencia -->



# Master Programación <br/> de Dispositivos Móviles



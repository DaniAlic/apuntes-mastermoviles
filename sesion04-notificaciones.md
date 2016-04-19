# Sesión 4: <br/> Notificaciones 

#### Servicios de las plataformas móviles - iOS

<small>Domingo Gallardo - domingo.gallardo@ua.es  
Departamento Ciencia de la Computación e Inteligencia Artificial  
Master Programación de Dispositivos Móviles  
2015-16</small>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Referencias

- [Local and Remote Programming Guide](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/Introduction.html)

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Notificaciones

<!-- .slide: class="image-right"-->

- En iOS sólo una única aplicación puede estar activa en un momento dado. Sin embargo, en muchas ocasiones las apps operan en un entorno basado en el tiempo o interconectado en el que es necesario avisar al usuario cuando sucede algún evento y la aplicación que no está activa. Las notificaciones locales y remotas permiten a estas apps notificar a sus usuarios cuando ocurre algún suceso de su interés.
- Las notificaciones locales y remotas tienen orígenes distintos. Una notificación local es planificada y enviada por la propia app, mientras que una notificación remota (también conocida como notificación _push_) tiene su origen fuera del dispositivo, en un servidor remoto denominado _proveedor de la app_ y se _empuja_ al dispositivo mediante el servicio _Apple Push Notification service_ (_APN_).

<img src="images/glances.png" />

- Además de los usos comentados, las notificaciones se utilizan también para la comunicación entre nuestra app y el recién introducido _Apple Watch_. Se puede consultar la [página de recursos](https://developer.apple.com/watchkit/) de Apple sobre el _WatchKit Framework_ para más información.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Uso de los tipos de notificaciones

- Las notificaciones remotas y locales satisfacen distintas necesidades de diseño.
- Una **notificación local** se planifica y envía por la propia app, sin que intervenga Internet.
- Una **notificación remota**, también llamada _notificación push_, llega del exterior del dispositivo. Se origina en un servidor remoto gestionado por el desarrollador de la app (denominado proveedor de la aplicación) y se envía al dispositivo del usuario a través del _Apple Push Notification service_ (APNs).


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de uso: lista to-do

- Un ejemplo del uso de notificaciones puede ser una app que gestiona una lista de tareas por hacer, en la que cada ítem tiene una fecha y hora en el que debe ser completado.
- El usuario puede pedir a la app que le notifique en un determinado intervalo de tiempo antes de que se cumpla esa fecha.
- Para implementar esta conducta, la app planifica una notificación local para esa fecha y hora. En lugar de especificar un mensaje de alerta, la app elige usar un globo (1) y un sonido. En el momento planificado, iOS hace sonar el sonido y muestra el número en la esquina del icono de la app.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Apariencia de las notificaciones

<img src="images/badge.png" width="100px"/> <br/>

Tanto las notificaciones locales como las remotas pueden aparecer como:

- Una alerta o tira (_banner_) en pantalla.
- Un globo (_badge_) en el icono de la app.
- Un sonido que acompaña la alerta, _banner_ o _badge_.
- Desde la perspectiva del usuario ha sucedido algo de interés en la app.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dónde aparecen las notificaciones

<img src="images/alerta.png" width=300px/> <br/>
<img style="vertical-align: middle; margin-right: 60px;" src="images/notificacion-centro-notificaciones.png" width=400px/>  <img style="vertical-align: middle; margin-right: 60px;" src="images/notificacion-pantalla-bloqueo.png" width=300px/> 

- Alerta modal, cuando el dispositivo está desbloqueado.
- Centro de notificaciones.
- Pantalla bloqueada.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dónde se originan las notificaciones locales

<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/local-notification.png" width=400px/>

- Las notificaciones locales se originan en la propia aplicación, que las crea y las planifica para una fecha futura.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dónde se originan las notificaciones remotas

- Las notificaciones remotas se originan en un servidor nuestro (_provider_) que realiza la petición al _Apple Push Notification Service_.
- El servicio APNs se encargar de enviar la notificación al dispositivo y éste a la app.

<img src="images/remote-notif-simple.png"/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Para qué se usan las notificaciones

- Notificaciones locales: alarmas, recordatorios, eventos de una forma sencilla, sin tocar las apps Calendario, Alarmas o Recordatorios (_EventKit Framework_).
- Notificaciones remotas <!-- .element: class="fragment" data-fragment-index="1" -->
    - Avisar al usuario de que han sucedido determinados eventos.
    - Notificar a la app para que descargue contenido nuevo para que esté disponible la próxima vez que el usuario la utilice.  <!-- .element: class="fragment" data-fragment-index="2" -->

<img class="fragment" data-fragment-index="2" src="images/notif-content-available.png" width=600px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Estados de la app

<img src="images/high_level_flow.png" width=450px/>

---

<table>
  <tr>
    <td><strong><em>Estado</em></strong></td> 
    <td><strong><em>Descripción</em></strong></td>
  </tr>
  <tr>
  <td><strong>No corriendo</strong></td> 
    <td>La app no ha sido lanzada o fue terminada por el usuario o por el sistema.</td>
  </tr>
  <tr>
    <td><strong>Inactiva</strong></td> 
    <td>La app está corriendo en primer plano pero no está recibiendo eventos (puede estar ejecutando código, sin embargo). Una app permanece en este estado brevemente, mientras realiza una transición a otro estado.</td>
  </tr>
  <tr>
    <td><strong>Activa</strong></td> 
    <td>La app está corriendo en primer plano y recibiendo eventos.</td>
  </tr>
  <tr>
    <td><strong>Background</strong></td> 
    <td>La app está ejecutando código pero no es visible en pantalla. Cuando el usuario sale de una app, el sistema mueve la app al estado de _background_ antes de suspenderla. En otros momentos, el sistema puede lanzar una aplicación en _background_ (o despertar una app suspendida) y darle tiempo para manejar ciertas tareas específicas. Por ejemplo, el sistema puede despertar una app para que procese descargas en _background_, o responda a notificaciones remotas. Una app en estado _background_ debe hacer el mínimo trabajo posible y devolver rápidamente el control al sistema.</td>
  </tr>
  <tr>
    <td><strong>Suspendida</strong></td> 
    <td>La app está en memoria pero no ejecuta código. El sistema suspende apps que están en _background_ y no tienen tareas pendientes que completar. El sistema puede eliminar apps suspendidas en cualquier momento sin despertarlas, para hacer sitio para otras apps.</td>
  </tr>
</table>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### _UIApplication_ y _UIApplication Delegate_

- `UIApplication` y `UIApplicationDelegate` son tipos muy importantes que debemos conocer.
- Contienen los métodos necesarios para gestionar las notificaciones y otros eventos del ciclo de vida de las apps.
- Xcode proporciona una clase _app delegate_ para cada proyecto. UIKit crea automáticamente una instancia de la clase proporcionada por Xcode y la usa para ejecutar los primeros bits de código _custom_ de la app. Todo lo que tienes que hacer es añadir tu código _custom_ a la clase que proporciona Xcode.
- La clase `UIApplication` define un _singleton_ al que podemos acceder con el método de clase `sharedApplication()`

```swift
let application = UIApplication.sharedApplication();
```

- Referencias: [`UIApplication`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/swift/cl/UIApplication) y [`UIApplicationDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/).

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Métodos relacionados con los cambios de estado en el `UIApplicationDelegate`

- En [`UIApplicationDelegate` - Monitoring App State Change](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006786-CH3-SW5)
    - `application(_:willFinishLaunchingWithOptions:)`
    - `application(_:didFinishLaunchingWithOptions:)`
    - `applicationDidBecomeActive(_:)`
    - `applicationWillResignActive(_:)`
    - `applicationDidEnterBackground(_:)`
    - `applicationWillEnterForeground(_:)`
    - `applicationWillTerminate(_:)`
- Todos reciben el parámetro `application`, el _singleton_ de tipo [`UIApplication`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplication_Class/index.html) que contiene todos los datos de la app.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registro de los tipos de notificación

- Las apps que usan notificaciones locales o remotas deben registrar los tipos de notificaciones que intentan enviar al usuario.
- El usuario debe aceptar el tipo de notificación: globos, alertas o sonidos. Inicialmente le aparecerá una alerta en el que permite aceptar o rechazar todos los tipos. 
- Después en cualquier momento puede modificar esta aceptación en los ajustes de la aplicación (Ajustes > Notificaciones).

<img src="images/alerta-notificaciones.png" width=400px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Inicialización de las notificaciones

- Podemos inicializar las notificaciones al arrancar la app con el método `aplication(_:didFinishLaunchingWithOptions:)` del [`UIApplicationDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/swift/intf/UIApplicationDelegate)
- Llamamos a `registerUserNotificationSettings(_:)` con los tipos de notificación deseados. La app pide permiso para usar las notificaciones y el usuario puede autorizar o no. Después, el sistema llama al método `application(_:didRegisterUserNotificationSettings:)` del delegado para informar de los resultados.
- Independientemente de que el usuario acepte o no las notificaciones, éstas se van a lanzar y los métodos que las manejan en el app delegado se van a disparar. Lo que el usuario desactiva es la aparición de las notificaciones en pantalla.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Código

```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: 
                                   [NSObject: AnyObject]?) -> Bool {
    let notificationSettings = UIUserNotificationSettings(
        forTypes: [.Badge, .Sound, .Alert], categories: nil)
    application.registerUserNotificationSettings(notificationSettings)

    // Resto de código para inicializar la app
    
    return true
}

func application(application: UIApplication,
                 didRegisterUserNotificationSettings notificationSettings: UIUserNotificationSettings) {
    print(notificationSettings)
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Notificaciones locales

- Un objeto [`UILocalNotification`](https://developer.apple.com/library/ios/documentation/iPhone/Reference/UILocalNotification_Class/) especifica una notificación que una app puede planificar que se envíe en una fecha y hora específica.
- El sistema operativo es responsable de entregar las notificaciones locales en la fecha y hora planificada, la app no tiene que estar en marcha para que esto suceda.
- Las notificaciones locales son similares a las remotas en el sentido de que se usan para mostrar alertas, ejecutar sonidos y añadir globos al icono del app.
- Se usan principalmente en apps con conductas basadas en temporizadores y en apps sencillas de calendarios o de listas de to-do. Una app que está ejecutándose en background también puede planificar una notificación para informar al usuario de que ha llegado un mensaje, un chat o se ha actualizado algún estado.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Qué contiene una notificación local

- Para crear una notificación local hay que crear una instancia de `UILocalNotification` y definir los siguientes atributos: 
    - `fireDate: NSDate?` - fecha en la que se lanza la notificación.
    - `repeatInterval: NSCalendarUnit` - intervalo de repetición.
    - `alertAction: String?` - título del botón de la alerta de la acción por defecto
    - `alertBody: String?` - mensaje en la alerta.
    - `applicationIconBadgetNumber: Int` - número a incluir en el globo cuando llegue la notificación.
    - `soundName: String?` - nombre del fichero del sonido a reproducir.
    - `userInfo: [NSObject : AnyObject]?` - un diccionario para pasar información _custom_ a la app notificada. En Swift se declaran todos los `NSDictionary` como diccionarios del tipo `[NSObject: AnyObject]`. Podemos inicializarlo a cualquier tipo de diccionario y después hay que hacer un _downcasting_.
    - `category: String?` - el nombre de un grupo de acciones a mostrar en la alerta (lo veremos más adelante).

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Planificación de una notificación local

- Para planificar una notificación local hay que invocar el método `scheduleLocalNotification(_:)` o `presentLocalNotificationNow(_:)` de la clase `UIApplication` pasando como parámetro la notificación creada.
- El primer método usa la fecha `fireDate` como fecha de entrega.
- El segundo método presenta la notificación inmediatamente, independientemente de la fecha de la notificación.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de código para enviar una notificación local

- Creamos una notificación local que aparece a los 10 segundos

```swift
let localNotification = UILocalNotification()
localNotification.alertBody = "¡¡Funciona!!"
localNotification.alertAction = "Volver a la app"
localNotification.soundName = UILocalNotificationDefaultSoundName
localNotification.fireDate = NSDate(timeIntervalSinceNow: 10)

// application contiene el singleton UIApplication
application.scheduleLocalNotification(localNotification)
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Práctica: app `Notificaciones`

<!-- .slide: data-background="#cbe0fc"-->

- Crea un proyecto `Notificaciones`en el directorio en el que has inicializado el control de versiones y tienes conectado con tu cuenta de Bitbucket. 
- _Bundle Identifier_: `es.ua.mastermoviles.Notificaciones`
- Escribe el código anterior para lanzar una notificación en el método de `application(_:, didFinishLaunchingWithOptions)` del `AppDelegate`. 
- Comprueba que se lanza la notificación correctamente.
- Cuando la termines haz un commit y un push para subirla a Bitbucket.
- **Avanzado**: Añade un botón en la app, de forma que cuando pulsemos el botón se planificará una nueva notificación. Se puede pulsar el botón tantas veces como queramos y se generarán otras tantas notificaciones.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Métodos del `UIApplicationDelegate` para manejar notificaciones

- Se deben consultar en la referencia de [`UIApplicationDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/)
- Dependiendo de si la notificación es local o remota:
-  <!-- .element: class="fragment" data-fragment-index="1" --> Manejo de notificaciones locales:
    - `application(_:didReceiveLocalNotification:)`
-  <!-- .element: class="fragment" data-fragment-index="2" --> Manejo de notificaciones remotas:
    - `application(_:didRegisterForRemoteNotificationsWithDeviceToken:)`
    - `application(_:didFailToRegisterForRemoteNotificationsWithError:)`
    - `application(_:didReceiveRemoteNotification:fetchCompletionHandler:)`
    - `application(_:didReceiveRemoteNotification:)`

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Manejo de notificaciones locales cuando la app no está en primer plano

- Cuando la aplicación no está en primer plano, puede mostrar al usuario una alerta, añadir un globo al icono, tocar un sonido y mostrar uno o más botones de acciones.
- Si el usuario toca la notificación el sistema lanza la app y llama al método `application(_:didReceiveLocalNotification:)` si la notificación es local o `application(_:didReceiveRemoteNotification:fetchCompletitionHandler:)` si es remota.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Manejo de notificaciones locales cuando la app está en primer plano

- Si la app está en primer plano el sistema no muestra al usuario ninguna alerta, pero sí añade la notificación en el centro de notificaciones y también se invoca a los métodos correspondientes del delegado de la app vistos anteriormente.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Código ejemplo que añade datos en la notificación

```swift
...
localNotification.userInfo = ["Mensaje":"Hola, mundo"]
...
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Código ejemplo para manejar una notificación

```swift
func application(application: UIApplication, didReceiveLocalNotification notification: UILocalNotification) {
    print("Recibida notificación")
    let userInfo = notification.userInfo as? Dictionary<String,String>
    if let s = userInfo?["Mensaje"] {
        print("Mensaje: \(s)")
    }
    else {
        print("No he encontrado el mensaje\n")
    }
}
```
<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Práctica: app `Notificaciones`

<!-- .slide: data-background="#cbe0fc"-->

- Añade el código anterior en la aplicación para probar el paso de datos y el manejo de la notificación.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Acciones en las notificaciones

- En versiones anteriores a 8, las notificaciones sólo podían tener una acción por defecto. 
- A partir de iOS 8, las notificaciones pueden tener acciones _custom_ adicionales.
- En la pantalla de bloqueo, en las tiras y en el Centro de Notificaciones se pueden mostrar dos acciones.
- En las alertas modales, las notificaciones pueden mostrar hasta cuatro acciones cuando el usuario pulsa el botón `Opciones`.
- Para usar acciones de notificación en tu app, debes crear los posibles conjuntos de acciones de tipo [`UIMutableUserNotificationAction`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIMutableUserNotificationAction_class/index.html#//apple_ref/occ/cl/UIMutableUserNotificationAction), agruparlos en la clase [`UIMutableUserNotificationCategory`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIMutableUserNotificationCategory_class/index.html#//apple_ref/occ/cl/UIMutableUserNotificationCategory), registrarlos al inicializar las notificaciones y seleccionar uno de los conjuntos en la notificación.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Acciones en las notificaciones

<img src="images/notif-actions-lock-screen.png" width=800px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Acciones en las notificaciones

<img src="images/notif-actions-alert.png" width=800px/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registro de acciones de notificación

- En la inicialización de las notificaciones definimos las posibles acciones:

```swift
let acceptAction = UIMutableUserNotificationAction()
acceptAction.identifier = "ACEPTAR"
acceptAction.title = "Aceptar"
// Indica si la app debe ser activada
acceptAction.activationMode = UIUserNotificationActivationMode.Foreground
// Acciones destructivas se muestran en rojo
acceptAction.destructive = false 
// Determina si el usuario necesita autenticarse
acceptAction.authenticationRequired = true

let declineAction = UIMutableUserNotificationAction()
declineAction.identifier = "DECLINAR"
declineAction.title = "Declinar"
declineAction.activationMode = UIUserNotificationActivationMode.Background
declineAction.destructive = false
declineAction.authenticationRequired = false
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Agrupación de acciones en categorías

- Las agrupamos en la categoría `INVITACION`:

```swift
let category = UIMutableUserNotificationCategory()
category.identifier = "INVITACION"
category.setActions([acceptAction, declineAction], 
                    forContext: 
                        UIUserNotificationActionContext.Default)
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registro de las categorías y actualización en la notificación

- Y registramos la categoría (puede haber más de una):

```swift
let notificationSettings = UIUserNotificationSettings(
     forTypes: [.Badge, .Sound, .Alert], categories: [category])

application.registerUserNotificationSettings(notificationSettings)
```

- En la notificación ya podemos usar la categoría recién registrada:

```swift
...
localNotification.category = "INVITACION"
...
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Métodos del `UIApplicationDelegate` para manejar acciones en las notificaciones

- En el protocolo [`UIApplicationDelegate`](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/)
-  Manejo de notificaciones locales:
    - `application(_:handleActionWithIdentifier:forLocalNotification:completionHandler:)`
-  Manejo de notificaciones remotas:
    - `application(_:handleActionWithIdentifier:forRemoteNotification:completionHandler:)`
- Cuando la app no está en primer plano si el usuario pulsa una acción en una notificación el sistema llama al `application(_:handleActionWithIdentifier:forLocalNotification:)` `completionHandler:` o a su método equivalente de notificación remota. En ambos métodos, obtenemos el identificador de la acción que el usuario ha pulsado, junto con el objeto notificación local o remoto. El _completionHandler_ es una clausura que nos pasa el sistema a la que hay invocar una vez terminado el procesamiento de la notificación. Si no se realiza esa invocación la app muere.
- Si la app está en primer plano el sistema llama a `application(_:didReceiveRemoteNotification:fetchCompletionHandler:)` 

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de código

```swift
func application(application: UIApplication, 
                 handleActionWithIdentifier identifier: String?, 
                 forLocalNotification notification: UILocalNotification, 
                 completionHandler: () -> Void) {
        print(identifier)
        completionHandler()
    }
```
<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Práctica: app `Notificaciones`

<!-- .slide: data-background="#cbe0fc"-->

- Prueba el código anterior para incluir acciones en la notificación que se lanza al arrancar la app.
- **(Opcional)**: Escribe un ejemplo de código en el que se usen los números en los globos de la app: incialízalo a 1 y ponlo a 0 cuando se pulse la acción de la notificación.

<!-- Tres líneas en blanco para la siguiente transparencia -->



# Master Programación <br/> de Dispositivos Móviles



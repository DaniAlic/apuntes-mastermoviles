<!--
Referencias:
WWDC vídeo What's next in CoreData: https://developer.apple.com/videos/wwdc/2014/?id=225
WWDC vídeo Advanced CloudKit: https://developer.apple.com/videos/wwdc/2014/?id=231
WWDC vídeo Introducing CloudKit: https://developer.apple.com/videos/wwdc/2014/?id=208
iCloud Design Guide: https://developer.apple.com/library/ios/documentation/General/Conceptual/iCloudDesignGuide/Chapters/Introduction.html
CloudKit Quick Start: https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987


Xamarin Tutorial CloudKit: http://developer.xamarin.com/guides/ios/platform_features/intro_to_cloudkit/
Ray Wenderlich Core Data Tutorial con Swift: http://www.raywenderlich.com/85578/first-core-data-app-using-swift


-->

# Sesión 6: iCloud y CloudKit

#### Servicios de las plataformas móviles - iOS

<small>Domingo Gallardo - domingo.gallardo@ua.es  
Departamento Ciencia de la Computación e Inteligencia Artificial  
Master Programación de Dispositivos Móviles   
2015-16</small> 

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Referencias

- [Recursos sobre iCloud](https://developer.apple.com/icloud/index.html)
- [iCloud Design Guide](https://developer.apple.com/library/ios/documentation/General/Conceptual/iCloudDesignGuide/Chapters/Introduction.html#//apple_ref/doc/uid/TP40012094)
- [CloudKit Quick Start](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)
- [iCloud Programming Guide for Core Data](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/UsingCoreDataWithiCloudPG/Introduction/Introduction.html#//apple_ref/doc/uid/TP40013491)

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### iCloud
<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/iCloud_intro.png" width=400px/>

- iCloud es un servicio de Apple que permite a un usuario acceder a su contenido personal (datos, documentos) en todos sus dispositivos utilizando su Apple ID.
- iCloud consigue esto combinando almacenamiento en la nube y APIs dedicadas integradas en el sistema operativo.
- Apple proporciona la infraestructura de servidores, de transmisión de datos y de cuentas de usuario, facilitando el trabajo a los desarrolladores que no necesitan crear sus propios servicios ni recurrir a soluciones de terceros.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Filosofía de iCloud

- La idea principal tras iCloud es eliminar la sincronización explícita entre dispositivos.
- Un usuario nunca necesita pensar sobre la sincronización, y la app nunca interactúa directamente con los servidores de iCloud.
- Para el usuario, los cambios aparecen automáticamente en todos los dispositivos conectados a la cuenta iCloud.
- Para el desarrollador, depende del API de iCloud que se utilice estos cambios son más o menos automáticos.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Cuenta iCloud

- Todo usuario de Apple puede activar una cuenta de iCloud usando su Apple ID
- Permite identificarse y mantenerse logeado en el dispositivo
- Puede activarse desde un Mac, desde un dispositivo iOS, desde la web o desde el simulador
- Permite mantener el estado en aplicaciones ejecutándose en distintos dispositivos: Recordatorios, Notas, ...

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Distintas APIs

- Almacenamiento **clave-valor en iCloud**: para mantener el estado de la aplicación (puntuación de un juego, última página leída, etc.).
- **Documentos en iCloud**: para mantener documentos sincronizados.
- **iCloud con Core Data**: para mantener de forma automática en iCloud una copia de todos los datos de la app gestionados con Core Data. Versión inicial con muchos problemas, muy mejorado en las últimas versiones.
- **CloudKit** (a partir de iOS 8): nueva tecnología a partir de iOS 8 que permite mayor flexibilidad y control. Basado en registros con diccionarios clave-valor, enfoque muy similar a las tecnologías NoSQL. 
  - API de transporte que no mantiene un estado local.
  - Basada en peticiones y respuestas asíncronas.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Preparación de aprovisionamiento y permisos para iCloud

- Para desarrollar con iCloud es necesario estar registrado como desarrollador en el programa de desarrollo de Apple. También puedes hacerlo con tu Apple ID registrado en el equipo de la UA.
- Para usar los servicios de iCloud es necesario crear un perfil de aprovisionamiento con un App Id concreto, añadir el servicio de iCloud y activar el permiso (_capabilities_) en la app con XCode.
- Si estás registrado en el equipo de desarrollo con un rol de administrador (o tienes una cuenta de pago en la que tienes todos los permisos de tu equipo), se puede hacer todo automaticamente desde XCode.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Creación del App ID

- Un administrador del equipo debe crear el App ID que otorgue la capacidad de acceso a iCloud.
- Llamamos al perfil `Master Moviles iCloud` y autorizamos el _bundle ID_ explícito `es.ua.mastermoviles.ToDoList` (el nombre de la aplicación que vamos a desarrollar). 

<img src="images/appid-icloud.png" width=450px/> <img src="images/appid-icloud-servicios.png" width=450px/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Asignación del container al App ID


<img src="images/aprovisionamiento-icloud-container.png" width=450px/> 

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Creación del perfil de aprovisionamiento

- Creamos el perfil de aprovisionamiento `Master Moviles ToDoList iCloud` con el App ID anterior.

<img src="images/aprovisionamiento-icloud.png" width=450px/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Clave-valor
<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/kvs_intro.png" width=300px/>

- Permite guardar y recuperar en iCloud claves y valores desde los dispositivos en los que el usuario está registrado con su Apple Id.
- Para acceder a los valores debemos usar la clase [NSUbiquitousKeyValueStore](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUbiquitousKeyValueStore_class/index.html).
- Puedes almacenar `String`s, valores escalares como `BOOL` o `Double`, diccionarios y también objetos de cualquiera de los siguientes tipos: `NSNumber`, `NSString`, `NSDate`, `NSData`, `NSArray`, or `NSDictionary`.
- El espacio de almacenamiento total, para un usuario dado, es de 1 MB y un máximo de 1024 claves.

```swift
let iCloudStore = NSUbiquitousKeyValueStore.defaultStore() 
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Obtención de valores del almacén de claves-valor

- Funciones que obtienen los distintos tipos de datos a partir de una clave (una cadena):
    - `arrayForKey`
    - `boolForKey`
    - `dictionaryForKey`
    - `StringForKey`
    - ...

- Por ejemplo, [`longlongForKey`](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUbiquitousKeyValueStore_class/index.html#//apple_ref/occ/instm/NSUbiquitousKeyValueStore/longLongForKey:)  devuelve el valor long long (`Int64`) asociado a una clave especificada:

```swift
func longlongForKey(_ aKey: String) -> Int64
```

- Parámetros y resultados del método:
    - `aKey`: la clave en el almacén de claves-valor
    - Devuelve: el valor asociado a la clave o 0 si la clave no existe o no contiene un valor numérico

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Por ejemplo, en el _ViewController_

```swift
func loadNumTerminados() {
    let store = NSUbiquitousKeyValueStore.defaultStore()
    let itemsStore = store.longLongForKey("numItemsDone")
    if (itemsStore > 0) {
        itemsTerminados = Int(itemsStore)
        print("Leído de iCloud: \(itemsTerminados) ítems terminados")
    } else {
        print("Sin valor en iCloud de ítems terminados")
    }
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Guardar valores en el almacén de claves-valor

- Para actualizar los valores hay que usar los métodos _setters_:
    - `setArray(_:forKey:)`
    - `setBool(_:forKey:)`
    - `setDiccionary(_:forKey_)`
    - ...


- Por ejemplo, [`setLongLong(_:forKey:)`](https://developer.apple.com/library/ios/documentation/Foundation/Reference/NSUbiquitousKeyValueStore_class/index.html#//apple_ref/occ/instm/NSUbiquitousKeyValueStore/setLongLong:forKey:) actualiza en el almacén el valor long long (`Int64`) asociándolo a una clave especificada:

```swift
func setLongLong(_ value: Int64, forKey aKey: String)
```

- Parámetros del método:
  - `value`: El valor long long que se quiere almacenar.
  - `aKey`: La clave en la que se almacena el valor.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Por ejemplo, en el `ViewController`

```swift
func saveNumItemsTerminados() {
    let store = NSUbiquitousKeyValueStore.defaultStore()
    store.setLongLong(Int64(itemsTerminados), forKey: "numItemsDone")
}
```
<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Método `synchronize`

```swift
func synchronize() -> Bool
```
- Devuelve `true` si las claves y valores en memoria y en disco están sincronizados o `false` si ha sucedido algún error. Por ejemplo, devuelve `false` si la app no se ha compilado con las peticiones adecuadas de _entitlement_.

- Se recomienda llamar a este método sólo en el momento de lanzar la app, o cuando vuelve al primer plano.

- Los cambios al almacén de claves-valor se salvan en memoria. El sistema sincroniza automáticamente estos datos con la caché del disco en los momentos apropiados. Por ejemplo, cuando el app pasa a segundo plano o cuando se reciben cambios de iCloud.

- Este método no fuerza la subida a iCloud de los nuevos valores y claves, sino que hace saber a iCloud que los valores están listos para ser subidos. El sistema controla cuándo subir los datos. La frecuencia de subida está limitada a bastantes por minuto.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de uso de `synchronize`


```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
    let store = NSUbiquitousKeyValueStore.defaultStore()
    if (store.synchronize()) {
        print("Sincronización OK")
    } else {
        print("Problemas en la sincronización")
    }
    return true
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Definiendo un observador de cambios

- Además de almacenar los valores podemos recibir notificaciones de cambio de los valores en otros dispositivos conectados a iCloud.
- En el lanzamiento del app hay que registrarse para la notificación `NSUbiquitousKeyValueStoreDidChangeExternallyNotification`.
- La notificación se envía cuando el valor de una o más claves han cambiado debido a datos que han llegado desde iCloud. La notificación no se envía cuando la propia app ha cambiado los valores.
- El diccionario atributo `userInfo` de la notificación contiene la razón de la notificación, así como una lista de los valores cambiados.
- El objeto en la notificación es el `NSUbiquitousKeyValueStore` cuyo contenido ha cambiado.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de definición de un observador de la notifiación

- Registramos una clausura para la notificación `NSUbiquitousKeyValueStoreDidChangeExternallyNotification` en el momento de lanzar la app, y llamamos al método `synchronize()` para obtener las parejas de claves-valor más recientes.

```swift
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
    NSNotificationCenter.defaultCenter().addObserverForName(
        NSUbiquitousKeyValueStoreDidChangeExternallyNotification, 
        object: NSUbiquitousKeyValueStore.defaultStore(), 
        queue: NSOperationQueue.mainQueue()) { 
            (notification) in 
                let ubiquitousKeyValueStore = 
                    notification.object as! NSUbiquitousKeyValueStore
                ubiquitousKeyValueStore.synchronize()
        }
    return true
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Práctica: ToDoList con iCloud clave-valor

<!-- .slide: data-background="#cbe0fc"-->
<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/to-do-list.png" width=200px/>

- Se ha dejado la app `es.ua.mastermoviles.ToDoList` en el repositorio [mastermoviles-servicios-ios](https://bitbucket.org/mastermoviles/mastermoviles-servicios-ios/) de Bitbucket.
- Modifica la aplicación ToDoList para que se guarden en el almacén clave-valor de iCloud del usuario:
    - Array de cadenas que representan las tareas a terminar
    - Número de tareas terminadas
- Debes usar el perfil de aprovisionamiento `Master Moviles iCloud: es.ua.mastermoviles.ToDoList`
- A veces la sincronización con iCloud no funciona del todo bien el simulador. Debes reiniciar el simulador cada vez que quieras actualizar los valores de iCloud. Lo probaremos también en el dispositivo real. Para hacer las pruebas en el simulador debes registrarte con tu cuenta de iCloud en el simulador.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### CloudKit

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Introducción a CloudKit
<!-- .slide: class="image-right"-->


<img style="margin-left:20px" src="images/cloudkit-base-otras-tecnologias.png" width=500px/>


- CloudKit es un proyecto interno de Apple en el que se basan muchas de sus APIs de persistencia.
- Su uso se ofrece a les desarrolladores en la WWDC de 2014, para apps a partir de iOS 8.
- Acceso a servidores de iCloud.
- Usa las cuentas iCloud de los usuarios.
- Bases de datos públicas (de la app) y privadas (de cada usuario).
- Permite datos estructurados y datos _bulk_.
- Tecnología de transporte, no proporciona base de datos local.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### CloudKit trabaja sobre registros en iCloud

- CloudKit proporciona una forma de mover datos estructurados entre tu aplicación y iCloud.
- CloudKit trabaja sobre registros, diccionarios de parejas clave-valor con cada clave representando un campo del registro.
- El valor de cada campo suele ser un tipo de datos simple como una cadena, una fecha o un número, pero es posible almacenar también bloques de datos arbitrarios (ficheros),
- Es posible guardar en los valores referencias a otros registros, permitiendo definir relaciones entre registros.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registros públicos y privados

- Se pueden guardar registros de forma pública y privada, dependiendo de si se guardan en la base de datos pública o en la privada.
- Los registros públicos son accesibles a todos los usuarios del app, mientras que los privados son sólo visibles por el usuario actual por defecto.
- La base de datos pública está siempre disponible para lectura, aunque el usuario no se haya identificado con su cuenta de iCloud
- Para salvar registros en la base de datos pública es necesario que el usuario esté identificado, porque siempre se guarda el usuario propietario del registro.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Tecnología de transporte

- CloudKit no proporciona ninguna forma de almacenar datos localmente.
- Es un servicio para mover datos a y desde iCloud y no está pensado para reemplazar los modelos de datos ya existentes en tu app (CoreData).
- El objetivo del framework es complementar estos modelos con una forma de empaquetar los datos para iCloud y recibir actualizaciones posteriores sobre esos datos.
- Con CloudKit, tu eres el responsable de mover los datos desde tu app a iCloud y desde iCloud a la App. Aunque CloudKit proporciona facilidades para mantenerte informado cuando sucede un cambio, tu debes obtener esos cambios explícitamente.
- Debido a que eres el responsable de obtener y salvar los datos, debes de asegurarte de que los datos se obtienen en el momento oportuno y en el orden correcto, y de manejar los errores que se producen.

<!-- Tres líneas en blanco para la siguiente transparencia -->




#### Elementos de CloudKit

- Contenedores
- Bases de datos
- Registros
- Zonas de registros
- Identificadores
- Referencias 
- _Assets_


<!-- Tres líneas en blanco para la siguiente transparencia -->




#### Contenedores 

<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/containers.png" width=400px/>

- Múltiples apps y usuarios tienen acceso a iCloud, pero los datos se encuentran segregados y encapsulados en particiones llamadas `contenedores`.
- Los contenedores de tus apps no pueden ser usados por apps de otro desarrollador.
- Puedes compartir un contenedor entre varias apps.
- Existe un contenedor por defecto para cada app, pero puedes crear contenedores adicionales.
- El identificador por defecto se llama igual que el bundle ID de la app.
- Los contenedores adicionales deben tener un nombre único entre todas las apps de los desarrolladores. 
- **Los contenedores no pueden borrarse.**

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dashboard

- _Dashboard_ es una interfaz web con la que podemos gestionar nuestros contenedores
- El administrador del equipo de desarrollo puede gestionar permisos para el resto de miembros. Los permisos se definen a nivel de contenedor.
- [https://icloud.developer.apple.com/dashboard/](https://icloud.developer.apple.com/dashboard/)
- Permite:
    - Crear, visualizar, editar y borrar esquemas, registros, etc.
    - Estadísticas de uso
    - Administración de acceso
    - Configuración de despliegue

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dashboard: tipos de registros
<!-- .slide: data-background="#cbe0fc"-->

- Actualizamos los permisos de los miembros del equipo de la universidad para que puedan acceder al _dashboard_.

<img src="images/dashboard-permisos.png" width="900px"/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dashboard: tipos de registros

<img src="images/dashboard.png" width="900px"/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dashboard: registros

<img src="images/dashboard-records.png" width="900px"/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Dashboard: uso

<img src="images/dashboard-uso.png" width="900px"/>

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Clase CKContainer

- La clase con la que trabajar para gestionar el contenedor es [CKContainer](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKContainer_class/index.html#//apple_ref/occ/cl/CKContainer)
- La debemos usar para:
  - Obtener las bases de datos públicas y privadas
  - Obtener el identificador del contenedor
  - Determinar el estado del acceso de la cuenta iCloud del usuario
  - Solicitar y determinar permisos de la app
  - Ejecutar operaciones sobre el contenedor
  - Descubrir registros de usuarios 
- En CloudKit todas las operaciones son asíncronas: se pasa el código de _callback_ al que se llamará cuando la petición devuelva la respuesta.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Características sociales de CloudKit

- CloudKit permite descubrir usuarios que están usando nuestra app que:
    - Están en los contactos del usuario actual
    - Han dado el permiso `CKApplicationPermissionUserDiscoverability` a la app
- Se pueden buscar los usuarios por su dirección de correo electrónico registrada en el Apple Id.
- La función [`discoverAllContactUserInfosWithCompletionHandler`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKContainer_class/#//apple_ref/occ/instm/CKContainer/discoverAllContactUserInfosWithCompletionHandler:) de `CKContainer` permite obtener estos usuarios
    - Se le pasa como parámetro `completionHandler`, una función que la consulta ejecutará cuando se obtengan los resultados. Tiene dos parámetros:
        - `userInfos`: Un array de objetos `CKDiscoveredUserInfo`. Cada objeto en el array contiene información sobre el usuario, incluyendo el ID de su registro de usuario correspondiente. Si no hay usuarios, el array estará vacío.
        - Un objeto error si sucede algún problema, o `nil` si los IDs se han obtenido correctamente.


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Permiso para acceder a usuarios

<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/cloudkit-permiso.png" width=250px/>

- Para que otros usuarios puedan acceder a la información del usuario actual, hay que solicitarle su aprobación llamando a la función [`requestApplicationPermission`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKContainer_class/index.html#//apple_ref/occ/instm/CKContainer/requestApplicationPermission:completionHandler:)

- Se le pasa como parámetro `completionHandler` el manejador de la respuesta del usuario. Recibiremos dos parámetros, el `applicationPermissionStatus` (constante que indica lo que ha respondido el usuario) y un objeto `error` que será `nil` si todo ha ido correctamente.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo de código


```swift
// Solicitamos permiso para que el usuario se haga descubrible
container?.requestApplicationPermission(
    CKApplicationPermissions.UserDiscoverability,
    completionHandler: { (permissionStatus, error) in
        print("Permiso concedido: " +
        "\(permissionStatus == CKApplicationPermissionStatus.Granted)")})

// Obtenemos los usuarios de la app que han dado permiso
container?.discoverAllContactUserInfosWithCompletionHandler { (optUsers, error) in
    if let users = optUsers {
        for user in users {
           // usamos user.userRecordID para buscar 
           // registros públicos de un usuario
        }
    }
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Bases de datos

<img src="images/database-container.png" width=1000px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Bases de datos

<img src="images/database-container-2.png" width=1000px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Bases de datos

- Las bases de datos son instancias de la clase [`CKDatabase`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKDatabase_class/index.html#//apple_ref/occ/cl/CKDatabase)
- Cada app tiene acceso a dos bases de datos
  - Base de datos pública
  - Base de datos privada
- Se obtienen a través del `CKContainer`:

```swift
let container : CKContainer
let publicDB : CKDatabase
let privateDB : CKDatabase
 
init() {
  container = CKContainer.defaultContainer() 
  publicDB = container?.publicCloudDatabase 
  privateDB = container?.privateCloudDatabase 
}
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Bases de datos

<img src="images/databases-permisos.png" width=1000px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registros

<img src="images/records.png" width=700px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Registros

- Se definen con la clase [CKRecord](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKRecord_class/).
- Conjunto de parejas clave y valor con un identificador único de la clase. [`CKRecordID`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKRecordID_class/index.html#//apple_ref/occ/cl/CKRecordID)
- Se crean a partir de un tipo registro (definido en el esquema del contenedor) definido por un `String`.

```swift
let tareaRecord = CKRecord(recordType: "Tarea")
tareaRecord["tarea"] = "Limpiar el coche"
```
- Los tipos de registro se pueden crear dinámicamente al añadir un registro a una base de datos, en tiempo de desarrollo.
- Se añaden registros a una base de datos usando la función [`saveRecord`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKDatabase_class/index.html#//apple_ref/occ/instm/CKDatabase/saveRecord:completionHandler:), a la que hay que pasar un bloque que recibe el registro salvado y un error (en caso en que no se haya podido salvar).

```swift
publicDB?.saveRecord(tareaRecord, completionHandler: {
     (record: CKRecord?, error: NSError?) -> Void in
         print("Registro: \(record)")
         print("Error: \(error)")})
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Datos en los registros

- `NSString`: Cadenas
- `NSNumber`: Números, incluidos enteros y punto flotante.
- `NSData`: Bytes arbitrarios de datos (por ejemplo, la serialización binaria de un `struct`. No usar para almacenar ficheros binarios grandes, usar `CKAsset` en su lugar.
- `NSDate`: Fechas
- `CLLocation`: Coordenadas geográficas
- `CKReference`: Referencias a otros registros para crear relaciones entre ellos.
- `CKAsset`: Fichero binario.
- Arrays de todo lo anterior

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Relaciones entre registros: referencias

<img src="images/referencias.png" width=700px/>


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Referencias

<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/ckreference_owner_owned.png" width=600px/>

- Clase [CKReference](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKReference_class/index.html#//apple_ref/occ/cl/CKReference)
- El servidor entiende las relaciones _Padre_ _Hijo_
- Borrados en cascada
- Cuidado con los problemas de integridad, será normal que no existan referencias
- Se prefieren las referencias hacia atrás para modelar las relaciones anteriores

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Queries

- Para realizar una consulta se debe utilizar la clase [`CKQuery`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKQuery_class/index.html#//apple_ref/occ/cl/CKQuery) para buscar objetos que cumplen una determinada condición en una base de datos. 
- La consulta almacena los parámetros de búsqueda, incluyendo el tipo de registros a buscar, el criterio (predicado) a aplicar, y el parámetro de ordenación que aplicar a los resultados.
- El objeto de la búsqueda se usa para ejecutar una consulta en la base de datos usando el método [`performQuery`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKDatabase_class/index.html#//apple_ref/occ/instm/CKDatabase/performQuery:inZoneWithID:completionHandler:). Se le pasa un manejador al que se llamará cuando se obtengan los resultados. La operación de búsqueda se restringe a los objetos de una zona (se pasa `nil` para la zona por defecto).
- Para realizar consultas con más control sobre el número de registros devueltos, o utilizar un cursor definido por el límite de registros devueltos, hay que realizar una [`CKQueryOperation`](https://developer.apple.com/library/ios/documentation/CloudKit/Reference/CKQueryOperation_class/index.html#//apple_ref/occ/cl/CKQueryOperation).


<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Ejemplo

```swift
let predicate = NSPredicate(format: "tarea BEGINSWITH 'Limpiar'")
let query = CKQuery(recordType: "Tarea", predicate: predicate)
database?.performQuery(query, inZoneWithID: nil, completionHandler: {
    (results, error) in
    for result in results! {
        if let tarea = result["tarea"] {
            print("Tarea: \(tarea)")
        }
    }
})
```

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Suscripciones

<img src="images/subscription.png" width=600px/>

- Posibilidad de hacer consultas "permanentes"
- Ejecutadas en background por el servidor tras cada registro salvado
- Generan notificaciones push con los resultados

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Flujo de trabajo con CloudKit

- Obtener el objeto CKContainer
- Obtener la CKDatabase que contiene los registros que buscamos
- Identificar los registros que queremos conseguir:
  - Si conocemos sus IDs, crear un `CKFetchRecordsOperation` para recuperarlos
  - Si no conocemos los IDs crear una `CKQuery` con un criterio de búsqueda y ejecutar la búsqueda.
  - Asignar clausuras a los _completion handlers_ de las búsquedas. Usar las clausuras para procesar los resultados.
  - Pasar los objetos operación al método `addOperation` de la `CKDatabase` para encolar la operación. 
  - La operación envía asíncronamente los resultados a las clausuras.
- Para salvar registros creados o modificados, crear un `CKModifyRecordsOperation`con el objeto que queremos salvar. Ejecutar la operación de la misma forma que antes. El resultado llega de forma asíncrona e informa del estado de la operación.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### CloudKit JS
<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/cloudkit-webservices.png" width=450px/>

- [CloudKit JS](https://developer.apple.com/library/ios/documentation/CloudKitJS/Reference/CloudKitJavaScriptReference/index.html) es una librería que proporciona un API JavaScript para acceder a los datos en los contenedores CloudKit.
- Lanzado en WWDC 2015.
- Necesita un token generado en el dashboard para acceso seguro al API en la conexión servidor-servidor.
- Permite autentificarse y realizar peticiones seguras JavaScript desde una aplicación web para acceder a los datos de CloudKit.
- Sistema usado por la interfaz web de las apps de Apple en la página web de iCloud (Notas, Photos, etc.)
- En 2016 Apple ha lanzado un [servicio web](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html) para acceder a CloudKit usando peticiones REST.

<!-- Tres líneas en blanco para la siguiente transparencia -->



#### Práctica: ToDoList en CloudKit

<!-- .slide: data-background="#cbe0fc"-->
<!-- .slide: class="image-right"-->

<img style="margin-left:20px" src="images/to-do-list.png" width=200px/>

- Copia la carpeta `ToDoList` y renómbrala a `ToDoList CloudKit`.
- Modifica la nueva versión de la aplicación ToDoList para que se utilice la base de datos privada del contenedor `es.ua.mastermoviles.ToDoList` para guardar y recuperar las tareas pendientes de terminar del usuario (no cambies el número de tareas terminadas y mantenlo en el almacén de clave-valor).

<!-- Tres líneas en blanco para la siguiente transparencia -->



# Master Programación <br/> de Dispositivos Móviles



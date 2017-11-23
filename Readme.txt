Привет.

Краткое описание Nearby:

	Технология предназначена для создания Mesh сетей.

	Технология может работать в нескольких состояниях:
		а. Advertising - устройство объявляет себя видимым в сети.
		б. Discovery - устройство начинает поиск устройств находящихся 
		   в состоянии Advertising.	
	Nearby позволяет устройству находиться одновременно в обоих состояниях.
	
	Помимо двух состояний, Nearby во время работы может использовать два 
	вида стратегии-поведения:
		а.Cluster - устройство пытается установить связь со всеми, кто ему 
					доступен. В данном режиме скорость передачи данных 
					заметно снижена.
		б.Star - 	одно из устройств становится точкой доступа, остальные 
					коннектятся к нему. В данном режиме скорость передачи данных 
					заметно выше чем в стратегии Cluster.
					
1. Работа Nearby сервиса.

Начальные приготовления:

	а. Перед началом использования сервиса, необходимо создать
	экземпляр googleClient. После этого, у данного экземпляра
	необходимо вызвать метод .connect();
	
	б. Запустить Nearby Advertising, чтобы Ваше устройство 
	   стало видно другим.
	   
	Пример:
	    Nearby.Connections.startAdvertising(
                googleApiClient,						-> Запущенный googleClient
                myDevice.getName(),						-> Наше имя в сети
                SERVICE_ID,								-> Специальное имя нашего приложения
                connectionLifecycleCallback,			-> См. описание Callback'ов [0]
                new AdvertisingOptions(STRATEGY))		-> Стратегия работы
                .setResultCallback(statusAdvertising);	-> См. описание Callback'ов [1]
	  
	в. Запустить Nearby Discovery, для начала поиска доступных устройств.
	
	Пример:
		Nearby.Connections.startDiscovery(
                googleApiClient(),						-> Запущенный googleClient
                SERVICE_ID,								-> Специальное имя нашего приложения
                endpointDiscoveryCallback,				-> См. описание Callback'ов [2]
                new DiscoveryOptions(STRATEGY))			-> Стратегия работы
                .setResultCallback(statusDiscovery);	-> См. описание Callback'ов [3]
	
	
Установка первого коннекта:
	
	a. После того, как Nearby сообщит вам о доступных для подключения точках,
	   необходимо послать запрос другому устройству на подключение. Для этого
	   используется метод requestConnection.
	
	Пример:	
		Nearby.Connections.requestConnection(
                googleApiClient,					-> Запущенный googleClient
                myDevice.getName(),					-> Наше имя в сети
                endPointId,							-> EndPoint предоставленный Nearby
                connectionLifecycleCallback)		-> См. описание Callback'ов [0]
				.setResultCallback(requestStatus);	-> См. описание Callback'ов [4]
	
	б. Для того, чтобы при соединении устройств, на экзане не отображалось сообщение 
	   о разрешении коннекта, необходимо дать это разрешение автоматически. Для этого
	   используется метод acceptConnection.
	   
	Пример:	
		Nearby.Connections.acceptConnection(
                googleApiClient,					-> Запущенный googleClient
                endPointId,							-> EndPoint клиента, желающего присоединиться
                messageListener)					-> См. описание Callback'ов [5]
				.setResultCallback(acceptStatus);	-> См. описание Callback'ов [6]
	

Отправка первого сообщения:

	а. После успешной установки соединения, устройства способны передавать друг другу
	   сообщения. Перед передачей, сообщение необходимо перевести в набор байт, и после
	   этого передать в метод sendPayload.
	   
	Пример:
		Nearby.Connections.sendPayload(
                googleApiClient,					-> Запущенный googleClient
                endPointId,							-> EndPoint клиента, которому передаем сообщение
                Payload.fromBytes(message)			-> Упаковка сообщения в Payload
                .setResultCallback(sendStatus);		-> См. описание Callback'ов [7]
	

	Поздравляем! Вы успешно отправили свое первое сообщение, и для этого Вам даже не понадобилось
	интернет соединение.
	

P.s.	
	Т.к. работа Nearby ведется асинхронно, результаты будут приходить
	в Callback'и.
	
Описание основных Callback'ов:
	[0]. ConnectionLifecycleCallback:
			-> onConnectionInitiated(String endPoint, ConnectionInfo connectionInfo)
				- оповещает о начале иничиализации коннекта.
			-> onConnectionResult(String idEndPoint, ConnectionResolution result)
				- оповещает о результатах коннекта: успешно/ошибка
			-> onDisconnected(String endPoint)
				- оповещает о том, что клиент отключился
				
	[1]. StatusAdvertising - 
			-> onResult(Result)
				- оповещает о статусе начала становления доступным для подключения: успешно/ошибка
				
	[2]. EndpointDiscoveryCallback - 
			-> onEndpointFound(String idEndPoint, DiscoveredEndpointInfo discoveredEndpointInfo)
				- оповещает о нахождении новой точки доступной для подключения
			-> onEndpointLost(String idEndPoint)
				- оповещает о потере доступной точки
				
	[3]. StatusDiscovery - 
			-> onResult(Result)
				- оповещает о статусе начала поиска доступных устройств: успешно/ошибка
				
	[4]. RequestStatus - 
			-> onResult(Result)
				- оповещает о статусе запроса на соединение: успешно/ошибка
				
	[5]. MessageListener - 
			-> onPayloadReceived(String idEndPoint, Payload payload)
				- оповещает о поступлении сообщения от подключенного клиента
			-> onPayloadTransferUpdate(String s, PayloadTransferUpdate payloadTransferUpdate)
				- обновление статуса передачи сообщения (при передаче больших файлов)
				
	[6]. AcceptStatus - 
			-> onResult(Result)
				- оповещает о статусе подтверждения подключения от другого устройства: успешно/ошибка
	
	[7]. SendStatus - 
			-> onResult(Result)
				- оповещает о статусе отправки сообщения: успешно/ошибка
	
	
Более подробная информацио о Nearby:

	1. Описание:			  https://developers.google.com/android/reference/com/google/android/gms/nearby/connection/package-summary
	2. Описание ошибок:		  https://developers.google.com/android/reference/com/google/android/gms/nearby/connection/ConnectionsStatusCodes
	3. Как работать с Nearby: https://developers.google.com/nearby/connections/overview
	
# Документация к вебсокет чату

## События, которые сервер отправляет вам

### initial_data

Приходит сразу после подключения. Содержит все данные чатов.

```json
[
	{
		"event": {
			"id": 10,
			"name": "Event name",
			"iconsUUIDs": ["icon uuid1", "icon uuid2"],
			"location": "ONLINE",
			"dateFromS": 2514131321,
			"dateToS": 1957817098
		},
		"eventOwnerId": 14, // id владельца мероприятия
		"chats": [
			{
				"chatId": "some_chat_id", // уникальный идентификатор чата
				"requestStatus": ["NOT_SENDED", "AWAITING", "REJECTED", "ALLOWED"], // статус заявки, один из
				"user": {
					"id": 1,
					"name": "User 1",
					"imageUUID": "uuid1"
				},
				"messages": [
					// массив новых сообщений чата
					{
						"messageId": 10, // уникальный номер сообщения в чате (начинаются с 1)
						"text": "Привет!",
						"photos": ["uuid1", "uuid2"],
						"date": "2025-03-01T13:51:53.285Z",
						"fromUserId": 14,
						"toUserId": 16
					}
				],
				"myUnreadCount": 10,
				"interlocutorUnreadCount": 20
			}
		]
	}
]
```

### new_message

Приходит при отправке нового сообщения в чат. Новое сообщение будет лежать в chats[0].messages[0]

```json
{
	"event": {
		"id": 10,
		"name": "Event name",
		"iconsUUIDs": ["icon uuid1", "icon uuid2"],
		"location": "ONLINE",
		"dateFromS": 2514131321,
		"dateToS": 1957817098
	},
	"eventOwnerId": 14, // id владельца мероприятия
	"chats": [
		{
			"chatId": "some_chat_id", // уникальный идентификатор чата
			"requestStatus": ["NOT_SENDED", "AWAITING", "REJECTED", "ALLOWED"], // статус заявки, один из
			"user": {
				"id": 1,
				"name": "User 1",
				"imageUUID": "uuid1"
			},
			"messages": [
				// массив из одного элемента, который содержит новое сообщение
				{
					"messageId": 10, // уникальный номер сообщения в чате (начинаются с 1)
					"text": "Привет!",
					"photos": ["uuid1", "uuid2"],
					"date": "2025-03-01T13:51:53.285Z",
					"fromUserId": 14,
					"toUserId": 16
				}
			],
			"myUnreadCount": 10,
			"interlocutorUnreadCount": 20
		}
	]
}
```

## События, которые вы отправляете серверу

### send_message

Отправляет сообщение в чат. Вызывает событие _new_message_ у вас и у вашего собеседника.

```json
{
	"chatId": "id чата",
	"text": "Текст сообщения",
	"photos": ["массив uuid картинок"],
	"attachedPhotos": ["массив всех привязанных картинок"]
}
```

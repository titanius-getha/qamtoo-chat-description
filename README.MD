# Документация к вебсокет чату

## События, которые сервер отправляет вам

### chats

Приходит после отправки ивента open. Присылает список всех чатов.

```json
{ // карта всех чатов
    "some_chat_id_1": {
        "chatId": "some_chat_id_1",
        "event": { // данные привязанного мероприятия
            "id": 10,
            "name": "Event name",
            "dateFromS": 10000,
            "dateToS": 20000,
            "iconsUUIDs": ["some_icon_uuid", "some_another_icon_uuid"],
            "location": "OPEN_AIR" | "ONLINE",
            "userId": 10 // id владельца мероприятия
        },
        "myUnreadCount": 10,
        "interlocutorUnreadCount": 12,
        "messages": [],
        "requestStatus": "NOT_SENDED" | "AWAITING" | "REJECTED" | "ALLOWED",
        "userId": 2, // id пользователя, который написал мероприятию
        "userName": "Пользователь, отправивший заявку",
        "userAvatarUUID": "some_image_uuid"
    },
    "some_chat_id_1": {
        "chatId": "some_chat_id_2",
        "event": { // данные привязанного мероприятия
            "id": 10,
            "name": "Event name",
            "dateFromS": 10000,
            "dateToS": 20000,
            "iconsUUIDs": ["some_icon_uuid", "some_another_icon_uuid"],
            "location": "OPEN_AIR" | "ONLINE",
            "userId": 10 // id владельца мероприятия
        },
        "myUnreadCount": 5,
        "interlocutorUnreadCount": 10,
        "messages": [],
        "requestStatus": "NOT_SENDED" | "AWAITING" | "REJECTED" | "ALLOWED",
        "userId": 3, // id пользователя, который написал мероприятию
        "userName": "Другой пользователь, отправивший заявку",
        "userAvatarUUID": "some_another_image_uuid"
    },
}
```

### updates

Присылает массив обновлений. Обновление - объект с полями _type_ и _value_. _type_ содержит тип обновления - `NEW_CHAT`, `NEW_MESSAGE`, `UNREAD`, `REMOVE_CHAT`. Обновления приходят в гарантированном порядке NEW_CHAT -> NEW_MESSAGE -> UNREAD -> REMOVE_CHAT, таким образом при обработке массива сообщений, сначала будет создан чат, потом добавлено сообщение, а затем остальные действия.

```json
[
    {
        "type": "NEW_CHAT",
        "value": {
            "chatId": "someNewChatId",
            "event": { // данные привязанного мероприятия
                "id": 10,
                "name": "Event name",
                "dateFromS": 10000,
                "dateToS": 20000,
                "iconsUUIDs": ["some_icon_uuid", "some_another_icon_uuid"],
                "location": "OPEN_AIR" | "ONLINE",
                "userId": 10 // id владельца мероприятия
            },
            "messages": [],
            "myUnreadCount": 0,
            "interlocutorUnreadCount": 0,
            "requestStatus": "NOT_SENDED" | "AWAITING" | "REJECTED" | "ALLOWED",
            "userId": 2,
            "userName": "Имя пользователя",
            "userAvatarUUID": "some_image_uuid"
        }
    },
    {
        "type": "NEW_MESSAGE",
        "value": {
            "chatId": "someNewChatId",
            "message": {
                "text": "some new message text",
                "date": "some date",
                "photos": [],
                "fromUserId": 1,
                "toUserId": 2,
                "messageId": 10 // уникальный id сообщения в чате, начинается с 1
            },
        }
    },
    {
        "type": "UNREAD",
        "value": {
            "chatId": "someUniqueChatId",
            "myUnreadCount": 10,
            "interlocutorUnreadCount": 20
        }
    },
    {
        "type": "REMOVE_CHAT",
        "value": {
            "chatId": "someChatIdToRemove"
        }
    }
]
```

## События, которые вы отправляете серверу

### open

Отправьте, когда пользователь открывает чат. Вызывает событие chats у клиента.

### send_message

Отправляет сообщение в чат. Вызывает событие updates с типом NEW_MESSAGE у обоих участников чата

```json
{
	"chatId": "some unique chat id",
	"text": "Привет!",
	"photos": [],
	"attachedPhotos": []
}
```

# Документация к вебсокет чату

## События, которые сервер отправляет вам

### tab_data

Приходит после отправки ивента open_tab. Присылает комнаты и чаты для соответствующей страницы чатов (Основные или Заявки).

```json
{
    "rooms": [ //массив всех комнат, в которые сгруппированы чаты
        {
            "roomId": "some_room_id",
            "chatsIds": ["some_chat_id_1", "some_chat_id_1"], // chatId чатов в этой комнате
            "event": { // данные привязанного мероприятия
                "id": 10,
                "name": "Event name",
                "dateFromS": 10000,
                "dateToS": 20000,
                "iconsUUIDs": ["some_icon_uuid", "some_another_icon_uuid"],
                "location": "OPEN_AIR" | "ONLINE",
            },
            "eventOwnerId": 10 //id пользователя - владельца мероприятия
        }
    ],
    "chats": { // карта всех чатов
        "some_chat_id_1": {
            "chatId": "some_chat_id_1",
            "roomId": "some_room_id",
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
            "roomId": "some_room_id",
            "myUnreadCount": 5,
            "interlocutorUnreadCount": 10,
            "messages": [],
            "requestStatus": "NOT_SENDED" | "AWAITING" | "REJECTED" | "ALLOWED",
            "userId": 3, // id пользователя, который написал мероприятию
            "userName": "Другой пользователь, отправивший заявку",
            "userAvatarUUID": "some_another_image_uuid"
        },
    }
}
```

### updates

Присылает массив обновлений. Обновление - объект с полями _type_ и _value_. _type_ содержит тип обновления - `NEW_ROOM`, `NEW_CHAT`, `NEW_MESSAGE`, `UNREAD`, `REMOVE_CHAT`. Обновления приходят в гарантированном порядке NEW_ROOM -> NEW_CHAT -> NEW_MESSAGE -> UNREAD -> REMOVE_CHAT, таким образом при обработке массива сообщений, сначала будет создана комната, если требуется, затем будет создан чат, и только потом добавлено сообщение, а затем остальные действия.

```json
[
    {
        "type": "NEW_ROOM",
        "value": {
            "roomId": "someNewRoomId",
            "event": {
                "id": 10,
                "name": "some new event room",
                "iconsUUIDs": ["uuid1", "uuid2"],
                "dateFromS": 10000,
                "dateToS": 20000,
                "location": "OPEN_AIR" | "ONLINE",
            },
            "eventOwnerId": 1,
            "chatsIds": [] // важно! Массив приходит пустым, потому что в дальнейшем придут событие на создание чата, и вы обновите этот массив в своем локальном стейте
        },
    },
    {
        "type": "NEW_CHAT",
        "value": {
            "roomId": "someNewRoomId",
            "chatId": "someNewChatId",
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

### open_tab

Отправьте, когда пользователь выбирает вкладку, Основные или Заявки, в разделе чатов. Пришлет в ответ событие _tab_data_ с комнатами и чатами выбранной вкладки.

```json
{
    "sort": "main" | "requests" // какую вкладку открыли, Основные или Заявки
}
```

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

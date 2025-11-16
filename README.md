# Практика №8: Работа с MongoDB: подключение, создание коллекции, CRUD-операции
### Бакланова ЕС
### Группа ЭФМО-01-25

<b>ЗАПУСК</b>:
- docker-compose up -d --build
<b>Остановка</b>:
- docker-compose down

<b>Требования (Go ≥ 1.21, Docker, curl/Postman)</b>

<b>СДЕЛАНО</b>:
- основная часть 
- текстовый поиск
- aggregation pipeline

<b>MongoDB относится к классу документно-ориентированных СУБД:</b>
- Данные хранятся в коллекциях документов,
- Каждый документ — это JSON-подобный объект (формат BSON),
- Документы могут иметь разные поля и вложенные структуры.

<b>Команды</b>:

1. Создать

```bash 
  curl -Method POST http://localhost:8080/api/v1/notes `
  -ContentType "application/json" `
  -Body '{"title":"First note","content":"Hello Mongo!"}'
```

<img width="974" height="682" alt="image" src="https://github.com/user-attachments/assets/6b426ed7-c19b-48c8-a5da-acb1a71ba6b5" />

Ошибка дубликата ключа

<img width="974" height="413" alt="image" src="https://github.com/user-attachments/assets/ed9b473b-ff1e-42d2-831f-bfae018000d1" />


2. Список

```bash
curl -s "http://localhost:8080/api/v1/notes?limit=5&skip=0&q=first"
```

<img width="974" height="681" alt="image" src="https://github.com/user-attachments/assets/0109e2c9-1356-4737-a770-0fee971275be" />


3. Получить по id

```bash
curl -s http://localhost:8080/api/v1/notes/<id>
```


4. Поиск по titile, context

```bash
curl -s http://localhost:8080/api/v1/notes?q=<TEXT>
```

<img width="974" height="533" alt="image" src="https://github.com/user-attachments/assets/7b9c79f6-d130-4cb2-88c8-ee8cdcd2dbbc" />

<img width="974" height="529" alt="image" src="https://github.com/user-attachments/assets/61160f86-aae9-464a-9259-094f3ddd59dd" />


5. Частичное обновление

```bash
  curl -s -X PATCH http://localhost:8080/api/v1/notes/<id> \
  -H "Content-Type: application/json" \
  -d '{"content":"Updated content"}'
```

<img width="974" height="480" alt="image" src="https://github.com/user-attachments/assets/6eac1152-4e6f-414f-b2e1-5b84a7025434" />


6. Удаление

```bash
curl -i -X DELETE http://localhost:8080/api/v1/notes/<id>
```

<img width="974" height="365" alt="image" src="https://github.com/user-attachments/assets/bdfae176-b2fc-4b21-8a96-0363001feb30" />


7. Статистика (количество заметок и средняя длина контекста)
   
```bash
curl -s http://localhost:8080/api/v1/stats
```

<img width="974" height="441" alt="image" src="https://github.com/user-attachments/assets/2aaa472a-803b-462d-afd1-5502a2990181" />


<b>Отладка и типовые ошибки</b>
-	connection refused / i/o timeout — проверьте, что контейнер с Mongo запущен и порт 27017 проброшен.
-	Аутентификация — используйте ?authSource=admin и правильные root/secret.
-	duplicate key error — сработал уникальный индекс title. HTTP 409 возвращаем в create.
-	invalid ObjectID — при неверном id возвращайте 404 (мы мапим ошибку на ErrNotFound).
-	CORS (если будете дергать из браузера) — добавьте middleware CORS либо тестируйте через curl/Postman.


<b>Контрольные вопросы</b>

1.	Чем документная модель MongoDB принципиально отличается от реляционной? Когда она удобнее?

  	MongoDB: JSON-документы, гибкая схема.
  	SQL: таблицы, строгая схема.
  	Удобнее для изменчивых данных.
  
2.	Что такое ObjectID и зачем нужен _id? Как корректно парсить/валидировать его в Go?

   ObjectID - уникальный ID с временем создания. Парсится через primitive.ObjectIDFromHex()
  	
3.	Какие операции CRUD предоставляет драйвер MongoDB и какие операторы обновления вы знаете?

  	CRUD: InsertOne, FindOne, Find, UpdateOne, DeleteOne.
  	Опраторы: $set, $inc, $push, $pull
  	
4.	Как устроены индексы в MongoDB? Как создать уникальный индекс и чем он грозит при вставке?
    
    Индексы ускоряют поиск. Уникальный индекс: CreateIndex({field:1}, {unique:true}). Ошибка при дубликате может быть
  	
5.	Почему важно использовать context.WithTimeout при вызовах к базе? Что произойдет при его срабатывании?

    Context.WithTimeout предотвращает зависание,при срабатывании - отмена операции и возврат ошибки





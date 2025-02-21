# Мобильное приложение "Частная пекарня"

## 1. Бизнес-процесс создания заказа

### Описание процесса

1. Пользователь выбирает товары из меню выпечки.
2. Пользователь добавляет выбранные товары в корзину.
3. Пользователь подтверждает заказ.
4. Система проверяет доступность товаров:
   - Если товар недоступен, уведомить пользователя.
5. Пользователь выбирает способ оплаты.
6. Система создаёт запись о заказе.
7. Пользователь получает подтверждение заказа с информацией о времени доставки.

### Примечание

Диаграмма бизнес-процесса (BPMN) будет представлена в графическом виде.

---

## 2. Синхронизация данных между клиентом и сервером

Для каждого действия (создание, редактирование, отмена заказа, изменение данных, оплата) синхронизация осуществляется следующим образом:

- **Создание заказа:**

  I. Клиент отправляет запрос на создание заказа через API (`POST /orders`).

  II. Сервер сохраняет данные в базе данных (БД) и возвращает подтверждение с идентификатором заказа.

- **Редактирование заказа:**

  I. Клиент отправляет запрос на изменение заказа (`PUT /orders/{orderId}`).

  II. Сервер обновляет данные заказа в БД и возвращает обновлённую информацию.

- **Отмена заказа:**

  I. Клиент отправляет запрос на отмену заказа (`DELETE /orders/{orderId}`).

  II. Сервер обновляет статус заказа в БД.

- **Изменение персональных данных:**

  I. Клиент отправляет запрос (`PUT /users/{userId}`).

  II. Сервер обновляет данные в таблице пользователей.

- **Оплата заказа:**

  I. Клиент отправляет запрос на оплату (`POST /orders/{orderId}/payment`).

  II. Сервер выполняет обработку через платёжный шлюз и обновляет статус заказа на "Оплачен".

### Диаграммы последовательности и классов (UML):

 ![Image alt](https://github.com/popa2/mediasoft/blob/main/UML_Sequence_Sync.png)

---

## 3. Прототип экрана приложения

### Экран "Создание заказа"

- **Поля:** список товаров, кнопка "Добавить в корзину", корзина с итоговой стоимостью.
- **Кнопки:** "Подтвердить заказ", "Выбрать способ оплаты".
- **Описание интерфейса:**
  - Удобная структура.
  - Минималистичный дизайн.
  - Выделение кнопок действия для интуитивного использования.

### Примечание

Прототип экрана будет представлен графически.

---

## 4. Описание функции редактирования заказа

### Функциональность

Пользователь может изменить количество или состав товаров в своём заказе.

### Процесс

1. Пользователь выбирает заказ из списка.
2. Система загружает текущие данные заказа.
3. Пользователь вносит изменения.
4. Система отправляет запрос на сервер для обновления данных.
5. Сервер сохраняет изменения и возвращает обновлённый заказ.

### API

- **Метод:** `PUT /orders/{orderId}`
- **Параметры:**
  - Входные: идентификатор заказа, новые данные (товары, количество, статус).
  - Выходные: статус операции, обновлённые данные заказа.

### Диаграммы активностей и классов (UML):

![Image alt](https://github.com/popa2/mediasoft/blob/main/UML_Activity_EditOrder.png)


---

## 5. SQL-запросы

1. **Вывести покупателей с количеством осуществлённых покупок:**

   ```sql
   SELECT customers.name, COUNT(orders.id) AS purchase_count
   FROM customers
   JOIN orders ON customers.id = orders.customer_id
   GROUP BY customers.name;
   ```

2. **Общая стоимость товаров для каждого покупателя:**

   ```sql
   SELECT customers.name, SUM(order_items.quantity * products.price) AS total_spent
   FROM customers
   JOIN orders ON customers.id = orders.customer_id
   JOIN order_items ON orders.id = order_items.order_id
   JOIN products ON order_items.product_id = products.id
   GROUP BY customers.name
   ORDER BY total_spent DESC;
   ```

3. **Получить покупателей, купивших только один товар:**

   ```sql
   SELECT customers.name
   FROM customers
   JOIN orders ON customers.id = orders.customer_id
   JOIN order_items ON orders.id = order_items.order_id
   GROUP BY customers.name
   HAVING COUNT(order_items.product_id) = 1;
   ```


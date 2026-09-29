# spec.md — Специфікація системи бронювання коворкінгу

## Сутності та атрибути

### 1. **User** (Користувач)
Представляє членів коворкінгу.

| Атрибут | Тип | Опис |
|---------|-----|------|
| user_id | INT, PK | Унікальний ідентифікатор |
| email | VARCHAR(255), UNIQUE | Email користувача |
| full_name | VARCHAR(255) | Повне ім'я |
| phone | VARCHAR(20) | Номер телефону |
| created_at | TIMESTAMP | Дата реєстрації |
| is_active | BOOLEAN | Статус активності |

---

### 2. **Membership** (Пакет членства)
Визначає права та обмеження користувача.

| Атрибут | Тип | Опис |
|---------|-----|------|
| membership_id | INT, PK | Унікальний ідентифікатор |
| name | VARCHAR(100) | Назва пакету (e.g. "Basic", "Pro", "Premium") |
| max_hours_per_month | INT | Максимум годин на місяць |
| price_per_month | DECIMAL(10,2) | Вартість пакету |
| created_at | TIMESTAMP | Дата створення |

---

### 3. **UserMembership** (Членство користувача)
Зв'язує користувача з пакетом членства.

| Атрибут | Тип | Опис |
|---------|-----|------|
| user_membership_id | INT, PK | Унікальний ідентифікатор |
| user_id | INT, FK → User | Посилання на користувача |
| membership_id | INT, FK → Membership | Посилання на пакет |
| start_date | DATE | Дата початку членства |
| end_date | DATE | Дата закінчення членства |
| is_active | BOOLEAN | Чи активне членство |

---

### 4. **Space** (Простір)
Представляє фізичні простори, доступні для бронювання.

| Атрибут | Тип | Опис |
|---------|-----|------|
| space_id | INT, PK | Унікальний ідентифікатор |
| name | VARCHAR(100) | Назва простору (e.g. "Conference Room A") |
| space_type | ENUM | Тип: 'desk', 'meeting_room', 'private_office' |
| capacity | INT | Кількість осіб |
| location | VARCHAR(100) | Розташування в будівлі |
| price_per_hour | DECIMAL(10,2) | Вартість на годину |
| is_available | BOOLEAN | Доступність |

---

### 5. **Booking** (Бронювання)
Представляє резервування простору.

| Атрибут | Тип | Опис |
|---------|-----|------|
| booking_id | INT, PK | Унікальний ідентифікатор |
| user_id | INT, FK → User | Який користувач забронював |
| space_id | INT, FK → Space | Який простір забронював |
| booking_date | DATE | Дата бронювання |
| start_time | TIME | Час початку |
| end_time | TIME | Час закінчення |
| status | ENUM | Статус: 'pending', 'confirmed', 'cancelled', 'completed' |
| created_at | TIMESTAMP | Дата створення бронювання |

---

### 6. **Payment** (Платіж)
Відстежує платежі користувачів.

| Атрибут | Тип | Опис |
|---------|-----|------|
| payment_id | INT, PK | Унікальний ідентифікатор |
| user_membership_id | INT, FK → UserMembership | Членство, за яке платіж |
| amount | DECIMAL(10,2) | Сума платежу |
| payment_date | TIMESTAMP | Дата платежу |
| payment_method | ENUM | Спосіб: 'card', 'bank_transfer', 'cash' |
| status | ENUM | Статус: 'pending', 'completed', 'failed' |

---

### 7. **Review** (Оцінка/Коментар)
Дозволяє користувачам оцінювати простори.

| Атрибут | Тип | Опис |
|---------|-----|------|
| review_id | INT, PK | Унікальний ідентифікатор |
| user_id | INT, FK → User | Хто залишив оцінку |
| space_id | INT, FK → Space | Яких простір оцінюється |
| rating | INT | Оцінка від 1 до 5 |
| comment | TEXT | Текст коментаря |
| created_at | TIMESTAMP | Дата оцінки |

---

## Зв'язки між сутностями

| Сутність 1 | Зв'язок | Сутність 2 | Кардинальність | Опис |
|-----------|--------|-----------|---------------|----|
| User | бронює | Space | 1:M | Один користувач може забронювати багато просторів |
| User | оцінює | Space | 1:M | Один користувач може оцінити багато просторів |
| User | має | UserMembership | 1:M | Один користувач може мати одне або кілька членств (послідовно) |
| Membership | є | UserMembership | 1:M | Один пакет має багато користувачів |
| UserMembership | має | Payment | 1:M | Одне членство може мати багато платежів |
| Space | має | Booking | 1:M | Один простір може бути забронований багато разів |
| Space | має | Review | 1:M | Один простір може мати багато оцінок |

---

## Критерії нормалізації (3NF)

1. **1NF (Atomic Values)**: Усі атрибути містять атомарні значення
2. **2NF (Partial Dependencies)**: Всі неключові атрибути залежать від повного первинного ключа
3. **3NF (Transitive Dependencies)**: Немає перехідних залежностей; атрибути залежать лише від ключа

### Нормалізація по сутностях:

- **User**: ✅ Атомарні поля, без залежностей від часткових ключів
- **Membership**: ✅ Незалежна сутність, всі атрибути залежать від membership_id
- **UserMembership**: ✅ Зв'язкова сутність, з'єднує User та Membership
- **Space**: ✅ Атомарні поля, всі залежать від space_id
- **Booking**: ✅ Залежить від user_id та space_id; дати/часи атомарні
- **Payment**: ✅ Залежить від user_membership_id; без перехідних залежностей
- **Review**: ✅ Залежить від user_id та space_id; рейтинг атомарний

---

## Первинні та зовнішні ключі

### Первинні ключі (PK):
- User.user_id
- Membership.membership_id
- UserMembership.user_membership_id
- Space.space_id
- Booking.booking_id
- Payment.payment_id
- Review.review_id

### Зовнішні ключі (FK):
- UserMembership.user_id → User.user_id
- UserMembership.membership_id → Membership.membership_id
- Booking.user_id → User.user_id
- Booking.space_id → Space.space_id
- Payment.user_membership_id → UserMembership.user_membership_id
- Review.user_id → User.user_id
- Review.space_id → Space.space_id

---

## Додаткові бізнес-правила

1. Користувач може мати лише одне **активне** членство на раз
2. Бронювання не можуть перекриватися на один простір та дату
3. Користувач не може забронювати простір, якщо його членство неактивне
4. Платіж пов'язаний з членством, не з окремими бронюваннями
5. Оцінка може залишатися лише після завершеного бронювання

---

## Допущення та обмеження

- Один користувач = один email
- Один простір може бути зареєстрований у системі раз
- Час бронювання виражається у форматі HH:MM (24-годинна)
- Рейтинг коливається від 1 до 5
- Центральна валюта позначена як DECIMAL(10,2) для гнучкості

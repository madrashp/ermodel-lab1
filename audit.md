# audit.md — Аудит ER-моделі та виявлені розбіжності

## Етап 1: Аудит проти spec.md

### Перевірка сутностей

| Сутність | spec.md | ermodel.md | Статус |
|----------|---------|-----------|--------|
| User | ✅ | ✅ | Синхронізовано |
| Membership | ✅ | ✅ | Синхронізовано |
| UserMembership | ✅ | ✅ | Синхронізовано |
| Space | ✅ | ✅ | Синхронізовано |
| Booking | ✅ | ✅ | Синхронізовано |
| Payment | ✅ | ✅ | Синхронізовано |
| Review | ✅ | ✅ | Синхронізовано |

### Перевірка атрибутів

**User:**
- spec.md: user_id, email, full_name, phone, created_at, is_active
- ermodel.md: user_id (PK), email (UK), full_name, phone, created_at, is_active
- ✅ Статус: Синхронізовано (додано UK для email)

**Membership:**
- spec.md: membership_id, name, max_hours_per_month, price_per_month, created_at
- ermodel.md: membership_id (PK), name, max_hours_per_month, price_per_month, created_at
- ✅ Статус: Синхронізовано

**UserMembership:**
- spec.md: user_membership_id, user_id (FK), membership_id (FK), start_date, end_date, is_active
- ermodel.md: user_membership_id (PK), user_id (FK), membership_id (FK), start_date, end_date, is_active
- ✅ Статус: Синхронізовано

**Space:**
- spec.md: space_id, name, space_type, capacity, location, price_per_hour, is_available
- ermodel.md: space_id (PK), name, space_type, capacity, location, price_per_hour, is_available
- ✅ Статус: Синхронізовано

**Booking:**
- spec.md: booking_id, user_id (FK), space_id (FK), booking_date, start_time, end_time, status, created_at
- ermodel.md: booking_id (PK), user_id (FK), space_id (FK), booking_date, start_time, end_time, status, created_at
- ✅ Статус: Синхронізовано

**Payment:**
- spec.md: payment_id, user_membership_id (FK), amount, payment_date, payment_method, status
- ermodel.md: payment_id (PK), user_membership_id (FK), amount, payment_date, payment_method, status
- ✅ Статус: Синхронізовано

**Review:**
- spec.md: review_id, user_id (FK), space_id (FK), rating, comment, created_at
- ermodel.md: review_id (PK), user_id (FK), space_id (FK), rating, comment, created_at
- ✅ Статус: Синхронізовано

### Перевірка кардинальностей

| Зв'язок | spec.md | ermodel.md | Статус |
|--------|---------|-----------|--------|
| User → UserMembership | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| Membership → UserMembership | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| UserMembership → Payment | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| User → Booking | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| Space → Booking | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| User → Review | 1:M | 1:M (`\|\|--o{`) | ✅ OK |
| Space → Review | 1:M | 1:M (`\|\|--o{`) | ✅ OK |

### Перевірка первинних та зовнішних ключів

**Первинні ключі (PK):** ✅ Всі сутності мають унікальний PK

**Зовнішні ключі (FK):**
- User_Membership: user_id FK ✅, membership_id FK ✅
- Booking: user_id FK ✅, space_id FK ✅
- Payment: user_membership_id FK ✅
- Review: user_id FK ✅, space_id FK ✅

### Перевірка нормалізації (3NF)

| Сутність | 1NF | 2NF | 3NF | Статус |
|----------|-----|-----|-----|--------|
| User | ✅ | ✅ | ✅ | Нормалізована |
| Membership | ✅ | ✅ | ✅ | Нормалізована |
| UserMembership | ✅ | ✅ | ✅ | Нормалізована |
| Space | ✅ | ✅ | ✅ | Нормалізована |
| Booking | ✅ | ✅ | ✅ | Нормалізована |
| Payment | ✅ | ✅ | ✅ | Нормалізована |
| Review | ✅ | ✅ | ✅ | Нормалізована |

---

## Етап 2: Виявлені розбіжності та критерії перевірки

### РОЗБІЖНІСТЬ #1: Унікальність email у User

**Проблема:**
- spec.md: описано "Email користувача" як простий VARCHAR(255)
- ermodel.md: додано обмеження UNIQUE (UK) на email
- Критерій: "Один користувач = один email" є бізнес-правилом, але явно не позначено в таблиці spec.md

**Обґрунтування:**
Електронна пошта є природним унікальним ідентифікатором користувача в реальній системі. Це стандартна практика.

**Вирішення:**
Оновити spec.md для явного позначення email як UNIQUE KEY.

**Комітить:** `audit/fix-email-uniqueness`

---

### РОЗБІЖНІСТЬ #2: Сутність UserMembership як асоціативна таблиця

**Проблема:**
- spec.md: описує UserMembership як повноцінну сутність з власними атрибутами (start_date, end_date, is_active)
- Питання: Чи це асоціативна таблиця "багато-до-багатьох" чи потрібна окремої таблиця для історії членств?

**Обґрунтування:**
UserMembership — це не просто об'єднувальна таблиця, а **повноцінна сутність**, бо несе власний семантичний зміст: історія членств користувача, терміни дії, статус. Це відповідає критеріям асоціативної сутності (коли зв'язок несе власні атрибути).

**Вирішення:**
Моделювання коректне. Додати поясненння в spec.md про характер сутності.

**Комітить:** `audit/clarify-usermembership-nature`

---

### РОЗБІЖНІСТЬ #3: Відсутність обмеження на Booking щодо перекриття часів

**Проблема:**
- spec.md: описано бізнес-правило "Бронювання не можуть перекриватися на один простір та дату"
- ermodel.md: це правило НЕ представлено в схемі (це справа БД, але варто позначити)

**Обґрунтування:**
На рівні ER-моделі ми описуємо структуру, а бізнес-правила мають бути явно позначені в spec.md як обмеження унікальності. В реальній БД це була б UNIQUE constraint на (space_id, booking_date, start_time).

**Вирішення:**
Додати в spec.md явну примітку про унікальність комбінації (space_id, booking_date, start_time).

**Комітить:** `audit/add-booking-constraints`

---

## Этап 3: Результат аудиту

✅ **Усі сутності присутні**  
✅ **Усі атрибути синхронізовані**  
✅ **Кардинальності коректні**  
✅ **Первинні та зовнішні ключі позначені**  
✅ **Модель нормалізована до 3NF**  

⚠️ **Три виправлення через оновлення spec.md:**
1. Явне позначення email як UNIQUE KEY
2. Пояснення природи UserMembership як асоціативної сутності
3. Додання обмеження унікальності на комбінацію полів Booking

---

## Висновок

ER-модель коректна та адекватна вимогам завдання. Розбіжності виправляються оновленням документації (spec.md), а не змінами самої діаграми.

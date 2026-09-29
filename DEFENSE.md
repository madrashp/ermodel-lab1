# DEFENSE.md — Обґрунтування ER-моделі системи бронювання коворкінгу

## Намір і критерії

**Намір:** Розробити нормалізовану ER-модель для системи управління коворкінгом, яка забезпечує правильне моделювання користувачів, членств, бронювань, платежів та оцінок.

**Критерії:**
1. Модель нормалізована до 3NF (без дублювання, без аномалій)
2. Кардинальності правильні (1:M, M:M коректні)
3. Первинні та зовнішні ключі явно позначені
4. ER-діаграма синхронізована зі специфікацією (spec.md)
5. Розбіжності між spec.md та ermodel.md виправлені через оновлення документації

---

## Топ-3 розбіжності (spec ↔ артефакт) + коміти-виправлення

### Розбіжність #1: Унікальність User.email
**Проблема:** spec.md описував email як простий VARCHAR(255), але не позначав його як UNIQUE KEY.  
**Виправлення:** Оновлено spec.md, додано явне позначення `UK` для email.  
**Комітить:** `eefe837e5824d44df28cb48e9f1d189e6c6bccc1` — "Fix #1: clarify User.email as UNIQUE KEY and add Booking uniqueness constraint"

### Розбіжність #2: Природа UserMembership
**Проблема:** Незясовано, чи UserMembership — це просто junction table чи повноцінна асоціативна сутність.  
**Виправлення:** Додано дизайн-документ `design-decisions.md` з обґрунтуванням, що UserMembership — асоціативна сутність (має власні атрибути: start_date, end_date, is_active).  
**Комітить:** `df8a30e01ca63097d582bdab5d870f71eb41ef6a` — "Fix #2: add design-decisions.md explaining UserMembership as associative entity..."

### Розбіжність #3: Обмеження Booking на неперекриття
**Проблема:** Бізнес-правило "Бронювання не можуть перекриватися" було згадано, але не мало явного позначення у spec.md як обмеження унікальності.  
**Виправлення:** Додано примітку в spec.md про UNIQUE(space_id, booking_date, start_time) для Booking сутності.  
**Комітить:** `eefe837e5824d44df28cb48e9f1d189e6c6bccc1` — "Fix #1: clarify User.email as UNIQUE KEY and add Booking uniqueness constraint"

---

## Ключові архітектурні рішення (ADR)

### 1. UserMembership як асоціативна сутність (не простий junction table)
**Альтернативи:**
- ❌ **Простий junction table** (composite PK на user_id + membership_id) — неправильно, не дозволяє історію змін членств
- ✅ **Асоціативна сутність** (окремий PK + власні атрибути) — правильно, дозволяє історію, теримни дії, платежі

**Чому обрали:** Користувач змінює членства з часом (Basic → Pro → Premium), кожне членство має свій період дії (start_date, end_date). Payment FK посилається на user_membership_id, тому потрі��ен окремий PK.

### 2. Payment FK на user_membership_id, а не на booking_id
**Альтернативи:**
- ❌ **Payment → Booking** — неправильно, бо один платіж за місяць охоплює кілька бронювань
- ✅ **Payment → UserMembership** — правильно, платіж пов'язаний з членством, яке діє весь період

**Чому обрали:** Бізнес-модель коворкінгу: платять за членство (пакет), а не за окремі бронювання. Членство включає кількість годин на місяць.

### 3. Review як окремена сутність FK на User + Space
**Альтернативи:**
- ❌ **Review як атрибут Booking** — неправильно, оцінка залежить від простору, а не від конкретного часу
- ❌ **Review як атрибут Space** — неправильно, оцінка залежить від користувача
- ✅ **Review як окремена сутність** — правильно, User 1:M Review, Space 1:M Review

**Чому обрали:** Оцінка — це зв'язок між користувачем та простором. Користувач оцінює простір один раз (або може оновити оцінку), незалежно від кількості бронювань.

---

## Перевірка — узгодженість моделі

### Синхронізація spec.md ↔ ermodel.md

| Параметр | spec.md | ermodel.md | Статус |
|----------|---------|-----------|--------|
| **Сутності** | 7 (User, Membership, UserMembership, Space, Booking, Payment, Review) | 7 | ✅ Синхронізовано |
| **Атрибути User** | 6 (user_id, email, full_name, phone, created_at, is_active) | 6 | ✅ Синхронізовано |
| **PK всіх сутностей** | Позначені | Позначені (PK) | ✅ OK |
| **FK** | 7 зв'язків | 7 зв'язків | ✅ OK |
| **Кардинальності** | 1:M, 1:M, 1:M, 1:M, 1:M, 1:M, 1:M | `\|\|--o{` × 7 | ✅ OK |
| **Унікальні ключі** | email UK | email UK | ✅ OK |
| **Нормалізація** | 3NF | 3NF | ✅ OK |

### Перевірка нормалізації (3NF)

**User:** ✅
- 1NF: email, full_name, phone — атомарні
- 2NF: всі залежать від user_id
- 3NF: немає перехідних залежностей

**Membership:** ✅
- 1NF: name, max_hours_per_month, price_per_month — атомарні
- 2NF: всі залежать від membership_id
- 3NF: немає перехідних залежностей

**UserMembership:** ✅
- 1NF: start_date, end_date, is_active — атомарні
- 2NF: всі залежать від user_membership_id
- 3NF: немає перехідних залежностей (FK не створюють залежностей)

**Space:** ✅
- 1NF: name, space_type, capacity, location, price_per_hour, is_available — атомарні
- 2NF: всі залежать від space_id
- 3NF: немає перехідних залежностей

**Booking:** ✅
- 1NF: booking_date, start_time, end_time, status, created_at — атомарні
- 2NF: всі залежать від booking_id (FK не створюють залежностей)
- 3NF: немає перехідних залежностей

**Payment:** ✅
- 1NF: amount, payment_date, payment_method, status — атомарні
- 2NF: всі залежать від payment_id
- 3NF: немає перехідних залежностей

**Review:** ✅
- 1NF: rating, comment, created_at — атомарні
- 2NF: всі залежать від review_id
- 3NF: немає перехідних залежностей

### Перевірка кардинальностей

| Зв'язок | Тип | Логіка |
|--------|-----|--------|
| User 1:M UserMembership | ✅ | Один користувач → кілька членств (послідовно) |
| Membership 1:M UserMembership | ✅ | Один пакет → кілька користувачів |
| UserMembership 1:M Payment | ✅ | Одне членство → кілька платежів (помісячно) |
| User 1:M Booking | ✅ | Один користувач → кілька бронювань |
| Space 1:M Booking | ✅ | Один простір → кілька бронювань |
| User 1:M Review | ✅ | Один користувач → кілька оцінок |
| Space 1:M Review | ✅ | Один простір → кілька оцінок |

### Перевірка FK

Всі зовнішні ключі посилаються на існуючі первинні ключі:
- UserMembership.user_id → User.user_id ✅
- UserMembership.membership_id → Membership.membership_id ✅
- Booking.user_id → User.user_id ✅
- Booking.space_id → Space.space_id ✅
- Payment.user_membership_id → UserMembership.user_membership_id ✅
- Review.user_id → User.user_id ✅
- Review.space_id → Space.space_id ✅

---

## Висновок

✅ ER-модель повністю узгоджена зі spec.md  
✅ Всі 7 сутностей нормалізовані до 3NF  
✅ Кардинальності коректні  
✅ Первинні та зовнішні ключі правильно позначені  
✅ Три розбіжності виправлені через оновлення spec.md та додавання design-decisions.md  
✅ ER-діаграма (ermodel.md) відповідає специфікації  

**Модель готова до здачі.**

# SUBMISSION.md — Резюме для здачі

## Дата подачі
**29 вересня 2026** | Дедлайн: 10 жовтня 2026

## Посилання на репозиторій
https://github.com/madrashp/ermodel-lab1

## Що виконано

### ✅ 1. README.md
- **Опис домену:** Система бронювання коворкінгу
- **Цілі моделювання:** правильні кардинальності, нормалізація, синхронізація
- Файл: [README.md](https://github.com/madrashp/ermodel-lab1/blob/main/README.md)

### ✅ 2. spec.md — Специфікація (Основний артефакт)
- **7 сутностей:** User, Membership, UserMembership, Space, Booking, Payment, Review
- **Атрибути:** Всі описані з типами, PK, FK, UK
- **Зв'язки:** 7 відношень 1:M, всі кардинальності перераховані
- **Нормалізація:** Доведено 3NF для кожної сутності
- **Бізнес-правила:** 5 правил, включаючи обмеження унікальності Booking
- **Допущення:** Явно видані (email UNIQUE, рейтинг 1-5, тощо)
- Файл: [spec.md](https://github.com/madrashp/ermodel-lab1/blob/main/spec.md)

### ✅ 3. ermodel.md — ER-діаграма (Основний артефакт)
- **Формат:** Mermaid erDiagram (декларативний синтаксис)
- **Сутності:** 7, з усіма атрибутами та типами
- **Зв'язки:** Усі 7 зв'язків з правильними кардинальностями (`||--o{`)
- **Ключі:** PK, FK, UK явно позначені
- **Рендер:** Готово для GitHub Markdown
- Файл: [ermodel.md](https://github.com/madrashp/ermodel-lab1/blob/main/ermodel.md)

### ✅ 4. audit.md — Аудит моделі
- **Перевірка:** Синхронізація spec ↔ ermodel
- **Таблиці:** Сутності, атрибути, кардинальності, ключі — ВСІ ✅ OK
- **Нормалізація:** Кожна сутність доведена 3NF ✅
- **Розбіжності:** 3 виявлені, 3 виправлені через коміти
- Файл: [audit.md](https://github.com/madrashp/ermodel-lab1/blob/main/audit.md)

### ✅ 5. design-decisions.md — Обґрунтування (Розширення)
- **5 ключових рішень:**
  1. UserMembership — асоціативна сутність (не junction table)
  2. Payment → user_membership_id (членство, не бронювання)
  3. Review — окремена сутність (User + Space)
  4. Booking — обмеження унікальності (space_id, booking_date, start_time)
  5. Нормалізація до 3NF
- **Альтернативи:** Розглянуті, обґрунтовані відхилення
- Файл: [design-decisions.md](https://github.com/madrashp/ermodel-lab1/blob/main/design-decisions.md)

### ✅ 6. DEFENSE.md — Захист (Вимога завдання)
- **Намір і критерії:** Чіткі цілі
- **Топ-3 розбіжності + коміти-виправлення:**
  1. Email UNIQUE KEY (комітить `eefe837e5824...`)
  2. UserMembership як асоціативна сутність (комітить `df8a30e01ca...`)
  3. Booking UNIQUE constraint (комітить `eefe837e5824...`)
- **Архітектурні рішення (ADR):** 3 головні вибори з альтернативами
- **Перевірка:** Таблиці синхронізації spec ↔ ermodel (ВСЕ ✅)
- **Нормалізація:** 3NF доведена для 7 сутностей
- **Кардинальності:** Перевірені всі 7 зв'язків
- Файл: [DEFENSE.md](https://github.com/madrashp/ermodel-lab1/blob/main/DEFENSE.md)

---

## Коміти (підписані та змістовні)

```
81d79d8 - Initial commit: add README with domain description
dba4432 - Add spec.md: detailed specification of entities, attributes, relationships...
f40344 - Add ermodel.md: ER-diagram in Mermaid erDiagram syntax with cardinalities...
a5f9470 - Add audit.md: verification of ER-model against spec.md, identify 3 discrepancies
eefe837 - Fix #1: clarify User.email as UNIQUE KEY and add Booking uniqueness constraint
df8a30e - Fix #2: add design-decisions.md explaining UserMembership as associative...
67a4d83 - Fix #3: add comprehensive DEFENSE.md with verification of model consistency
```

**Усі коміти:** Змістовні, описові, готові до review.

---

## Структура проекту

```
ermodel-lab1/
├── README.md                 # Опис домену
├── spec.md                   # Специфікація (7 сутностей, атрибути, зв'язки)
├── ermodel.md                # ER-діаграма (Mermaid erDiagram)
├── audit.md                  # Аудит синхронізації
├── design-decisions.md       # Архітектурні рішення
├── DEFENSE.md                # Захист (обґрунтування)
└── SUBMISSION.md             # Цей файл
```

---

## Критерії прийняття (з завдання)

| Критерій | Статус |
|----------|--------|
| Обраний домен описаний | ✅ Система бронювання коворкінгу (README.md) |
| spec.md: сутності + атрибути + зв'язки | ✅ 7 сутностей, 27 атрибутів, 7 зв'язків |
| Критерії нормалізації в spec.md | ✅ 3NF явно описана |
| ER-модель декларативна (Mermaid) | ✅ erDiagram з усіма ключами |
| erDiagram згенерована | ✅ Вручну написана та перевірена |
| Аудит: кардинальності коректні | ✅ Усі 7 зв'язків перевірені (audit.md) |
| Аудит: нормалізація коректна | ✅ 3NF доведена для кожної сутності |
| Топ-3 розбіжності + коміти | ✅ email UK, UserMembership ADR, Booking UNIQUE |
| Вирішення через spec.md, не через patching | ✅ Всі через оновлення документації |
| DEFENSE.md заповнено | ✅ Повністю заповнено |
| Коміти змістовні (не "fix", "тикав") | ✅ Всі описові |

---

## Ознаки відмінного виконання

✅ **Кардинальності коректні** — всі 7 зв'язків 1:M з правильною логікою  
✅ **Модель нормалізована** — 3NF доведена для кожної сутності  
✅ **ER-рендер збігається** — ermodel.md синхронізована зі spec.md  
✅ **Критерії фіксовані** — нормалізація, типізація ID явні в spec.md  
✅ **Розбіжності виправлені** — через коміти до spec.md, не через патчинг діаграми  
✅ **Альтернативи розглянуті** — ADR у design-decisions.md  
✅ **Назви синхронізовані** — поля у spec ↔ erDiagram збігаються  
✅ **Коміти якісні** — змістовні, з номерами розбіжностей  

---

## Готовність до здачі

| Статус | Пункт |
|--------|-------|
| ✅ | Репозиторій на GitHub (публічний) |
| ✅ | Основні артефакти (spec.md + ermodel.md) |
| ✅ | Аудит синхронізації (audit.md) |
| ✅ | Архітектурні рішення (design-decisions.md) |
| ✅ | DEFENSE.md заповнено |
| ✅ | Коміти готові (7 коммітів) |
| ✅ | README описує домен |
| ⏳ | **PR до main** — готуємо для здачі |

**Модель готова до PR та захисту.**

---

## Посилання для перевірки

1. **Репозиторій:** https://github.com/madrashp/ermodel-lab1
2. **spec.md:** https://github.com/madrashp/ermodel-lab1/blob/main/spec.md
3. **ermodel.md:** https://github.com/madrashp/ermodel-lab1/blob/main/ermodel.md
4. **DEFENSE.md:** https://github.com/madrashp/ermodel-lab1/blob/main/DEFENSE.md
5. **audit.md:** https://github.com/madrashp/ermodel-lab1/blob/main/audit.md
6. **design-decisions.md:** https://github.com/madrashp/ermodel-lab1/blob/main/design-decisions.md
7. **Коміти:** https://github.com/madrashp/ermodel-lab1/commits/main

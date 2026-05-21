# 📐 Шаблоны артефактов

Готовые структуры для черновиков требований. Копируйте, заполняйте `[TBD]`, проверяйте по чек-листу.

## 🛠 Правила
1. Не удаляйте `[TBD]` без подтверждения бизнеса/архитектора
2. Связывайте артефакт с версией промпта в метаданных
3. При импорте в Confluence используйте `Code Block` для YAML/JSON
4. Храните черновики в ветке задачи, мержьте только после ревью

## US-<ID>: <Краткое название>

**Как** `<роль пользователя>`,  
**я хочу** `<конкретное действие>`,  
**чтобы** `<бизнес-ценность>`.

### Критерии приёмки
```gherkin
Scenario: Счастливый путь
  Given `<предусловие>`
  When `<действие>`
  Then `<ожидаемый результат>`

Scenario: Альтернативный путь / Ошибка
  Given `<предусловие>`
  When `<действие, вызывающее альтернативу>`
  Then `<ожидаемый результат>`

## Статусная модель: `<Название процесса>`

| Текущий статус | Событие | Следующий статус | Guard (условие) | Побочный эффект |
|---------------|---------|-----------------|----------------|----------------|
| `created` | Оплата получена | `paid` | Сумма = заказ | Запись в биллинг |
| `paid` | Отмена | `cancelled` | Право отмены | Инициировать возврат |
| `processing` | Ошибка валидации | `failed` | Таймаут > 30с | Лог, уведомление |
| `*` | Fallback | `cancelled` | [TBD] Лимит重试 | Закрыть тикет |

### Правила валидации
- [ ] Все статусы имеют исходящие переходы
- [ ] Guard-условия тестируемы
- [ ] Rollback-сценарии описаны
- [ ] [TBD] помечены таймауты/лимиты

openapi: 3.1.0
info:
  title: Draft API - [Resource Name]
  version: 0.1.0-draft
paths:
  /<resource>:
    post:
      summary: Создание ресурса
      operationId: createResource
      parameters:
        - name: Idempotency-Key
          in: header
          required: true
          schema: { type: string, format: uuid }
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              properties:
                field_1: { type: string, pattern: '^[A-Z0-9]+$' }
                field_2: { type: integer, minimum: 0 }
              required: [field_1]
      responses:
        '201': { description: Created, content: { application/json: { schema: { $ref: '#/components/schemas/Resource' } } } }
        '400': { description: Validation Error }
        '409': { description: Conflict }
        '422': { description: Business Rule Violation }
components:
  schemas:
    Resource:
      type: object
      properties:
        id: { type: string, format: uuid }
        status: { type: string, enum: [pending, active] }

## Маппинг: `<Система А>` → `<Система Б>`

| Поле A | Тип | Обяз. | Поле Б | Трансформация | Примечание |
|--------|-----|-------|--------|---------------|------------|
| `client.inn` | str(10) | ✅ | `counterparty.tax_id` | Валидация ФНС | [TBD] Иностранные ИНН |
| `report.size` | int | ✅ | `storage.max_bytes` | * 1024^2 | Лимит: 50MB |
| `period.start` | date | ❌ | `filter.date_from` | ISO 8601, UTC | По умолч.: 1-е число |

### Правила
- [ ] Все обязательные поля замаплены
- [ ] Трансформации документируемы
- [ ] Обработаны `null` и невалидные форматы

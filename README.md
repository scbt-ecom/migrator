# Пакет Migrator

## Обзор

Пакет `migrator` — это библиотека на Go, предназначенная для управления миграциями схемы базы данных. Она предоставляет простой и эффективный способ применения SQL-миграций к базе данных, гарантируя, что каждая миграция будет применена только один раз и в правильном порядке. Пакет также включает механизмы для предотвращения одновременных миграций от разных сервисов, обеспечивая целостность данных.

## Возможности

- **Блокировка миграций**: Предотвращает одновременные миграции от разных сервисов.
- **Автоматическая инициализация схемы**: Инициализирует необходимые таблицы для отслеживания миграций.
- **Файловые миграции**: Миграции определяются в файлах с расширением `.up.sql`, которые применяются в лексикографическом порядке.
- **Обработка ошибок**: Предоставляет подробные сообщения об ошибках и механизмы отката для обеспечения согласованности данных.

## Установка
```
go get github.com/scbt-ecom/migrator
```

Чтобы использовать пакет `migrator` в вашем проекте на Go, вы можете импортировать его следующим образом:

```go
import "github.com/scbt-ecom/migrator"
```

## Использование

### Создание экземпляра Migrator

Для создания нового экземпляра `Migrator` вам нужно предоставить соединение с базой данных, идентификатор сервиса и путь к директории, содержащей ваши файлы миграций.

```go
import (
    "database/sql"
    "github.com/yourusername/migrator"
)

func main() {
    // Открываем соединение с базой данных
    db, err := sql.Open("mysql", "user:password@tcp(127.0.0.1:3306)/dbname")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()

    // Создаем новый экземпляр Migrator
    migrator := migrator.NewMigrator(db, "service-1", "./migrations")

    // Инициализируем схему миграций
    if err := migrator.InitSchema(); err != nil {
        log.Fatal(err)
    }

    // Применяем ожидающие миграции
    if err := migrator.ApplyMigrations(); err != nil {
        log.Fatal(err)
    }
}
```

### Файлы миграций

Файлы миграций должны быть помещены в директорию, указанную при создании экземпляра `Migrator`. Каждый файл миграции должен иметь расширение `.up.sql` и содержать SQL-команды для выполнения.

Пример файла миграции (`001_create_users_table.up.sql`):

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE
);
```

### Выполнение миграций

Чтобы применить ожидающие миграции, просто вызовите метод `ApplyMigrations` на вашем экземпляре `Migrator`. Метод автоматически применит любые миграции, которые еще не были применены к базе данных.

```go
if err := migrator.ApplyMigrations(); err != nil {
    log.Fatal(err)
}
```

### Механизм блокировки

Пакет `migrator` включает механизм блокировки для предотвращения одновременных миграций от разных сервисов. Когда сервис пытается применить миграции, он сначала пытается получить блокировку. Если блокировка уже удерживается другим сервисом, процесс миграции завершится ошибкой.

### Обработка ошибок

Пакет `migrator` предоставляет подробные сообщения об ошибках и механизмы отката для обеспечения согласованности данных. Если миграция завершается неудачно, вся транзакция откатывается, и ошибка возвращается вызывающей стороне.

## Вклад в проект

Вклады в пакет `migrator` приветствуются! Не стесняйтесь отправлять вопросы, запросы на извлечение или предложения по улучшению.

## Лицензия

Этот проект лицензирован под лицензией MIT. Подробности смотрите в файле [LICENSE](LICENSE).

---

Следуя этим инструкциям, вы можете легко интегрировать пакет `migrator` в свой проект на Go и эффективно управлять миграциями схемы базы данных.
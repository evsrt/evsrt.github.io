---
layout: default
title: postgres
parent: kb
---

<details close markdown="block">
  <summary>
    Содержание
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

# postgres.conf logs
Для обеспечения безопасности и эффективного расследования инцидентов в PostgreSQL важно настроить параметры логирования. Вот основные параметры, которые следует учитывать:

### 1. Настройки логирования

- **`logging_collector`**: Включите сборщик логов, установив этот параметр в `on`. Это позволит PostgreSQL записывать логи в файлы.

- **`log_directory`**: Укажите директорию для хранения логов. Рекомендуется использовать отдельную директорию, чтобы облегчить управление логами.

- **`log_filename`**: Настройте формат имени файлов логов, например, `postgresql-%Y-%m-%d_%H%M%S.log`.

- **`log_rotation_age`**: Установите время, через которое логи будут ротироваться. Например, `1d` (один день).

- **`log_rotation_size`**: Определите максимальный размер файла лога до его ротации. Например, `100MB`.

### 2. Уровень детализации логов

- **`log_min_messages`**: Установите уровень сообщений, которые должны записываться. Рекомендуется `warning` или `error` для общего логирования, но можно использовать `info` для более детальной информации.

- **`log_min_error_statement`**: Определите уровень ошибок, для которых будут записываться SQL-запросы. Рекомендуется `error` для безопасности.

- **`log_statement`**: Настройте, какие SQL-запросы логировать. Вы можете установить `ddl` (только DDL-запросы) или `all` (все запросы) для более детального мониторинга.

### 3. Логирование активности

- **`log_connections`**: Включите логирование успешных подключений к базе данных.

- **`log_disconnections`**: Включите логирование отключений от базы данных.

- **`log_duration`**: Записывайте время выполнения каждого запроса. Это может помочь в анализе производительности и выявлении аномалий.

- **`log_lock_waits`**: Включите логирование ожидания блокировок для выявления проблем с производительностью и потенциальных инцидентов.

### 4. Логирование выполнения запросов

- **`track_activities`**: Включите отслеживание активностей для мониторинга текущих запросов.

- **`track_counts`**: Включите отслеживание статистики, чтобы иметь возможность анализировать частоту выполнения запросов.

### 5. Настройки безопасности

- **`log_line_prefix`**: Настройте префикс для строк логов. Например, можно добавить метку времени, имя пользователя и IP-адрес клиента для удобства анализа.

### Пример конфигурации

Вот пример конфигурации для файла `postgresql.conf`:

```plaintext
logging_collector = on
log_directory = 'pg_log'
log_filename = 'postgresql-%Y-%m-%d_%H%M%S.log'
log_rotation_age = '1d'
log_rotation_size = '100MB'
log_min_messages = warning
log_min_error_statement = error
log_statement = 'ddl'
log_connections = on
log_disconnections = on
log_duration = on
log_lock_waits = on
track_activities = on
track_counts = on
log_line_prefix = '%m [%p]: [%l-1] user=%u,db=%d,client=%h '
```

### Заключение

Регулярно проверяйте и анализируйте логи для выявления подозрительной активности и быстрого реагирования на инциденты. Убедитесь, что доступ к логам ограничен только для авторизованных пользователей.


# ROLE
В PostgreSQL атрибуты роли (roles) определяют права и 
возможности, предоставляемые роли. Роли в PostgreSQL могут быть либо пользователями (логинами), либо группами пользователей. Вот основные атрибуты роли:

1. **LOGIN** - роль может выполнять вход в систему. Это атрибут по умолчанию для новых пользователей.
2. **NOLOGIN** - роль не может выполнять вход в систему. Обычно используется для групп ролей.
3. **SUPERUSER** - роль является суперпользователем и имеет все права в базе данных.
4. **NOSUPERUSER** - роль не является суперпользователем.
5. **CREATEDB** - роль может создавать базы данных.
6. **NOCREATEDB** - роль не может создавать базы данных.
7. **CREATEROLE** - роль может создавать и изменять другие роли.
8. **NOCREATEROLE** - роль не может создавать и изменять другие роли.
9. **INHERIT** - роль наследует права от ролей, которые ей назначены.
10. **NOINHERIT** - роль не наследует права от ролей, которые ей назначены.
11. **REPLICATION** - роль может выполнять действия, связанные с репликацией (например, создавать слот репликации).
12. **NOREPLICATION** - роль не может выполнять действия, связанные с репликацией.
13. **BYPASSRLS** - роль может обходить политику управления доступом к строкам (Row Level Security).
14. **NOBYPASSRLS** - роль не может обходить политику управления доступом к строкам.
15. **CONNECTION LIMIT** - ограничивает количество одновременных соединений, которые может установить роль.

Примеры создания ролей с различными атрибутами:

```sql
-- Создание роли пользователя с правами суперпользователя
CREATE ROLE superuser_role WITH SUPERUSER LOGIN PASSWORD 'password';

-- Создание роли пользователя без прав суперпользователя и без возможности создавать базы данных
CREATE ROLE normal_user WITH NOSUPERUSER NOCREATEDB LOGIN PASSWORD 'password';

-- Создание группы ролей без возможности входа
CREATE ROLE readonly_group WITH NOLOGIN;
```

Для изменения атрибутов существующей роли используется команда `ALTER ROLE`:

```sql
-- Добавление прав на создание баз данных роли normal_user
ALTER ROLE normal_user CREATEDB;

-- Удаление прав суперпользователя у роли superuser_role
ALTER ROLE superuser_role NOSUPERUSER;
```

Эти атрибуты позволяют гибко управлять правами доступа и возможностями пользователей в базе данных PostgreSQL.

# 100 команд для администрирования postgres 
### Управление ролями и пользователями

1. **Создание роли:**
   ```sql
   CREATE ROLE role_name;
   ```

2. **Создание пользователя:**
   ```sql
   CREATE USER user_name WITH PASSWORD 'password';
   ```

3. **Удаление роли:**
   ```sql
   DROP ROLE role_name;
   ```

4. **Удаление пользователя:**
   ```sql
   DROP USER user_name;
   ```

5. **Изменение роли:**
   ```sql
   ALTER ROLE role_name WITH LOGIN;
   ```

6. **Назначение роли другой роли:**
   ```sql
   GRANT role_name TO another_role;
   ```

7. **Отзыв роли у другой роли:**
   ```sql
   REVOKE role_name FROM another_role;
   ```

8. **Добавление прав на создание баз данных:**
   ```sql
   ALTER ROLE role_name CREATEDB;
   ```

9. **Удаление прав на создание баз данных:**
   ```sql
   ALTER ROLE role_name NOCREATEDB;
   ```

10. **Добавление прав суперпользователя:**
    ```sql
    ALTER ROLE role_name SUPERUSER;
    ```

11. **Удаление прав суперпользователя:**
    ```sql
    ALTER ROLE role_name NOSUPERUSER;
    ```

### Управление правами доступа

12. **Предоставление прав на таблицу:**
    ```sql
    GRANT SELECT, INSERT ON table_name TO role_name;
    ```

13. **Отзыв прав на таблицу:**
    ```sql
    REVOKE SELECT, INSERT ON table_name FROM role_name;
    ```

14. **Предоставление прав на все таблицы в схеме:**
    ```sql
    GRANT SELECT, INSERT ON ALL TABLES IN SCHEMA schema_name TO role_name;
    ```

15. **Отзыв прав на все таблицы в схеме:**
    ```sql
    REVOKE SELECT, INSERT ON ALL TABLES IN SCHEMA schema_name FROM role_name;
    ```

16. **Предоставление прав на схему:**
    ```sql
    GRANT USAGE ON SCHEMA schema_name TO role_name;
    ```

17. **Отзыв прав на схему:**
    ```sql
    REVOKE USAGE ON SCHEMA schema_name FROM role_name;
    ```

18. **Предоставление прав на базу данных:**
    ```sql
    GRANT CONNECT ON DATABASE db_name TO role_name;
    ```

19. **Отзыв прав на базу данных:**
    ```sql
    REVOKE CONNECT ON DATABASE db_name FROM role_name;
    ```

20. **Предоставление прав на функцию:**
    ```sql
    GRANT EXECUTE ON FUNCTION func_name TO role_name;
    ```

21. **Отзыв прав на функцию:**
    ```sql
    REVOKE EXECUTE ON FUNCTION func_name FROM role_name;
    ```

22. **Предоставление прав на последовательность:**
    ```sql
    GRANT USAGE, SELECT ON SEQUENCE seq_name TO role_name;
    ```

23. **Отзыв прав на последовательность:**
    ```sql
    REVOKE USAGE, SELECT ON SEQUENCE seq_name FROM role_name;
    ```

### Политики безопасности строк (Row Level Security)

24. **Включение политики RLS для таблицы:**
    ```sql
    ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;
    ```

25. **Отключение политики RLS для таблицы:**
    ```sql
    ALTER TABLE table_name DISABLE ROW LEVEL SECURITY;
    ```

26. **Создание политики RLS:**
    ```sql
    CREATE POLICY policy_name ON table_name FOR SELECT TO role_name USING (condition);
    ```

27. **Удаление политики RLS:**
    ```sql
    DROP POLICY policy_name ON table_name;
    ```

### Управление аудитом и журналированием

28. **Включение параметра журнала:**
    ```sql
    ALTER SYSTEM SET log_statement = 'all';
    ```

29. **Отключение параметра журнала:**
    ```sql
    ALTER SYSTEM SET log_statement = 'none';
    ```

30. **Включение аудита для определенной таблицы:**
    ```sql
    CREATE EXTENSION IF NOT EXISTS pgaudit;
    ALTER SYSTEM SET pgaudit.log = 'read, write';
    ```

31. **Настройка журнала сессий:**
    ```sql
    ALTER SYSTEM SET log_connections = 'on';
    ALTER SYSTEM SET log_disconnections = 'on';
    ```

32. **Настройка уровня журналирования ошибок:**
    ```sql
    ALTER SYSTEM SET log_min_error_statement = 'error';
    ```

### Управление сертификатами и шифрованием

33. **Настройка SSL для сервера:**
    ```sql
    ALTER SYSTEM SET ssl = 'on';
    ```

34. **Указание пути к сертификатам:**
    ```sql
    ALTER SYSTEM SET ssl_cert_file = '/path/to/server.crt';
    ALTER SYSTEM SET ssl_key_file = '/path/to/server.key';
    ALTER SYSTEM SET ssl_ca_file = '/path/to/root.crt';
    ```

35. **Включение шифрования SSL для клиентов:**
    ```sql
    ALTER SYSTEM SET ssl_prefer_server_ciphers = 'on';
    ```

### Резервное копирование и восстановление

36. **Создание резервной копии базы данных:**
    ```sh
    pg_dump db_name > db_name_backup.sql
    ```

37. **Восстановление базы данных из резервной копии:**
    ```sh
    psql db_name < db_name_backup.sql
    ```

### Мониторинг и управление производительностью

38. **Настройка параметров журналирования запросов:**
    ```sql
    ALTER SYSTEM SET log_duration = 'on';
    ```

39. **Просмотр текущих процессов:**
    ```sql
    SELECT * FROM pg_stat_activity;
    ```

40. **Завершение процесса:**
    ```sql
    SELECT pg_terminate_backend(pid);
    ```

### Управление подключениями

41. **Ограничение количества подключений:**
    ```sql
    ALTER ROLE role_name CONNECTION LIMIT 5;
    ```

42. **Отключение роли от возможности подключения:**
    ```sql
    ALTER ROLE role_name NOLOGIN;
    ```

### Полезные запросы для безопасности

43. **Проверка прав на таблицы:**
    ```sql
    SELECT grantee, table_catalog, table_schema, table_name, privilege_type
    FROM information_schema.role_table_grants
    WHERE grantee = 'role_name';
    ```

44. **Просмотр всех пользователей:**
    ```sql
    SELECT usename FROM pg_user;
    ```

45. **Просмотр всех ролей:**
    ```sql
    SELECT rolname FROM pg_roles;
    ```

46. **Просмотр прав пользователей на функции:**
    ```sql
    SELECT nspname AS schema, proname AS function, proowner::regrole AS owner
    FROM pg_proc p
    JOIN pg_namespace n ON p.pronamespace = n.oid;
    ```

### Примеры использования схем и контекстов безопасности

47. **Создание схемы и назначение прав:**
    ```sql
    CREATE SCHEMA schema_name AUTHORIZATION role_name;
    GRANT USAGE ON SCHEMA schema_name TO another_role;
    ```

48. **Перемещение таблицы в другую схему:**
    ```sql
    ALTER TABLE table_name SET SCHEMA new_schema_name;
    ```

49. **Создание контекста безопасности (security context):**
    ```sql
    CREATE EXTENSION IF NOT EXISTS sepgsql;
    ALTER TABLE table_name ENABLE ROW LEVEL SECURITY;
    ```

50. **Применение контекста безопасности к пользователям:**
    ```sql
    ALTER ROLE role_name SET sepgsql.enable = 'on';
    ```

### Команды для управления таблицами

51. **Создание таблицы с определенными правами:**
    ```sql
    CREATE TABLE table_name (id SERIAL PRIMARY KEY, name TEXT) WITH (OIDS=FALSE);
    GRANT SELECT, INSERT ON table_name TO role_name;
    ```

52. **Удаление таблицы:**
    ```sql
    DROP TABLE table_name;
    ```

53. **Изменение владельца таблицы:**
    ```sql
    ALTER TABLE table_name OWNER TO new_owner;
    ```

54. **Добавление столбца в таблицу:**
    ```sql
    ALTER TABLE table_name ADD COLUMN new_column TEXT;
    ```

55. **Удаление столбца из таблицы:**
    ```sql
    ALTER TABLE table_name DROP COLUMN column_name;
    ```

### Работа с индексами

56. **Создание индекса:**
    ```sql
    CREATE INDEX idx_name ON table_name (column_name);
    ```

57. **Удаление индекса:**
    ```sql
    DROP INDEX idx_name;
    ```

58. **Создание уникального индекса:**
    ```sql
    CREATE UNIQUE INDEX unique_idx_name ON table_name (column_name);
    ```

### Управление представлениями

59. **Создание представления:**
    ```sql
    CREATE VIEW view_name AS SELECT column1, column2 FROM table_name;
    ```

60. **Удаление представления:**
    ```sql
    DROP VIEW view_name;
    ```

61. **Обновление представления:**
    ```sql
    CREATE OR REPLACE VIEW view_name AS SELECT column1, column2 FROM table_name;
    ```

### Управление триггерами

62. **Создание триггера:**
    ```sql
    CREATE TRIGGER trigger_name
    AFTER INSERT ON table_name
    FOR EACH ROW
    EXECUTE FUNCTION trigger_function();
    ```

63. **Удаление триггера:**
    ```sql
    DROP TRIGGER trigger_name ON table_name;
    ```

64. **Создание функции триггера:**
    ```sql
    CREATE FUNCTION trigger_function()
    RETURNS TRIGGER AS $$
    BEGIN
      -- код триггера
      RETURN NEW;
    END;
    $$ LANGUAGE plpgsql;
    ```

### Расширения безопасности

65. **Установка расширения pgcrypto для шифрования:**
    ```sql
    CREATE EXTENSION IF NOT EXISTS pgcrypto;
    ```

66. **Использование функции для хэширования:**
    ```sql
    SELECT crypt('my_password', gen_salt('bf'));
    ```

67. **Использование функции для генерации UUID:**
    ```sql
    SELECT gen_random_uuid();
    ```

### Резервное копирование и восстановление с помощью утилит

68. **Резервное копирование только структуры базы данных:**
    ```sh
    pg_dump -s db_name > db_schema_backup.sql
    ```

69. **Восстановление только данных в существующую базу данных:**
    ```sh
    pg_restore -d db_name db_data_backup.dump
    ```

70. **Полное резервное копирование базы данных:**
    ```sh
    pg_basebackup -D /path/to/backup -F tar -z -P
    ```

### Управление логами и мониторингом

71. **Просмотр текущих настроек логирования:**
    ```sql
    SHOW log_statement;
    ```

72. **Включение логирования длительных запросов:**
    ```sql
    ALTER SYSTEM SET log_min_duration_statement = '1000'; -- в миллисекундах
    ```

73. **Просмотр логов в реальном времени:**
    ```sh
    tail -f /var/log/postgresql/postgresql.log
    ```

### Управление точками восстановления и транзакциями

74. **Создание точки восстановления:**
    ```sql
    SAVEPOINT savepoint_name;
    ```

75. **Откат к точке восстановления:**
    ```sql
    ROLLBACK TO SAVEPOINT savepoint_name;
    ```

76. **Коммит транзакции:**
    ```sql
    COMMIT;
    ```

77. **Откат транзакции:**
    ```sql
    ROLLBACK;
    ```

### Полезные функции и утилиты

78. **Проверка размер таблицы:**
    ```sql
    SELECT pg_size_pretty(pg_total_relation_size('table_name'));
    ```

79. **Проверка размер базы данных:**
    ```sql
    SELECT pg_size_pretty(pg_database_size('db_name'));
    ```

80. **Проверка активных блокировок:**
    ```sql
    SELECT * FROM pg_locks;
    ```

81. **Анализ и очистка таблицы:**
    ```sql
    VACUUM ANALYZE table_name;
    ```
    VACUUM ANALYZE выполняет очистку (VACUUM), а затем анализ (ANALYZE) всех указанных таблиц. Это удобная комбинация для регулярного обслуживания БД.

82. **Анализ всех таблиц в базе данных:**
    ```sql
    VACUUM ANALYZE;
    ```

### Управление таблицами партиций

83. **Создание таблицы с партициями:**
    ```sql
    CREATE TABLE partitioned_table (
        id SERIAL,
        data TEXT,
        created_at TIMESTAMP
    ) PARTITION BY RANGE (created_at);
    ```

84. **Создание партиции для таблицы:**
    ```sql
    CREATE TABLE partition_jan2024 PARTITION OF partitioned_table
    FOR VALUES FROM ('2024-01-01') TO ('2024-02-01');
    ```

### Настройки безопасности на уровне сети

85. **Ограничение доступа к базе данных по IP-адресу:**
    В файле `pg_hba.conf`:
    ```plaintext
    host    all             all             192.168.0.0/24            md5
    ```

### Управление резервным копированием на уровне файлов

86. **Резервное копирование конфигурационных файлов:**
    ```sh
    cp /etc/postgresql/12/main/postgresql.conf /backup/path/
    cp /etc/postgresql/12/main/pg_hba.conf /backup/path/
    ```

### Управление репликацией

87. **Настройка главного сервера для репликации:**
    В файле `postgresql.conf`:
    ```plaintext
    wal_level = replica
    max_wal_senders = 5
    ```

88. **Создание слота репликации:**
    ```sql
    SELECT * FROM pg_create_physical_replication_slot('replica_slot');
    ```

### Управление миграцией данных

89. **Импорт данных из CSV файла:**
    ```sql
    COPY table_name FROM '/path/to/file.csv' DELIMITER ',' CSV HEADER;
    ```

90. **Экспорт данных в CSV файл:**
    ```sql
    COPY table_name TO '/path/to/file.csv' DELIMITER ',' CSV HEADER;
    ```

### Управление данными в JSON

91. **Создание таблицы с JSON полем:**
    ```sql
    CREATE TABLE json_table (id SERIAL, data JSONB);
    ```

92. **Добавление данных в JSON поле:**
    ```sql
    INSERT INTO json_table (data) VALUES ('{"key": "value"}');
    ```

93. **Запрос данных из JSON поля:**
    ```sql
    SELECT data->>'key' FROM json_table;
    ```

### Настройки производительности

94. **Настройка параметров кеша:**
    ```sql
    ALTER SYSTEM SET shared_buffers = '1GB';
    ```

95. **Настройка параметров воркера для параллельного выполнения:**
    ```sql
    ALTER SYSTEM SET max_parallel_workers_per_gather = 4;
    ```

### Полезные команды для администрирования

96. **Просмотр текущего состояния системы:**
    ```sql
    SELECT * FROM pg_stat_database;
    ```

97. **Проверка состояния подключений:**
    ```sql
    SELECT * FROM pg_stat_activity;
    ```

98. **Просмотр истории транзакций:**
    ```sql
    SELECT * FROM pg_stat_xact_all_tables;
    ```

99. **Просмотр статистики выполнения запросов:**
    ```sql
    SELECT * FROM pg_stat_statements;
    ```

100. **Удаление временных файлов:**
```sql
SELECT pg_catalog.pg_stat_file('base/pgsql_tmp', 'm');
```
    
Этот список включает наиболее часто используемые команды и действия для управления безопасностью и администрирования PostgreSQL. Он охватывает основные аспекты управления пользователями, ролями, правами доступа, а также настройки безопасности и производительности.

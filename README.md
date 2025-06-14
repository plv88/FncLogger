# FncLogger

![Python](https://img.shields.io/badge/python-3.7+-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![PyPI](https://img.shields.io/pypi/v/fnclogger.svg)

Простой и мощный логгер для Python с поддержкой цветного вывода и гибкой конфигурации.

## 🚀 Установка

```bash
# Базовая установка
pip install fnclogger

# С поддержкой Rich (рекомендуется для красивого вывода)
pip install fnclogger[rich]
```

## 📋 Быстрый старт

```python
from fnclogger import get_logger

# Создание логгера
logger = get_logger("my_app")

# Основные методы логирования
logger.debug("Отладочное сообщение")
logger.info("Приложение запущено")
logger.warning("Предупреждение")
logger.error("Произошла ошибка")
logger.critical("Критическая ошибка")

# Цветные методы с иконками
logger.success("Операция выполнена успешно!")  # ✓ зеленый
logger.highlight("Важная информация")          # → синий  
logger.alert("Требуется внимание")             # ⚠ желтый
logger.fail("Критическая ошибка")              # ✗ красный

# Логирование с дополнительными данными
logger.info("Пользователь вошел в систему", extra={
    "user_id": 123,
    "email": "user@example.com",
    "action": "login"
})
```

## ⚙️ Конфигурация

### Режимы работы

```python
from fnclogger import FncLogger, LogMode

# Только в консоль
console_logger = FncLogger(
    name="console_app",
    mode=LogMode.CONSOLE_ONLY
)

# Только в файл
file_logger = FncLogger(
    name="file_app", 
    mode=LogMode.FILE_ONLY,
    log_dir="./logs"
)

# В консоль и файл одновременно (по умолчанию)
both_logger = FncLogger(
    name="full_app",
    mode=LogMode.BOTH
)
```

### Уровни логирования

```python
from fnclogger import LogLevel

# Разные уровни для консоли и файла
mixed_logger = FncLogger(
    name="mixed_app",
    console_level=LogLevel.INFO,    # В консоль только INFO и выше
    file_level=LogLevel.WARNING     # В файл только WARNING и выше
)
```

### JSON форматирование

```python
from fnclogger import OutputFormat

# JSON в файле, обычный текст в консоли
json_logger = FncLogger(
    name="api_server",
    file_format=OutputFormat.JSON,
    console_format=OutputFormat.TEXT
)

json_logger.info("API запрос", extra={
    "method": "GET",
    "url": "/api/users",
    "status": 200,
    "response_time": 0.15
})
```

## 🎯 Примеры использования

### Веб-приложение

```python
from fnclogger import FncLogger, LogMode, OutputFormat, LogLevel

# Настройка логгера для веб-приложения
app_logger = FncLogger(
    name="webapp",
    mode=LogMode.BOTH,
    console_level=LogLevel.INFO,
    file_level=LogLevel.WARNING,
    file_format=OutputFormat.JSON,
    log_dir="./logs"
)

def process_request(user_id, endpoint):
    app_logger.info("Начало обработки запроса", extra={
        "user_id": user_id,
        "endpoint": endpoint
    })
    
    try:
        # Ваша бизнес-логика здесь
        result = handle_business_logic(endpoint)
        app_logger.success(f"Запрос успешно обработан: {endpoint}")
        return result
    except Exception as e:
        app_logger.fail(f"Ошибка обработки запроса: {e}", exc_info=True)
        raise
```

### Обработка ошибок

```python
logger = get_logger("error_handler")

def safe_operation():
    try:
        # Потенциально опасная операция
        result = risky_function()
        logger.success("Операция выполнена успешно")
        return result
    except ValueError as e:
        logger.alert(f"Некорректные входные данные: {e}")
        return None
    except ConnectionError as e:
        logger.fail(f"Ошибка соединения: {e}", exc_info=True)
        raise
    except Exception as e:
        logger.critical(f"Неожиданная ошибка: {e}", exc_info=True)
        raise
```

### Микросервис с разными компонентами

```python
# Основной логгер приложения
main_logger = get_logger("service")

# Специализированные логгеры для разных компонентов
db_logger = FncLogger("service.database", mode=LogMode.FILE_ONLY)
api_logger = FncLogger("service.api", console_level=LogLevel.DEBUG)

def database_operation(query):
    db_logger.debug(f"Выполнение SQL: {query}")
    try:
        result = execute_query(query)
        db_logger.info(f"Запрос выполнен, получено строк: {len(result)}")
        return result
    except Exception as e:
        db_logger.error(f"Ошибка БД: {e}", exc_info=True)
        raise

def api_endpoint(request):
    api_logger.info("Получен API запрос", extra={
        "method": request.method,
        "path": request.path,
        "user_agent": request.headers.get("User-Agent")
    })
    # Обработка запроса...
```

## 🔧 API Reference

### Основные классы и функции

```python
# Быстрое создание логгера
get_logger(name: str, **kwargs) -> FncLogger

# Базовая настройка
setup_basic_logger(name: str, level: str) -> FncLogger

# Создание настраиваемого логгера
FncLogger(
    name: str,
    mode: LogMode = LogMode.BOTH,
    level: LogLevel = LogLevel.INFO,
    console_level: Optional[LogLevel] = None,
    file_level: Optional[LogLevel] = None,
    log_dir: Optional[str] = None,
    file_format: OutputFormat = OutputFormat.TEXT,
    console_format: OutputFormat = OutputFormat.TEXT,
    use_rich: bool = True,
    max_file_size: int = 10 * 1024 * 1024,  # 10MB
    backup_count: int = 5
)
```

### Методы логирования

```python
# Стандартные методы
logger.debug(message, extra=None, **kwargs)
logger.info(message, extra=None, **kwargs)
logger.warning(message, extra=None, **kwargs)
logger.error(message, extra=None, exc_info=False, **kwargs)
logger.critical(message, extra=None, exc_info=True, **kwargs)

# Цветные методы с иконками
logger.success(message, extra=None, **kwargs)    # ✓ зеленый
logger.highlight(message, extra=None, **kwargs)  # → синий
logger.alert(message, extra=None, **kwargs)      # ⚠ желтый
logger.fail(message, extra=None, **kwargs)       # ✗ красный
```

### Enum классы

```python
class LogLevel(Enum):
    DEBUG = logging.DEBUG
    INFO = logging.INFO
    WARNING = logging.WARNING
    ERROR = logging.ERROR
    CRITICAL = logging.CRITICAL

class LogMode(Enum):
    CONSOLE_ONLY = "console"
    FILE_ONLY = "file"
    BOTH = "both"

class OutputFormat(Enum):
    TEXT = "text"
    JSON = "json"
```

## 🎨 Особенности

### Автоматическая ротация файлов
- Файлы логов автоматически ротируются при достижении максимального размера
- По умолчанию: 10MB на файл, 5 backup файлов

### Поддержка Rich (опционально)
- Если установлен Rich - красивый цветной вывод с форматированием
- Если Rich нет - базовые ANSI цвета
- Автоматическое определение доступности Rich

### Thread-Safe
- Безопасное использование в многопоточных приложениях
- Синглтон паттерн с блокировками

### Гибкое форматирование
- Разные форматы для консоли и файла
- JSON для структурированных логов
- Поддержка дополнительных данных через `extra`

## 🧪 Тестирование

После установки можете протестировать:

```python
from fnclogger import get_logger

logger = get_logger("test")
logger.success("FncLogger установлен и работает!")
logger.info("Все системы в норме", extra={"status": "ok"})
```

Или запустите пример:

```bash
# Если клонировали репозиторий
python examples/basic_example.py
```

## 📁 Структура логов

По умолчанию логи сохраняются в папку `logs/` в текущей директории:

```
logs/
├── my_app.log        # Основные логи
├── my_app.log.1      # Backup 1
├── my_app.log.2      # Backup 2
└── ...
```

Формат логов в файле:
```
[2025-05-30 14:30:25] [INFO    ] [my_app] Приложение запущено
[2025-05-30 14:30:26] [WARNING ] [my_app] ⚠ Требуется внимание
[2025-05-30 14:30:27] [ERROR   ] [my_app] ✗ Произошла ошибка
```

JSON формат:
```json
{
  "timestamp": "2025-05-30T14:30:25.123456",
  "level": "INFO",
  "logger": "my_app",
  "message": "Пользователь вошел",
  "module": "main",
  "function": "login",
  "line": 42,
  "user_id": 123,
  "action": "login"
}
```

## 🤝 Участие в разработке

1. Fork репозитория
2. Создайте feature branch: `git checkout -b feature/amazing-feature`
3. Commit изменения: `git commit -m 'Add amazing feature'`
4. Push в branch: `git push origin feature/amazing-feature`
5. Создайте Pull Request

## 📝 Changelog

### v1.0.0
- Первый релиз FncLogger
- Поддержка цветного вывода с Rich и без
- JSON форматирование
- Ротация файлов
- Thread-safe операции
- Раздельные уровни для консоли и файла

## 📄 Лицензия

Этот проект распространяется под лицензией MIT. Подробности в файле [LICENSE](LICENSE).

## 🙏 Благодарности

- [Rich](https://github.com/Textualize/rich) - за отличную библиотеку цветного вывода
- Python Logging - за базовую функциональность
- Сообщество Python за вдохновение

---

**Создано для простого и эффективного логирования** 🪵✨
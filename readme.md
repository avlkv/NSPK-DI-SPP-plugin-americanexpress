# <Название плагина>

## Для создателей плагинов

`Этот раздел можно вырезать из своего репозитория`

### Основные правила
1. В этой инструкции могут встречаться параметры в `< >`. Всё, что находится внутри скобок должно быть единым текстом, все проблемы и тире должны быть заменены на `_`.
Например `<source name>` означает, что тут должно быть уникальное имя источника.
2. Парсер должен быть протетирован перед передачей в платформу.
3. Парсер должен перехватывать только те ошибки и exception, которые он непосредственно обрабатывает. Всё остальное должно пропускаться во вне парсера, их поймает и обработает платформа
   1. Все перехватываемые ошибки должны быть ожидаемыми. Например, если нам нужен `title`, а парсинг этой области вызывает ошибку, тогда нужно либо пропустить эту страницу/документ или выкинуть ошибку парсинга. 



### Deploy
1. Необходимо слить изменения мз `dev` ветки в `main` ветку.
2. Из `main` ветки нужно удалить все лишние файлы.
3. Из `main` ветки сделать релиз, с кратким описанием изменений и обновленным тегом.
4. Передать ссылку на репозиторий админу платформы.


### Дерево репозитория
Файлы помеченный `(*)` являются обязательными для платформы. Остальные не обрабатываются. Они служат для удобства написания и тестирования плагина.
```bash
└── NSPK-DI-SPP-plugin-<source name>
    ├── src
    │   └── spp
    │       └── types.py # Содержит датакласс SPP_document. Объект, который необходимо использовать при заполнении списка документов источника
    ├── .gitignore # Содержит информацию о том, что не должен обрабатывать git (например, метаданные IDE и виртуальные окружения python)  
    ├── LICENSE # Лицензия MIT  
    ├── readme.md # Инструкция по созданию плагинов   
    ├── <source name>.py # (*) Класс парсера         
    └── SPPfile # (*) Файл конфигурации для платформы. переименовывать нельзя 
```

### Наименование репозитория
При использовании шаблона, новый репозиторий стоит назвать так:
`NSPK-DI-SPP-plugin-<source name>`


### Тестирование 
Для удобства запуска и тестирования работы парсера можно создать `main.py` файл в корне репозитория. `main.py` файл можно хранить в репозитории, но только в ветке `dev`. Перед добавлением парсера в платформы нужно изменения из ветки `dev` вливаются в ветку `master` с удалением всех сторонних файлов.

#### Журналирование
Для тестирования журналирования `logging` нужно перед инициализацией класса парсера вставить следующие строки.
```python
from logging import config

config.fileConfig('dev.logger.conf')
```
В корне репозитория создать файл `dev.logger.conf`
```editorconfig
[loggers]
keys=root

[handlers]
keys=console

[formatters]
keys=dev

[logger_root]
handlers = console
level = DEBUG

[handler_console]
class = logging.StreamHandler
level = DEBUG
formatter = dev

[formatter_dev]
format = %(asctime)s.%(msecs)03d %(levelname)s %(module)s : %(message)s
datefmt = %d-%m-%Y %I:%M:%S
```

#### Использование Selenium
Для использования WebDriver в первую очередь нужно настроить класс парсера `PARSER_CLASS`. Для этого добавляем параметр `webdriver` в конструкторе парсера и сохраняем в свойстве класса 
```python
class PARSER_CLASS:
    
    def __init__(self, webdriver, *args, **kwargs):
        """
        Конструктор класса парсера
        По умолчанию внего ничего не передается, но если требуется (например: driver селениума), то нужно будет
        заполнить конфигурацию
        """
        # Обнуление списка документов
        self._content_document = []

        # Webdriver Selenium для парсера
        self.driver = webdriver
```
Теперь в файле `main.py` можно создавать `webdriver` и передавать его в конструктор класса.
```python
from selenium import webdriver

driver = webdriver.Chrome()
parser = <PARSER_CLASS>(driver)
docs = parser.content()
print(docs)
```

#### Основной парсер
Для запуска парсера и просмотра результатов нужно инициализировать класс парсера `PARSER_CLASS` и вызвать метод `content` в файле `main.py`
```python
docs = <PARSER_CLASS>().content()
print(docs)
```

## Источник

Здесь нужно написать про источник, который обрабатывает плагин

## DEV

Инструкции и рекомендации для тех, кто будет дорабатывать плагин

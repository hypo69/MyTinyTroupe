# Создание интеллектуальных агентов с TinyTroupe: Быстрый старт

## Введение

### Что такое TinyTroupe?

TinyTroupe — это экспериментальная Python-библиотека от Microsoft, предназначенная для моделирования людей с определёнными личностными характеристиками, интересами и целями. Эти искусственные агенты, называемые "TinyPerson", могут взаимодействовать между собой и с окружающей средой, имитируя реалистичное поведение.

### Что такое TinyPerson?

В основе TinyTroupe лежит класс `TinyPerson`.
`TinyPerson` — это основной строительный блок для создания сложных, управляемых LLM агентов. Экспериментируйте с различными промптами, конфигурациями памяти и ментальными способностями, чтобы раскрыть весь потенциал моделирования человеческого поведения.

Каждый `TinyPerson` обладает:

- **Имя:** Уникальный идентификатор.
- **Когнитивные состояния:** Внутренние переменные, представляющие внимание, эмоции, цели и другие ментальные аспекты, влияющие на поведение.
- **Память:** Разделена на `episodic_memory` (конкретные события) и `semantic_memory` (общие знания), отражая структуру человеческой памяти.
- **Поведенческие абстракции:** Методы `listen()` и `act()` моделируют циклы стимул-реакция.

## Установка

В папке ваших проектов выполните команду:

```bash
git clone https://github.com/microsoft/TinyTroupe.git
cd TinyTroupe
python -m venv venv
venv\Scripts\activate
pip install git+https://github.com/microsoft/TinyTroupe.git@main
```

Переименуйте файл `.env.example` в `.env` и добавьте в него свой ключ OpenAI API.

## Примеры использования

### 1. Создание TinyPerson

Внутри директории проекта создайте файл `my_person.py` и добавьте следующий код:

```python
import os
from dotenv import load_dotenv
# Если ключ хранится в файле .env
load_dotenv()
os.environ["OPENAI_API_KEY"] = os.getenv("OPENAI_API_KEY")

from tinytroupe.agent import TinyPerson

# Создание агента
john = TinyPerson(name="John")

# Определение характеристик
john.define("age", 35)
john.define("occupation", "Software Engineer")
john.define("nationality", "American")
john.define("skills", [{"skill": "Coding in Python"}])

# Взаимодействие
john.listen("Hello, John! How are you today?")
john.act()
john.pp_current_interactions()
```

Этот пример создаёт агента, задаёт его характеристики, инициирует взаимодействие и показывает историю взаимодействия.

### 2. Методы взаимодействия

- `act()`: Определяет поведение агента, запрашивая у LLM действие на основе памяти и когнитивного состояния.
- `listen()`: Моделирует получение сообщения от другого агента или среды, сохраняя информацию в памяти.
- `_observe()`: Записывает стимулы (события, ощущения) во внутреннюю память агента.

### 3. Управление памятью

`TinyPerson` использует `episodic_memory` и `semantic_memory`:

- **`episodic_memory`**: Хранит конкретные события, организует их по времени и ограничивает количество используемых записей.
- **`semantic_memory`**: Хранит общие знания, индексируя текстовые документы с помощью `llama_index`.

### 4. Внутреннее состояние и конфигурация

- `_update_cognitive_state()`: Изменяет цели, внимание, эмоции и другие внутренние переменные.
- `define()`: Устанавливает или обновляет параметры конфигурации агента.
- `reset_prompt()`: Пересоздаёт системный промпт для LLM на основе текущей конфигурации и памяти агента.

## Управление акторами и средой

### 5. Создание и управление мирами

В `TinyTroupe` среда моделируется с помощью `TinyWorld`, а социальные связи — через `TinySocialNetwork`.

```python
from tinytroupe.environment import TinyWorld
from tinytroupe.agent import TinyPerson

# Создаём агентов
alice = TinyPerson(name="Alice")
bob = TinyPerson(name="Bob")

# Создаём мир и добавляем агентов
world = TinyWorld("Chat Room", [alice, bob])
world.make_everyone_accessible()
```

Метод `make_everyone_accessible()` позволяет всем агентам взаимодействовать друг с другом.

### 6. Управление акторами в комнате

Если акторы уже определены, но вам нужно их обновить или заменить, можно использовать:

```python
# Удаление агента из мира
world.remove_agent(alice)

# Добавление нового агента
charlie = TinyPerson(name="Charlie")
world.add_agent(charlie)
```

### 7. Запуск симуляции

Для моделирования взаимодействий используется `run()`:

```python
bob.listen("Talk to Alice to know more about her")
world.run(4)
```

Здесь `4` означает количество итераций взаимодействий между агентами.

### 8. Управление взаимодействиями

Если вам нужно контролировать диалоги между агентами:

```python
alice.listen("Hey Bob, what do you think about AI?")
alice.act()
```

Этот код инициирует разговор и генерирует ответ агента.

### 9. Создание социальных сетей

`TinySocialNetwork` позволяет моделировать социальные связи и группы:

```python
from tinytroupe.environment import TinySocialNetwork

network = TinySocialNetwork("Workplace Network", [alice, bob, charlie])
network.connect(alice, bob, "colleagues")
network.connect(bob, charlie, "friends")
```


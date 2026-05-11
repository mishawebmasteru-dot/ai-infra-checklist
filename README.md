# AI Infrastructure Checklist

Чек-лист для выбора инфраструктуры под LLM, AI-агентов, n8n, Ollama, Open WebUI, Telegram-ботов и автоматизацию.

Он не заменяет проектирование, но помогает быстро понять базовую схему: где хватит VPS, где лучше использовать внешний API, а где без GPU будет больно.

## Быстрый выбор

| Задача | Что выбрать | Минимум | Когда нужен GPU | Ссылка |
| --- | --- | --- | --- | --- |
| n8n и AI-агенты | VPS + API | 4 vCPU / 8 GB RAM | Обычно не нужен | [VPS для n8n и AI-агентов](https://wturm.ru/ai-llm/vps-dlya-n8n-ai-agentov/) |
| Ollama | VPS или GPU | 4 vCPU / 16 GB RAM | Для тяжёлых моделей | [VPS для Ollama](https://wturm.ru/ai-llm/vps-dlya-ollama/) |
| Open WebUI | VPS + Ollama/API | 4 vCPU / 8-16 GB RAM | Если модели локальные | [Open WebUI на сервере](https://wturm.ru/ai-llm/open-webui-na-servere/) |
| LLM API | OpenRouter/аналоги | Сервер не обязателен | Не нужен | [Аналоги OpenRouter](https://wturm.ru/ai-llm/analogi-openrouter/) |
| Stable Diffusion/ComfyUI | GPU cloud | RTX 4090/A100 | Нужен | [GPU-хостинг для нейросетей](https://wturm.ru/ai-llm/gpu-hosting-dlya-neirosetey/) |
| AI-агент под ключ | VPS + n8n/API | Зависит от задачи | Редко | [Настройка AI-инфраструктуры](https://wturm.ru/ai-llm/nastroyka-ai-infrastruktury/) |

## Минимальные конфигурации

| Сценарий | CPU | RAM | SSD | Комментарий |
| --- | --- | --- | --- | --- |
| Тесты и обучение | 2 vCPU | 4 GB | 40-60 GB | Простые сценарии, личные эксперименты, первые workflow |
| n8n + API | 4 vCPU | 8 GB | 80-120 GB | Нормальный старт для автоматизаций, вебхуков и интеграций |
| Ollama без GPU | 4 vCPU | 16 GB | 120 GB | Лёгкие модели, невысокая скорость, без ожиданий как у GPU |
| Open WebUI + Ollama/API | 4 vCPU | 16 GB | 120 GB | Личный чат, тесты моделей, работа через API или небольшие локальные модели |
| Несколько сервисов | 8 vCPU | 16-32 GB | 200+ GB | n8n, база, reverse proxy, боты, очереди и несколько проектов |
| Тяжёлые LLM/изображения | GPU | 32+ GB | 250+ GB | GPU cloud для локальных LLM, Stable Diffusion, ComfyUI и похожих задач |

## Что выбрать: API или свой сервер

API проще для старта. Не нужно поднимать модели, считать VRAM, подбирать драйверы и следить за GPU. Для n8n, Telegram-ботов и большинства AI-агентов этого обычно достаточно: логика живёт на VPS, а модель вызывается через внешний API.

Свой сервер даёт больше контроля. Можно хранить данные у себя, собирать свою схему, подключать базы, очереди, панели и внутренние сервисы. Но вместе с контролем появляются администрирование, обновления, SSL, бэкапы, логи и безопасность.

GPU нужен не потому, что в проекте есть AI, а потому что модель считается локально. Если LLM работает через OpenRouter, OpenAI-compatible API, Claude/GPT/Qwen/DeepSeek или похожий сервис, обычного VPS чаще всего хватает.

На практике часто удобен гибрид: VPS держит n8n, бота, базу, вебхуки и бизнес-логику, а модели работают через API. Если потом появляется причина запускать модель локально, инфраструктуру можно расширить.

Общий вход в материалы и калькулятор: https://wturm.ru/ai-llm/

## Типовые ошибки

- Брать VPS на 1 GB RAM и пытаться поставить туда n8n, базу, бота и ещё панель управления.
- Запускать тяжёлые модели без GPU и ждать нормальной скорости.
- Не делать бэкапы базы n8n, `.env`, docker volumes и конфигов.
- Открывать панели наружу без авторизации, firewall или хотя бы базовой защиты.
- Не ставить SSL и потом ловить проблемы с вебхуками, браузерами и интеграциями.
- Не ограничивать расходы на API: один зацикленный workflow может сжечь бюджет быстрее, чем кажется.
- Хранить токены в открытом виде: в README, публичном репозитории, скриншотах или логах.
- Не смотреть логи и искать проблему наугад.
- Строить рабочий проект на бесплатных лимитах, которые могут закончиться в самый неудобный момент.

## Базовый стек для n8n/AI-агента

- Ubuntu — понятная база для VPS, Docker и большинства инструкций.
- Docker — чтобы не ставить всё вручную в систему.
- Docker Compose — чтобы описать n8n, базу, proxy и дополнительные сервисы в одном месте.
- n8n — сценарии, вебхуки, интеграции, обработка событий.
- PostgreSQL — нормальная база для рабочего n8n вместо SQLite в контейнере.
- nginx/reverse proxy — маршрутизация доменов, HTTPS, несколько сервисов на одном сервере.
- SSL — нужен для вебхуков, браузеров, Telegram-интеграций и нормального доступа к панелям.
- Домен — удобнее, чем ходить по IP и портам.
- LLM API — OpenRouter, OpenAI-compatible endpoint или другой провайдер моделей.
- Бэкапы — база, volumes, конфиги, `.env`, compose-файлы.
- Мониторинг логов — хотя бы `docker logs`, ротация логов и понятный способ посмотреть ошибки.

## Полезные материалы

Ссылки уже вынесены в таблицу быстрого выбора выше, чтобы не дублировать одно и то же по README.

Короткая навигация:

- Калькулятор AI-инфраструктуры — общий вход для выбора схемы.
- VPS для n8n и AI-агентов — если нужны автоматизации, вебхуки, боты и API.
- VPS для Ollama — если хочется запускать модели локально без отдельного GPU-сервера.
- Open WebUI на сервере — если нужен свой веб-интерфейс к моделям.
- Аналоги OpenRouter — если выбираете API-провайдера.
- GPU-хостинг для нейросетей — если нужны Stable Diffusion, ComfyUI или тяжёлые локальные модели.
- Настройка AI-инфраструктуры — если проект уже рабочий и собирать всё наугад не хочется.

## License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

---
title: 'Тест 6 Vision API моделей: Gemini 2.5, GPT-4.1 и Qwen3 VL для понимания изображений'
published: true
description: 'Практический benchmark шести моделей для image understanding API: Gemini 2.5 Flash, Gemini 2.5 Flash Lite, GPT-4.1 Mini, GPT-4.1 Nano, Qwen3 VL Flash и Qwen3 VL Plus. Сравниваем accuracy, la'
tags: ai, api, llm, webdev
cover_image: 'https://raw.githubusercontent.com/xujfcn/images/main/blog/covers/gemini-2-5-flash-lite-vs-gpt-4-1-mini-vision-api-benchmark-2026.webp'
canonical_url: 'https://crazyrouter.com/blog/six-vision-api-models-benchmark-2026-gemini-gpt-qwen-image-understanding-ru?utm_source=devto&utm_medium=article&utm_campaign=vision_six_model_benchmark_2026_ru'
---

# Тест 6 Vision API моделей: Gemini 2.5, GPT-4.1 и Qwen3 VL для понимания изображений

Если вы добавляете image understanding в продукт, формулировки вроде «модель поддерживает изображения» недостаточно.

Страница модели может обещать vision input, но production route всё равно должен ответить на более практичные вопросы:

- Доходит ли OpenAI-compatible payload с `image_url` до модели?
- Означает ли HTTP 200, что модель действительно увидела изображение?
- Какая модель достаточно быстрая для user-facing image uploads?
- Какая модель достаточно дешёвая для bulk image classification?
- Какую модель использовать как fallback?
- Можно ли по usage metadata заметить сломанный media path?

Чтобы сравнение было ближе к реальной разработке, я протестировал шесть vision-capable моделей через один и тот же OpenAI-compatible API формат:

- `gemini-2.5-flash`
- `gemini-2.5-flash-lite`
- `gpt-4.1-mini`
- `gpt-4.1-nano`
- `qwen3-vl-flash`
- `qwen3-vl-plus`

Цель — не найти «абсолютного победителя». Более полезный вопрос звучит так: **какую модель стоит выбрать для конкретного пользовательского workflow?**

---

## Как проводился тест

Все запросы шли через Crazyrouter OpenAI-compatible Base URL:

```text
https://cn.crazyrouter.com/v1
```

Формат запроса — `chat/completions`; изображение передавалось через `messages[].content[]` как объект `image_url`.

Для теста использовались две стабильные публичные картинки:

- Python logo
- GitHub logo

Каждое изображение запускалось по 3 раза на каждой модели. Итого — 6 запросов на модель.

Время теста: `2026-06-21T13:36:32Z`.

Это **vision API smoke test**. Он помогает проверить, работает ли маршрут `image_url`, и способна ли модель выполнить простое визуальное распознавание. Это не полноценный benchmark для OCR, chart reasoning, document extraction, handwriting или medical images.

---

## Короткая рекомендация

По результатам этого запуска:

- **Real-time user uploads / минимальная latency:** `gpt-4.1-mini`
- **Bulk logo, icon или простая image classification:** `qwen3-vl-flash`
- **Низкобюджетный Gemini route:** `gemini-2.5-flash-lite`
- **Низкобюджетный OpenAI-family route:** `gpt-4.1-nano`
- **Qwen VL route с приоритетом качества:** `qwen3-vl-plus` как upgrade path
- **Не использовать как default image_url vision route в этом запуске:** `gemini-2.5-flash`

Самый важный вывод:

> **HTTP 200 не доказывает, что понимание изображения сработало.**

В этом тесте `gemini-2.5-flash` вернул HTTP success во всех 6 запросах, но visual recognition score был **0/6**. В ответах встречались “no image provided”, неправильное распознавание CBC logo и нерелевантные описания объектов.

Это опасный failure mode: API call выглядит успешным, но модель не обработала изображение корректно.

---

## Общая таблица результатов

| Модель | HTTP success | Correct recognition | No-image replies | Avg latency | Median latency | Slowest request | Input price / 1M tokens | Output price / 1M tokens | Estimated cost / 10k test-style calls | Позиционирование |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---|
| `qwen3-vl-flash` | 6/6 | 6/6 | 0 | 3.819s | 3.493s | 5.975s | $0.05 | $0.40 | $0.0915 | Лучший low-cost route для bulk recognition |
| `gpt-4.1-mini` | 6/6 | 6/6 | 0 | 1.491s | 1.292s | 2.189s | $0.26 | $1.04 | $0.5226 | Лучший low-latency route для user-facing функций |
| `gpt-4.1-nano` | 6/6 | 6/6 | 0 | 2.863s | 2.562s | 4.213s | $0.065 | $0.26 | $0.1666 | Низкобюджетный OpenAI-family route |
| `qwen3-vl-plus` | 6/6 | 6/6 | 0 | 3.859s | 3.729s | 4.821s | $0.1429 | $1.4286 | $0.3848 | Qwen VL upgrade route с приоритетом качества |
| `gemini-2.5-flash` | 6/6 | 0/6 | 1 | 4.965s | 4.333s | 9.507s | $0.17 | $0.68 | $0.6168 | image_url path не сработал в этом запуске |
| `gemini-2.5-flash-lite` | 6/6 | 6/6 | 0 | 2.618s | 2.627s | 4.195s | $0.055 | $0.22 | $0.5466 | Низкобюджетный Gemini lightweight route |

Оценка стоимости 10k calls основана на observed usage в этом простом logo recognition тесте. Это не универсальная цена для всех image workloads. Более крупные изображения, OCR, длинные описания и multi-image prompts могут сильно изменить token usage.

Практическая production-метрика — это не просто цена модели. Это **cost per successful image task**.

Дешёвый route, который часто требует retry или fallback, может оказаться дороже более надёжного route.

---

## Accuracy: пять моделей прошли, одна провалилась

Correct recognition в smoke test:

1. `qwen3-vl-flash`: 6/6
2. `gpt-4.1-mini`: 6/6
3. `gpt-4.1-nano`: 6/6
4. `qwen3-vl-plus`: 6/6
5. `gemini-2.5-flash-lite`: 6/6
6. `gemini-2.5-flash`: 0/6

Для простого logo и icon recognition пять из шести маршрутов сработали корректно. Это означает, что для базовой image classification часто достаточно lightweight models.

Но `gemini-2.5-flash` — важный предупреждающий пример: HTTP success не означает, что image path здоров.

---

## Latency: GPT-4.1 Mini оказался самым быстрым

Average latency от меньшей к большей:

1. `gpt-4.1-mini`: avg 1.491s, median 1.292s, slowest 2.189s
2. `gemini-2.5-flash-lite`: avg 2.618s, median 2.627s, slowest 4.195s
3. `gpt-4.1-nano`: avg 2.863s, median 2.562s, slowest 4.213s
4. `qwen3-vl-flash`: avg 3.819s, median 3.493s, slowest 5.975s
5. `qwen3-vl-plus`: avg 3.859s, median 3.729s, slowest 4.821s
6. `gemini-2.5-flash`: avg 4.965s, median 4.333s, slowest 9.507s

Для user-facing функций latency — часть качества продукта. Если пользователь загружает изображение и ждёт ответа, разница в одну-две секунды заметна.

Для таких workflow `gpt-4.1-mini` — самый сильный default route в этом запуске.

---

## Стоимость: Qwen3 VL Flash — самый дешёвый успешный route

Estimated cost для 10,000 test-style calls:

1. `qwen3-vl-flash`: около $0.0915
2. `gpt-4.1-nano`: около $0.1666
3. `qwen3-vl-plus`: около $0.3848
4. `gpt-4.1-mini`: около $0.5226
5. `gemini-2.5-flash-lite`: около $0.5466
6. `gemini-2.5-flash`: около $0.6168

Для high-volume задач — logo detection, icon classification, screenshot pre-filtering, dataset tagging — `qwen3-vl-flash` выглядит самым сильным low-cost кандидатом.

Важно, что он не только дешёвый. Он ещё и прошёл visual recognition smoke test.

---

## Заметки по каждой модели

### GPT-4.1 Mini: лучше всего для real-time interactions

`gpt-4.1-mini` показал минимальную average latency и прошёл 6/6 recognition.

Подходит для:

- user image uploads
- support screenshot analysis
- chat apps with image input
- latency-sensitive agent workflows

Компромисс — стоимость. Это не самый дешёвый route, поэтому не стоит автоматически использовать его для всех bulk image tasks.

### Qwen3 VL Flash: лучше всего для bulk low-cost recognition

`qwen3-vl-flash` прошёл 6/6 recognition и имел самую низкую estimated cost.

Подходит для:

- bulk logo recognition
- icon detection
- simple image classification
- screenshot pre-classification
- high-volume visual tagging

Он медленнее, чем `gpt-4.1-mini`, но для batch workloads это часто приемлемо.

### Gemini 2.5 Flash Lite: рабочий low-cost Gemini route

`gemini-2.5-flash-lite` прошёл 6/6 recognition и показал приемлемую latency.

Это разумный кандидат, если вам нужен Gemini-family backup route. Но usage metadata не так прозрачен, как у Qwen route, поэтому в production стоит оставить visual smoke test.

### GPT-4.1 Nano: low-cost OpenAI-family backup

`gpt-4.1-nano` прошёл 6/6 recognition и заметно дешевле `gpt-4.1-mini`.

Используйте его для простых visual tags и lightweight classification. Не стоит считать его лучшим route для сложного document understanding, OCR или глубокого visual reasoning.

### Qwen3 VL Plus: Qwen upgrade route с приоритетом качества

`qwen3-vl-plus` прошёл тест, но latency и output price выше, чем у flash.

Его лучше рассматривать как upgrade route, когда `qwen3-vl-flash` недостаточно, а не как default для каждого простого logo recognition task.

### Gemini 2.5 Flash: не использовать как default в этом image_url route

Это был проблемный route.

Результаты:

- HTTP success: 6/6
- Correct recognition: 0/6
- No-image reply: 1
- Нерелевантные или неправильные ответы
- Подозрительные usage/image-token signals

Это не обязательно доказывает, что сама модель неспособна. Возможно, проблема в adapter, media-fetch, payload-conversion или upstream routing именно в этом `image_url` path.

Но для production вывод тот же: не используйте его как default vision route, пока собственный smoke test не подтвердит, что image handling исправлен.

---

## Routing advice по сценариям

| Сценарий | Default route | Fallback | Почему |
|---|---|---|---|
| Real-time user image uploads | `gpt-4.1-mini` | `qwen3-vl-flash` или `gemini-2.5-flash-lite` | latency и reliability важнее всего |
| Bulk logo или icon recognition | `qwen3-vl-flash` | `gpt-4.1-nano` | lowest cost среди успешных routes |
| Simple screenshot classification | `qwen3-vl-flash` или `gpt-4.1-nano` | `gpt-4.1-mini` | сначала дешёвый route, сложные случаи — upgrade |
| Support screenshot analysis | `gpt-4.1-mini` | `qwen3-vl-plus` | user-facing latency важна |
| OCR или document pre-filtering | нужен отдельный benchmark | stronger OCR/document model | logo tests не доказывают OCR quality |
| Agent visual input | `gpt-4.1-mini` или `qwen3-vl-flash` | forced smoke test + fallback | agents усиливают ошибочные visual inputs |
| Gemini backup route | `gemini-2.5-flash-lite` | `gpt-4.1-nano` | Flash Lite сработал; Flash провалился в этом запуске |

---

## Почему usage signals важны

Многие image benchmarks проверяют только output text. В production usage metadata тоже может быть health signal.

Если запрос возвращает HTTP 200, но:

- prompt tokens похожи только на text prompt
- image token fields равны нулю или отсутствуют
- модель говорит “no image provided”
- ответ не связан с изображением

значит проблема может быть в image transport path, а не в самой модели.

Возможные причины:

- `image_url` не был корректно передан
- gateway media fetch failure
- base64 или inline conversion failure
- OpenAI-compatible payload неправильно конвертирован
- upstream принял запрос, но проигнорировал image content
- token accounting не совпадает с реальной media processing

Для vision routes text-only health checks недостаточно. Нужны visual smoke tests.

---

## API example

```python
from openai import OpenAI

client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://cn.crazyrouter.com/v1"
)

response = client.chat.completions.create(
    model="qwen3-vl-flash",
    messages=[{
        "role": "user",
        "content": [
            {"type": "text", "text": "Identify the main logo or object in this image."},
            {
                "type": "image_url",
                "image_url": {
                    "url": "https://raw.githubusercontent.com/github/explore/main/topics/python/python.png",
                    "detail": "low"
                }
            }
        ]
    }],
    max_tokens=40,
    temperature=0,
)

print(response.choices[0].message.content)
```

Не добавляйте UTM-параметры в API endpoints. UTM нужен для human-facing links, а не для SDK `base_url`.

---

## Итог

Выбор Vision API должен зависеть от пользовательского workflow, а не только от названия модели.

- Для real-time interactions оптимизируйте correct recognition + low latency.
- Для bulk classification оптимизируйте cost per successful image.
- Для agents оптимизируйте reliability, monitoring и fallback behavior.
- Для OCR и document understanding запускайте отдельный benchmark на реальных документах.

Мой практический рейтинг в этом запуске:

1. Default для real-time interaction: `gpt-4.1-mini`
2. Default для bulk low-cost recognition: `qwen3-vl-flash`
3. Low-cost Gemini backup: `gemini-2.5-flash-lite`
4. Low-cost OpenAI backup: `gpt-4.1-nano`
5. Qwen quality upgrade route: `qwen3-vl-plus`
6. Avoid as default for now: `gemini-2.5-flash`

Главный вопрос не в том, “поддерживает ли модель изображения?”

Более правильный вопрос:

> Надёжно ли этот route доставляет изображение до модели в моём production API path?

Именно это нужно проверить перед запуском.

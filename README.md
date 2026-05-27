# y-az-publish

Публичный репозиторий с HTML-блоками, данными и loader'ами для сайта **yacht.az** (и в перспективе всей экосистемы Profbroker). Тянется в Tilda через **jsDelivr CDN**.

## Структура

```
y-az-publish/
├── catalog/                       Каталог яхт + страницы яхт
│   ├── data/
│   │   ├── yachts.csv             Каталог яхт (slug, name, описания ×3 языка, фото, спецификация, активность)
│   │   ├── prices.csv             Почасовые ставки h1..h9, сезонность high/low
│   │   └── settings.csv           Системные настройки (даты high-сезона)
│   ├── blocks/
│   │   ├── yacht-page.html        Шаблон страницы /ru/yachts/<slug> (рендерит по slug из URL)
│   │   └── prices.html            Блок цен на странице каталога /ru/yachts
│   └── loaders/
│       ├── yacht-page-loader.html Loader для страниц яхт (вставляется в T123 Tilda)
│       └── prices-loader.html     Loader для блока цен
│
├── (calc/)                        Калькуляторы — будут перенесены сюда позже из yacht-calc
├── (nav/)                         Меню — будет перенесено позже из yacht-az-nav
└── (pages/)                       Лендинги (регата и т.д.) — позже
```

## Как добавить или изменить яхту

**Полная пошаговая инструкция — [HOW-TO-ADD-YACHT.md](HOW-TO-ADD-YACHT.md)** (для добавления новой яхты).

Если меняешь только данные существующей:
1. Открыть `catalog/data/yachts.csv` или `catalog/data/prices.csv` через GitHub web (карандаш «Edit»)
2. Поправить → Commit прямо в `main`
3. Подождать ~1-2 минуты — CDN пропагирует
4. В инкогнито проверить страницу яхты

В Tilda ничего трогать не надо — master T123 на скрытой странице уже подключён ко всем.

## CDN

Сейчас используем **`rawcdn.githack.com`** — мирроит GitHub, отдаёт свежак через ~1-2 минуты после push. Никаких purge-команд не нужно.

Раньше пробовали jsDelivr — у них ref-cache залипает на старом SHA даже после `purge.jsdelivr.net`. Перешли на githack.

## Подключение в Tilda

На сайте — **master T123** с loader-кодом на скрытой странице, alias-блоки на реальных страницах яхт. Одна правка master → все 9 страниц обновляются.

Master содержит код из [`catalog/loaders/yacht-page-loader.html`](catalog/loaders/yacht-page-loader.html). Шаблон сам определит slug по URL и подтянет правильную яхту.

## Известный рассинхрон

- **Калькулятор аренды** (`tilda-calc-block.html` в репо `yacht-calc`) ещё фетчит цены **напрямую из Google Sheets** (gid=0). Каталог теперь — из этого репо. Если поменять цену здесь, в калькуляторе она не обновится. До миграции калькулятора нужно править цены **в обоих местах**.
- В `yachts.csv` slug'и `Alora` и `Caspian` с заглавной буквы, а `amina` — со строчной. URL'ы все со строчной. Tilda обычно case-insensitive, но желательно унифицировать.

## История

- **2026-05-26** — репо создан. Данные перенесены из Google Sheets (`1y_T5RCqFPmJ8mYE1VI7W2KlnmWjOBxUTcS3VXS6Fh-U`). Шаблоны `yacht-page.html` и `prices.html` переключены с Sheets-источника на jsDelivr.

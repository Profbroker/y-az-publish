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

1. Откройте `catalog/data/yachts.csv` в этом репо (через GitHub web или локально)
2. Добавьте новую строку или измените существующую
3. Commit + push в `main`
4. Сбросьте edge-кэш jsDelivr:
   ```bash
   curl -X POST https://purge.jsdelivr.net/ -H "Content-Type: application/json" \
     -d '{"path":["/gh/Profbroker/y-az-publish@main/catalog/data/yachts.csv"]}'
   ```
5. На сайте обновится через ~30 секунд

Если добавили новую яхту — создайте 3 страницы в Tilda (`/ru/yachts/<slug>`, `/en/yachts/<slug>`, `/az/yachts/<slug>`), в каждую вставьте T123 с loader'ом `yacht-page-loader.html` (см. подключение ниже).

## Как изменить цену

Откройте `catalog/data/prices.csv` → редактируйте → push → purge:
```bash
curl -X POST https://purge.jsdelivr.net/ -H "Content-Type: application/json" \
  -d '{"path":["/gh/Profbroker/y-az-publish@main/catalog/data/prices.csv"]}'
```

## Подключение в Tilda

### Страница каталога /ru/yachts (и языковые варианты)

Один T123 Full Width на странице — содержимое из [`catalog/loaders/prices-loader.html`](catalog/loaders/prices-loader.html).

### Страница отдельной яхты /ru/yachts/<slug>

Один T123 Full Width на странице — содержимое из [`catalog/loaders/yacht-page-loader.html`](catalog/loaders/yacht-page-loader.html).

Шаблон сам определит slug по URL и подтянет правильную яхту.

## Кэш jsDelivr

После любых правок в CSV или шаблонах — purge:

```bash
# Один файл
curl -X POST https://purge.jsdelivr.net/ -H "Content-Type: application/json" \
  -d '{"path":["/gh/Profbroker/y-az-publish@main/<path>"]}'

# Несколько файлов сразу
curl -X POST https://purge.jsdelivr.net/ -H "Content-Type: application/json" \
  -d '{"path":[
    "/gh/Profbroker/y-az-publish@main/catalog/data/yachts.csv",
    "/gh/Profbroker/y-az-publish@main/catalog/data/prices.csv"
  ]}'
```

Без purge edge-кэш висит до 12 часов (`s-maxage=43200`).

## Известный рассинхрон

- **Калькулятор аренды** (`tilda-calc-block.html` в репо `yacht-calc`) ещё фетчит цены **напрямую из Google Sheets** (gid=0). Каталог теперь — из этого репо. Если поменять цену здесь, в калькуляторе она не обновится. До миграции калькулятора нужно править цены **в обоих местах**.
- В `yachts.csv` slug'и `Alora` и `Caspian` с заглавной буквы, а `amina` — со строчной. URL'ы все со строчной. Tilda обычно case-insensitive, но желательно унифицировать.

## История

- **2026-05-26** — репо создан. Данные перенесены из Google Sheets (`1y_T5RCqFPmJ8mYE1VI7W2KlnmWjOBxUTcS3VXS6Fh-U`). Шаблоны `yacht-page.html` и `prices.html` переключены с Sheets-источника на jsDelivr.

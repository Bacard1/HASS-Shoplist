![BANNER](/img/banner.png)

# 🛒 HASS СПИСЪК ЗА ПАЗАРУВАНЕ С ИЗОБРАЖЕНИЯ
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?color=ff00d8)](https://opensource.org/licenses/MIT)
![GitHub last commit](https://img.shields.io/github/last-commit/Bacard1/HASS-Shoplist.svg?color=ff00d8)
[![hacs_badge](https://img.shields.io/badge/HACS-2025.5.3-orange.svg?color=ff00d8)](https://github.com/hacs/integration)

[![Home Assistant](https://img.shields.io/badge/.-HOME_ASSISTANT-blue?logo=homeassistant)](https://www.home-assistant.io/) 
[![Donate via PayPal](https://img.shields.io/badge/PayPal-DONATE-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/Script-YAML-blue?logo=yaml)

[![Български](https://img.shields.io/badge/BG-ЕЗИК-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](BG.md)
[![Deutch](https://img.shields.io/badge/DE-SPRACHE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg
)](DE.md)
[![English](https://img.shields.io/badge/EN-LANGUAGE-gree?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)

Това хранилище предоставя персонализиран интерфейс за списък за пазаруване в Home Assistant, който подобрява стандартната функционалност чрез визуално представяне на продуктите. Позволява лесно добавяне, управление и сортиране на артикули с изображения, групирани по категории за по-бързо разпознаване и удобство.

Основни предимства:

🧠 Бързо разпознаване на артикули чрез изображения

📦 Подредени по категории

🏠 Пълна интеграция с Home Assistant

📱 Подходящо за мобилни устройства

🇬🇧 Включва поддръжка и на английски език

Подходящо е за всяко домакинство, използващо Home Assistant, което търси по-интуитивен и визуално приятен начин за съставяне на списъци за пазаруване.
Проектът е отворен за предложения и подобрения!

---

## 📦 Съдържание

- [🛒 HASS СПИСЪК ЗА ПАЗАРУВАНЕ С ИЗОБРАЖЕНИЯ](#-hass-списък-за-пазаруване-с-изображения)
  - [📦 Съдържание](#-съдържание)
  - [🚀 Подготовка](#-подготовка)
  - [🧰 Създаване](#-създаване)
  - [🧲 Автоматизации](#-автоматизации)

---

## 🚀 Подготовка

- Инсталирайте следните пакети от HACS или посетете хранилищата на следните линкове 🔽:
  - [Local Conditional card](https://github.com/PiotrMachowski/Home-Assistant-Lovelace-Local-Conditional-card)
  - [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card)
  - [mini-graph-card](https://github.com/kalkih/mini-graph-card)

  След инсталацията на пакетите рестартирайте своят Home Assistant и преминете към следващите стъпки 🔽:

## 🧰 Създаване

- **Създаване на нов списък:** създайте нов списък към, който ще препращате имената на натиснатите от Вас артикули. 🔽:
  
![create list](/img/create_list.gif)

- **Табло Shoplist:** Създайте ново Табло с името "Shoplist" и там ще създадем всички необходими списъци. 🔽:

![create dashboard](/img/create_shoplist_dashboard.gif)

- Добавете, като нова карта списъкът, който ще използвате. 🔽:
  
  ![image](/img/todo_card.png)

> Изберете ръчно добавяне на карта 🔽:.

```yaml
type: todo-list
entity: todo.list
```
  
- **Артикули:** неоходимо е еднократно създаване на "Помощник Бутон", който ще използваме за добавянето на артикулите. Не е необходимо създаването за всеки един артикул, понеже така или иначе не е възможно натискането на няколко артикула едновременно. Бутонът позволява при всяко добавяне да се правят индивидуални промени. 🔽:

![create button](/img/create_button_helper.gif)

- **Категории:** за всяка една категоря е необходимо да се създаде помощник. Ако използвате един и същ помощник за всички категории, при отваряне на една категория, ще се отварят и всички други. Като удобство, ще добавим и автоматизация, която ще затваря всички останали категории при отваряне на категория. 🔽:

![create nameskategorie](/img/create_Namenskategorie_helpers.gif)

След създаването на помощникът за категорията, я добавете като карта в таблото. 🔽:

![shoplist & categorie](/img/shoplist_und_kategodie.gif)

> Изберете ръчно добавяне на карта 🔽:

```yaml
type: vertical-stack
cards:
  - type: entities
    entities:
      - entity: input_boolean.namenskategorie
        tap_action:
          action: toggle
        name: Namenskategorie
        image: https://github.com/Bacard1/Home-Assistant-Shoplist/blob/main/IMG/shoplist/Alkoholische-Getr%C3%A4nke/%D0%91%D0%B8%D1%80%D0%B0.png?raw=true
  - type: conditional
    conditions:
      - condition: state
        entity: input_boolean.namenskategorie
        state: "on"
      - condition: state
        entity: input_boolean.namenskategorie
        state_not: "off"
    card:
      type: grid
      cards:
        - show_state: false
          show_name: true
          camera_view: auto
          type: picture-entity
          entity: input_button.artikul
          image: https://github.com/Bacard1/Home-Assistant-Shoplist/blob/main/IMG/shoplist/Alkoholische-Getr%C3%A4nke/%D0%91%D0%B8%D1%80%D0%B0.png?raw=true
          name: Arikul 1
          theme: yourname
          tap_action:
            action: call-service
            service: shopping_list.add_item
            service_data:
              name: Arikul 1
          card_mod:
            style: |
              ha-card {                
                border: 1;
                width: 90%;
                height: 90%;    
              }              
card_mod:
  style: |
    ha-card {
      --ha-card-background: none;
      border: 2; 
    }
```
> [!WARNING]
> Вкарайте картата с кодът точно така както е даден, като замените само "- entity: input_boolean.namenskategorie" с името на помошникът (категория). След като го запазите, можете лесно да промените останалите работи през графиката на картата или да добавите нов артикул. 🔽:

![kategodie card edit](/img/kategorie_card_edit.gif)

- [ТУК](https://github.com/Bacard1/icon-set-project/tree/main/HASS-Shoplist) ще намерите изчерпателна колекция от изображения за вашият списък за пазаруване, разделени по категории.

## 🧲 Автоматизации

> [!IMPORTANT]
> Автоматизациите по долу не са задължителни, а препоръчителни. Липсата им няма да попречи на работата на списъкът за пазаруване, но ще я улесни.

- **1. Следи за вече отметнати продукти и активира чистача. 🔽:**

```yaml
alias: ""
  description: ""
  triggers:
    - event_type: shopping_list_updated
      trigger: event
  conditions: []
  actions:
    - delay: "00:01:00"
      enabled: false
    - data: {}
      enabled: false
      action: python_script.clear_completed_items
    - metadata: {}
      data:
        skip_condition: true
      target:
        entity_id: automation.izchisti_spiska_za_pazaruvane_sled_20_minuti
      action: automation.trigger
```

- **2. Чистач на списък с отметнати продукти -** тази автоматизация ще изчиства всички отметнати продукти след престой от 10 минути. Това не ви ангажира и обърква при ежедневна употреба. 🔽:

```yaml
alias: ""
  description: ""
  triggers:
    - event_type: shopping_list_updated
      trigger: event
  conditions:
    - condition: template
      value_template: |
        {% if state_attr('sensor.shopping_list', 'items') is defined %}
          {% for item in state_attr('sensor.shopping_list', 'items') %}
            {% if item['complete'] %}
              {% set completed = true %}
            {% endif %}
          {% endfor %}
          {{ completed | default(false) }}
        {% else %}
          false
        {% endif %}
  actions:
    - delay:
        hours: 0
        minutes: 10
        seconds: 0
        milliseconds: 0
    - data: {}
      action: shopping_list.clear_completed_items
```

> [!CAUTION]
> Автоматизации 1 и 2 вървят комлект понеже едната активира другата!

- **3. Само една отворена категория:** тази автоматизация не позволява отварянето на повече от една категория. При отваряне на категория, всички останали се затварят. 🔽:

```yaml
alias: ""
description: ""
triggers:
  - entity_id:
      - input_boolean.kategorie1
    to: "on"
    trigger: state
conditions: []
actions:
  - target:
      entity_id:
        - input_boolean.input_boolean.kategorie2
        - input_boolean.input_boolean.kategorie3
        - input_boolean.input_boolean.kategorie4
        - input_boolean.input_boolean.kategorie5
        - input_boolean.input_boolean.kategorie6
    data: {}
    action: input_boolean.turn_off
mode: single
```
  
- **4. Нов артикул в списъкът за пазаруване:** изпраща известие до всички мобилни устройства за добавянето на нов артикул към списъкът.🔽:

```yaml
alias: ""
triggers:
  - event_type: shopping_list_updated
    event_data:
      action: add
    trigger: event
actions:
  - data:
      message: "{{ trigger.event.data.item.name }} has been added to the shopping list"
      data:
        clickAction: /shopping-list
        url: /shopping-list
    action: notify.notify
```  
<br>

> [!TIP]
> Ако този проект ви е харесъл, [ТУК](https://github.com/Bacard1?tab=repositories) ще намерите още интересни гранилища направени от мен.<br>
> Ако срещате затруднения или имате въпроси не се колебайте да се свържете с мен.
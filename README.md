# 🛒 HASS Image shopping list

You create an easy -to -find list by easy and quickly finding your items using images sorted by categories.Family members are notified of the addition of an item.

---

## 📦 Content

- [🛒 Списък за пазаруване с изображения](#-списък-за-пазаруване-с-изображения)
  - [📦 Съдържание](#-съдържание)
  - [🚀 Подготовка](#-подготовка)
  - [🧰 Създаване](#-създаване)
  - [🧲 Автоматизации](#-автоматизации)

---

## 🚀 Preparation

- Install the following packages from HACS or visit the repositories of the following links 🔽:
  - [Local Conditional card](https://github.com/PiotrMachowski/Home-Assistant-Lovelace-Local-Conditional-card)
  - [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card)
  - [mini-graph-card](https://github.com/kalkih/mini-graph-card)

After installing the packages, restart your Home Assistant and move on to the next steps 🔽:

## 🧰 Creation

- **Create a new list:** Create a new list to which you will forward the names of the items you have pressed. 🔽:
  
![create list](/img/create_list.gif)

- **SHOPLIST Dashboard:** Create a new panel named "Shoplist" and there we will create all the necessary lists. 🔽:

![create dashboard](/img/create_shoplist_dashboard.gif)

- Add as a new card the list you will use. 🔽:
  
  ![image](https://github.com/user-attachments/assets/44c76922-a2d6-4bc2-bfb1-e4b05acbd0a8)

> Select a hand -adding card 🔽:.

```yaml
type: todo-list
entity: todo.list
```
  
- **Articles:** It is irrelevant to create a "assistant button" one that we will use to add the items.It is not necessary to create for each item, because it is not possible to press several items at the same time.The button allows for each adding individual changes. 🔽:

![create button](/img/create_button_helper.gif)

- **Categories:** For each category it is necessary to create an assistant.If you use the same helper for all categories, all others will open when opening one category.In convenience, we will also add automation that will close all other categories when opening a category.🔽:

![create nameskategorie](/img/create_Namenskategorie_helpers.gif)

After creating the helper for the category, add it as a card in the dashboard.🔽:

![shoplist & categorie](/img/shoplist_und_kategodie.gif)

> Select a hand -adding card 🔽:

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

⚠️ Insert the card card exactly as it is given, replacing only "- entity: input_boolean.Namenskategorie" with the name of the help (category).Once you save it, you can easily change the rest of the card graphics or add a new item. 🔽:

![kategodie card edit](/img/kategorie_card_edit.gif)

- [HERE](https://github.com/Bacard1/icon-set-project/tree/main/HASS-Shoplist) you will find an exhaustive collection of images for your shopping list, divided by categories.
  
## 🧲 Automation

⚠️ Automation below is not mandatory but recommended.Their lack will not interfere with the work of the shopping list, but it will make it easier.

- **1. It monitors the products already removed and activates the cleaner. 🔽:**

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

- **2. Cleaner of a list of removed products -** This automation will clean all the products you have been disposed of after a 10 -minute stay.This does not engage you and confuses you with daily use.🔽:

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

⚠️ Automation 1 and 2 are settled because one activates the other!

- **3. Only one open category:** This automation does not allow more than one category.When opening a category, everyone else is closed.🔽:

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
  
- **4. New item in the shopping list:** Sends notification to all mobile devices to add a new item to the list.🔽:

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
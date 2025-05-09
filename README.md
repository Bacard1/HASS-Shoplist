![BANNER](/img/banner.png)

# 🛒 HASS SHOPPING LIST WITH IMAGES
[![PayPal donation](https://img.shields.io/badge/PayPal-donation-синьо?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)
[![Български](https://img.shields.io/badge/Български-език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](BG.md)

This repository provides a custom shopping list interface for Home Assistant, designed to enhance the native functionality with visual item representation. It allows you to easily add, manage, and organize shopping items with images grouped by categories for faster recognition and convenience.

Key Features:

🧠 Quick item identification using images

📦 Organized by categories for better structure

🏠 Full Home Assistant integration

📱 Mobile and desktop responsive

🇧🇬 Includes Bulgarian language support

This solution is perfect for households using Home Assistant who want a more intuitive and visually enhanced shopping experience.
Feel free to contribute or adapt it to your needs!

---

## 📦 Contents

- [🛒 HASS SHOPPING LIST WITH IMAGES](#-hass-shopping-list-with-images)
  - [📦 Contents](#-contents)
  - [🚀 Preparation](#-preparation)
  - [🧰 Setup](#-setup)
  - [🧲 Automations](#-automations)

---

## 🚀 Preparation

- Install the following packages from HACS or visit their repositories 🔽:
  - [Local Conditional card](https://github.com/PiotrMachowski/Home-Assistant-Lovelace-Local-Conditional-card)
  - [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card)
  - [mini-graph-card](https://github.com/kalkih/mini-graph-card)

  After installing the packages, restart your Home Assistant and proceed with the next steps 🔽:

## 🧰 Setup

- **Create a new list:** create a new list where the names of the pressed items will be added. 🔽:
  
![create list](/img/create_list.gif)

- **Shoplist Dashboard:** Create a new Dashboard named "Shoplist" where we will add all necessary lists. 🔽:

![create dashboard](/img/create_shoplist_dashboard.gif)

- Add the list you will use as a new card. 🔽:
  
  ![image](/img/todo_card.png)

> Choose manual card addition 🔽:

```yaml
type: todo-list
entity: todo.list
```
  
- **Items:** you need to create a "Button Helper" once, which will be used for adding items. You don't need one for each item since pressing multiple items at once is not possible anyway. The button allows for individual changes during each addition. 🔽:

![create button](/img/create_button_helper.gif)

- **Categories:** for each category, a helper needs to be created. If you use the same helper for all categories, opening one will open all. For convenience, we’ll add an automation to close all other categories when one is opened. 🔽:

![create nameskategorie](/img/create_Namenskategorie_helpers.gif)

After creating the helper for the category, add it as a card in the dashboard. 🔽:

![shoplist & categorie](/img/shoplist_und_kategodie.gif)

> Choose manual card addition 🔽:

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
> Insert the card with the code exactly as shown, replacing only "- entity: input_boolean.namenskategorie" with your helper's name (category). After saving, you can easily adjust the rest via the card's graphical interface or add new items. 🔽:

![kategodie card edit](/img/kategorie_card_edit.gif)

- [HERE](https://github.com/Bacard1/icon-set-project/tree/main/HASS-Shoplist) you’ll find a complete image collection for your shopping list, sorted by category.

## 🧲 Automations

> [!IMPORTANT]
> The automations below are optional but recommended. Not having them won’t break the shopping list, but they will improve usability.

- **1. Monitor checked-off items and trigger the cleaner. 🔽:**

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

- **2. Cleaner for checked-off items -** this automation clears all checked items after 10 minutes, so they don’t clutter your list during daily use. 🔽:

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
> Automations 1 and 2 work together – one triggers the other!

- **3. Only one open category:** this automation ensures that only one category is open at a time. When one is opened, the others are closed. 🔽:

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
  
- **4. New item in shopping list:** sends a notification to all mobile devices when a new item is added. 🔽:

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
> If you liked this project, [HERE](https://github.com/Bacard1?tab=repositories) you’ll find more interesting repositories I’ve made.<br>
> If you encounter any difficulties or have questions, don’t hesitate to contact me.

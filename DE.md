![BANNER](/img/banner.png)  

# 🛒 HASS EINKAUFSLISTE MIT BILDERN  
[![Home Assistant](https://img.shields.io/badge/🏠_Home_Assistant-41BDF5?logo=homeassistant)](https://www.home-assistant.io/) [![Donate via PayPal](https://img.shields.io/badge/PayPal-Donate-blue?logo=paypal)](https://www.paypal.com/donate/?hosted_button_id=AAWFZVF2XCP5A)  
![Script](https://img.shields.io/badge/logo-yaml-green?logo=yaml)  
[![Български](https://img.shields.io/badge/BG_Български-език-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg  
)](BG.md)  
[![Deutch](https://img.shields.io/badge/DE_Deutsche-sprache-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg  
)](DE.md)  
[![English](https://img.shields.io/badge/EN_English-language-green?logo=translate&labelColor=gray&style=flat-square&link=https://example.com/bg)](README.md)  

Dieses Repository bietet eine benutzerdefinierte Einkaufslisten-Oberfläche für Home Assistant, die die Standardfunktionalität durch visuelle Produktdarstellungen verbessert. Es ermöglicht das einfache Hinzufügen, Verwalten und Sortieren von Artikeln mit Bildern, gruppiert nach Kategorien für schnelle Erkennung und Komfort.  

**Hauptvorteile:**  

🧠 Schnelle Artikelerkennung durch Bilder  
📦 Nach Kategorien geordnet  
🏠 Vollständige Integration mit Home Assistant  
📱 Optimiert für mobile Geräte  
🇬🇧 Englische Sprachunterstützung inklusive  

Ideal für jeden Haushalt, der Home Assistant nutzt und eine intuitivere, visuell ansprechende Möglichkeit zur Erstellung von Einkaufslisten sucht.  
Das Projekt ist offen für Vorschläge und Verbesserungen!  

---  

## 📦 Inhalt  

- [🛒 HASS EINKAUFSLISTE MIT BILDERN](#-hass-einkaufsliste-mit-bildern)
	- [📦 Inhalt](#-inhalt)
	- [🚀 Vorbereitung](#-vorbereitung)
	- [🧰 Erstellung](#-erstellung)
	- [🧲 Automatisierungen](#-automatisierungen)

---  

## 🚀 Vorbereitung  

- Installieren Sie die folgenden Pakete über HACS oder besuchen Sie die verlinkten Repositories 🔽:  
  - [Local Conditional card](https://github.com/PiotrMachowski/Home-Assistant-Lovelace-Local-Conditional-card)  
  - [Vertical Stack In Card](https://github.com/ofekashery/vertical-stack-in-card)  
  - [mini-graph-card](https://github.com/kalkih/mini-graph-card)  

  Nach der Installation starten Sie Home Assistant neu und fahren Sie mit den nächsten Schritten fort 🔽:  

## 🧰 Erstellung  

- **Neue Liste erstellen:** Erstellen Sie eine neue Liste, auf die die Namen der von Ihnen ausgewählten Artikel verweisen. 🔽:  

![create list](/img/create_list.gif)  

- **Shoplist-Dashboard:** Erstellen Sie ein neues Dashboard mit dem Namen "Shoplist" und fügen Sie dort alle erforderlichen Listen hinzu. 🔽:  

![create dashboard](/img/create_shoplist_dashboard.gif)  

- Fügen Sie die Liste als neue Karte hinzu, die Sie verwenden möchten. 🔽:  

  ![image](/img/todo_card.png)  

> Wählen Sie manuelles Hinzufügen einer Karte 🔽:  

```yaml  
type: todo-list  
entity: todo.list  
```  

- **Artikel:** Es ist notwendig, einen "Helfer-Button" zu erstellen, den wir zum Hinzufügen von Artikeln verwenden. Es ist nicht erforderlich, für jeden Artikel einen separaten Button zu erstellen, da gleichzeitiges Drücken mehrerer Artikel ohnehin nicht möglich ist. Der Button ermöglicht individuelle Anpassungen bei jedem Hinzufügen. 🔽:  

![create button](/img/create_button_helper.gif)  

- **Kategorien:** Für jede Kategorie muss ein Helfer erstellt werden. Wenn Sie denselben Helfer für alle Kategorien verwenden, öffnen sich alle Kategorien gleichzeitig. Zur Vereinfachung fügen wir eine Automatisierung hinzu, die alle anderen Kategorien schließt, wenn eine Kategorie geöffnet wird. 🔽:  

![create nameskategorie](/img/create_Namenskategorie_helpers.gif)  

Nachdem der Helfer für die Kategorie erstellt wurde, fügen Sie ihn als Karte zum Dashboard hinzu. 🔽:  

![shoplist & categorie](/img/shoplist_und_kategodie.gif)  

> Wählen Sie manuelles Hinzufügen einer Karte 🔽:  

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
          name: Artikel 1  
          theme: yourname  
          tap_action:  
            action: call-service  
            service: shopping_list.add_item  
            service_data:  
              name: Artikel 1  
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
> Fügen Sie die Karte mit dem Code genau wie angegeben ein, ersetzen Sie jedoch nur "- entity: input_boolean.namenskategorie" durch den Namen des Helfers (Kategorie). Nach dem Speichern können Sie die restlichen Einstellungen einfach über die Kartenbearbeitung anpassen oder einen neuen Artikel hinzufügen. 🔽:  

![kategodie card edit](/img/kategorie_card_edit.gif)  

- [HIER](https://github.com/Bacard1/icon-set-project/tree/main/HASS-Shoplist) finden Sie eine umfassende Sammlung von Bildern für Ihre Einkaufsliste, sortiert nach Kategorien.  

## 🧲 Automatisierungen  

> [!IMPORTANT]  
> Die folgenden Automatisierungen sind nicht verpflichtend, aber empfohlen. Ihr Fehlen beeinträchtigt nicht die Funktionalität der Einkaufsliste, erleichtert jedoch die Nutzung.  

- **1. Überwacht bereits abgehakte Produkte und aktiviert den Reiniger. 🔽:**  

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

- **2. Reiniger für abgehakte Produkte:** Diese Automatisierung löscht alle abgehakten Produkte nach einer Inaktivität von 10 Minuten. Dies sorgt für eine übersichtliche Liste im täglichen Gebrauch. 🔽:  

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
> Die Automatisierungen 1 und 2 arbeiten zusammen, da eine die andere aktiviert!  

- **3. Nur eine geöffnete Kategorie:** Diese Automatisierung verhindert, dass mehr als eine Kategorie gleichzeitig geöffnet ist. Beim Öffnen einer Kategorie werden alle anderen geschlossen. 🔽:  

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

- **4. Neuer Artikel in der Einkaufsliste:** Sendet eine Benachrichtigung an alle mobilen Geräte, wenn ein neuer Artikel zur Liste hinzugefügt wird. 🔽:  

```yaml  
alias: ""  
triggers:  
  - event_type: shopping_list_updated  
    event_data:  
      action: add  
    trigger: event  
actions:  
  - data:  
      message: "{{ trigger.event.data.item.name }} wurde zur Einkaufsliste hinzugefügt"  
      data:  
        clickAction: /shopping-list  
        url: /shopping-list  
    action: notify.notify  
```  

<br>  

> [!TIP]  
> Wenn Ihnen dieses Projekt gefällt, finden Sie [HIER](https://github.com/Bacard1?tab=repositories) weitere interessante Repositories von mir.  
> Bei Fragen oder Problemen zögern Sie nicht, mich zu kontaktieren.  
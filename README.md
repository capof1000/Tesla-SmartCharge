# ⚡ Tesla SmartCharge Blueprint

> 🇫🇷 Français | [🇬🇧 English below](#-tesla-smartcharge-blueprint-1)

Blueprint Home Assistant pour optimiser la recharge d'une Tesla en fonction de la production photovoltaïque.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/capof1000/Tesla-SmartCharge/refs/heads/main/Blueprint_TeslaSmartCharge.yaml)

---

## 🎯 Objectif

Piloter automatiquement la charge d'une Tesla en maximisant l'utilisation de l'énergie solaire disponible, tout en protégeant l'installation électrique du foyer.

> ℹ️ Ce blueprint est conçu pour fonctionner avec le projet **[Guition ESP32-S3 Tesla BLE Gateway](https://github.com/capof1000/guition-tesla-ble)**, mais peut être adapté à toute intégration Tesla BLE compatible.

---

## 🔄 Modes de fonctionnement

### `manual` — Manuel
Le blueprint **n'intervient pas**. Le contrôle est assuré directement depuis l'écran Guition ou Home Assistant.

### `solar` — Solaire ☀️
Le blueprint régule l'ampérage de charge automatiquement selon le bilan énergétique du foyer :

| Situation | Action |
|-----------|--------|
| Surplus solaire détecté (net < offset) | Démarrage de la charge |
| Surplus > tension de charge | Augmentation de 1A |
| Consommation réseau > tension de charge | Diminution de 1A |
| Consommation trop élevée ET ampérage au minimum | Arrêt de la charge |

### `forced` — Forcé ⚡
La charge démarre immédiatement à l'ampérage maximum autorisé, avec une **sécurité de délestage** active : si la puissance totale du foyer dépasse le seuil configuré, l'ampérage est réduit progressivement.

---

## 🛡️ Sécurités

- **Câble non branché** : aucune action si le capteur de câble indique que la voiture n'est pas connectée.
- **Délestage automatique** (mode forcé) : réduction de l'ampérage si la puissance du foyer dépasse `max_house_power`.
- **Limites d'ampérage** : le courant reste toujours dans la plage `amp_min` / `amp_max`.
- **Mode `single`** : une seule instance s'exécute à la fois, les déclenchements simultanés sont ignorés silencieusement.

---

## ⚙️ Paramètres

| Paramètre | Description | Défaut |
|-----------|-------------|--------|
| `net_consumption` | Consommation nette du foyer (W) — positif = consommation, négatif = surplus | `sensor.consommation_nette_w` |
| `tesla_switch` | Switch pour démarrer/arrêter la charge | `switch.tesla_charger` |
| `tesla_amperage` | Consigne d'ampérage (number) | `number.tesla_charging_amps` |
| `tesla_current_amperage` | Ampérage réel mesuré pendant la charge (sensor) | `sensor.tesla_charge_current` |
| `tesla_voltage` | Tension de charge mesurée (sensor) | `sensor.tesla_charge_voltage` |
| `charge_cable` | Capteur câble branché (binary_sensor) | `binary_sensor.tesla_charge_flap` |
| `amp_min` | Ampérage minimum de charge | `5 A` |
| `amp_max` | Ampérage maximum de charge | `32 A` |
| `tesla_soc` | Niveau de charge de la batterie (%) | `sensor.tesla_charge_level` |
| `charge_mode` | Entité select du mode de charge | `select.tesla_mode_charge_tesla` |
| `max_house_power` | Puissance max du foyer avant délestage (W) | `12000 W` |
| `offset_start` | Surplus minimum pour démarrer la charge (W négatif) | `-50 W` |
| `delay_tempo` | Temporisation entre chaque action de régulation (s) | `15 s` |

---

## 📡 Déclencheurs

L'automation se déclenche sur :
- Tout changement d'état des entités configurées (consommation, switch, ampérage, SOC, câble, mode)
- Une vérification périodique toutes les **5 minutes**

---

## 📋 Prérequis

- Home Assistant avec une intégration Tesla BLE fonctionnelle
- Un capteur de **consommation nette** du foyer (ex: Enphase, Shelly EM, Linky...)
- L'intégration **[Guition Tesla BLE Gateway](https://github.com/capof1000/guition-tesla-ble)** (recommandé) ou toute intégration exposant les entités Tesla listées ci-dessus

---

## 📝 Changelog

| Version | Changements |
|---------|-------------|
| v1.8 | Fix sélecteurs `tesla_current_amperage` et `tesla_voltage` : domaine `number` → `sensor` |
| v1.7 | Valeurs de mode en anglais (`manual/solar/forced`) pour compatibilité Guition b28 |
| v1.6 | Remplacement du capteur de présence BLE par le capteur de câble de charge |
| v1.5 | Raccourci mode forcé → puissance maximale directe |
| v1.4 | Réorganisation logique solaire : démarrage → augmentation → diminution → arrêt |
| v1.3 | Fix mode solaire : coupure intempestive au lieu de diminution |
| v1.2 | Mode `single` |
| v1.1 | Horodatage dans les messages de log |
| v1.0 | Version initiale |

---

## 🙏 Crédits

- **[PedroKTFC](https://github.com/PedroKTFC/esphome-tesla-ble)** — librairie esphome-tesla-ble
- **[ESPHome](https://esphome.io)** — framework
- **[Home Assistant](https://www.home-assistant.io)** — plateforme domotique

---
---

# ⚡ Tesla SmartCharge Blueprint

> [🇫🇷 Français ci-dessus](#-tesla-smartcharge-blueprint) | 🇬🇧 English

Home Assistant blueprint to optimize Tesla charging based on photovoltaic solar production.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/capof1000/Tesla-SmartCharge/refs/heads/main/Blueprint_TeslaSmartCharge.yaml)

---

## 🎯 Goal

Automatically manage Tesla charging to maximize the use of available solar energy, while protecting the home electrical installation.

> ℹ️ This blueprint is designed to work with the **[Guition ESP32-S3 Tesla BLE Gateway](https://github.com/capof1000/guition-tesla-ble)** project, but can be adapted to any compatible Tesla BLE integration.

---

## 🔄 Operating Modes

### `manual` — Manual
The blueprint **does nothing**. Control is handled directly from the Guition screen or Home Assistant.

### `solar` — Solar ☀️
The blueprint automatically regulates the charging amperage based on the home's energy balance:

| Situation | Action |
|-----------|--------|
| Solar surplus detected (net < offset) | Start charging |
| Surplus > charge voltage | Increase by 1A |
| Grid consumption > charge voltage | Decrease by 1A |
| Consumption too high AND amperage at minimum | Stop charging |

### `forced` — Forced ⚡
Charging starts immediately at the maximum allowed amperage, with an active **load-shedding safety**: if total home power exceeds the configured threshold, the amperage is progressively reduced.

---

## 🛡️ Safety Features

- **Cable not connected**: no action if the cable sensor indicates the car is not plugged in.
- **Automatic load shedding** (forced mode): amperage reduced if home power exceeds `max_house_power`.
- **Amperage limits**: current always stays within the `amp_min` / `amp_max` range.
- **`single` mode**: only one instance runs at a time, simultaneous triggers are silently ignored.

---

## ⚙️ Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `net_consumption` | Net home consumption (W) — positive = consuming, negative = surplus | `sensor.consommation_nette_w` |
| `tesla_switch` | Switch to start/stop charging | `switch.tesla_charger` |
| `tesla_amperage` | Amperage setpoint (number) | `number.tesla_charging_amps` |
| `tesla_current_amperage` | Actual measured amperage during charge (sensor) | `sensor.tesla_charge_current` |
| `tesla_voltage` | Measured charge voltage (sensor) | `sensor.tesla_charge_voltage` |
| `charge_cable` | Charge cable plugged sensor (binary_sensor) | `binary_sensor.tesla_charge_flap` |
| `amp_min` | Minimum charge amperage | `5 A` |
| `amp_max` | Maximum charge amperage | `32 A` |
| `tesla_soc` | Battery charge level (%) | `sensor.tesla_charge_level` |
| `charge_mode` | Charge mode select entity | `select.tesla_mode_charge_tesla` |
| `max_house_power` | Max home power before load shedding (W) | `12000 W` |
| `offset_start` | Minimum surplus to start charging (negative W) | `-50 W` |
| `delay_tempo` | Delay between each regulation action (s) | `15 s` |

---

## 📡 Triggers

The automation triggers on:
- Any state change of the configured entities (consumption, switch, amperage, SOC, cable, mode)
- A periodic check every **5 minutes**

---

## 📋 Prerequisites

- Home Assistant with a working Tesla BLE integration
- A **net consumption sensor** for your home (e.g. Enphase, Shelly EM, Linky...)
- The **[Guition Tesla BLE Gateway](https://github.com/capof1000/guition-tesla-ble)** integration (recommended), or any integration exposing the Tesla entities listed above

---

## 📝 Changelog

| Version | Changes |
|---------|---------|
| v1.8 | Fix `tesla_current_amperage` and `tesla_voltage` selectors: domain `number` → `sensor` |
| v1.7 | Mode values in English (`manual/solar/forced`) for Guition b28 compatibility |
| v1.6 | Replace BLE presence sensor with charge cable sensor |
| v1.5 | Forced mode shortcut → direct max power |
| v1.4 | Reorganized solar logic: start → increase → decrease → stop |
| v1.3 | Fix solar mode: unexpected cutoff instead of decrease |
| v1.2 | `single` mode |
| v1.1 | Timestamp in log messages |
| v1.0 | Initial release |

---

## 🙏 Credits

- **[PedroKTFC](https://github.com/PedroKTFC/esphome-tesla-ble)** — esphome-tesla-ble library
- **[ESPHome](https://esphome.io)** — framework
- **[Home Assistant](https://www.home-assistant.io)** — home automation platform

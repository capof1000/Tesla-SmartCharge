# Smart Energy / Tesla Charging -- Cahier des charges v1

## 1. Objectif du projet

Développer une **intégration Home Assistant** permettant de piloter
intelligemment la charge d'un véhicule Tesla en fonction :

-   de la production solaire
-   de la consommation du logement
-   de l'état de charge (SOC) du véhicule
-   des contraintes électriques de l'installation

L'intégration doit être : - lisible - portable - maintenable -
extensible

Les heures pleines / heures creuses sont **volontairement exclues de
cette version (v1)**.

------------------------------------------------------------------------

## 2. Contraintes techniques générales

### 2.1 Contraintes Tesla (charge AC)

-   Courant minimum : **5 A**
-   Courant maximum : **32 A**
-   Pas de charge possible entre 1 et 4 A
-   Incrément du courant : **1 A**
-   Toute consigne doit être un entier compris entre **5 et 32 A**

```{=html}
<!-- -->
```
    courant = clamp(courant, 5A, 32A)

------------------------------------------------------------------------

### 2.2 Sécurité électrique -- Délestage (prioritaire)

Une **sécurité de délestage** est **toujours active**, quel que soit le
mode sélectionné.

-   Si la puissance totale du logement dépasse un seuil configurable :
    -   réduction progressive du courant de charge
    -   arrêt de la charge si la réduction n'est plus possible (\< 5 A)

➡️ La sécurité électrique **a priorité absolue** sur tous les modes de
fonctionnement.

------------------------------------------------------------------------

## 3. Entité centrale de pilotage

### 3.1 Mode de fonctionnement

Une entité `select` unique définit le mode actif :

    select.smart_energy_mode

### 3.2 Valeurs possibles

-   disabled --- Désactivé\
-   forced --- Forcé\
-   economic --- Économique\
-   ecologic --- Écologique\
-   smart --- Smart

------------------------------------------------------------------------

## 4. Modes de fonctionnement -- Spécifications détaillées

### Mode Désactivé (`disabled`)

Charge interdite, aucun démarrage automatique.

### Mode Forcé (`forced`)

Charge immédiate avec sécurité de délestage active.

### Mode Économique (`economic`)

Charge uniquement si surplus solaire ≥ 5 A.

### Mode Écologique (`ecologic`)

Charge dès surproduction pour maximiser l'autoconsommation.

### Mode Smart (`smart`)

Seuil de charge dynamique selon le SOC du véhicule.

------------------------------------------------------------------------

## 5. Logique de décision globale

``` python
def evaluate():
    apply_safety_delestage()

    match mode:
        case "disabled":
            stop_charge()
        case "forced":
            charge(forced_current)
        case "economic":
            handle_economic()
        case "ecologic":
            handle_ecologic()
        case "smart":
            handle_smart()
```

------------------------------------------------------------------------

## 6. Philosophie

Un comportement **prévisible**, une logique **déterministe**, une
intégration **compréhensible**.

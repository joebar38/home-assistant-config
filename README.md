<h1 align="center">
  🏠<br/>Ma config' de Home Assistant<br/>
</h1>

[![Paypal](https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://paypal.me/joebar38)

![Release](https://img.shields.io/badge/Release-V1.0-18BCF2?style=for-the-badge)
[![HA Version - 2024.5.1](https://img.shields.io/badge/HA_Version-2024.5.1-18BCF2?style=for-the-badge)](https://www.home-assistant.io/blog/2024/05/01/release-20245/)
[![Home Assistant - Groupe Francophone](https://img.shields.io/badge/Facebook-Home_Assistant_Groupe_Francophone-18BCF2?style=for-the-badge)](https://www.facebook.com/groups/homeassistantgroupefrance)

Bien le bonjour les homies ! J'ai voulu partager mon interface Home Assistant avec vous tous. Il est sûrement perfectible, alors soyez indulgents ! Avec le temps, j'ajouterais un peu plus d'informations quant au fonctionnement des cartes.

La base du Dashboard vient du repo d'Aurel RV : [Son repo](https://github.com/herveaurel/)  

La base du Floorplan vient du repo de re-1st : [Son repo](https://github.com/re-1st/HA-3d-floorplan)  

# DASHBOARD

Vue principale :

![Accueil](https://github.com/joebar38/home-assistant-config/blob/main/Captures/01.png)

Sous-vue Energie :

![Energie](https://github.com/joebar38/home-assistant-config/blob/main/Captures/02.png)

Sous-vue Système :

![Système](https://github.com/joebar38/home-assistant-config/blob/main/Captures/03.png)

Sous-vue Météo/agenda :

![Météo/Agenda](https://github.com/joebar38/home-assistant-config/blob/main/Captures/04.png)

Sous-vue Qualité de l'air :

![Qualité de l'air](https://github.com/joebar38/home-assistant-config/blob/main/Captures/05.png)

Vue Chauffage/climatisation :

![Chauffage/Clim](https://github.com/joebar38/home-assistant-config/blob/main/Captures/06.png)

[![RETOUR AU SOMMAIRE](https://img.shields.io/badge/RETOUR_AU_SOMMAIRE-FF0000?style=for-the-badge)](#sommaire)

---

# FLOORPLAN

Le floorplan a été concu avec **Picture Elements** et est affiché sur un tablette 10 pouces utilisé en "Wallpanel" avec l'application **Home Assistant Companion**.

Les images ont été faite avec **SketchUp** et le rendu avec **Enscape**, le tout retravaillé sur **Photoshop** (les PSD Photoshop : **[LIEN](https://github.com/joebar38/home-assistant-config/tree/main/www/floorplan))**.

Vue principale : 


![Floorplan principal 1](https://github.com/joebar38/home-assistant-config/blob/main/Captures/10.png)

![Floorplan principal 2](https://github.com/joebar38/home-assistant-config/blob/main/Captures/11.png)

![Floorplan principal 3](https://github.com/joebar38/home-assistant-config/blob/main/Captures/12.png)

Vue principale en mode nuit:

![Floorplan nuit](https://github.com/joebar38/home-assistant-config/blob/main/Captures/16.png)

Sous-vue Alarme (s'active automatiquement lorsque l'alarme est en suspens) :

![Floorplan alarme](https://github.com/joebar38/home-assistant-config/blob/main/Captures/13.png)

Page Courses/recettes (s'active lorsque l'on clique sur la plaque de cuisson):

![Floorplan courses recettes](https://github.com/joebar38/home-assistant-config/blob/main/Captures/14.png)

Toutes les animations : 

![Floorplan animations](https://github.com/joebar38/home-assistant-config/blob/main/Captures/15.gif)

**Voici la structure de mon dashboard :**
<details>

1. **Decluttering Templates :** toutes les cartes lourdes ou appelés plusieurs fois sont ici, et permet d'alléger le code et d'y voir plus clair dans les vues.
    <details>

      Exemple simple : haut_heure est créé dans cette partie et est appelé dans une carte *custom:decluttering-card*
      ```YAML
      decluttering_templates:
        haut_heure:
          card:
            type: custom:button-card
            label: >
              [[[return states['sensor.date_jour'].state]]]
      ```
      ```YAML
      - type: custom:decluttering-card
        template: haut_heure # Appel du template précédemment créé
      ```

    Exemple avancé : bloc_3_thermostat_mitsubishi est créé dans cette partie avec des variables permettant de l'utiliser avec plusieurs climatiseurs dans HA et est appelé de la même façon que l'exemple précédent dans une carte *custom:decluttering-card*

      ```YAML
      bloc_3_thermostat_mitsubishi:
        card:
          type: picture-elements
          image: /local/images/mitsubishi/fond.png
          elements:
          ...
            - type: custom:button-card
              entity: '[[climate]]' # variable climate
              name: '[[name_clim]]' # variable name_clim
          ...
      ```
      ```YAML
      - type: custom:decluttering-card
        template: bloc_3_thermostat_mitsubishi
        variables:
          - name_clim: Salon # nom variable name_clim
          - climate: climate.salon # entité variable climate
          ...
      ```
      </details>

2. **Button Card Templates :** idem que Decluttering Templates, mais pour le style des cartes.

3. **Vues :** Toutes les vues de mon floorplan
    - La carte *picture-element*, base du floorplan avec appel de l'image de fond

    - Affichage du floorplan en mode nuit en fonction du sensor sun.sun

      <details>

      ```YAML
      - action: none
        entity: sun.sun
        hold_action:
          action: none
        state_image:
          above_horizon: /local/floorplan/images/nixx.png # Image transparente
          below_horizon: /local/floorplan/night/floorplan_night.png?v=2 # Image floorplan mode nuit
        style:
          left: 50%
          top: 50%
          width: 100%
        tap_action:
          action: none
        type: image
      ```
      </details>
    
    - Superposition d'images ou d'animations avec la carte *custom:config-template-card*
        
      **Focus sur la carte de sélection des titres:**
      <details>

      ```YAML
      - type: custom:config-template-card
        entities:
          - input_select.floorplan_title_bloc_1 # Création d'une entité liste déroulante pour sélection
        style:
          left: 12%
          top: 67%
          width: 20%
        element:
          action: none
          entity: input_select.floorplan_title_bloc_1
          image: /local/floorplan/images/nixx.png # Image transparente si off
          hold_action:
            action: none # Rend la carte non cliquable
          state_image: # Permet d'avoir une image différente en fonction de l'état de l'entité
            Météo: /local/floorplan/images/title_meteo.png # Affichage titre météo
            Qualité air: /local/floorplan/images/title_qualite_air.png # Affichage titre Qualité air
            Températures: /local/floorplan/images/title_temperatures.png?v=2 # Affichage titre Températures
          tap_action:
            action: none # Rend la carte non cliquable
      ```
      </details>

      **Focus sur une carte d'affichage de lumière:**
      <details>

      ```YAML
      - type: custom:config-template-card
        entities:
          - switch.on_off_cadrepi # Entité à surveiller
        style:
          left: 50%
          top: 50%
          width: 100%
        element:
          action: none 
          entity: switch.on_off_cadrepi # Entité à surveiller
          image: /local/floorplan/images/nixx.png # Image transparente si off
          hold_action:
            action: none
          state_image:
            'on': /local/floorplan/day/floorplan_day_light_cadrepi.png # Si CadrePi on, alors affichage image
          tap_action:
            action: none
          type: image
      ```
      </details>



    - Boutons d'actions avec une image transparente

      **Focus sur une carte:**
      <details>

      ```YAML
      - type: image
        image: /local/floorplan/images/nix100100.png # Image transparente
        entity: light.lampe_chevet # Entité à contrôler
        title: Allumer Lampe de chevet
        style:
          left: 29%
          top: 33%
          width: 2%
          opacity: 100
        tap_action:
          action: toggle
        hold_action:
          action: more-info
      ```
      </details>

    - Icône des blocs du bas

      **Focus sur une carte:**
      <details>

      ```YAML
      - type: custom:button-card
        template:
          - no_style
        show_name: false
        entity: input_select.floorplan_title_bloc_1 # Entité à surveiller
        color: var(--secondary-color)
        state:
          - operator: '=='
            value: Météo # Si la valeur est égale à Météo
            color: var(--primary-color) # Affichage de l'icône en couleur
        color_type: icon
        haptic: medium
        icon: mdi:weather-partly-cloudy # Icône à afficher
        tap_action:
          action: call-service
          haptic: medium
          service: input_select.select_option # Changer l'option de l'entité pour affichage de la carte concernée
          service_data:
            entity_id: input_select.floorplan_title_bloc_1
            option: Météo
        style:
          left: 15%
          top: 67%
          width: 5%
      ```
      </details>

    - Affichage des blocs

      **Focus sur une carte:**
      <details>

      ```YAML
      - type: conditional # Condition si l'option Météo de la liste déroulante est sélectionnée
        conditions:
          - entity: input_select.floorplan_title_bloc_1
            state: Météo
        elements:
          - type: custom:decluttering-card # Affichage du template bloc_1_meteo de decluttering_templates
            template: bloc_1_meteo
            style:
              left: 13%
              top: 83%
              width: 26%
      ```
      </details>

</details>

--- 

# MODULES

<details>

[AdGuard Home](https://www.home-assistant.io/integrations/adguard/)  
_Bloqueur de publicité_

[DOODS2](https://github.com/snowzach/doods2)  
_Système de reconnaissance d'objets_

[Home Assistant Google Drive Backup](https://github.com/sabeechen/hassio-google-drive-backup)  
_Faire ses backups entre HA et Google Drive_

[motionEye](https://github.com/sabeechen/hassio-google-drive-backup)  
_CCTV/NVR pour caméras_

[MyElectricalData](https://github.com/MyElectricalData/myelectricaldata_import)  
_Récupère toutes les informations du compteur Linky_

[Network UPS Tools](https://www.home-assistant.io/integrations/nut/)  
_Remontée d'information d'un onduleur_

[Piper](https://www.home-assistant.io/integrations/piper/)  
_Text to speech_

[RPC Shutdown](https://github.com/home-assistant/addons/blob/master/rpc_shutdown/DOCS.md)  
_Wake on Lan périphériques_

[Samba share](https://github.com/home-assistant/addons/blob/master/rpc_shutdown/DOCS.md)  
_Synchronisation dossier HA sur PC_

[Studio Code Server](https://github.com/home-assistant/addons/blob/master/rpc_shutdown/DOCS.md)  
_Éditeur de code hébergé sur HA_

[Whisper](https://www.home-assistant.io/integrations/whisper/)  
_Speech to text_

[Zigbee2MQTT](https://www.zigbee2mqtt.io/)  
_Intégration des appareils ZigBee_

</details>

--- 

# CAPTEURS

[Electricity Maps](https://www.home-assistant.io/integrations/co2signal)  
_Connaître l’intensité de CO2 d’une région spécifique_

[Sensor.Community](https://www.home-assistant.io/integrations/luftdaten)  
_Capteurs qualité d'air extérieurs PM2.5 et PM10_

# HACS

[Installer HACS](https://hacs.xyz/)

## Intégrations :

<details>
[Alarmo](https://github.com/nielsfaber/alarmo)  
_Système d'alarme_

[Browser Mod](https://github.com/thomasloven/hass-browser_mod)  
_Transformer un navigateur en entitées contrôlables_

[Freebox Player](https://github.com/Pouzor/freebox_player)  
_Télécommande pour Freebox Player_

[ICS Calendar (iCalendar)](https://github.com/franc6/ics_calendar)  
_Intégration des calendriers ICS_

[Lovelace Gen](https://github.com/thomasloven/hass-lovelace_gen)  
_Améliorer l’analyseur YAML lovelace pour Home Assistant_

[Signal Ecogaz](https://github.com/kamaradclimber/signal_ecogaz)  
_Création d'entités pour les prévisions écogaz_

[Spook](https://github.com/frenck/spook)  
_Analyseur de problèmes lovelace, automatisations..._

[Spotcast](https://github.com/fondberg/spotcast)  
_Lecture Spotify sur une Chromecast ou sur un appareil Spotify Connect_

[Tapo: Cameras Control](https://github.com/JurajNyiri/home-assistant-config-Tapo-Control)  
_Intégration caméras Tapo_

[Vigieau](https://github.com/kamaradclimber/vigieau)  
_Suivi des restrictions eau_

[WebRTC Camera](https://github.com/AlexxIT/WebRTC)  
_Vue en temps réel stream caméras_

</details>

## Interface : 

<details>
[Alarmo Card](https://github.com/AlexxIT/WebRTC)  
_Carte pour intégration Alarmo_

[ApexCharts card](https://github.com/AlexxIT/WebRTC)  
_Cartes graphiques personnalisées_

[Bar Card](https://github.com/AlexxIT/WebRTC)  
_Carte en forme de barre animé personnalisable_

[Button card](https://github.com/AlexxIT/WebRTC)  
_Lovelace button-card_

[Decluttering Card](https://github.com/AlexxIT/WebRTC)  
_Réutiliser plusieurs fois une configuration de carte avec variables pour simplifier la configuration_

[Lovelace Card Mod](https://github.com/AlexxIT/WebRTC)  
_Ajout de styles CSS_

[Lovelace Layout Card](https://github.com/AlexxIT/WebRTC)  
_Contrôle poussé sur le placement des cartes_

[Lovelace Mini Graph Card](https://github.com/AlexxIT/WebRTC)  
_Cartes graphiques minimalistes_

[Paper Buttons Row](https://github.com/AlexxIT/WebRTC)  
_Ajout de boutons configurables utilisant des actions et un style par état_

[Swipe Card](https://github.com/AlexxIT/WebRTC)  
_Carte utilsant le swipe tactile pour en parcourir plusieurs_

[Vertical Stack in Card](https://github.com/AlexxIT/WebRTC)  
_Groupe plusieurs cartes en une seule verticalement_

</details>

# APPAREILS

| Matériel | Modèle |
| --- | --- |
| Raspberry |  `Raspberry Pi 4B 4Go` |
| Tablette |  `Dodgee U10` |
| SSD |  `SAMSUNG 840 Pro 128Go - MZ-7PD128` |
| Zigbee | `SONOFF EFR32MG21 (Type E)` sur Zigbee2MQTT |
| Onduleur | `EATON 3S 550 FR` |
| Box internet | `Freebox Mini 4K` |
| Caméras | `TP-LINK Tapo C210 + D-LINK DCS-8300LH` |
| Capteurs de porte | `AQARA MCCGQ11LM + SONOFF SNZB-04` |
| Moniteur qualité d'air | `AQARA TVOC AAQS-S01` |
| Thermomètres | `SONOFF SNZB-02D` |
| Ampoule | `LEXMAN RGBTW P45 BULB` |
| Télécommande | `LEXMAN HR-C99C-Z-C045-B` |
| Télécommande Alarme | `LEXMAN LDSENK09` |
| Thermostat | `NETATMO sur chaudière gaz individuel` |
| Climatisation | `MITSUBISHI MSZ-AY25VGK + MITSUBISHI MSZ-AY42VGK` |


# MERCI

La base du Dashboard vient de mon mentor Aurel : [Son repo](https://github.com/herveaurel/)  

La base du Floorplan vient du repo de re-1st : [Son repo](https://github.com/re-1st/HA-3d-floorplan)  

Un grand merci pour leur travail titanesque qui m'a vraiment aidé à progresser et à mieux comprendre le fonctionnement de Home Assistant !

[![Paypal](https://img.shields.io/badge/PayPal-00457C?style=for-the-badge&logo=paypal&logoColor=white)](https://paypal.me/joebar38)

[![Home Assistant - Groupe Francophone](https://img.shields.io/badge/Facebook-Home_Assistant_Groupe_Francophone-18BCF2?style=for-the-badge)](https://www.facebook.com/groups/homeassistantgroupefrance)
# ClimaNodeRed-HA
Gestione dei condizionatori con Nodered in homeassistant.
(fork del repository mironalessandro/ClimaNRHA:master)

Questo è un flow che gestisce appunto il condizionatore in maniera automatica in base all'indice di Thom calcolato da un sensore. 
Opzionalmente si può decidere se fare un check sulle finestre aperte.

## Prerequisiti

  - Sensore di Umidità / Temperatura 
  - Entità climate
  - Una variabile che indichi se una delle finestre è aperta (opzionale. Se non volete usare questo parametro basta che la variabile input_boolean.climate_check_windows sia off)
  - Una gruppo (famiglia ad esempio) che nel mio caso indica se qualcuno è presente o no in casa.

### Inoltre abbiamo bisogno di queste variabili:

input_boolean.automatic_climate --> serve ad attivare o disattivare l'automazione 
input_boolean.climate_check_windows --> serve a gestire accensione e spegnimento in base alle finestre aperte (bisogna creare un'automazione che accenda o spegna l'interruttore in base allo stato delle finestre monitorate) 
input_number.thom_maximum --> Indice di Thom massimo al quale far partire il clima
input_number.thom_minimum --> Indice di Thom minimo al quale far spegnere il clima
input_number.thom_minimum_nobody_home --> Indice di Thom massimo al quale far partire il clima quando nessuno è a casa
input_number.thom_maximum_nobody_home --> Indice di Thom minimo al quale far spegnere il clima quando nessuno è a casa
input_number.gradi_cold --> Gradi celsius in caso di accensione del clima in modalità "Cold"
input_number.gradi_dry --> Gradi celsius in caso di accensione del clima in modalità "Dry"
input_number.limite_humidity_dry_or_cold --> Percentuale per accendere in modalità dry o cold.

# Indice di THOM

sensor:
  - platform: template
    sensors:
      indice_thom_soggiorno:
        friendly_name: "Indice Thom"
        value_template: "{{ (states('sensor.ble_soggiorno_temperature')|float - (0.55 - 0.0055 * states('sensor.ble_soggiorno_humidity')|float) * (states('sensor.ble_soggiorno_temperature')|float - 14.5))|round(1) }}"
        unit_of_measurement: 'index'


Se si hanno più split da gestire con questa automazione si potranno usare le stesse variabili, basterà:
- creare un nuovo Flow in NodeRed
- aggiornare l'entità "climate" in tutti i nodi
- creare un nuovo sensore per calcolare l'indice di Thom della stanza dove è installato lo split
- aggiornare il flw per usare il nuovo sensore di Thom creato


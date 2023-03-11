# Testuji 

#

11.3.2022

- **MULTI HOTEND** 

 Pokud je použit hotend typu 2 IN 1 OUT ( nebo xx IN 1 OUT), je provedena 

 automatická výměna filamentu mimo tiskovou ploxchu.

 Defaultne je to nasaveno na pozici endstopu + 20 mmm. Otestováno i na IDEX 

 Makro je zde: [MULTI_HOTEND.cfg](https://github.com/DrumClock/my_config/blob/main/MULTI/MULTI_HOTEND.cfg)

 Toto makro je automaticky voláné z maker : SETINGS_TOOL, ACTIVATE_CARRIAGE, ACTIVATE_GANTRY 
 

14.1.2023

- **ENDLESS SPOOL** - nekonečná cívka (označovaný jako **Backup mode** )

 Pokud je detekován konec filamentu pomocí sensoru (switch nebo motion), dojde k výměně za extruder který má filament.
  
 Zároveň jsou všechny tiskové G-cody  **`Tx`** směrovány na nový extruder a to včetně  **`M104 / M109`**.
 
 Proavidlo pro náhradu extruderu je následující:  **extruder > extruder1 > extruder2 > extruder3 > etc**
 
 První extruder který má detekovaný filament se pouĹľije.
 
 Makro je zde: [ENDLESS_SPOOL_macro.cfg](https://github.com/DrumClock/my_config/blob/main/MACRO/ENDLESS_SPOOL_macro.cfg)
  
#

26.12.2022

- **DUAL GANTRY** (dvojitý portál) - nová kinematika  **`dualgantry_cartesian`** 

 Je typ kinematiky pro dvě nástrojové hlavy, které se pohybují pomocí dvou zcela nezávislých os XY.
  
 Makro je zde: [ACTIVATE_dual_gantry.cfg](https://github.com/DrumClock/my_config/blob/main/DUAL_GANTRY/ACTIVATE_dual_gantry.cfg)
    
 Video testu na: [Virtual Printer](https://youtu.be/5AJfQ59xB38)

# Informace

Všechny uvedené sloĹľky a soubory jsou v Raspberry uloĹľené zde:  **`/home/pi/my_config/`**

Pro zobrazení sloĹľky **`my_config`** v rozhranní **Mainsail** je nutné vytvořit "soft-link" pomocí:

**` ln -s /home/pi/my_config /home/pi/xxx/my_config `**   (xxx - adresář s cfg např. klipper_config)

# Makra jsou univerzální pro tyto konfigurace

- single carriage pro 2 aĹľ 4 extrudery
- dual carriige (IDEX) pro 2 aĹľ 4 extrudery

# Popis adresářů:
**Klipper zatím nepodoruje - moduly pro testování poskytl Tircown**
- **`IDEX`**          - M605 mody - DUPLICATION a MIRRORED 
- **`4EX2`**          - rozšíření IDEX modu M605 pro 4 extrudery (dvoubarevný tisk DUPLICATION a MIRRORED)
- **`DUAL_GANTRY`**   - dvojitý portál X/Y  
#
- **`SWITCHING`**      - makro pro SWITCHING EXTRUDER / HOTEND  
- **`MULTI`**          - makro pro MULTI-HOTENDY typi 2 IN 1 OUT
#
- **`MAIN`**          - základní univerzální makra pro 2-4 extrudery 
- **`MACRO`**         - rozšířená univerzální makra  
- **`MCU`**           - HW konfigurace MCU desek
- **`PRINTER`**       - definice druhu tiskárny pro testy
- **`TEST`**          - testovací makra
#
- **`LCD`**           - HW konfigurace pro LCD nebo OLED displeje 
- **`MENU`**          - menu obrazovky pro displeje
#
**ve vývoji maker**
- **`P_L_R`**         - mod POWER LOSS RECOVERY  
- **`W_T`**           - mod WIPE TOWER 

# Jednotlivá makra se načítají pomocí [include ...] např.

```
### MCU configurations  ###

[include /home/pi/my_config/MCU/MCU_rumba32_.cfg]
[include /home/pi/my_config/MCU/MCU_rumba32_4ex2.cfg]
[include /home/pi/my_config/MCU/MCU_rumba32_dual_X.cfg]

### LCD configurations  ###

[include /home/pi/my_config/LCD/display_LCD12864_rumba_32.cfg]
[include /home/pi/my_config/LCD/group_klipper_logo.cfg]
[include /home/pi/my_config/LCD/group_16x4_main.cfg]

[include /home/pi/my_config/MENU/*]


### users mareo configurations  ###

[include /home/pi/my_config/MACRO/*]
[include /home/pi/my_config/W_T/*]
[include /home/pi/my_config/4EX2/*]
```

# Proměnné pro makra se vytvoří pomocí RUN_MACRO_INIT 
spuštěného v [delayed_gcode _INIT] z konfigurace tiskárny a to po restartu FW např.

```
printer['gcode_macro VARIABLE'].aaa = {'printer': '4EX2'}
printer['gcode_macro VARIABLE'].active = {'z': {'restore': 4.0, 'print_offset': 0.0, 'hop': 4.0}, 'mode': 0}
printer['gcode_macro VARIABLE'].beeper = {'enable': True, 'silent': False, 'output_pin': 'beeper'}
printer['gcode_macro VARIABLE'].dual_carriage = {'parking': [-52, 298], 'movespeed': 500, 'feedrate': 30000, 'auto_exchange': True, 'sync_exchange': True}
printer['gcode_macro VARIABLE'].toolhead = {0: ['extruder', 'extruder1'], 1: ['extruder2', 'extruder3']}
printer['gcode_macro VARIABLE'].tool = {0: 'extruder', 1: 'extruder1', 2: 'extruder2', 3: 'extruder3'}
printer['gcode_macro VARIABLE'].extrude_factor = {'extruder3': 1.0, 'extruder1': 1.0, 'extruder2': 1.0, 'extruder': 1.0}
printer['gcode_macro VARIABLE'].fan = {'active': 0, 'menu': True, 'index': ['fan', 'fan1']}
printer['gcode_macro VARIABLE'].hotend_offset = {0: {'y': 0.0, 'x': 0.0, 'z': 0.0}, 1: {'y': 0.0, 'x': -10.0, 'z': 0.0}, 2: {'y': 0.0, 'x': 0.0, 'z': 0.0}, 3: {'y': 0.0, 'x': -10.0, 'z': 0.0}, 'change_T0': False}
printer['gcode_macro VARIABLE'].idex_mode = {'active': 1, 'movespeed': 500, 'feedrate': 30000, 'position': {'dupl_max': 120, 'dupl_min': 52, 'mirrored': 240}, 'carriage_offset': 0}
printer['gcode_macro VARIABLE'].neopixel = {'index': {0: 'axis_X'}, 'RGB': {0: '0,0,0'}, 'enable': True, 'menu': {'active': 0}}
printer['gcode_macro VARIABLE'].offset_temp = {'extruder3': 0, 'extruder1': 0, 'extruder2': 0, 'extruder': 0}
printer['gcode_macro VARIABLE'].restore_variable = ['active', 'hotend_offset', 'tool_change', 'tool', 'toolhead', 'switching_hotend']
printer['gcode_macro VARIABLE'].servo = {'index': {0: 'tool_1', 1: 'tool_2'}, 'enable': True, 'angle': {0: '--', 1: '--'}, 'menu': {'active': 0, 'angle_2': 135, 'angle_1': 45}}
printer['gcode_macro VARIABLE'].switching_extruder = {'enable': True, 'extruder1': 'extruder', 'extruder3': 'extruder2'}
printer['gcode_macro VARIABLE'].switching_hotend = {'enable': False, 'name': {0: 'tool_1', 1: 'tool_2'}, 'turn_off': {0: False, 1: False}, 'extruder': {'delay': 200, 'index': 0, 'angle': 45, 'off': False}, 'extruder1': {'delay': 200, 'index': 0, 'angle': 135, 'off': False}, 'extruder2': {'delay': 200, 'index': 1, 'angle': 45, 'off': False}, 'extruder3': {'delay': 200, 'index': 1, 'angle': 135, 'off': False}}
printer['gcode_macro VARIABLE'].sync_switching_tool = {'active': 'none', 'enable': True, 'T0': {0: 'extruder', 1: 'extruder2'}, 'T1': {0: 'extruder1', 1: 'extruder3'}}
printer['gcode_macro VARIABLE'].tool_change = {'delay': {'extruder': 250, 'extruder1': 250, 'extruder2': 250, 'extruder3': 250}, 'menu': 'extruder', 'enable': True, 'axis_z': {'restore': 0.0, 'hop': 3.0}, 'filament': {'load': 0.5, 'speed': 45, 'unload': 1.0}}
printer['gcode_macro VARIABLE'].tower = {'tool': {0: {'nozzle': 0.4, 'z': 0.0}, 1: {'nozzle': 0.4, 'z': 0.0}, 2: {'nozzle': 0.6, 'z': 0.0}, 3: {'nozzle': 0.8, 'z': 0.0}}, 'enable': False, 'pos': {'y': 200.0, 'x': 200.0}, 'change': True, 'gap': {'y': 40, 'x': 50}}
```



#

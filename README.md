# Viola's Cube

A bedside voice satellite for Home Assistant: a Voice PE for listening and speaking, a 4" touch panel in ESPHome, a real speaker for music, all inside a 15 cm 3D-printed cube.

![Assembled cube (rendering)](immagini/3_cubo_montato.png)

**Status: prototype.** The electronics and firmware are working on the bench. The enclosure is at version 3 and is being printed for the first time: the measurements have only been verified on the computer, and no part has yet been assembled. The image above is a rendering.

## Why
At home we have an Echo in each room, but our assistant is different: Viola, built inside Home Assistant with a language model (Claude, via the HA Anthropic integration). You can converse with her: she knows the house, has her own memory, searches the web, and carries messages between rooms. She cannot fit inside an Echo. The cube is a way to have her in every bedroom.

This repository contains **the cube** (enclosure, parts list, panel firmware). The "Viola" component inside Home Assistant (prompt, memory, fast path for simple commands) is not included here.

## What's inside
| Folder | Contents |
|---|---|
| `3d/` | OpenSCAD source (`cubo.scad`), the same part in CadQuery to generate STEP files (`cubo_step.py`), STEP and STL files for the cube, the cube with engraved lettering, and the tray |
| `firmware-pannello/` | ESPHome configuration for the touch panel (`cubo-pannello.yaml`) |
| `immagini/` | Views with labels |

![Outside](immagini/1_cubo_fuori.png)
![Inside](immagini/2_cubo_dentro.png)

## Hardware
| Part | What we use | Notes |
|---|---|---|
| Voice satellite | Home Assistant Voice Preview Edition, in its casing (83 x 83 x 20 mm) | flush-mounted into the lid; right-angle USB-C and audio jack plugs |
| Panel | Waveshare ESP32-S3-Touch-LCD-4, 480 x 480 (84 mm glass, 75 mm board) | 77 mm opening, glued in from the outside |
| Speaker | Cambridge Audio Minx (78 x 78 x 83 mm) | at the rear, facing the wall through the grille; secured by a bracket using its rear screw |
| Amplifier | 46 x 35 mm board, M2 holes spaced 40 x 28 mm | receives audio from the Voice PE jack |
| Power supply | 12 V power supply, 10 mm diameter panel socket, 12 → 5 V converter (46 x 27 x 13 mm) | 12 V to the amplifier, 5 V to the Voice PE and panel |
| Audio ground loop isolator | 3.5 mm isolator | loose inside the cube; removes hum between the PE and amplifier |

Everything mounts on the tray; then the cube, with the panel and Voice PE, is lowered over it and closed with 4 M3 screws in heat-set inserts.

## The panel
The panel does not know Home Assistant entities. HA talks to it with a few actions (`pannello_messaggio`, `pannello_risposta`, `pannello_squilla`, `pannello_in_chiamata`, `pannello_sveglia`, `pannello_chiudi`), and it replies with a single event (`esphome.banco_pannello`, with the `azione` field). The logic remains in HA.

Features: clock with room name and temperatures · do not disturb · scheduled night dimming · inter-room messages with read confirmation · inter-room calls · music page with cover art (Music Assistant) · Voice PE timer · alarm · settings menu with long press.

**Before using it at home:** the file has no API encryption and no OTA password, and if it loses the network it opens an unsecured access point. These must be added. There are no credentials in the file: Wi-Fi is configured using improv over USB.

## Regenerate the parts
```
openscad -o cubo_corpo.stl -D 'parte="corpo"' 3d/cubo.scad
openscad -o cubo_fondo.stl -D 'parte="fondo"' 3d/cubo.scad
python 3d/cubo_step.py <output_folder>            # STEP; CadQuery is required
python 3d/cubo_step.py <output_folder> scritta    # variant with the engraved lettering
```
The dimensions are written twice, in `cubo.scad` and `cubo_step.py`: if you change one, change it in both. Comments and variable names are in Italian.

## What still doesn't work
- Music on the Voice PE sometimes stutters.
- The enclosure has not yet been physically tested.
- The Home Assistant automations that use the panel (messages, calls, alarm) are not in this repository.

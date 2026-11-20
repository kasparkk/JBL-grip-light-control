# JBL Grip light control

A single web page that controls the light of a **JBL Grip** Bluetooth speaker from Chrome, using Web Bluetooth. No install, no app.

Unofficial hobby project. Not affiliated with or endorsed by JBL or Harman. Use at your own risk.

## What it does
- 5 built-in light shows: Freeze, Bounce, Trim, Switch, Neon
- 4 colours: orange, pink, red, and **blue** (blue is not offered in the JBL app)
- Brightness 3 to 100 %, light on and off
- Own shows driven from the page: breathe, colour cycle (pick any 2 to 4 colours) on top of any theme, theme rotation, and a bass-beat music mode (microphone, or shared tab audio on desktop)

## Remote mode (phone without Bluetooth)
A device that is connected to the Grip (e.g. a laptop) presses **Relais starten**; another device enters the same secret code and presses **Als Fernbedienung**. Commands go through a Supabase Realtime broadcast channel named after the code. Only theme, brightness and colour commands are forwarded. Anyone who knows your code can control your light, so pick a code nobody can guess. If you fork this, put your own Supabase project URL and publishable key in `index.html`.

## Use
1. Close the JBL app on your phone, switch the Grip on.
2. Open the page in Chrome (desktop or Android) and press **Verbinden** (connect).
3. On Android: Chrome menu, "Add to home screen".

UI is in German.

## Protocol (as decoded from an Android Bluetooth HCI log)
- GATT service `65786365-6c70-6f69-6e74-2e636f6d0000`, write `...0002`, notify `...0001`
- JBL "Protocol 4" frame: `00 DD`, command u16le (2 = SET), `01 00`, payload length u16le, payload
- Payload: feature id u16le, value length u16le, value

| Feature | ID | Values |
|---|---|---|
| Light show | 0x0D40 | Freeze 0x8C, Bounce 0x89, Trim 0x8A, Switch 0x8B, Neon 0x81 (unknown values fall back to Neon) |
| Brightness | 0x0D41 | 0x80 \| percent = on, percent without 0x80 = off |
| Colour | 0x0D46 | 1 orange, 2 pink, 3 blue, 4 red |

The speaker also reports 0x0D43 (`81`) and 0x0D80 (4 bytes, default `01 ff ff ff`). Writing 0x0D80 is accepted and changes the light, but the meaning is unknown and the effect persisted until the battery ran out. The page does not write it.

Protocol background: [NiceDayZc/openjbl](https://github.com/NiceDayZc/openjbl), [ffrafat/JBLControl](https://github.com/ffrafat/JBLControl).

## License
MIT

# DMX Controller Library for Node.js

A custom DMX controller library for Node.js.

## Installation

Install the package using npm:

```sh
npm install dmx
```

## Library API

```javascript
const DMX = require('dmx');
```

### DMX Class

#### `new DMX()`

Creates a new DMX instance. This class is used to manage multiple universes.

#### `dmx.registerDriver(name, module)`

Registers a new DMX driver module.

- **name** *(String)* – Name of the driver.
- **module** *(Object)* – Object implementing the Driver API.

##### Default Registered Drivers:

- **null** – Development driver that prints the universe to stdout.
- **socketio** – Sends the universe via Socket.IO as an array ([Example Client](./demo_socket_client.js)).
- **artnet** – Driver for Enttec ODE.
- **bbdmx** – Driver for [BeagleBone-DMX](https://github.com/boxysean/beaglebone-DMX).
- **dmx4all** – Driver for DMX4ALL devices like "NanoDMX USB Interface".
- **enttec-usb-dmx-pro** – Driver for devices using an Enttec USB DMX Pro chip (e.g., DMXKing ultraDMX Micro).
- **enttec-open-usb-dmx** – Driver for "Enttec Open DMX USB" *(Not recommended due to hardware limitations)*.
- **dmxking-ultra-dmx-pro** – Driver for DMXKing Ultra DMX Pro interface (supports multiple universes via Port A or B).

#### `dmx.addUniverse(name, driver, device_id, options)`

Adds a new DMX universe.

- **name** *(String)* – Universe name.
- **driver** *(String)* – A registered driver name.
- **device_id** *(Number/Object)* – Identifier used by the driver.
- **options** *(Object)* – Driver-specific options.

Example device IDs:
- For `enttec-usb-dmx-pro` and `enttec-open-usb-dmx`, this is the serial device path.
- For `artnet`, this is the target IP.

#### `dmx.update(universe, channels[, extraData])`

Updates one or more channels in a universe.

- **universe** *(String)* – Universe name.
- **channels** *(Object)* – Channel numbers as keys, values as brightness levels.
- **extraData** *(Object, optional)* – Additional data passed to the `update` event *(default: `{}`)*.

Emits an `update` event containing the same information.

#### `DMX.devices`

A JSON object describing devices and their channel usage. More devices can be added to `devices.js` (Pull requests welcome!).

##### Known Devices:

- **generic** – One-channel dimmer.
- **showtec-multidim2** – 4-channel dimmer (4A per channel).
- **eurolite-led-bar** – LED bar with 3 RGB color segments and effects.
- **stairville-led-par-56** – RGB LED Par Can with effects.

### DMX.Animation Class

#### `new DMX.Animation([options])`

Creates a new DMX animation instance, supporting method chaining.

##### Options:

- **loop** *(Number)* – Number of times the sequence loops in `run` *(Overridden by `runLoop`)*.
- **filter** *(Function)* – Allows modifying channel values in real-time (e.g., scaling brightness with a master fader).

#### `animation.add(to, duration, options)`

Adds an animation step.

- **to** *(Object)* – Channels and their target values.
- **duration** *(Number)* – Duration in milliseconds.
- **options** *(Object)* – Supports `easing` functions.

##### Easing Functions:

- **Linear (default)**
- **Quadratic**: inQuad, outQuad, inOutQuad
- **Cubic**: inCubic, outCubic, inOutCubic
- **Quartic**: inQuart, outQuart, inOutQuart
- **Quintic**: inQuint, outQuint, inOutQuint
- **Sine**: inSine, outSine, inOutSine
- **Exponential**: inExpo, outExpo, inOutExpo
- **Circular**: inCirc, outCirc, inOutCirc
- **Elastic**: inElastic, outElastic, inOutElastic
- **Back**: inBack, outBack, inOutBack
- **Bounce**: inBounce, outBounce, inOutBounce

#### `animation.delay(duration)`

Adds a delay step in milliseconds.

#### `animation.run(universe, onFinish)`

Runs the animation on the specified universe.

- **universe** *(Object)* – Reference to the universe driver.
- **onFinish** *(Function)* – Callback executed when the animation completes.

#### `animation.runLoop(universe)`

Runs the animation continuously until `animation.stop()` is called.

##### Example: Animate a channel for 5 seconds

```javascript
const animation = new DMX.Animation()
  .add({ 1: 255 }, 100)
  .add({ 1: 0 }, 100)
  .runLoop(universe);

setTimeout(() => {
  animation.stop();
}, 5000);
```

### `update` Event

Emitted whenever `update` is called (either manually or by an animation step).

- **universe** *(String)* – Universe name.
- **channels** *(Object)* – Channels and their new values.
- **extraData** *(Object)* – Data passed in `update`. If triggered by animation, `extraData.origin` will be `'animation'`.

## Web Interface

Versions prior to `0.2` included a web interface, now available as a separate project: [dmx-web](https://github.com/node-dmx/dmx-web).

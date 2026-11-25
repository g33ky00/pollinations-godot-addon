# Pollinations Godot Add-on

[![CI](https://github.com/g33ky00/pollinations-godot-addon/actions/workflows/ci-unified.yml/badge.svg)](https://github.com/g33ky00/pollinations-godot-addon/actions/workflows/ci-unified.yml)

[![Quest](https://img.shields.io/badge/Quest-%2315579%20(FIXED)-blue)](https://github.com/pollinations/pollinations/issues/15579)
[![Godot](https://img.shields.io/badge/Godot-4.3%2B-478bff)](https://godotengine.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

GDScript add-on that lets Godot 4 developers generate **text, images, and speech** with [Pollinations AI](https://pollinations.ai) inside their games.

## About

This add-on exposes `PollinationsClient`, `ModelCatalog` and Text/Image/Speech generator nodes for Godot 4. It supports async generation via Promises, a live model catalog, and OAuth 2.0 Device Flow (BYOP) so players can pay with their own Pollen.

## Structure

```
addon/pollinations/
├── plugin.gd
├── plugin.cfg
├── pollinations_client.gd
├── pollinations_text.gd
├── pollinations_image.gd
├── pollinations_speech.gd
└── model_catalog.gd
example_scene.tscn
CHANGELOG.md
LICENSE
```

## Stack

- Godot 4.3+
- GDScript
- Pollinations AI API (gen.pollinations.ai, image.pollinations.ai, enter.pollinations.ai)

## Features

- **Nodes** for text, image, and speech generation — usable from GDScript
- **Async API** with Promises
- **Live model catalog** — fetch available text, image, and audio models
- **OAuth 2.0 Device Flow** (BYOP) — players pay with their own Pollen
- **Example scene** included

## Installation

### Manual

1. Download this repository
2. Copy the `addon/pollinations/` folder to `<your-project>/addons/pollinations/`
3. In Godot, go to **Project → Project Settings → Plugins**
4. Enable **Pollinations AI**

## Quick Start

### Setup

```gdscript
var client = PollinationsClient.new()
client.api_key = "sk_..." # Get one at https://enter.pollinations.ai/keys
add_child(client)

var catalog = ModelCatalog.new(client)
add_child(catalog)
await catalog.refresh()
```

### Generate text

```gdscript
var gen = PollinationsText.new()
gen.client = client
gen.prompt = "Describe a fantasy tavern"
var text = await gen.generate()
print(text)
```

### Generate an image

```gdscript
var gen = PollinationsImage.new()
gen.client = client
gen.prompt = "A cyberpunk city at night"
gen.width = 512
gen.height = 512
var tex = await gen.generate()
gen.save_to_disk("user://output.png")
```

### Generate speech

```gdscript
var gen = PollinationsSpeech.new()
gen.client = client
gen.prompt = "Hello, adventurer!"
gen.voice = "nova"
var clip = await gen.generate()
var player = AudioStreamPlayer.new()
add_child(player)
gen.play_on(player)
```

## API Endpoints

| Feature | Endpoint |
|---|---|
| Text (chat) | `POST https://gen.pollinations.ai/v1/chat/completions` |
| Image | `GET https://image.pollinations.ai/{prompt}?model=&width=&height=` |
| Speech | `GET https://gen.pollinations.ai/audio/{prompt}?voice=&model=` |
| Models | `GET https://gen.pollinations.ai/{text\|image\|audio}/models` |
| Device flow | `POST https://enter.pollinations.ai/device` |

## BYOP (Bring Your Own Pollen)

Players can authenticate with their own Pollinations account:

```gdscript
var auth = PollinationsAuth.new()
auth.client_id = "pk_..."
var result = await auth.start_device_flow()

while result.state == DeviceFlowState.Pending:
    result = await auth.poll_token(result.device_code)
    await get_tree().create_timer(5.0).timeout

if result.state == DeviceFlowState.Granted:
    client.api_key = result.access_token
```

## Contributing

Contributions welcome. Open an issue or PR; keep changes focused.

## License

MIT — see [LICENSE](LICENSE). See [CHANGELOG.md](CHANGELOG.md) for release notes.

## Quest

This add-on fulfills [Quest #15579](https://github.com/pollinations/pollinations/issues/15579) — _[QUEST] Pollinations add-on for Godot 4_

## About

Project maintained by g33ky00.

## Contributing

Contributions are welcome via pull request.

## License

MIT

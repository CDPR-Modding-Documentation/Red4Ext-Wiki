---
description: Description about the configuration file.
---

# Configuration

The configuration file can be found in `<game_directory>/red4ext/config.ini`.

{% hint style="info" %}
If there is no configuration file present then the file is automatically generated, with the default values, when the game is started.
{% endhint %}

{% hint style="info" %}
Even if the configuration file has `ini` extension, in fact, it is a `TOML` file See [toml.io](https://toml.io/) for more information.
{% endhint %}

## Options

| Name                | Type               | Default | Description                                                                                                                                                                                                             |
| ------------------- | ------------------ | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `version`           | `unsigned integer` | `0`     | The file's version.                                                                                                                                                                                                     |
|                     |                    |         |                                                                                                                                                                                                                         |
| **logging**         |                    |         |                                                                                                                                                                                                                         |
| `level`             | `string`           | `info`  | <p>The global log level.</p><p></p><p><strong>Accepted values</strong>:</p><ul><li>off</li><li>trace</li><li>debug</li><li>info</li><li>warning</li><li>error</li><li>critical</li></ul>                                |
| `flush_on`          | `string`           | `info`  | <p>The minimum log level that will trigger the flush.</p><p><strong></strong><br><strong>Accepted values</strong>:</p><ul><li>trace</li><li>debug</li><li>info</li><li>warning</li><li>error</li><li>critical</li></ul> |
| `max_files`         | `integer`          | `5`     | <p>The maximum number of rotated log files.</p><p></p><p><strong>Note:</strong> A log file is rotated every time the game starts.</p>                                                                                   |
| `max_file_size`     | `integer`          | `10`    | The maximum size, in megabytes, of a log file.                                                                                                                                                                          |
|                     |                    |         |                                                                                                                                                                                                                         |
| **plugins**         |                    |         |                                                                                                                                                                                                                         |
| `enabled`           | `boolean`          | `true`  | Enable / disable plugins system.                                                                                                                                                                                        |
| `ignored`           | `string[]`         | `[]`    | The specified plugins are ignored and will not loaded.                                                                                                                                                                  |
|                     |                    |         |                                                                                                                                                                                                                         |
| **dev**             |                    |         |                                                                                                                                                                                                                         |
| `console`           | `boolean`          | `true`  | Enable / disable the external console (**useful for development**).                                                                                                                                                     |
| `wait_for_debugger` | `boolean`          | `false` | Block the process until a debugger is attached.                                                                                                                                                                         |

## Example

An example of the configuration file.

```
version = 0

[logging]
level = "info"
flush_on = "info"
max_files = 5
max_file_size = 10

[plugins]
enabled = true
ignored = [ "RED4ext.Playgorund" ]

[dev]
console = false
wait_for_debugger = false
```

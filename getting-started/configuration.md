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

<table data-header-hidden><thead><tr><th width="203">Name</th><th width="150">Type</th><th>Default</th><th>Description</th></tr></thead><tbody><tr><td>Name</td><td>Type</td><td>Default</td><td>Description</td></tr><tr><td><code>version</code></td><td><code>unsigned integer</code></td><td><code>0</code></td><td>The file's version.</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td><strong>logging</strong></td><td></td><td></td><td></td></tr><tr><td><code>level</code></td><td><code>string</code></td><td><code>info</code></td><td><p>The global log level.</p><p></p><p><strong>Accepted values</strong>:</p><ul><li>off</li><li>trace</li><li>debug</li><li>info</li><li>warning</li><li>error</li><li>critical</li></ul></td></tr><tr><td><code>flush_on</code></td><td><code>string</code></td><td><code>info</code></td><td><p>The minimum log level that will trigger the flush.</p><p><br><strong>Accepted values</strong>:</p><ul><li>trace</li><li>debug</li><li>info</li><li>warning</li><li>error</li><li>critical</li></ul></td></tr><tr><td><code>max_files</code></td><td><code>integer</code></td><td><code>5</code></td><td><p>The maximum number of rotated log files.</p><p></p><p><strong>Note:</strong> A log file is rotated every time the game starts.</p></td></tr><tr><td><code>max_file_size</code></td><td><code>integer</code></td><td><code>10</code></td><td>The maximum size, in megabytes, of a log file.</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td><strong>plugins</strong></td><td></td><td></td><td></td></tr><tr><td><code>enabled</code></td><td><code>boolean</code></td><td><code>true</code></td><td>Enable / disable plugins system.</td></tr><tr><td><code>ignored</code></td><td><code>string[]</code></td><td><code>[]</code></td><td>The specified plugins are ignored and will not loaded.</td></tr><tr><td></td><td></td><td></td><td></td></tr><tr><td><strong>dev</strong></td><td></td><td></td><td></td></tr><tr><td><code>console</code></td><td><code>boolean</code></td><td><code>true</code></td><td>Enable / disable the external console (<strong>useful for development</strong>).</td></tr><tr><td><code>wait_for_debugger</code></td><td><code>boolean</code></td><td><code>false</code></td><td>Block the process until a debugger is attached.</td></tr></tbody></table>

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

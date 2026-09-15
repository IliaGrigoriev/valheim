# Valheim Player Activity

Small helper script for viewing player activity from Valheim dedicated servers running as `systemd` services.

## Usage

```bash
vhplayers <server-number> "<time-span>"
```

### Examples

```bash
# Server 1, last 7 days
vhplayers 1 "7 days ago"

# Server 2, last 24 hours
vhplayers 2 "24 hours ago"

# Server 1, since today
vhplayers 1 "today"

# Server 2, default time span (7 days)
vhplayers 2
```

## Server Mapping

| Argument | systemd service    |
| -------- | ------------------ |
| `1`      | `valheim.service`  |
| `2`      | `valheim2.service` |
| `3`      | `valheim3.service` |

Additional server numbers follow the same pattern.

## Output

The script only shows relevant player activity:

* Character login (`Got character ZDOID`)
* Periods with at least one active connection (`Connections 1`, `Connections 2`, etc.)
* Socket disconnects (`Closing socket`)

Entries where `Connections 0` are excluded.

## Script

```bash
#!/bin/bash

SERVER="${1:-1}"
SPAN="${2:-7 days ago}"

if [ "$SERVER" = "1" ]; then
    SERVICE="valheim.service"
else
    SERVICE="valheim${SERVER}.service"
fi

sudo journalctl -u "$SERVICE" --since "$SPAN" --no-pager \
    | grep -E "Connections [1-9][0-9]* |Got character ZDOID|Closing socket"
```

## Installation

Save the script as:

```text
/usr/local/bin/vhplayers
```

Make it executable:

```bash
sudo chmod +x /usr/local/bin/vhplayers
```

Then `vhplayers` can be run from anywhere.

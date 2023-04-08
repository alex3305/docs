# .gitignore

These are my personal `.gitignore` fules.

## Keep all yaml files

This rule keeps all `yaml` files and directories that we chose to keep.

```gitignore
# Home Assistant yaml's
!*.yaml
```

## Exclude Home Assistant internals

These files are managed by Home Assistant and/or should be included in backups.

```gitignore
# Home Assistant directory exclusions
!.managed-by-home-assistant
!**/.managed-by-home-assistant

# Exclude Home Assistant internals
blueprints/
image/
.HA_VERSION
.cloud
.shopping_list.json
.storage
ip_bans.yaml
known_devices.yaml
```

## Exclude log files

I don't want any log files in version control.

```gitignore
# Exclude log files
*.log
*.log*
```

## Exclude secrets 

I manage my secrets through Bitwarden. And these are also included in the backups.

```gitignore
# Exclude secrets
.google.token
.google_assistant_service_account.json
esphome/secrets.yaml
google_calendars.yaml
secrets.yaml
```

## HACS

HACS manages itself.

```gitignore
# HACS
!**/.managed-by-hacs
!.managed-by-hacs
custom_components/**
themes/**
www/community/**
```

## UI generated files

These files are generated from UI and should be included in backup.

```gitignore
# Home Assistant UI generated files
automations.yaml
scripts.yaml
scenes.yaml
```

## Editors

Exclude some editor configuration.

```gitignore
.idea/
*.iml
*.swp
.vscode/
```

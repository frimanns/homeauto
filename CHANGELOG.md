# Changelog

## [v1.0.0] - 2025-10-15
### Added
- Initial Docker setup for Home Assistant and Mosquitto MQTT
- MQTT integration confirmed via Home Assistant and MQTT Explorer
- Manual publish test working
- Automation example: MQTT publish on switch toggle
- Backup script for config folders
- README archive with troubleshooting notes

### Fixed
- Typo in mosquitto.conf filename (`msquitto.conf` → `mosquitto.conf`)
- Mosquitto container restart loop

### Next
- Add MQTT sensors and switches
- Automate backups
- Create dashboard for live MQTT traffic
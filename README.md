# Ansible Role: `aisbergg.logrotate`

This Ansible role is used to install and configure logrotate on Debian, Arch Linux and RedHat Linux distributions.

## Requirements

None.

## Role Variables

| Variable |  Default | Comments |
|----------|----------|----------|
| `logrotate_debian_repo` |  | The repository to install logrotate from. Applies only to Debian systems. |
| `logrotate_redhat_enablerepo` |  | Repository to enable for installing logrotate. Applies only to RedHat systems. |
| `logrotate_options` | `[]` | List of global configuration options. (defaults to the distribution options) |
| `logrotate_hourly_rotation_enabled` | `false` | Enable hourly rotation. |
| `logrotate_applications` | `[]` | List of custom log rotation information for applications. |
| `logrotate_applications.[].name`</br>_required_ | `[]` | Name of the application log rotation information (filename in `/etc/logrotate.d/`). |
| `logrotate_applications.[].logs`</br>_required_ | `[]` | The log files, that the options should be applied to. |
| `logrotate_applications.[].options` | `[]` | List of log rotation options. |
| `logrotate_applications.[].prerotate` |  | A `prerotate` script (either list of lines or multi-line string). |
| `logrotate_applications.[].postrotate` |  | A `postrotate` script (either list of lines or multi-line string). |
| `logrotate_applications.[].preremove` |  | A `preremove` script (either list of lines or multi-line string). |
| `logrotate_applications.[].firstaction` |  | A `firstaction` script (either list of lines or multi-line string). |
| `logrotate_applications.[].lastaction` |  | A `lastaction` script (either list of lines or multi-line string). |

## Dependencies

None.

## Example Playbook

```yaml
- hosts: all
  vars:
    logrotate_options:
      - weekly
      - rotate 4
      - create
      - compress
    logrotate_applications:
      - name: httpd
        logs:
          - "/var/log/httpd/*log"
        options:
          - missingok
          - notifempty
          - sharedscripts
          - delaycompress
        prerotate: |-
          # anonymize IPs
          sed -i -e 's/([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})\.([0-9]{1,3})/\1.\2.XXX.XXX' $1 || true
        postrotate: /bin/systemctl reload httpd.service > /dev/null 2>/dev/null || true
  roles:
    - aisbergg.logrotate
```

## License

MIT

## Author Information

Andre Lehmann (aisberg@posteo.de)

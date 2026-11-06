<!-- markdownlint-disable MD041 -->
<!-- https://developers.home-assistant.io/docs/apps/presentation#keeping-a-changelog -->
## 2.3.4-7

- Fix AppArmor profile

## 2.3.4-6

- Updated AppArmor profile to allow cgroup2 & proc reads without host_pid.

## 2.3.4-5

- remove hostpid and add /proc and /sys mounts

## 2.3.4-4

- Update AppArmor profile for periphery
- Extend container permissions

## 2.3.4-3

- Fix periphery image version for amd64

## 2.3.4-2

- Bump hassio-addons/debian-base from 9.5.1 to 9.5.2

## 2.3.4-1

- Use upstream version as base
- Bump to 2.3.4

## 1.0.7

- Add generic /root permissions to AppArmor profile

## 1.0.6

- Add generic /root permissions to AppArmor profile

## 1.0.5

- Add permissions for starship to AppArmor profile

## 1.0.4

- Add shell permissions to AppArmor profile

## 1.0.3

- Add DNS to AppArmor profile

## 1.0.2

- Update AppArmor profile
- Update application config
- Update application description

## 1.0.1

- Fix name in services.d

## 1.0.0

- Initial release

# Ansible role Keycloak with PostgreSQL

This role is designed to deploy Keycloak on a systemd managed system. The role will install Keycloak from an official downloaded Keycloak zip archive on the target system.

The Keycloak server will be configured as a systemd service named `keycloak`, running as a `keycloak` system user. The role will handle the creation of the system user and the service.

This role also handles some initial Keycloak server configuration. This includes configuring what ports to listen on, creating an initial admin user, set welcome theme and configuring the PostgreSQL database.

## Requirements
Before running this role following requirements have to be fulfilled:

* Java JDK (version 8 or higher) for example with [geerlingguy.java](https://github.com/geerlingguy/ansible-role-java)
* Running PostgreSQL with a user and database for Keycloak for example with [geerlingguy.postgresql](https://github.com/geerlingguy/ansible-role-postgresql)
* In most cases also a reverse proxy configured doing the following:
  * Add `X-Forwarded-For` header
  * proxying to the http or https endpoint (without certificate check) of Keycloak

## Role Variables

### General (required)

| Variable Name | Function | default | comment |
| ------------- | -------- | ------- | ------- |
| `keycloak_version` | Version of Keycloak going to be installed | `"12.0.1"` | The role is build for working with the default version. Try other versions with your own risk |
| `keycloak_url` | URL of the Keycloak archive which is downloaded | `"https://downloads.jboss.org/keycloak/{{ keycloak_version }}/keycloak-{{ keycloak_version }}.zip"` | |
| `keycloak_force_install` | Whether Keycloak should be (re-)installed ignoring the check if it is already installed | `false` | |
| `keycloak_create_admin` | Whether a Keycloak admin user should be created | `false` | should be only run one time after first keycloak installation (no matter which version)  |
| `keycloak_admin_user` | Username of the Keycloak admin user which is going to be created | `"admin"` | |
| `keycloak_admin_password` | Password of Keycloak admin user |  | (required when `keycloak_create_admin` is true |

### General (optional)

| Variable Name | Function | default | comment |
| ------------- | -------- | ------- | ------- |
| `keycloak_service_user` | Name of the user of the Keycloak service  | `"keycloak"` |  | 
| `keycloak_service_group` | Name of the main group of the user of the Keycloak service  | `"{{ keycloak_service_user }}"` |  | 
| `keycloak_service_name` | Name of the Keycloak service file  | `"keycloak"` |  | 
| `keycloak_base_path` | Base path of the Keycloak installation | `"/opt/keycloak"` | (will be the home of the user `keycloak_service_user`)  | 
| `keycloak_jboss_home` | Base path of the current version Keycloak installation | `"{{ keycloak_base_path }}/keycloak-{{ keycloak_version }}"` | (is used by check if version is already installed) | 
| `keycloak_config_dir` | Configuration directory of the Keycloak installation | `"{{ keycloak_jboss_home }}/standalone/configuration"` | (only change if you know what you're doing)  |
| `keycloak_startup_timeout` | Time to wait Keycloak to start (given to SystemD service)  | `"300"` | in seconds | 
| `keycloak_java_opts` | JAVA_OPTS used by Keycloak | `"-Xms256m -Xmx1024m"""` | if you run a large instance or experience problems you should have a look on this | 

### Database (required)

| Variable Name | Function | default | comment |
| ------------- | -------- | ------- | ------- |
| `keycloak_postgresql_jdbc_version` | Version of PostgreSQL JDBC driver that should be installed | `"42.2.18"` | (only needed when using the default PostgreSQL JDBC download url) |
| `keycloak_postgresql_jdbc_url` | URL of PostgreSQL JDBC driver that should be downloaded | `"https://jdbc.postgresql.org/download/postgresql-{{ keycloak_postgresql_jdbc_version }}.jar"` | |
| `keycloak_postgresql_host` | Host running the PostgreSQL database | `"localhost"` | |
| `keycloak_postgresql_port` | Port of the PostgreSQL database | `"5432"` | |
| `keycloak_postgresql_database` | Name of the PostgreSQL database | `"keycloak"` | |
| `keycloak_postgresql_username` | User to connect to the PostgreSQL database |  | (required) |
| `keycloak_postgresql_password` | Password of the user to connect to the PostgreSQL database | | (required) |

### Networking (optional)

| Variable Name | Function | default | comment |
| ------------- | -------- | ------- | ------- |
| `keycloak_behind_reverseproxy` | Whether Keycloak is running behind a reverse-proxy | `true` | When this setting is activated, Keycloak uses the `X-Forwarded-For`-header to get the Client IP instead of the network package  |
| `keycloak_bind_address` | Address Keycloak listens on (for http and https) | `"127.0.0.1"` | |
| `keycloak_http_port` | Port of Keycloak HTTP endpoint | `"8080"` | |
| `keycloak_https_port` | Port of Keycloak HTTPS endpoint | `"8443"` | (The HTTPS is using a self-signed certificate) |
| `keycloak_management_http_port` | Port of the HTTP endpoint of Keycloaks management interface | `"9990"` | (only listens on localhost, no matter what `keycloak_bind_address` is set to) |
| `keycloak_management_https_port` | Port of the HTTP endpoint of Keycloaks management interface | `"9993"` | (only listens on localhost, no matter what `keycloak_bind_address` is set to) |

### Customization

| Variable Name | Function | default | comment |
| ------------- | -------- | ------- | ------- |
| `keycloak_profile_preview` | Whether preview features should be enabled by default | `false` | |
| `keycloak_welcome_theme` | Name of the theme for the welcome page | `"keycloak"` | |

## Related and License
This role is a highly modified fork of [nkinder.keycloak](https://github.com/nkinder/ansible-keycloak). Both are licensed under GPLv3



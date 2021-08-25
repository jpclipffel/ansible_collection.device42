# Ansible collection for Device42

This Ansible collection contains the following resources:

| Resource                          | Type            | Description                                  |
|-----------------------------------|-----------------|----------------------------------------------|
| `plugins/httpapi/httpapi.py`      | HTTP API plugin | Authenticate to Device42 and wraps API calls |
| `plugins/modules/device42_api.py` | Module          | Low-level interfaces to Device42 API         |

## Usage

### Device42 inventory configuration

One may add Device42 host(s) into an inventory to easily set the connection
variables.

Example:

```yaml
all:
  children:
    device42:
      vars:
        ansible_network_os: jpclipffel.device42.httpapi
        ansible_connection: httpapi
        ansible_httpapi_port: 443
        ansible_httpapi_use_ssl: yes
        ansible_httpapi_validate_certs: no
        ansible_user: "{{ d42_user | default(lookup('env', 'DEVICE42_USERNAME')) }}"
        ansible_password: "{{ d42_password | default(lookup('env', 'DEVICE42_PASSWORD')) }}"
      hosts:
        "d42.tld":
```

### Module `device42_api`

This module allows one to communicate with Device42 API using common HTTP verbs
as `GET`, `POST`, etc.

The module takes 3 parameters:

| Parameter | Type | Values | Description |
| `meth`    | `string` | HTTP method (`GET`, `POST`, etc.) | HTTP method |
| `path`    | `string` | `api/{{ version }}/object` | Device42 API path |
| `data`    | `object` | A JSON-like object | Device42 API data |

`data` is properly encoded and formatted depending on the method.

Examples:

```yaml
- name: Get all devices
  jpclipffel.device42.device42_api:
    meth: GET
    path: api/2.0/devices
    data:
      limit: 10

- name: Get devices by name
  jpclipffel.device42.device42_api:
    meth: GET
    path: api/2.0/devices
    data:
      name: SomeDeviceName

- name: Create subnet
  jpclipffel.device42.device42_api:
    meth: POST
    path: api/1.0/subnets
    data:
      network: "172.27.253.128"
      mask_bits: "25"
```

### Ansible Tower / AWX credential

In Ansible Tower / AWX, you may create a custom credential type as follow.

Input configuration:

```yaml
fields:
  - id: username
    type: string
    label: Username
  - id: password
    type: string
    label: Password
    secret: true
required:
  - username
  - password

```

Injector configuration:

```yaml
env:
  DEVICE42_PASSWORD: '{{ password }}'
  DEVICE42_USERNAME: '{{ username }}'
```

## Installation

## Development

### Build and publish

* `ansible-galaxy collection build jpclipffel/device42`
* `ansible-galaxy collection publish jpclipffel-device42-*.tar.gz`

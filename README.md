# idam-chart

Strategic IDAM product chart

This chart is intended for deploying the IDAM product stack, including:
 - ForgeRock components
 - IDAM API
 - IDAM frontend for public users
 - database

In addition to the above configuration importer is available as well to setup IDAM instance with oAuth clients, roles and users.

## Example configuration

Below is example configuration for running this chart on a PR to test your application with IDAM.

### IDAM

#### requirements.yaml

```
dependencies:
  - name: idam
    version: '1.0.0'
    repository: '@hmctspublic'
```

#### values.preview.template.yaml

```
idam:
  global:
    ingressIP: ${INGRESS_IP}
    consulIP: ${CONSUL_LB_IP}
    adminUser: ${IDAM_ADMIN_USER}
    adminPassword: ${IDAM_ADMIN_PASSWORD}
```

The INGRESS_IP and CONSUL_LB_IP are all provided by the pipeline, but chart requires you to pass them through for preview environments.

### IDAM Configurer

In addition to the core services you can include some configurer pods to import services, roles and users:

#### values.preview.template.yaml

```
idam:
  # above config for the stack
  configurer:
    enabled: true
    services:
    - label: CCD
      clientID: ccd_gateway
      clientSecret: ccd_gateway_secret
      redirectURL: http://localhost:3451/oauth2redirect
    roles:
    - id: ccd-import
    users:
    - email: ccd.docker.default@hmcts.net
      group: caseworker
      roles:
      - ccd-import
```

## Configuration

The following table lists the configurable parameters of the IDAM chart and their default values.

| Parameter                                     | Description                          | Default                                          |
| --------------------------------------------- | ------------------------------------ | ------------------------------------------------ |
| `global.adminUser`                            | Username of IDAM service owner       | `nil` (required)                                 |
| `global.adminPassword`                        | Password of IDAM service owner       | `nil` (required, provided by the pipeline)       |
| `global.ingressIP`                            | Ingress controllers IP address       | `nil` (required, provided by the pipeline)       |
| `global.consulIP`                             | Consul servers IP address            | `nil` (required, provided by the pipeline)       |
| `configurer.enabled`                          | Enabling IDAM configurer             | `false`                                          |
| `configurer.services.label`                   | Service label                        | `nil` (required if configurer is enabled)        |
| `configurer.services.clientID`                | Service client ID                    | `nil` (required if configurer is enabled)        |
| `configurer.services.clientSecret`            | Service client secret                | `nil` (required if configurer is enabled)        |
| `configurer.services.redirectURL`             | Service redirect URL                 | `nil` (required if configurer is enabled)        |
| `configurer.services.allowedRole`             | Service role restriction             | `[]` (optional if configurer is enabled)         |
| `configurer.services.selfRegistrationAllowed` | Whether self registration is allowed | `false` (optional if configurer is enabled)      |
| `configurer.roles.id`                         | Role ID                              | `nil` (required if configurer is enabled)        |
| `configurer.users.email`                      | User email                           | `nil` (required if configurer is enabled)        |
| `configurer.users.password`                   | User password                        | `Password12` (optional if configurer is enabled) |
| `configurer.users.forename`                   | User forename                        | `Forename` (optional if configurer is enabled)   |
| `configurer.users.surname`                    | User surname                         | `Surname` (optional if configurer is enabled)    |
| `configurer.users.group`                      | User group                           | `nil` (required if configurer is enabled)        |
| `configurer.users.roles`                      | List of user roles                   | `[]` (optional if configurer is enabled)         |

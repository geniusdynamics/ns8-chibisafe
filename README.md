# ns8-chibisafe

chibisafe is a beautiful and performant vault to save all your files in the cloud.

Module id: `chibisafe`. Images are pinned in `build-images.sh` and updated through reviewed changes.

## Install

```bash
add-module ghcr.io/geniusdynamics/chibisafe:latest 1
```

The command returns the instance id, for example `chibisafe1`. Use the id NS8 returns.

## Configure

```bash
api-cli run configure-module --agent module/chibisafe1 --data - <<'EOF'
{
  "host": "chibisafe.example.org",
  "http2https": true,
  "lets_encrypt": false
}
EOF
```

| Parameter | Required | Type | Description |
|---|---|---|---|
| `host` | yes | string | Host name for the application, like 'chibisafe.domain.org' |
| `lets_encrypt` | yes | boolean | Request a valid Let's Encrypt certificate. |
| `http2https` | yes | boolean | Redirect all the HTTP requests to HTTPS |

## Configuration output

`get-configuration` returns: `host`, `lets_encrypt`, `http2https`.

## Images

| Image | Version |
|---|---|
| `docker.io/chibisafe/chibisafe` | `v6.7.0` |
| `docker.io/chibisafe/chibisafe-server` | `v6.7.0` |

## Network and access

- Reserves 1 TCP port(s) on the node. The web application is published through Traefik on the configured host name.
- Runs rootless.
- Declares these NS8 authorizations: `traefik@node:routeadm`.

## Backup

The following paths are included in the NS8 backup (`imageroot/etc/state-include.conf`):

- `state/chibi.env`
- `volumes/database`
- `volumes/uploads`
- `volumes/logs`

## Services

- `caddy-app.service`
- `chibisafe-app.service`
- `chibisafe-server.service`
- `chibisafe.service`

## Get the configuration

```bash
api-cli run get-configuration --agent module/chibisafe1
```

## Update and uninstall

```bash
api-cli run update-module --data '{"module_url":"ghcr.io/geniusdynamics/chibisafe:latest","instances":["chibisafe1"],"force":true}'
remove-module --no-preserve chibisafe1
```

## Testing

Continuous integration:

- `Publish images` builds the module image on every push.
- `Test module` installs the image on a QEMU NS8 node and runs the Robot Framework suite in `tests/`. It runs after `Publish images` succeeds on `dev` and on `main`.
- `Build API JSON Schema docs` and the security scan run alongside.

The suite installs the module, configures a host name, checks that the web application answers through Traefik, takes UI screenshots and removes the module.

Run it locally with the NS8 standard infrastructure described in the [ns8-github-actions README](https://github.com/NethServer/ns8-github-actions/blob/v1/README.md#running-tests-locally).

## Open items

Found by the NS8 module standards check. These must be resolved before a stable release:

- metadata: categories empty (use standard categories)
- CADDY_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- CHIBISAFE_IMAGE is used by the module but no image in the label provides it: leftover from the template?
- CHIBISAFE_SERVER_IMAGE is used by the module but no image in the label provides it: leftover from the template?

## Upstream

Application documentation: https://chibisafe.moe/

## License

GPL-3.0-or-later. See [LICENSE](LICENSE).

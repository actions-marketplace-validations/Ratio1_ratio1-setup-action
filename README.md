# ratio1-setup-action

Reusable GitHub Action to set up a Ratio1 SDK runtime in CI.

What it does:

- installs Python and `ratio1` (latest or pinned with `sdk_version`)
- always runs `r1ctl config reset`
- sets network (`mainnet`, `testnet`, `devnet`; default `testnet`)
- optionally sets alias
- optionally writes PEM key to `~/.ratio1/_naeural.pem`
- optionally executes a command after setup

This action is designed to be published and reused as:

```yaml
uses: Ratio1/ratio1-setup-action@v1
```

## Inputs

| Name                 | Required | Default   | Description                                                           |
| -------------------- | -------- | --------- | --------------------------------------------------------------------- |
| `python_version`     | no       | `3.11`    | Python version used for setup.                                        |
| `sdk_version`        | no       | empty     | Version for `ratio1` (`ratio1==<version>`). If empty, install latest. |
| `extra_pip_packages` | no       | empty     | Extra pip packages, space-separated.                                  |
| `network`            | no       | `testnet` | Allowed values: `mainnet`, `testnet`, `devnet`.                       |
| `alias`              | no       | empty     | Alias set with `r1ctl config alias --set`.                            |
| `private_key_pem`    | no       | empty     | Raw multiline PEM content. If empty, a random generated key is used   |
| `command`            | no       | empty     | Optional shell command executed after setup.                          |

All inputs are optional.

## Recommended Usage (Setup + Run)

```yaml
name: Ratio1 job

on:
    workflow_dispatch:

jobs:
    run:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v4

            - uses: Ratio1/ratio1-setup-action@v1
              with:
                  sdk_version: "3.5.13"
                  network: testnet
                  alias: test-sdk
                  command: python3 example_script.py
              env:
                  RATIO1_NODE: ${{ secrets.RATIO1_NODE }}
```

## With Private Key

```yaml
- uses: Ratio1/ratio1-setup-action@v1
  with:
      network: mainnet
      private_key_pem: ${{ secrets.RATIO1_PRIVATE_KEY_PEM }}
```

If you do not set `private_key_pem`, the SDK will use a randomly generated key.
That key might not be authorized for some actions if it is not whitelisted by target nodes.

## Security Notes

- Pass sensitive data only through GitHub Actions secrets.
- Do not commit PEM files in repositories.

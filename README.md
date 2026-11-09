# amivuln-action

GitHub Action that runs [amivuln](https://github.com/nubian-ibex/amivuln) on a Linux runner. It answers which CVEs apply to the packages on the runner, taking distro backports into account.

Before anything is executed the action downloads the release archive, verifies its build provenance attestation with `gh attestation verify` (pinned to the signer workflow `nubian-ibex/amivuln/.github/workflows/release.yml`), checks the SHA-256 and only then unpacks the binary. If any check fails, the job fails and amivuln does not run.

## Usage

```yaml
permissions:
  contents: read
  attestations: read

steps:
  - uses: nubian-ibex/amivuln-action@<full-commit-sha> # v0.1.0
    with:
      args: --all --fail-on high
```

Pin the action to a full commit SHA. The `version` input selects the amivuln release; the default is the one this action release was tested with.

## Inputs

- `version`: amivuln release without the leading `v`. Default: the release this action was tested with.
- `args`: arguments for amivuln, split on whitespace. Default: `--all --fail-on high`.
- `github-token`: token used to download the release and verify the attestation. Default: `github.token`.

## Outputs

- `exit-code`: the amivuln exit code. The step fails with the same code: 1 policy violation, 2 incomplete result, 3 unresolved target, 64 usage error, 70 internal error.

## Notes

- Linux x86_64 and aarch64 runners only (static musl build).
- The inputs reach the shell through environment variables, never through string interpolation. `version` is restricted to `[0-9A-Za-z.+-]`.

## License

MIT OR Apache-2.0.

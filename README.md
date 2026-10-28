# AgentArmor pull request gate

Holds pull requests that add AI tools your company has not approved.

You keep a list of approved AI tools (MCP servers and skills) in AgentArmor. This action checks each pull request against that list and fails the check when the pull request adds something blocked, not on the list, or off its pin.

## Use it

1. In your AgentArmor dashboard, create a CI token. It starts with `aaci_`.
2. Save it as a repository secret named `AGENTARMOR_ORG_TOKEN`.
3. Add `.github/workflows/agentarmor.yml`:

```yaml
name: AgentArmor
on: [pull_request]
permissions:
  contents: read
jobs:
  gate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@<full commit SHA>
      - uses: agentarmorhq/action@<full commit SHA>
        with:
          org-token: ${{ secrets.AGENTARMOR_ORG_TOKEN }}
```

Pin both `uses:` lines to a full commit SHA, not a tag. A tag can be moved.

No other key is needed.

## Inputs

| Input | Default | What it does |
| --- | --- | --- |
| `org-token` | none, required | Your CI token, from a repository secret. |
| `mode` | `strict` | `strict` also holds anything not on your list. `soft` holds only what you block and warns about the rest. |
| `cli-version` | set by each release | The tool version to run. |
| `cli-sha256` | set by each release | The SHA-256 of that version. |

## What it does

It downloads the tool from `agentarmorhq.com` at the version your release names. It checks the SHA-256. If the hash does not match, or the download fails, the job stops and nothing runs. Then it runs the pull request check and passes your token in an environment variable only.

Full setup, results and the repository file: https://agentarmorhq.com/docs/teams/pr-gate

## Licence

MIT. See `LICENSE`.

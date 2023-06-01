# GitHub Action: nmap

This action runs `nmap` to scan a URL with the given parameters. Recommended use is with a @vX tag to ensure a stable version is used instead of @master. Versions can be found here [tags](https://github.com/thereisnotime/Action-nmap/tags) and nmap documentation for all parameters [here](https://nmap.org/docs.html).

## Examples

### Start a scan

```yaml
- name: Scan with nmap
  uses: thereisnotime/action-nmap@master
  with:
    target: "blackfox-security.com"
```

### Weekly scheduled scans

```yaml
on:
  schedule:
    - cron: 0 11 * * 1 # Monday at 11 UTC

jobs:
  scan_nmap:
    runs-on: ubuntu-latest
    name: SCAN | nmap
    steps:
      - name: Scan with nmap
        uses: thereisnotime/action-nmap@master
        with:
          target: "blackfox-security.com"
          additional_args: "-oA ./reports/net/report-nmap -p- -sS -sV --script vulners --script-args mincvss=5.0 -A -O -T4 -n -Pn -v"
```

### Use latest version in a job

```yaml
scan_nmap:
  runs-on: ubuntu-latest
  name: SCAN | nmap
  steps:
    - name: Scan with nmap
      uses: thereisnotime/action-nmap@master
      with:
        target: "blackfox-security.com"
        additional_args: "-oA ./reports/net/report-nmap -p- -sS -sV --script vulners --script-args mincvss=5.0 -A -O -T4 -n -Pn -v"
```

### Use latest version in a job and upload the results back to the repository

```yaml
scan_nmap:
  runs-on: ubuntu-latest
  name: SCAN | nmap
  steps:
    - name: Prepare environment
      run: |
        echo "START_DATE=$(date +'%d-%m-%YT%H-%M-%S-%Z')" >> $GITHUB_ENV
        echo "START_TIMESTAMP=$(date +'%s')" >> $GITHUB_ENV
        TMP_TARGET="blackfox-security.com"
        echo "TARGET=$TMP_TARGET" >> $GITHUB_ENV
    - name: Checkout
      uses: actions/checkout@v3
    - name: Scan with nmap
      uses: thereisnotime/action-nmap@master
      with:
        target: ${{ env.TARGET }}
        additional_args: "-oA ./reports/net/report-nmap -p- -sS -sV --script vulners --script-args mincvss=5.0 -A -O -T4 -n -Pn -v"
    - name: Commit and push changes
      uses: EndBug/add-and-commit@v9
      with:
        author_name: GitHub Actions
        author_email: 41898282+github-actions[bot]@users.noreply.github.com
        message: "👷 chore(nmap): add report for ${{ env.TARGET }}-${{ env.START_DATE }}-${{ env.START_TIMESTAMP }}"
        add: "./reports/net/*"
        pull: "--rebase --autostash"
```

## Inputs

### `target`

**Required**. Target URL (e.g. <blackfox-security.com>)

### `additional_args`

Additional arguments to pass to nmap. Default is `-oA report` to generate reports.

## Outputs

### `result`

JSON scan results.

### `resultb64`

JSON scan results, base64 encoded.

## Roadmap

If there is enough time or interest, I will add the following features (feel free to open PRs):

- [ ] Finish support for outputs (e.g. `result` and `resultb64`).
- [ ] Add support for notification via Slack.
- [ ] Add support for notification via Telegram.
- [ ] Add support for notification via email.
- [ ] Add support for notification via custom webhooks.
- [ ] Improve argument handling.

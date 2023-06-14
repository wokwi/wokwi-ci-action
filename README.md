# wokwi-ci-action

GitHub action for using Wokwi Embedded Systems Simulator in your CI workflow.

## Usage

Add the following step to your workflow:

```yaml
- name: Test with Wokwi
  uses: wokwi/wokwi-ci-action@v1
  with:
    token: ${{ secrets.WOKWI_CLI_TOKEN }}
    path: / # directory with wokwi.toml, relative to repo's root
    timeout: 10000
    expect_text: 'Hello, world!'
    fail_text: 'Error'
    scenario: 'test-hello-world.yaml' # optional
```

You need to set the `WOKWI_CLI_TOKEN` secret in your repository settings. You can get the token from the [Wokwi CI Dashboard](https://wokwi.com/dashboard/ci).

Your repository needs to contain a `wokwi.toml` file that describes the simulation to run, and a `diagram.json` file that describes the components in the simulation and their connections. For more information, see the [project config documentation](https://docs.wokwi.com/vscode/project-config).

## Inputs

| Name          | Description                                                | Default |
| ------------- | ---------------------------------------------------------- | ------- |
| `token`       | Wokwi CLI token                                            |         |
| `path`        | Path to the project directory (where wokwi.toml is)        | /       |
| `timeout`     | Timeout in milliseconds                                    | 10000   |
| `expect_text` | Text to expect in the serial output                        |         |
| `fail_text`   | Text that indicates a test failure                         |         |
| `scenario`    | Path to scenario yaml file (relative to project directory) |         |

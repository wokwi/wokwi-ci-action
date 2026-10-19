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
    serial_log_file: 'log-hello-world.txt' # optional
```

You need to set the `WOKWI_CLI_TOKEN` secret in your repository settings. You can get the token from the [Wokwi CI Dashboard](https://wokwi.com/dashboard/ci).

Your repository needs to contain a `wokwi.toml` file that describes the simulation to run, and a `diagram.json` file that describes the components in the simulation and their connections. For more information, see the [project config documentation](https://docs.wokwi.com/vscode/project-config).

## Inputs

| Name              | Description                                                                      | Default       |
|-------------------|----------------------------------------------------------------------------------|---------------|
| `token`           | Wokwi CLI token                                                                  |               |
| `path`            | Path to the project directory (where wokwi.toml is)                              | /             |
| `timeout`         | Timeout in milliseconds                                                          | 10000         |
| `expect_text`     | Text to expect in the serial output                                              |               |
| `fail_text`       | Text that indicates a test failure                                               |               |
| `scenario`        | Path to scenario yaml file (relative to project directory)                       |               |
| `serial_log_file` | Serial log file to be written (relative to the repository root)                  |               |
| `diagram_file`    | Path to diagram.json file (relative to project directory)                        | diagram.json  |
| `elf`             | Path to the ELF file to load into the simulation (relative to project directory) | wokwi.toml \* |
| `heap_interval`   | Sample the ESP-IDF heap every this many milliseconds of simulated time           | 1000          |
| `heap_min_free`   | Fail the test when the free ESP-IDF heap drops below this many bytes             |               |
| `heap_csv`        | Write every heap sample to this CSV file (relative to the repository root)       |               |
| `args`            | Additional [wokwi-cli options](https://docs.wokwi.com/wokwi-ci/cli-usage), separated by spaces |               |

\* If not specified, the action will look for the `firmware` or `elf` keys in the `wokwi.toml` file.

## Heap monitoring

For ESP-IDF firmware, the action can follow the free heap during the simulation and fail the test when it drops below a limit, which catches memory leaks without any logging code in the firmware. Setting any of the `heap_*` inputs turns heap monitoring on. The ELF file must be set (`elf` in `wokwi.toml`, or the `elf` input):

```yaml
- name: Leak test with Wokwi
  uses: wokwi/wokwi-ci-action@v1
  with:
    token: ${{ secrets.WOKWI_CLI_TOKEN }}
    scenario: 'scenarios/leak.test.yaml'
    timeout: 60000
    heap_interval: 500
    heap_min_free: 350000
    heap_csv: 'heap.csv'

- name: Upload heap samples
  if: always()
  uses: actions/upload-artifact@v4
  with:
    name: heap
    path: heap.csv
```

See [Heap Monitoring](https://docs.wokwi.com/wokwi-ci/heap-monitoring) for details.

## Other CLI options

Use `args` to pass [wokwi-cli options](https://docs.wokwi.com/wokwi-ci/cli-usage) that have no input of their own. The value is split on spaces, so the options' values cannot contain spaces:

```yaml
- uses: wokwi/wokwi-ci-action@v1
  with:
    token: ${{ secrets.WOKWI_CLI_TOKEN }}
    args: --vcd-file signals.vcd --timeout-exit-code 1
```

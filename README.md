# Quill Preview Action

This action uploads your built mobile app to our platform at https://app.getquill.dev, and gives you a convenient link
to view your mobile app changes as soon as they are commited.

# What's new

- Provide only your API Key from the Quill workspace, as well as the path to your app and the app name

# Usage

### Inputs

See [action.yml](action.yml)

| Name       | Description                                                              | Required |
| ---------- | ------------------------------------------------------------------------ | -------- |
| `app-path` | The path to the app you'd like to add to your Quill Workspace            | Yes      |
| `pkg-name` | The name of your app package e.g. `com.example.myapp`                    | Yes      |
| `api-key`  | API key for your Quill workspace which you can find on the settings page | Yes      |

### Outputs

See [action.yml](action.yml)
| Name | Description |
| ----------------- | ------------------------------------------------ |
| `mobile_app_link` | A link to an emulator that will run the built mobile app |

### Sample workflow for a Flutter app

1. Create a workflow file `quill.yml` in your repo's `.github/workflows` directory.
2. Here's a basic set up that builds an android apk for a flutter app gives you a preview link to view your app on our
   emulator on the web.

```yaml
# .github/workflows/quill.yml

# This name will appear under your 'Actions' tab on GitHub
name: Build and view android app on Quill

# Controls when the workflow will run. It is recommended to run it when a pull request is
# opened and when the pull request code is updated (synchronized)
on:
  pull_request:
    types: [opened, synchronize]
permissions:
  pull-requests: write # Needed to add comments to the Pull Request that triggered the build
  actions: write # Needed to cancel a workflow when it no longer needs to run
  contents: read # Needed so that GitHub can pull the code and build the app

# A workflow run is made up of one or more jobs that can run sequentially or in parallel.
jobs:
  # This workflow contains one job called "build-and-get-emulator-link"
  build-and-get-emulator-link:
    name: Build apk and get emulator link
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-java@v1
        with:
          java-version: "11.0"
      - uses: subosito/flutter-action@v1
        with:
          flutter-version: "3.19.2"

      # Fetch Dependencies
      - run: flutter pub get

      # Builf Android App
      - run: flutter build apk --release

      # Create a preview on Quill
      - uses: quilldevtools/preview@main
        with:
          app-path: "build/app/outputs/flutter-apk/app-release.apk"
          pkg-name: "com.example" # REPLACE THIS WITH THE NAME OF YOUR APP PACKAGE
          api-key: ${{secrets.QUILL_API_KEY}}
```

In order to **add the app to your workspace on Quill**, you will need the **Quill API key** which you can find on the settings page of your Quill workspace. Follow these [steps to create secrets for a repository](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions?tool=webui#creating-secrets-for-a-repository).

## Supported

### Runners

- `ubuntu-*`
- `self-hosted` runners running on Linux OS

`windows-*` and `macos-*` runners are not supported since some the dependencies use docker container actions.
These actions [can only execute on runners with a Linux operating system](https://docs.github.com/en/actions/creating-actions/about-custom-actions#docker-container-actions)

### Events

- Any event can be used to trigger the action but we recommend using the `pull_request` event` as such:

```yaml
# Runs when a pull request is opened and when the pull request code is updated (synchronized)
on:
  pull_request:
    types: [opened, synchronize]
```

## Dependencies

- Bash shell
- [mshick/add-pr-comment](https://github.com/mshick/add-pr-comment)

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE).

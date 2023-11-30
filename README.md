# Quill Flutter Action

This action builds your flutter mobile application and installs it on a web based emulator allowing you to view your
changes as soon as they are commited.

See also [quill-android-gradle-action](https://github.com/quilldevtools/quill-android-gradle-action).

# What's new

- Support for apk uploads to Google Drive storage
- Customizable flutter commands `test`, `analyze` and `build`

# Usage

To use the action, create a workflow file `quill.yml` in your repo's `.github/workflows` directory.
Here's a basic set up that builds the android apk and uploads it to Google Drive.

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

# A workflow run is made up of one or more jobs that can run sequentially or in parallel.
jobs:
  # This workflow contains one job called "build-and-get-emulator-link"
  build-and-get-emulator-link:
    name: Build apk and get emulator link
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    steps:
      - uses: quilldevtools/quill-flutter-action@v1
        with:
          gdrive-sa-key: ${{ secrets.GDRIVE_SA_KEY }}
```

In order to **upload the app to Google Drive**, you will need the **Google Service Account (GSA) key** that the Quill
action uses. This will be provided to you. Follow these [steps to create secrets for a repository](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions?tool=webui#creating-secrets-for-a-repository).

### Customize flutter commands

To customize flutter commands, use the respective inputs without the `flutter` keyword

```yaml
steps:
    - uses: quilldevtools/quill-flutter-action@main
    with:
        test-command: "test"
        analyze-command: "analyze"
        build-command: "build apk --release"
        gdrive-sa-key: ${{ secrets.GDRIVE_SA_KEY }}
```

### Inputs

See [action.yml](action.yml)

| Name              | Description                                                                                           | Required                                 |
| ----------------- | ----------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| `build-command`   | Flutter build command to run instead of the default `flutter build apk --release`                     | No                                       |
| `test-command`    | Flutter test command to run instead of the default `flutter test`. Set to empty string to skip        | No                                       |
| `analyze-command` | Flutter analyze command to run instead of the default `flutter analyze`. Set to empty string to skip  | No                                       |
| `storage-type`    | Which storage type to use - 'aws-s3', 'google-drive' or 'github-artifact'. Default is 'google-drive'. | No                                       |
| `gdrive-sa-key`   | The Google Drive Service Account Key needed to upload the mobile build file to Google Drive           | Yes, if `storage-type` is `google-drive` |

### Outputs

See [action.yml](action.yml)
| Name | Description |
| ----------------- | ------------------------------------------------ |
| `emulator-link` | A link to an emulator that will run the built mobile app |

### Retention Period

If using GitHub Artifacts, note that these are retained for a maximum of 90 days. The other storage options do not have
a retention limit and the APK files are kept perpetually unless deleted. For more information see
[artifact and log retention policies](https://docs.github.com/en/free-pro-team@latest/actions/reference/usage-limits-billing-and-administration#artifact-and-log-retention-policy).

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
- [actions/checkout](https://github.com/actions/checkout)
- [actions/setup-java](https://github.com/actions/setup-java)
- [subosito/flutter-action](https://github.com/subosito/flutter-action)
- [wcyn/google-drive-path-upload-action](https://github.com/wcyn/google-drive-path-upload-action)
- [actions/cache](https://github.com/actions/cache)

# License

The scripts and documentation in this project are released under the [MIT License](LICENSE).

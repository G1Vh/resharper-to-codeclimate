# resharper-to-codeclimate

converts an xml file containing [resharper inspectcode results](https://www.jetbrains.com/help/resharper/InspectCode.html#understanding-output) to
a json file that follows the [code climate spec](https://github.com/codeclimate/platform/blob/master/spec/analyzers/SPEC.md#data-types).

note, currently only converts to the subset of the code climate spec [required for gitlab-ci](https://docs.gitlab.com/ee/user/project/merge_requests/code_quality.html#implementing-a-custom-tool).

## usage

```powershell
dotnet tool install -g dotnet-resharper-to-codeclimate
dotnet resharper-to-codeclimate results.xml results.json
```

## example .gitlab-ci.yml

```yaml
variables:
  NUGET_PACKAGES: '$CI_PROJECT_DIR\.nuget\packages'

codequality:
  stage: codequality
  script:
    - dotnet tools restore
    - dotnet jb inspectcode AEExport.sln -o=results.xml
    - dotnet resharper-to-codeclimate results.xml results.json
  cache:
    key:
      files: 
        - .config/dotnet-tools.json
    paths:
      - .nuget/
  artifacts:
    when: always
    reports:
      codequality:
        - results.json
```
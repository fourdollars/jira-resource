*WIP*

 [![GitHub: fourdollars/jira-resource](https://img.shields.io/badge/GitHub-fourdollars%2Fjira%E2%80%90resource-darkgreen.svg)](https://github.com/fourdollars/jira-resource/) [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT) [![Bash](https://img.shields.io/badge/Language-Bash-red.svg)](https://www.gnu.org/software/bash/) ![Docker](https://github.com/fourdollars/jira-resource/workflows/Docker/badge.svg) [![Docker Pulls](https://img.shields.io/docker/pulls/fourdollars/jira-resource.svg)](https://hub.docker.com/r/fourdollars/jira-resource/)
# jira-resource
[Concourse CI](https://concourse-ci.org/)'s Jira resource to interact with [Jira REST APIs](https://developer.atlassian.com/server/jira/platform/rest-apis/).

## Config

### Resource Type

```yaml
resource_types:
- name: jira
  type: registry-image
  source:
    repository: fourdollars/jira-resource
    tag: latest
```

or

```yaml
resource_types:
- name: jira
  type: registry-image
  source:
    repository: ghcr.io/fourdollars/jira-resource
    tag: latest
```

### Resource

* url: **Required**
* user: **Required**
* token: **Required**
* resource: **Required**

```yaml
resources:
- name: jira-json
  icon: arrows_more_up
  type: jira
  check_every: 5m
  source:
    url: https://jira.atlassian.com/rest/api/latest/
    user: username
    token: wxdnqsclxzrmhb2k27frgjc7hdp3zqk0b4
    resource: issue/JRA-9
```

### get step

```yaml
  - get: jira-json
```
```shell
# It acts like the following commands.
$ cd /tmp/build/get
$ curl -fsSL --user "username:wxdnqsclxzrmhb2k27frgjc7hdp3zqk0b4" https://jira.atlassian.com/rest/api/latest/issue/JRA-9 > payload.json
```

### Job Example

```yaml
jobs:
- name: check-jira-issue
  plan:
  - get: jira-json
  - task: check
    config:
      platform: linux
      image_resource:
        type: registry-image
        source:
          repository: alpine
          tag: latest
      inputs:
        - name: jira-json
      run:
        path: sh
        args:
        - -exc
        - |
          apk add jq
          jq -r .< jira-json/payload.json
```

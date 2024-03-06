# test-project

Test project to test renovate with CI_SERVER_HOST

This project include a ci/cd component with the $CI_SERVER_HOST syntax

```yaml
include:
  # test component
   - component: $CI_SERVER_HOST/ad_mcas/test-pipeline/testme@1.0.0
```

This syntax is correct for gitlab

* `CI_SERVER_HOST` is a [predefined variable](https://docs.gitlab.com/ee/ci/variables/predefined_variables.html) for indicate the host of the GitLab instance URL, without protocol or port. For example gitlab.example.com.

## Current Behavior

When we launch renovate into this project, it fail to update the component with this error:

```
DEBUG: Found 1 package file(s) (repository=ad_mcas/test-project)
 INFO: Dependency extraction complete (repository=ad_mcas/test-project, baseBranch=main)
       "stats": {
         "managers": {"gitlabci": {"fileCount": 1, "depCount": 1}},
         "total": {"fileCount": 1, "depCount": 1}
       }
DEBUG: hostRules: no authentication for $ci_server_host (repository=ad_mcas/test-project)
DEBUG: Using throttle 200 intervalMs for host $ci_server_host (repository=ad_mcas/test-project)
DEBUG: Using queue: host=$ci_server_host, concurrency=5 (repository=ad_mcas/test-project)
DEBUG: GET https://$CI_SERVER_HOST/api/v4/projects/ad_mcas%2Ftest-pipeline/repository/tags?per_page=100 = (code=ENOTFOUND, statusCode=-1 retryCount=2, duration=0) (repository=ad_mcas/test-project)
DEBUG: Gitlab API error (repository=ad_mcas/test-project)
       "err": {
         "name": "RequestError",
         "code": "ENOTFOUND",
         "timings": {
           "start": 1709748923848,
           "socket": 1709748923848,
           "lookup": 1709748923848,
           "error": 1709748923848,
           "phases": {"wait": 0, "dns": 0, "total": 0}
         },
         "message": "getaddrinfo ENOTFOUND $ci_server_host",
         "stack": "RequestError: getaddrinfo ENOTFOUND $ci_server_host\n    at ClientRequest.<anonymous> (/usr/local/renovate/node_modules/.pnpm/got@11.8.6/node_modules/got/dist/source/core/index.js:970:111)\n    at Object.onceWrapper (node:events:632:26)\n    at ClientRequest.emit (node:events:529:35)\n    at ClientRequest.emit (node:domain:489:12)\n    at ClientRequest.origin.emit (/usr/local/renovate/node_modules/.pnpm/@szmarczak+http-timer@4.0.6/node_modules/@szmarczak/http-timer/dist/source/index.js:43:20)\n    at TLSSocket.socketErrorListener (node:_http_client:501:9)\n    at TLSSocket.emit (node:events:517:28)\n    at TLSSocket.emit (node:domain:489:12)\n    at emitErrorNT (node:internal/streams/destroy:151:8)\n    at emitErrorCloseNT (node:internal/streams/destroy:116:3)\n    at processTicksAndRejections (node:internal/process/task_queues:82:21)\n    at GetAddrInfoReqWrap.onlookup [as oncomplete] (node:dns:107:26)",
         "options": {
           "headers": {
             "user-agent": "RenovateBot/37.229.3 (https://github.com/renovatebot/renovate)",
             "accept": "application/json",
             "accept-encoding": "gzip, deflate, br"
           },
           "url": "https://$ci_server_host/api/v4/projects/ad_mcas%2Ftest-pipeline/repository/tags?per_page=100",
           "hostType": "gitlab-tags",
           "username": "",
           "password": "",
           "method": "GET",
           "http2": false
         }
       }
DEBUG: Datasource connection error (repository=ad_mcas/test-project)
       "datasource": "gitlab-tags",
       "packageName": "ad_mcas/test-pipeline",
       "url": undefined,
       "errCode": "ENOTFOUND"
DEBUG: Failed to look up gitlab-tags package ad_mcas/test-pipeline (repository=ad_mcas/test-project, packageFile=.gitlab-ci.yml, dependency=ad_mcas/test-pipeline)
```

### Excepted behavior

Renovate should know that $CI_SERVER_HOST is the "gitlab site" of the current_project (or use $CI_SERVER_HOST env variable) to get release tags of the component and will propose to update the component to 

```yaml
include:
  # test component
   - component: $CI_SERVER_HOST/ad_mcas/test-pipeline/testme@1.0.1
```
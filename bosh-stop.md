# BOSH Stop Results

## Setup
```
bosh deploy ../cf-deployment/cf-deployment.yml /
-o ../cf-deployment/operations/scale-to-one-az.yml /
-o ../cf-deployment/operations/experimental/fast-deploy-with-downtime-and-danger.yml \
-o ../cf-deployment/operations/use-compiled-releases.yml \
-v system_domain=hagrid.cf-app.com \
-vars-store vars-store/deployment-vars.yml
```

### bosh stop database
```
st+nb $ cf push node
Error Code: 500
Raw Response: {}
FAILED
```
```
st+nb $ cf apps
Getting apps in org system / space dev as admin...
FAILED
Server error, status code: 500, error code: , message:
```

### bosh stop api
```
st+nb $ cf target
API endpoint not found at 'https://api.hagrid.cf-app.com'
FAILED
```
```
st+nb $ cf push node
API endpoint not found at 'https://api.hagrid.cf-app.com'
FAILED
```
### bosh stop adapter
This only affects syslog-drain, and I'm going to put off fussing with drains for now.

## bosh stop cc-worker
Stops async jobs but I'm not sure how to demonstrate that yet

### bosh stop consul
#### Failing jobs
api
diego-api
scheduler

#### Errors
`cf push` panics -- filed [issue 1351](https://github.com/cloudfoundry/cli/issues/1351) with the cli team

Weirdly, `cf apps` works even though the api instance is failing, 
because all that's actually failing is the `routing-api` job

### bosh stop diego-api
`cf push`

Bits upload succeed but then we get to staging
```
Response code: 503
CC code:       0
CC error code:
Request ID:    1922e32d-044d-4d5a-6d65-d8c903ebf0ea::ee08398b-915b-43c0-83a8-e88d6d617ac4
Description:   {
  "description": "Stager is unavailable: getaddrinfo: Name or service not known (bbs.service.cf.internal:8889)",
  "error_code": "CF-StagerUnavailable",
  "code": 170010
}
```
CC also does not replay the stage request when the diego-api comes back online, so the app doesn't start at that point

`cf delete`
Processes the request and reports success, but the app doesn't go away
It does get cleaned up once the bbs comes back online

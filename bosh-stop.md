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


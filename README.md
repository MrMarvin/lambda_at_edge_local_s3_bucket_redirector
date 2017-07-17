These AWS Lambda functions utilize AWS Lambda@Edge, which allows user defined functions to run in reaction to CloudFront requests and responses. See the offical AWS documentation to learn more. Currently the RC1 GA environment is targeted and future changes might be necessary.

# viewer_request_universe_versioned_json.js
This function implements our server-side logic to redirect DC/OS clients to their specific version of the universe `repo.json`. Based on https://github.com/mesosphere/prod-universe/blob/master/nginx/etc/nginx/conf.d/universe.mesosphere.com.conf

It is important to deploy it to both:

* `/repo*`
* `/v?/schema/repo`

Also note that this will shadow (a.k.a. block) all requests to potential files with this prefix in your origin.

# viewer_request_local_s3_bucket_redirector.js
This function should be running for all distributed files that are somewhat large and should rather be served directly from S3 from within the same AWS region, instead of from CloudFront.
This is done via inspecting the clients source IP address and matching that against the well known list ob AWS networks.
Currently we deploy it for the following Path Pattern:

* `*.tar.*z*` (this applies to most tar+compressed files, .tar.gz, .tar.bz2, .tar.xz, ...)
* `*.zip`

## updating the ip-range.json
```
wget https://ip-ranges.amazonaws.com/ip-ranges.json
```

# Running tests
There is a single spec file per function, which can be used with `mocha` and a npm script to call run it:
```
npm install & npm test
```

# Building a release .zip
```
npm start
```
or see what this shorthand script does in package.json.

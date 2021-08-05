# docker-aideck

Docker image for building the aideck gap8 hosted on [docker hub](https://hub.docker.com/r/bitcraze/aideck)

The version tags are based on the versions for the [gap_sdk of greenwaves technologies](https://github.com/GreenWaves-Technologies/gap_sdk)

Versions tag available: 3.8.1/ 3.9.1
Latest stable version: 3.8.1

Pull the latest version with
```
docker pull bitcraze/aideck
```

or a specific version with
```
docker pull bitcraze/aideck:3.9.1
```

Note that this docker file is **without the autotiler** so please follow [these instructions](https://www.bitcraze.io/documentation/repository/AIdeck_examples/master/getting-started/docker-gap8/) in order to install it on your docker container.
## Older versions:

If you want to build a docker file for an older version of the gap_sdk, take the dockerfile of src/3.9.1/ change the version in the following line:

```
  ARG GAP_SDK_VERSION=3.8.1
```


to either 3.4, 3.5, 3.6 or 3.7

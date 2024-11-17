```shell
./mvnw clean package

# Build with Pack
pack build com.example/demo:latest \
    --builder paketobuildpacks/builder-jammy-tiny \
    --env 'BP_LOG_LEVEL=debug' \
    --env 'BP_JAVA_INSTALL_NODE=true' \
    --env 'BP_JVM_VERSION=17' \
    --env 'NODE_ENV=development' \
    --platform linux/amd64 | tee good_buildpack.log

# Build with Lifecycle
docker run -it --rm \
    --env CNB_PLATFORM_API=0.13 \
    --volume $PWD:/workspace \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    --platform linux/amd64 \
    --user root \
    paketobuildpacks/builder-jammy-tiny \
    bash -c '(
        set -e
        echo "true" > /platform/env/BP_JAVA_INSTALL_NODE
        echo "17" > /platform/env/BP_JVM_VERSION
        echo "DEBUG" > /platform/env/BP_LOG_LEVEL
        echo "development" > /platform/env/NODE_ENV
        /cnb/lifecycle/creator -daemon com.example/demo:latest
        )' | tee bad_buildpack.log
```
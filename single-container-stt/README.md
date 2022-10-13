# Run a Single-Container Speech-to-Text Service on Docker
This recipe shows how you can build a container image with a set of pretrained models included and run the Speech-to-Text (STT) service on Docker, by following a few simple steps.

## Prerequisites
- Ensure you have your entitlement key to access the IBM Entitled Registry
- Install [Docker](https://docs.docker.com/get-docker/)

**Tip**:
- [Podman](https://podman.io/getting-started/installation) provides a Docker-compatible command line front end. Unless otherwise noted, all the the Docker commands in this tutorial should work for Podman, if you simply alias the Docker CLI with `alias docker=podman` shell command.


## Step 1: Login to the IBM Entitled Registry
IBM Entitled Registry contains various container images for Watson Speech. Once you've obtained the entitlement key from the [container software library](https://myibm.ibm.com/products-services/containerlibrary), you can login to the registry with the key, and pull the container images to your local machine.
```
echo $IBM_ENTITLEMENT_KEY | docker login -u cp --password-stdin cp.icr.io
```


## Step 2: Build the container image
Build a container image with the provided `Dockerfile` with two pretrained models ( `en-us-multimedia` and `fr-fr-multimedia` ) included to support two different languages: English (en_US) and French (fr_FR). More models can be added to support other languages by updating the provided `Dockerfile`, as well as `env_config.json` and `sessionPools.yaml` in the `chuck_var` directory.
```
docker build . -t speech-standalone
```


## Step 3: Run the container to start the STT service
You can run the container on Docker as follows, using the container image created in the previous step.
```
docker run --rm --publish 1080:1080 speech-standalone
```


## Step 4: Send an example `/recognize` request with an audio file you have
Use the default STT model, which is configured as `en-US_Multimedia` in `env_config.json`:
```
curl "http://localhost:1080/speech-to-text/api/v1/recognize" \
  --header "Content-Type: audio/wav" \
  --data-binary @output.wav
```
Specify a model to use, e.g. `fr-FR_Multimedia`:
```
curl "http://localhost:1080/speech-to-text/api/v1/recognize?model=fr-FR_Multimedia" \
  --header "Content-Type: audio/wav" \
  --data-binary @output.wav
```

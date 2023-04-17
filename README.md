# GenNet

GenNet is dedicated to delivering a scalable framework that enables applications to harness the power of a distributed worker network for AI inference, while also rewarding participants for contributing their processing capabilities. Initially focusing on AI-driven image generation, our long-term vision encompasses the provision of diverse AI services, such as text, video, voice, and sound generation and classification, among others. By leveraging the untapped potential of idle time on home and office systems, we aim to offer these services at more competitive prices than traditional cloud providers.

## How To Participate
Participants will need a Nvidia RTX-capable GPU with the Nvidia drivers installed as well as Docker and Docker Compose.

### Install Docker

[Install Docker on Windows](https://docs.docker.com/desktop/install/windows-install/)

[Install Docker on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)


### Nvidia Drivers
- Make sure you have the nvida drivers installed on the host machine

### Choose Models to Host
- Search [CivitAI](https://civitai.com/) or [HuggingFace](https://huggingface.co/) for the model(s) that you would like to host
- Copy the links to those models
- Add the links to the docker-compose.yml file as shown in the 'command' section in the example below
- Keep your GPU memory in mind when adding/removing models. The number and size of models you add should be less than your vRAM

### Sign up for a wallet
TODO:

### Run the Container
- Download the docker-compose.yml file in this repo
- Replace <API_KEY> with your (Crypto TODO) wallet address in order to receive monthly rewards
- Add the links to the models you selected in the previous step to the docker-compose.yml file in the same way as in the example below.
- Replace any special characters in the file names with the '_' character in the output file at `-O` e.g.  `mdjrny-v4.ckpt` to `mdjrny_v4.ckpt`
- The line containing `test` will ensure that models are not downloaded again when restarting the container
- Separate links to models with `&& \`
- run the container from the dir containing the docker-compose.yml file using: `docker compose up -d`
- Rewards will be paid monthly to the address specified above

## Docker Compose Example File

```
version: '3'
#Make sure you have the nvida drivers installed on the host machine
#Download the models to use in the container in the 'command' section below - Exmples are provided
#Replace any special characters in the file names with the '_' character in the `-O` like `mdjrny-v4.ckpt` to `mdjrny_v4.ckpt`
#Separate models with `&& \`
#Keep your GPU memory in mind when adding/removing models. The number and size of models you add should be less than your vRAM

services:
  gen-net:
    image: gennet/gen-net:latest
    container_name: gen-net
    environment:
      - WORKER_ID=<API_KEY>
    restart: unless-stopped
    volumes:
      - models:/stable-diffusion-webui/models/Stable-diffusion/
    ports: #Port per model - Starting at 12000
      - '12000:12000'
      - '12001:12001'
      - '12002:12002'
      - '12003:12003'
      - '12004:12004'
      - '12005:12005'
      - '12006:12006'
      - '12007:12007'
      - '12008:12008'
      - '12009:12009'
    command: bash -c "\
      test -f /stable-diffusion-webui/models/Stable-diffusion/mdjrny_v4.ckpt || \
      wget -N -O /stable-diffusion-webui/models/Stable-diffusion/mdjrny_v4.ckpt https://huggingface.co/prompthero/openjourney/resolve/main/mdjrny-v4.ckpt && \
      test -f /stable-diffusion-webui/models/Stable-diffusion/epic_diffusion.ckpt || \
      wget -N -O /stable-diffusion-webui/models/Stable-diffusion/epic_diffusion.ckpt https://huggingface.co/johnslegers/epic-diffusion/resolve/main/epic-diffusion.ckpt && \
      test -f /stable-diffusion-webui/models/Stable-diffusion/Anything_V3.ckpt || \
      wget -N -O /stable-diffusion-webui/models/Stable-diffusion/Anything_V3.ckpt https://huggingface.co/andite/anything-v4.0/resolve/main/Anything-V3.0-pruned.ckpt && \
      test -f /stable-diffusion-webui/models/Stable-diffusion/f222.ckpt || \
      wget -N -O /stable-diffusion-webui/models/Stable-diffusion/f222.ckpt https://civitai.com/api/download/models/1224 && \
      test -f /stable-diffusion-webui/models/Stable-diffusion/moDi_v1_pruned.ckpt || \
      wget -N -O /stable-diffusion-webui/models/Stable-diffusion/moDi_v1_pruned.ckpt https://huggingface.co/nitrosocke/mo-di-diffusion/resolve/main/moDi-v1-pruned.ckpt && \
      cd /artgen_backend/ && git reset --hard HEAD && git checkout main && git pull && \
      /app/venv/bin/python /artgen_backend/api/worker_manager.py"
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1 #all
              capabilities: [gpu]
volumes:
  models:

```

name: immich_remote_ml

services:
  immich-machine-learning:
    container_name: immich_machine_learning
    # For hardware acceleration, add one of -[armnn, cuda, rocm, openvino, rknn] to the image tag.
    # Example tag: ${IMMICH_VERSION:-release}-cuda
    image: ghcr.io/immich-app/immich-machine-learning:${IMMICH_VERSION:-release}
    # extends:
    #   file: hwaccel.ml.yml
    #   service: # set to one of [armnn, cuda, rocm, openvino, openvino-wsl, rknn] for accelerated inference - use the `-wsl` version for WSL2 where applicable
    volumes:
      - model-cache:/cache
    restart: always
    ports:
      - 3003:3003

volumes:
  model-cache:

altran1502/immich-machine-learning:latest
/mnt/app-data/immich-remote-ml:/cache

ghcr.io/immich-app/immich-machine-learning:v1.142.1:-release

altran1502/immich-machine-learning:v1.142.1
ghcr.io/immich-app/immich-machine-learning:release



Asia/Hong_Kong

services:
  immich:
    image: ghcr.io/imagegenius/immich:v1.142.1
    container_name: immich
    environment:
      - PUID=568
      - PGID=568
      - TZ=Asia/Hong_Kong
      - DB_HOSTNAME=172.20.0.18
      - DB_PORT=5432 #optional
      - DB_USERNAME=postgres
      - DB_PASSWORD=postgres
      - DB_DATABASE_NAME=immich

      - REDIS_HOSTNAME=172.20.0.19
      - REDIS_PORT=6379 #optional
      # - REDIS_PASSWORD= #optional
      # - SERVER_HOST=0.0.0.0 #optional
      # - SERVER_PORT=8080 #optional
      - MACHINE_LEARNING_HOST=172.20.0.20 #optional
      # - MACHINE_LEARNING_PORT=3003 #optional
      # - MACHINE_LEARNING_WORKERS=1 #optional
      # - MACHINE_LEARNING_WORKER_TIMEOUT=120 #optional
    volumes:
      - path_to_appdata:/config
      - path_to_photos:/photos
      - path_to_libraries:/libraries #optional
    ports:
      - 18080:8080
    restart: unless-stopped


    /mnt/app-data/immich/photos
    /mnt/app-data/immich/config
    /mnt/app-data/immich/libraries


    /mnt/app-data/immich-server:/data

    /mnt/app-data/postgres
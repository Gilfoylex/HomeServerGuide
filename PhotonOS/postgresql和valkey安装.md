$ docker run --name some-postgres -e POSTGRES_PASSWORD=mysecretpassword -d postgres

GID=568
UID=568
PGDATA=/var/lib/postgresql/data # 默认
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=immich  #可以是其他，这里是装immich的时候装的postgre，所以先用这个


-v /mnt/app-data/postgre-sql:/var/lib/postgresql/data
-p 15432:5432

postgres:14




docker run -d \
  --name=valkey \
  -p 6379:6379 \
  valkey/valkey:8-bookworm

valkey/valkey:8-bookworm
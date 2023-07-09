# Comandos para docker registry
## Desde la CLI

### List de REPOSITORIOS en el registry server
curl -sS 192.168.1.15:5000/v2/_catalog

### Lista de Repo por TAG 
curl -sS <domain-on-ip>:5000/v2/<repo>/tags/list

### Determinar la "digest" del tag seleccionado
$ curl -sS -H 'Accept: application/vnd.docker.distribution.manifest.v2+json' \
-o /dev/null \
-w '%header{Docker-Content-Digest}' \
<domain-or-ip>:5000/v2/<repo>/manifests/<tag>

Do note, the Accept header is needed here. Without it you'll get a different value and the deletion will fail.

The response will be in the following format:

sha256:6de813fb93debd551ea6781e90b02f1f93efab9d882a6cd06bbd96a07188b073

### Garbage collect
Correr este comando en el contenedor de docker registry:

$ registry garbage-collect /etc/docker/registry/config.yml
# Despliegue en Anglo

> **Prerrequisito:** Las imágenes Docker correspondientes de cada servicio a subir deben haber sido creadas previamente.

## 1. Conectarse a la VPN

## 2. Subir imágenes al MinIO

- **URL:** [gemelodigitalqvc.anglo.local/minio](https://gemelodigitalqvc.anglo.local/minio)
- **Usuario:** `minioadmin`
- **Contraseña:** `minioadmin`

## 3. Conectarse al Bastion

Conectarse por escritorio remoto a `10.4.170.5` usando las credenciales de Roberto.

## 4. Conectarse vía SSH al servidor correspondiente

```bash
ssh adminserver@10.4.159.17  # Camiones - app server 1
ssh adminserver@10.4.159.26  # TOPO y Blocks - app server 2 (Nuevo)
```

## 5. Generar el link de descarga desde MinIO

El MinIO se encuentra en el **app server 2**. Ejecutar:

```bash
sudo docker ps | grep minio

sudo docker exec -it ID_CONTAINER sh

mc share download --expire 2h mi-minio/images/"nombre.tar"
```

Guardar la URL generada:

```bash
URL_BUCKET="xxxxx"
```

## 6. Descargar la imagen

```bash
curl "${URL_BUCKET}" -o nombre.tar
```

## 7. Hacer copia de seguridad de la imagen actual

Usar `docker tag` para respaldar la imagen del servicio a desplegar:

```bash
sudo docker images | grep nombre_servicio

sudo docker rmi image:previous

sudo docker tag image:latest nombre_servicio:previous
```

## 8. Cargar la nueva imagen

```bash
sudo docker load -i nombre.tar
```

## 9. Reiniciar el servicio

> **Ubicación del `docker-compose.yml`:**
> - Backend: `microservices/`
> - Frontend: `frontend/tmp/`

```bash
sudo docker compose -f docker-compose.yml down
sudo docker compose -f docker-compose.yml up -d
```

## 10. Verificar el servicio

Acceder a [https://gemelodigitalqvc.anglo.local/](https://gemelodigitalqvc.anglo.local/) y comprobar que el servicio esté funcionando correctamente.

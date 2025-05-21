# Docker compose

```markdow
**Autor:** [Kamil Baran](https://github.com/kamilbaran)
**Licencia:** {{Aviso de derechos de autor}}
```

## Instalación de Docker Compose
```bash
sudo -i
curl -L https://github.com/docker/compose/releases/download/1.26.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
curl -L https://raw.githubusercontent.com/docker/compose/$(docker-compose version --short)/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
exit
docker-compose --version
```

Más información: [docs.docker.com/compose/install](https://docs.docker.com/compose/install)

## Ejemplo de Wordpress

### Iniciar Wordpress sin Docker Compose

Para iniciar un sitio de Wordpress en contenedores, sigue estos pasos:

1. Crear una red personalizada para la aplicación
2. Crear un volumen para el contenedor de base de datos
3. Crear un volumen para el contenedor del servidor web
4. Crear el contenedor de base de datos
5. Crear el contenedor del servidor web

```bash
docker network create wordpress_net
docker volume create wordpress_mysql_data
docker volume create wordpress_html
docker run -d --name wordpress_mysql --network wordpress_net -v wordpress_mysql_data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=NobleProg -e MYSQL_DATABASE=wordpress -e MYSQL_USER=wordpress -e MYSQL_PASSWORD=wordpress mysql:5.7
docker run -d --name wordpress_apache --network wordpress_net -v wordpress_html:/var/www/html -p 80:80 -e WORDPRESS_DB_HOST=wordpress_mysql:3306 -e WORDPRESS_DB_USER=wordpress -e WORDPRESS_DB_PASSWORD=wordpress wordpress:php7.2-apache
```

Para eliminar completamente el sitio Wordpress:

```bash
docker container rm -f wordpress_mysql wordpress_apache
docker volume rm wordpress_mysql_data wordpress_html
docker network rm wordpress_net
```

### Iniciar Wordpress con Docker Compose

Docker Compose te permite gestionar aplicaciones multi-contenedor fácilmente. Para iniciar, detener o eliminar el sitio Wordpress:

```bash
docker-compose up -d
docker-compose stop
docker-compose down --volumes
```

Pasos previos necesarios:

1. Instalar Docker Compose (no se incluye por defecto con Docker)
2. Crear un archivo `docker-compose.yml` con el siguiente contenido
3. Ejecutar los comandos anteriores en el mismo directorio o usar `-f` para indicar la ruta del archivo

```yaml
version: '3'
services:
  mysql:
    image: mysql:latest
    volumes:
      - mysql_data:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=NobleProg
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - MYSQL_PASSWORD=wordpress
    networks: 
      - net

  apache:
    depends_on:
      - mysql
    image: wordpress:latest
    volumes:
      - html:/var/www/html
    ports:
      - 80:80
    environment:
      - WORDPRESS_DB_HOST=mysql:3306
      - WORDPRESS_DB_USER=wordpress
      - WORDPRESS_DB_PASSWORD=wordpress
    networks:
      - net

volumes:
  mysql_data:
  html:

networks:
  net:
```

## Ejemplo Mongo Counter

1. Crear un directorio vacío llamado `mc-app`
2. Copiar el contenido de los directorios `httpd` (Dockerfile y carpeta html) y `mongodb` (Dockerfile)
3. Crear el siguiente archivo `docker-compose.yml`:

```yaml
version: '3'
services:
  httpd:
    build: ./httpd
    image: kamilbaran/training:httpd
    network_mode: host

  mongo:
    build: ./mongo
    image: kamilbaran/training:mongo
    network_mode: host
```

4. Para construir todas las imágenes e iniciar la aplicación:

```bash
docker-compose up --build -d
```

> Categorías: `Docker`, `course_code_dockcm`

## Cursos de Docker
[Docker cursos | NobleProg](https://www.nobleprog.mx/cursos-docker)

[Docker website](https://www.docker.com)

Más información sobre archivos Compose: [docs.docker.com/compose/compose-file](https://docs.docker.com/compose/compose-file)

FROM mysql

# Creo la base de datos.
ENV MYSQL_DATABASE drupal

# Añado el contenido de sql-scripts/ directory a la imagen.
# Todos los scripts en docker-entrypoint-initdb.d/ se ejecutan
# automáticamente al crear el contenedor.
COPY ./sql-scripts/ /docker-entrypoint-initdb.d/

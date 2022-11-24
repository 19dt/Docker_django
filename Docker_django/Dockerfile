
# IMAGEN BASE OFICIAL
FROM python:3.9.6-alpine

# ASIGNAMOS VARIABLES POR DEFECTO
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1

# CREACION DE DIRECTORIO DE TRABAJO
RUN mkdir /app
WORKDIR /app

# INSTALACION DE LAS DEPENDECIAS DE PSYCOPG2
RUN apk update \
    && apk add postgresql-dev gcc python3-dev musl-dev

# INSTALACION Y COPIA DEL PROYECTO
RUN pip install --upgrade pip
COPY ./requirements.txt /app/
RUN pip install -r requirements.txt
COPY . /app/

# COPIA Y EJECUCION DEL ENTRYPOINT
RUN mv /app/entrypoint.sh /opt/entrypoint.sh
RUN chmod +x /opt/entrypoint.sh
ENTRYPOINT ["/opt/entrypoint.sh"]


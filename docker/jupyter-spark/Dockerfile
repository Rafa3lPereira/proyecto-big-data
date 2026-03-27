FROM jupyter/pyspark-notebook:spark-3.5.3

USER root

# Instalar dependencias del sistema
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    netcat-openbsd \
    && rm -rf /var/lib/apt/lists/*

# Instalar uv (gestor de paquetes ultrarapido de Astral)
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

USER ${NB_UID}

# Copiar e instalar dependencias Python con uv (10-100x mas rapido que pip)
COPY requirements.txt /tmp/requirements.txt
RUN uv pip install --system --no-cache -r /tmp/requirements.txt

# Configurar Spark para conectar a cluster externo y Hive
ENV SPARK_MASTER_URL=spark://spark-master:7077
ENV PYSPARK_PYTHON=python3
ENV PYSPARK_DRIVER_PYTHON=python3
ENV UV_SYSTEM_PYTHON=1

# Directorio de trabajo
WORKDIR /home/jovyan

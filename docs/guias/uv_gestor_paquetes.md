# uv — Gestor de Paquetes Python Ultrarapido

## Que es uv?

[uv](https://docs.astral.sh/uv/) es un gestor de paquetes y proyectos Python creado por [Astral](https://astral.sh/), la misma empresa detras de [Ruff](https://docs.astral.sh/ruff/) (el linter mas rapido de Python). Escrito en Rust, **uv es entre 10x y 100x mas rapido que pip**.

uv reemplaza a varias herramientas del ecosistema Python en una sola:

| Herramienta tradicional | Equivalente uv | Funcion |
|-------------------------|---------------|---------|
| `pip` | `uv pip` | Instalar paquetes |
| `pip-tools` | `uv pip compile` | Resolver dependencias |
| `virtualenv` / `venv` | `uv venv` | Crear entornos virtuales |
| `pyenv` | `uv python` | Gestionar versiones de Python |
| `pipx` | `uv tool` | Instalar herramientas CLI |

## Por que usamos uv en este proyecto?

En el `Dockerfile` del entorno de practica usamos uv en lugar de pip para instalar dependencias:

```dockerfile
# Instalar uv (gestor de paquetes ultrarapido de Astral)
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# Instalar dependencias con uv (10-100x mas rapido que pip)
RUN uv pip install --system --no-cache -r /tmp/requirements.txt
```

Esto hace que la imagen Docker se construya significativamente mas rapido.

## Instalacion en tu equipo local

### macOS / Linux

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### Windows (PowerShell)

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### Con pip (cualquier OS)

```bash
pip install uv
```

### Con Homebrew (macOS)

```bash
brew install uv
```

## Uso basico

### Instalar paquetes (reemplazo de pip)

```bash
# Equivalente a: pip install pandas numpy matplotlib
uv pip install pandas numpy matplotlib

# Desde requirements.txt
uv pip install -r requirements.txt
```

### Crear entorno virtual

```bash
# Crear entorno virtual
uv venv

# Activar (macOS/Linux)
source .venv/bin/activate

# Activar (Windows)
.venv\Scripts\activate

# Instalar paquetes en el entorno
uv pip install -r requirements.txt
```

### Gestionar versiones de Python

```bash
# Ver versiones disponibles
uv python list

# Instalar una version especifica
uv python install 3.12

# Usar una version especifica
uv run --python 3.12 python script.py
```

### Ejecutar scripts directamente

```bash
# Ejecutar un script con dependencias automaticas
uv run --with pandas --with matplotlib script.py

# Ejecutar herramientas sin instalarlas globalmente
uv tool run ruff check .
uv tool run black .
```

## Gestion de proyectos con uv

### Inicializar un proyecto

```bash
# Crear un nuevo proyecto Python
uv init mi-proyecto
cd mi-proyecto

# Estructura creada:
# mi-proyecto/
# ├── pyproject.toml    # Configuracion del proyecto
# ├── README.md
# └── src/
#     └── mi_proyecto/
#         └── __init__.py
```

### Agregar dependencias

```bash
# Agregar una dependencia
uv add pandas
uv add "numpy>=1.26"

# Agregar dependencia de desarrollo
uv add --dev pytest ruff

# Sincronizar (instalar todo lo del pyproject.toml)
uv sync
```

### pyproject.toml de ejemplo

```toml
[project]
name = "mi-proyecto-bigdata"
version = "0.1.0"
description = "Proyecto de practica Big Data"
requires-python = ">=3.11"
dependencies = [
    "pyspark>=3.5.0",
    "pandas>=2.1.0",
    "numpy>=1.26.0",
    "matplotlib>=3.8.0",
    "seaborn>=0.13.0",
]

[tool.uv]
dev-dependencies = [
    "pytest>=8.0.0",
    "ruff>=0.4.0",
]
```

### Lock file

```bash
# Generar uv.lock (versiones exactas de todas las dependencias)
uv lock

# Instalar exactamente las versiones del lock
uv sync
```

## Comparacion de velocidad

Ejemplo real instalando las dependencias de este proyecto:

| Gestor | Tiempo | Factor |
|--------|--------|--------|
| pip | ~45 segundos | 1x |
| uv | ~2 segundos | **22x mas rapido** |

## Uso dentro de JupyterLab (este proyecto)

Dentro del entorno Docker de este proyecto, uv ya esta instalado. Puedes usarlo desde una celda de notebook:

```python
# Instalar un paquete adicional dentro de Jupyter
!uv pip install --system scikit-learn

# Verificar version de uv
!uv --version
```

O desde la terminal integrada de JupyterLab:

```bash
uv pip install --system nombre_paquete
```

> **Nota:** Usamos `--system` porque dentro del contenedor Docker no necesitamos entorno virtual (el contenedor ya es un entorno aislado).

## Recursos

- **Documentacion oficial:** https://docs.astral.sh/uv/
- **Repositorio GitHub:** https://github.com/astral-sh/uv
- **Blog de Astral:** https://astral.sh/blog
- **Comparacion pip vs uv:** https://docs.astral.sh/uv/pip/compatibility/

## Resumen de comandos mas utiles

```bash
# Instalar uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Instalar paquetes
uv pip install paquete
uv pip install -r requirements.txt

# Entorno virtual
uv venv && source .venv/bin/activate

# Proyecto nuevo
uv init mi-proyecto && cd mi-proyecto
uv add pandas numpy matplotlib

# Ejecutar sin instalar
uv run --with pandas script.py
uv tool run ruff check .

# Gestionar Python
uv python install 3.12
uv python list
```

# afip-services-applied

<p>
  <img alt="Python" src="https://img.shields.io/badge/python-3.9%2B-blue?logo=python&logoColor=white">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-green">
  <img alt="Status" src="https://img.shields.io/badge/status-stable-green">
</p>

> Cliente Python que consume `afip-services-api` (FastAPI) para procesar listas de NITs: JWT auth con refresh automático, chunking y reportes JSON.

## Features

- Autenticación contra `afip-services-api` — obtiene JWT en `/token` y refresca cuando expira.
- Procesamiento por chunks configurables para listas largas de NITs.
- Reintentos con backoff exponencial ante fallos o respuestas vacías.
- Consulta de servicios (`inscription`, `padron`) con extracción limpia de errores por registro.
- Rate limiting del lado del cliente: pausas tras N llamadas consecutivas.
- Transforma la respuesta `list[dict]` en `dict` indexado por NIT para análisis posterior.
- Dos aplicaciones demo: `monotributo_checker.py` (verifica condición monotributo) y `nit_errors_report.py` (reporte de errores).

## Requirements

- Python 3.9+
- Instancia corriendo de [`afip-services-api`](https://github.com/GDelpo/afip-services-api) (local o remota).

## Quickstart

### Install

```bash
git clone https://github.com/GDelpo/afip-services-applied.git
cd afip-services-applied
python -m venv env
source env/bin/activate
pip install -r requirements.txt
```

### Configure

```bash
cp .env-example .env
# Editar .env con URL del API y credenciales
```

### Run demos

```bash
# Verifica condición monotributo contra AFIP
python monotributo_checker.py --input nits.txt

# Genera reporte de errores AFIP por NIT
python nit_errors_report.py --input nits.txt --output errores.json
```

## Configuration

| Variable | Descripción |
|----------|-------------|
| `API_BASE_URL` | URL base de `afip-services-api` (ej. `http://localhost:8000`) |
| `API_USERNAME` | Usuario registrado en el API |
| `API_PASSWORD` | Password |
| `CHUNK_SIZE` | Cantidad de NITs por request (default 100) |
| `MAX_CALLS_BEFORE_PAUSE` | Número de calls antes de una pausa de rate limit |

## Usage programática

```python
from afip_service import AFIPService

svc = AFIPService(
    base_url=API_BASE_URL,
    username=API_USERNAME,
    password=API_PASSWORD,
)

result = svc.fetch_data_service(
    service="inscription",
    nits=["20-12345678-9", "30-98765432-1"],
)
```

## Relacionados

- [`afip-services-api`](https://github.com/GDelpo/afip-services-api) — backend FastAPI que este cliente consume.
- [`afip-services`](https://github.com/GDelpo/afip-services) — lib SOAP base standalone.
- [`afip-iva-checker`](https://github.com/GDelpo/afip-iva-checker) — ejemplo completo de uso.

## License

[MIT](LICENSE) © 2026 Guido Delponte

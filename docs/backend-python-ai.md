# Backend Python - Microservicio de Análisis de Código con IA

## Tecnologías
- Python 3.11+
- FastAPI
- Groq API (modelo `llama-3.3-70b-versatile`)
- Uvicorn (servidor ASGI)
- python-dotenv

## Estructura de proyecto
```
backend-python/
├── app/
│   ├── __init__.py
│   └── main.py # Código principal con endpoints
├── requirements.txt
├── .env # Contiene GROQ_API_KEY (no se sube a git)
└── Dockerfile (opcional)
```

## Endpoint principal

| Método | Endpoint   | Descripción                                       |
|--------|------------|---------------------------------------------------|
| POST   | `/analyze` | Recibe código y lenguaje, devuelve auditoría JSON |

### Formato de request

```json
{
  "code": "código fuente a analizar",
  "language": "python"
}
```

### Formato de response

```json
{
  "severity": "Crítico|Advertencia|Sugerencia",
  "vulnerabilities": ["vulnerabilidad1", "vulnerabilidad2"],
  "refactored_code": "código mejorado",
  "explanation": "explicación pedagógica del problema",
  "clean_code_tips": ["tip1", "tip2"]
}
```

## Configuración

- Obtener una API key gratuita en https://console.groq.com

Crear archivo `.env` en `backend-python/` con:

```
GROQ_API_KEY=gsk_tu_clave_aqui
```

## Instalación y ejecución

### En Windows (Git Bash o CMD)

```bash
cd backend-python
python -m venv venv
source venv/Scripts/activate   # o venv\Scripts\activate en CMD
pip install -r requirements.txt
python app/main.py
```

El servidor corre en:  
http://localhost:8000

## Requisitos (`requirements.txt`)

```text
fastapi==0.115.11
uvicorn==0.34.0
python-dotenv==1.0.1
groq==0.23.1
httpx==0.28.1
```

## Prueba exitosa (SQL Injection)

```bash
curl -X POST http://localhost:8000/analyze \
  -H "Content-Type: application/json" \
  -d '{"code":"query = \"SELECT * FROM users WHERE id = \" + input", "language":"python"}'
```

### Respuesta obtenida:

```json
{
  "severity": "Crítico",
  "vulnerabilities": ["Inyección SQL"],
  "refactored_code": "query = 'SELECT * FROM users WHERE id = %s'; cursor.execute(query, (input,))",
  "explanation": "El código original es vulnerable a inyección SQL, ya que concatena directamente la entrada del usuario...",
  "clean_code_tips": ["Utilizar parámetros en consultas SQL", "Validar y sanitizar las entradas del usuario"]
}
```

## Modelo de IA

- Se utiliza `llama-3.3-70b-versatile` de Groq (reemplaza al descontinuado `llama3-70b-8192`).
- Temperatura = 0.2 (para respuestas determinísticas).
- Se fuerza respuesta en JSON válido mediante:
  ```
  response_format={"type": "json_object"}
  ```

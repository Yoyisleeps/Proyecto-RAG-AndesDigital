# 🏛️ Agente Orquestador - Secretaría Académica (RAG + Resiliencia)

Este proyecto implementa un asistente inteligente y un agente orquestador para la **Secretaría Académica** utilizando **LangChain**, **FAISS**, **Mistral AI** y **LangSmith**. 

El sistema está diseñado para resolver consultas académicas comunes mediante un flujo **RAG (Retrieval-Augmented Generation)**, clasificar e interpretar intenciones, e interponer un filtro de seguridad local que escala automáticamente casos sensibles a revisión humana.

---

## 🚀 Características Principales

1. **Filtro Local de Seguridad (Escalamiento Instantáneo):**
   * Detecta consultas de alto riesgo (salud mental, apelaciones de grado, temas legales, datos personales) mediante palabras clave locales.
   * Escala la consulta a un ejecutivo humano sin realizar llamadas al LLM ($0$ costo de API y tiempo de respuesta instantáneo).

2. **Búsqueda Vectorial con FAISS + Mistral Embeddings:**
   * Utiliza el modelo `mistral-embed` para indexar y buscar similitud semántica en documentos reglamentarios, mallas curriculares y calendarios de becas.

3. **Arquitectura RAG Ultra Eficiente:**
   * Procesa la consulta y genera la respuesta basada únicamente en contexto oficial en **una sola llamada al LLM**, maximizando la velocidad y respetando las cuotas de API.

4. **Patrón de Fallback y Alta Disponibilidad (Resiliencia Rate-Limit 429):**
   * Ante bloqueos o excedentes de cuota de la API (`HTTP 429 Rate Limit`), el sistema activa un *fallback* automático que entrega la información recuperada de la base vectorial, garantizando continuidad de servicio y evitando caídas del sistema.

5. **Evaluación y Trazabilidad con LangSmith:**
   * Monitoreo de latencia, consumo de tokens y ejecuciones.
   * Evaluación automatizada con métricas de coincidencia exacta (`exact_escalation_match`) para validar que las consultas de riesgo se escalen correctamente.

---

## 🛠️ Requisitos Previos e Instalación

### 1. Clonar el repositorio y preparar el entorno
```bash
python -m venv venv
source venv/bin/activate  # En Linux/Mac
# venv\Scripts\activate   # En Windows
```

### 2. Instalar dependencias
```bash
pip install langchain>=0.3 langchain-community>=0.3 langchain-openai>=0.2 langchain-text-splitters>=0.3 faiss-cpu>=1.8 openai>=1.40 python-dotenv>=1.0 scikit-learn>=1.4 langsmith
```

---

## ⚙️ Configuración de Variables de Entorno

Crea un archivo `.env` en la raíz del proyecto basándote en el archivo `.env.example`:

```bash
cp .env.example .env
```

Edita el archivo `.env` e ingresa tus credenciales:

* **`LLM_API_KEY`**: Clave de API de Mistral AI para el orquestador.
* **`MISTRAL_EMBED_KEY`**: Clave de API de Mistral AI para embeddings (`mistral-embed`).
* **`LANGCHAIN_API_KEY`**: Clave de API de LangSmith para rastreo y evaluación.

---

## 📁 Estructura del Proyecto

```text
.
├── notebook.ipynb         # Notebook principal con la construcción del RAG, Agente y Evaluación
├── README.md              # Documentación general del proyecto
├── .env                   # Variables de entorno (NO subir al repositorio)
├── .env.example           # Plantilla de variables de entorno
└── .gitignore             # Reglas para excluir archivos sensibles y temporales
```

---

## 🧪 Evaluación del Agente con LangSmith

El notebook incluye un script de evaluación automatizada que crea un dataset dinámico en LangSmith con casos de prueba para verificar:

* **Casos de Escalamiento (`escalado: True`):**
  * *"Quiero apelar mi nota de grado"*
  * *"Tengo depresión y no puedo rendir"*
* **Casos RAG Ordinarios (`escalado: False`):**
  * *"¿Cuándo postulo a las becas?"*

Para ejecutar la evaluación, simplemente corre las celdas correspondientes en el Jupyter Notebook. El panel de LangSmith mostrará la latencia, los rastreos (traces) y el score promedio (**1.00 AVG**).
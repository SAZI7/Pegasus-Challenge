# Santos Pegasus Soluciones - Lector de Documentos PDF con IA (Cohere Chatbot)

Un chatbot interactivo impulsado por Inteligencia Artificial para la lectura, extracción y consulta en lenguaje natural de fuentes documentales en formato PDF. Desarrollado para el ecosistema tecnológico de **Santos Pegasus Soluciones**.

## URL de despliegue del proyecto

https://chatbot-bim-bam-challenge.vercel.app/

---

## Descripción general

**Santos Pegasus Soluciones** es una empresa de tecnología especializada en el desarrollo de software escalable bajo arquitectura de microservicios y en la implementación de soluciones de Inteligencia Artificial basadas en Generación Aumentada por Recuperación, conocida como RAG. La empresa se distingue por sus rigurosos estándares técnicos en ingeniería back-end y front-end, garantizando excelencia operativa, disponibilidad y seguridad en infraestructuras de nube, principalmente sobre Oracle Cloud Infrastructure, OCI.

Este proyecto implementa una solución de **Generación Aumentada por Recuperación (RAG - Retrieval-Augmented Generation)** utilizando la API de **Cohere AI**. Permite a los usuarios seleccionar o subir documentos PDF institucionales y técnicos, como manuales de arquitectura, documentación de APIs, estándares de desarrollo, procedimientos de seguridad, políticas de despliegue y guías operativas. Después, el usuario puede realizar preguntas complejas en lenguaje natural desde una interfaz web fluida y atractiva.

La lógica principal del sistema se mantiene sin cambios: los documentos se procesan, se dividen en fragmentos, se convierten en vectores mediante embeddings y se consultan por similitud semántica para generar respuestas contextualizadas con citas de las fuentes utilizadas.

---

## Arquitectura de la solución

El sistema cuenta con una arquitectura desacoplada organizada de la siguiente manera:

```
┌─────────────────────────────────────────────────────────┐
│              Interfaz Web (HTML5 / CSS / JS)            │
│  - Columna Derecha: Gestión y selección de fuentes PDF  │
│  - Área Central: Chat interactivo y sugerencias         │
└───────────────────────────┬─────────────────────────────┘
                            │ (HTTP / JSON REST API)
┌───────────────────────────▼─────────────────────────────┐
│                 Servidor Backend (Flask)                │
│                     [app.py]                            │
└─────────────┬─────────────────────────────┬─────────────┘
              │                             │
┌─────────────▼─────────────┐ ┌─────────────▼─────────────┐
│  Extractor y Chunking PDF │ │  Cohere RAG y Chat Engine │
│   [pdf_processor.py]      │ │    [cohere_service.py]    │
│  - Extracción con PyPDF   │ │ - Embeddings Multilingual │
│  - Sliding Window Chunk   │ │ - Cosine Similarity Search│
│                           │ │ - Command-R / Command-R+  │
└───────────────────────────┘ └───────────────────────────┘
```

### Flujo de datos RAG paso a paso

1. **Indexación y fragmentación**: Al iniciar el servidor o subir un nuevo PDF, `pdf_processor.py` extrae el texto por páginas y lo divide en *chunks*, es decir, fragmentos con solapamiento, preservando metadatos como nombre de archivo y número de página.
2. **Generación de embeddings**: Al enviar una consulta, `cohere_service.py` utiliza el modelo `embed-multilingual-v3.0` de Cohere para vectorizar tanto la pregunta del usuario como los fragmentos de los PDF activos.
3. **Búsqueda vectorial**: Se calcula la similitud cosenoidal entre el vector de la consulta y los vectores de los documentos, seleccionando los cinco fragmentos más relevantes.
4. **Generación con citas**: Los fragmentos seleccionados se envían al modelo `command-r-08-2024` o `command-r-plus` junto con instrucciones del sistema en español. El modelo genera una respuesta precisa y cita el documento y número de página correspondiente.

---

## Tecnologías y herramientas

### Backend, lógica del sistema

- **Python 3.9+**: Lenguaje principal de desarrollo backend.
- **Flask y Flask-CORS**: Framework web ligero para la exposición de endpoints REST API.
- **Cohere SDK (`cohere`)**: Integración con modelos LLM para RAG y chat.
- **PyPDF**: Extracción de texto desde archivos PDF.
- **NumPy**: Cálculo de similitud cosenoidal para vectores de embeddings.
- **Python-Dotenv**: Manejo seguro de variables de entorno mediante archivos `.env`.

### Frontend, interfaz de usuario

- **HTML5 semántico**: Estructura limpia y accesible.
- **Vanilla CSS3**: Diseño personalizado en modo oscuro, gradientes y microanimaciones sin dependencias externas pesadas.
- **Vanilla JavaScript ES6+**: Consumo asíncrono de la API REST mediante `fetch`, gestión dinámica del historial de chat, carga de archivos mediante drag and drop y selección interactiva de fuentes.
- **Google Fonts y FontAwesome**: Tipografía profesional e iconografía para la interfaz.

### Infraestructura y seguridad

- **Oracle Cloud Infrastructure, OCI**: Plataforma de nube considerada para el despliegue de servicios escalables y seguros.
- **Arquitectura de microservicios**: Separación de responsabilidades para facilitar escalabilidad, mantenimiento y evolución del sistema.
- **Variables de entorno**: Protección de credenciales y claves de API fuera del código fuente.
- **Comunicación REST**: Intercambio de información entre frontend y backend mediante solicitudes HTTP y respuestas JSON.

---

## Instrucciones para ejecutar el proyecto

### Requisitos previos

- Tener instalado **Python 3.9** o superior.
- Contar con una clave de API de Cohere disponible en [dashboard.cohere.com](https://dashboard.cohere.com/api-keys).

### Paso 1: Clonar o descargar la carpeta del proyecto

Asegúrate de estar ubicado en el directorio raíz del proyecto:

```bash
cd "c:/Users/CASA/Desktop/Proyecto Oracle/Proyecto 1"
```

### Paso 2: Crear e instalar el entorno virtual

```bash
# Crear entorno virtual, opcional pero recomendado
python -m venv venv

# Activar entorno virtual en Windows PowerShell
.\venv\Scripts\Activate.ps1

# Instalar dependencias
pip install -r requirements.txt
```

### Paso 3: Configurar la API Key de Cohere

Tienes dos opciones para proporcionar la API Key.

#### Opción A: Archivo `.env`, recomendado

Crea un archivo llamado `.env` en la raíz del proyecto con la siguiente variable:

```env
COHERE_API_KEY=tu_api_key_aqui
```

#### Opción B: Desde la interfaz web

Si no configuras el archivo `.env`, puedes ingresar la API Key directamente desde el botón **API Key**, ubicado en la esquina superior derecha de la interfaz web.

### Paso 4: Iniciar el servidor backend

```bash
python app.py
```

El servidor se ejecutará en:

```text
http://localhost:5000
```

### Paso 5: Abrir la aplicación web

Abre Chrome, Edge o Firefox e ingresa a:

```text
http://localhost:5000
```

---

## Ejemplos de preguntas que el agente puede responder

Mediante documentos institucionales y técnicos de **Santos Pegasus Soluciones**, el agente puede responder preguntas como:

1. **Arquitectura de software**: *¿Cuáles son los componentes principales de la arquitectura de microservicios del proyecto?*
2. **Desarrollo backend**: *¿Qué estándares deben seguirse para crear y documentar un endpoint REST?*
3. **Desarrollo frontend**: *¿Qué lineamientos de accesibilidad y rendimiento deben aplicarse en la interfaz?*
4. **Infraestructura OCI**: *¿Cuál es el procedimiento recomendado para desplegar el servicio en Oracle Cloud Infrastructure?*
5. **Seguridad**: *¿Cómo deben almacenarse y administrarse las credenciales, claves de API y variables de entorno?*
6. **Inteligencia Artificial RAG**: *¿Cómo se seleccionan los fragmentos documentales utilizados para generar una respuesta?*

---

## Ejemplos de respuestas generadas por el agente

### Ejemplo 1: Consulta sobre arquitectura de microservicios

> **Usuario**: *¿Cómo se distribuyen las responsabilidades dentro de la arquitectura del sistema?*
>
> **Santos Pegasus AI Agent**:
> La solución separa la interfaz web, el servidor backend, el procesamiento de documentos y el motor RAG. Esta distribución permite mantener componentes independientes, facilitar las pruebas y escalar los servicios de acuerdo con su carga de trabajo.
>
> *Fuente citada*: `Arquitectura de Microservicios.pdf (Pág. 2)`

---

### Ejemplo 2: Consulta sobre seguridad y variables de entorno

> **Usuario**: *¿Dónde debe almacenarse la clave de Cohere?*
>
> **Santos Pegasus AI Agent**:
> La clave debe almacenarse mediante una variable de entorno denominada `COHERE_API_KEY`. No debe escribirse directamente dentro del código fuente ni publicarse en el repositorio de GitHub.
>
> *Fuente citada*: `Estándares de Seguridad y Configuración.pdf (Pág. 3)`

---

### Ejemplo 3: Consulta sobre el funcionamiento de RAG

> **Usuario**: *¿Cómo encuentra el agente la información relacionada con una pregunta?*
>
> **Santos Pegasus AI Agent**:
> El sistema transforma la pregunta y los fragmentos documentales en vectores numéricos. Después calcula la similitud cosenoidal y selecciona los fragmentos con mayor relación semántica para construir la respuesta final.
>
> *Fuente citada*: `Guía Técnica de Soluciones RAG.pdf (Pág. 4)`

---

## Estructura del proyecto

```text
Proyecto 1/
├── app.py                                      # Servidor web Flask REST API
├── cohere_service.py                           # Integración RAG y Cohere Chat Client
├── pdf_processor.py                            # Extracción y fragmentación de PDF
├── requirements.txt                            # Dependencias de Python
├── README.md                                   # Documentación general y arquitectura
├── documentos/                                 # Documentación institucional y técnica
│   ├── Arquitectura de Microservicios.pdf
│   ├── Estándares de Desarrollo Backend.pdf
│   ├── Lineamientos de Desarrollo Frontend.pdf
│   ├── Estándares de Seguridad y Configuración.pdf
│   └── Guía Técnica de Soluciones RAG.pdf
├── static/
│   ├── index.html                              # Interfaz web HTML5
│   ├── styles.css                              # Estilos CSS
│   └── app.js                                  # Lógica cliente JavaScript
└── uploads/                                    # Archivos PDF subidos por usuarios
```

> Nota: Los nombres de los documentos mostrados en la estructura son referencias sugeridas. Deben ajustarse a los nombres reales de los PDF incluidos en el repositorio.

---

## Despliegue en Vercel

La aplicación fue desplegada en Vercel mediante la integración con GitHub. El proyecto utiliza Flask para el backend y archivos estáticos para el frontend, como `index.html`, `app.js` y `styles.css`.

La configuración utilizada fue la siguiente:

```text
Framework Preset: Flask
Root Directory: ./
Build Command: None
Output Directory: N/A
```

También se configuró la variable de entorno `COHERE_API_KEY` directamente en Vercel para evitar publicar información sensible dentro del repositorio.

Cada vez que se realiza un nuevo `git push` a la rama `main`, Vercel detecta los cambios y genera automáticamente un nuevo despliegue de la aplicación.

---

## Imágenes de ejemplo de la aplicación funcionando

<img width="1600" height="813" alt="Interfaz del agente RAG de Santos Pegasus Soluciones" src="https://github.com/user-attachments/assets/e4551297-8ca5-45fa-8c46-b743706d405a" />

<img width="1598" height="862" alt="Consulta documental en Santos Pegasus Soluciones" src="https://github.com/user-attachments/assets/a4ee3505-ee29-408e-b304-80614ade7d52" />

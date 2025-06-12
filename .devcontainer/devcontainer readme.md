

# 🐳 Configuración del DevContainer para este proyecto

Este archivo documenta cómo está configurado el entorno de desarrollo en VS Code con Docker DevContainers.

---

## 🔧 Imagen base
Se usa la imagen oficial de Microsoft para Python 3.11:

```json
"image": "mcr.microsoft.com/devcontainers/python:0-3.11"
```

---

## 📂 Estructura de montaje

```json
"workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind",
"workspaceFolder": "/workspace"
```

Esto monta tu carpeta local en `/workspace`.

---

## 📦 Instalación de dependencias

### Al crear el contenedor (`onCreateCommand`):



### Al iniciar el contenedor (`postStartCommand`):

```bash
pip install -r requirements.txt &&
if [ -f requirements-dev.txt ]; then pip install -r requirements-dev.txt; fi &&
streamlit run app.py
```

- Siempre instala `requirements.txt`
- Si `requirements-dev.txt` existe, también lo instala
- Lanza automáticamente la app con `streamlit`

---

## 🧠 Configuración adicional

- Se preconfigura el entorno IPython para mostrar números con separador de miles.
- Se instala el kernel `"Python 3 (devcontainer)"` para notebooks.

---

## 🧩 Extensiones de VS Code

Se instalan automáticamente:

- Monokai Pro
- Jupyter
- Pylance
- Data Wrangler
- Docker
- Copilot
- Rainbow CSV

---

## 📁 Archivos ignorados

Para evitar errores en Render:

- `requirements-dev.txt` está incluido en `.gitignore`

## Puertos:
    "forwardPorts": [8501]

📌 Con eso, cuando se levante el contenedor, VS Code te permitirá acceder directamente al puerto 8501 (donde corre Streamlit por defecto), sin necesidad de exponerlo manualmente.


###################################################################################################################
CODIGO:

{
    // Nombre del contenedor que aparecerá en VS Code
    "name": "Python 3 - Data Science",
  
    // Imagen base oficial de Microsoft con Python 3.11
    "image": "mcr.microsoft.com/devcontainers/python:0-3.11",
  
    "workspaceMount": "source=${localWorkspaceFolder},target=/workspace,type=bind",  // Monta tu carpeta local dentro del contenedor
    "workspaceFolder": "/workspace", // Hace que VS Code trabaje desde esa carpeta montada

   // Comando que se ejecuta al crear el contenedor.
   // Instala los paquetes listados en requirements.txt
   // Si existe requirements-dev.txt, lo usa (ideal para desarrollo local).
   // Si no existe (como en Render), usa requirements.txt.
   "onCreateCommand": "cd /workspace && if [ -f requirements-dev.txt ]; then pip install -r requirements-dev.txt; else pip install -r requirements.txt; fi",

                    
    // Comando que se ejecuta después de que el contenedor está listo:
    // - sudo apt-get update                        | Actualiza paquetes del sistema
    // - sudo apt-get install -y tree               | Instala el comando `tree` para ver estructuras de carpetas
    // - python3 -m pip install ipykernel           | Instala `ipykernel` para poder registrar kernels personalizados
    // - python3 -m ipykernel install --user --name devcontainer --display-name "Python 3 (devcontainer)"        | "Registra el kernel con el nombre visible 'Python 3 (devcontainer)'.
    // - echo 'export PS1="(devcontainer) $PS1"' >> ~/.bashrc     |  Personaliza el prompt del terminal
    // - Los tres parametros siguientes, crean script para que pandas siempre muestre la cantidad de decimales
    //   definida y el separador de miles ({:,.1f}). 
    //   Esto es solo a nivel de visualizacion. No modifica el df.        
    //    - mkdir -p ~/.ipython/profile_default/startup              | Crea la carpeta de arranque automática de IPython si no existe
    //    - echo "import pandas as pd\npd.set_option('display.float_format', '{:,.1f}'.format)" \     | Crea un archivo .py ahí dentro.
    //    - > ~/.ipython/profile_default/startup/00-display-float-format.py       | Escribe el código que configura pandas para mostrar siempre la cantidad de decimales definidos, y el separador de miles.
    

    // JSON y soporta varias líneas de codigo en una misma instruccion
    "postCreateCommand": "sudo apt-get update && sudo apt-get install -y tree && python3 -m pip install ipykernel && python3 -m ipykernel install --user --name devcontainer --display-name 'Python 3 (devcontainer)' && echo 'export PS1=\"(devcontainer) $PS1\"' >> ~/.bashrc && mkdir -p ~/.ipython/profile_default/startup && echo \"import pandas as pd\npd.set_option('display.float_format', '{:,.1f}'.format)\" > ~/.ipython/profile_default/startup/00-display-float-format.py", 
   
    // ✅ Este es el nuevo bloque que se ejecuta siempre que se inicia el contenedor
    "postStartCommand": "cd /workspace && pip install -r requirements.txt && if [ -f requirements-dev.txt ]; then pip install -r requirements-dev.txt; fi && streamlit run app.py",
        // 🧠 ¿Qué hace?
          // Entra a tu carpeta de trabajo
          // Instala requirements.txt (siempre)
          // Si existe requirements-dev.txt, lo instala también
          // Lanza streamlit run app.py
          // Si estás en Render, no usás este archivo, así que no afecta nada.

        // ✅ Resultado
          // Cada vez que abras el proyecto en VS Code:
          // Se instalarán las dependencias si hace falta
          // Se abrirá el terminal en /workspace
          // Verás el log del contenedor
          // Y streamlit se lanzará solo, sin tener que escribirlo


    // Personalizaciones para el entorno de Visual Studio Code
    "customizations": {
      "vscode": {
        // Extensiones que se instalarán automáticamente
        "extensions": [
          "monokai.theme-monokai-pro-vscode", // Tema Monokai Pro para Visual Studio Code, proporcionando un esquema de colores atractivo y fácil de leer. (Me interesa Monokai++)              
          "ms-toolsai.jupyter",           // Soporte para trabajar con Jupyter Notebooks, permitiendo la ejecución y visualización de código interactivo.
          "ms-toolsai.jupyter-keymap",    // Atajos de teclado para trabajar con Jupyter Notebooks, facilitando la navegación y edición.
          "ms-toolsai.jupyter-renderers", // Extensión para mejorar la visualización de datos en Jupyter Notebooks, permitiendo renderizar gráficos y otros tipos de datos de manera más efectiva.      
          "ms-python.python",             // Extensión esencial para el desarrollo en Python, habilitando características como linting, autocompletado y depuración.
          "ms-python.vscode-pylance",     // Proporciona soporte avanzado para Python, incluyendo autocompletado y análisis de código.
          "ms-edgedev.data-wrangler",     // Herramienta para manipulación de datos, permitiendo transformar, limpiar y visualizar datos de manera sencilla y rápida.
          "ms-azuretools.vscode-docker",  // Extensión para trabajar con Docker, facilitando la creación, gestión y depuración de contenedores directamente desde VS Code.
          "GitHub.copilot",               // Proporciona sugerencias de código basadas en IA, acelerando la escritura de código al predecir las líneas siguientes (requiere suscripción a Copilot Pro).
          "mechatroner.rainbow-csv"       // Extensión para mejorar la visualización de archivos CSV, asignando colores a las columnas para facilitar la lectura y análisis de datos.
        ]
      }
    },
    // Use 'forwardPorts' to make a list of ports inside the container available locally.
	  // "forwardPorts": [],
    "forwardPorts": [8501]
    📌 Con eso, cuando se levante el contenedor, VS Code te permitirá acceder directamente al puerto 8501 (donde corre Streamlit por defecto), sin necesidad de exponerlo manualmente.
  }

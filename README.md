# 📚 Proyecto Final ETL - Seudo-anonimización y Calendarización

**Autor:** [Tu Nombre Completo]  
**Materia:** Administración de Bases de Datos  
**Repositorio:** https://www.youtube.com/watch?v=YwyJyXtq6U4

Este proyecto implementa un proceso ETL (Extract, Transform, Load) para migrar datos transaccionales sensibles a un ambiente de Calidad (QA), haciendo énfasis en la **Seudo-anonimización (R1)** y la **Calendarización (R10)**.

---

## 1. ⚙️ Instalación y Requerimientos (a.1.)

### 1.1. Requerimientos del Sistema

| Requerimiento | Archivo de Validación | Propósito |
| :--- | :--- | :--- |
| **Python 3.9+** | `requirements.txt` | Lenguaje de ejecución del ETL. |
| **Docker Desktop** | `Dockerfile` | Contenerización y despliegue inmutable (R10). |
| **PostgreSQL / Supabase** | `config.yaml` | Motor de Base de Datos para Fuente y Destino. |

### 1.2. Configuración Inicial (Setup)

1.  **Clonar el Repositorio:**
    ```bash
    git clone [URL_DE_TU_REPOSITORIO] ENTREGA_FINAL_ETL
    cd ENTREGA_FINAL_ETL
    ```
2.  **Configuración de Conexión:** Las URLs de conexión (`source_url` y `target_url`) para tu instancia de Supabase/PostgreSQL están definidas en el archivo **`config.yaml`**.
    * **Nota:** Estas URLs deben copiarse del Dashboard de Supabase (Sección: Database -> Connection String).
3.  **Verificación de BD:** Ejecuta el diagnóstico para confirmar que el `config.yaml` es válido y que las tablas existen:
    ```bash
    python diagnostico.py
    ```

---

## 2. 🛡️ Manejo de Seguridad y Permisos (a.2.)

### 2.1. Almacenamiento de Credenciales

* **Implementación Actual:** Las URLs de conexión están almacenadas en el archivo **`config.yaml`** subido al repositorio.
* **Advertencia de Seguridad:** En un entorno real de producción, el acceso a los secretos debe separarse del código. La práctica recomendada es usar **Variables de Entorno** (`os.environ`) inyectadas en tiempo de ejecución.

### 2.2. Control de Acceso Basado en Roles (RBAC - R7)

El ETL utiliza un mecanismo de seguridad para autorizar operaciones, diferenciando entre un operador de rutina y un desarrollador con permisos de reinicio de datos.

| Rol | Propósito | Comando CLI | Contraseña UI | Operación Desbloqueada |
| :--- | :--- | :--- | :--- | :--- |
| **`operador`** | Calendarización / Rutina | `operador 2` | N/A | Carga **Incremental** y Modo Ensayo. |
| **`dev`** | Desarrollo / Pruebas de Carga | `dev 1` | `ABD123` | **Carga Completa (Full Load)**, que trunca la tabla destino. |

---

## 3. 🚀 Modos de Ejecución del ETL

### Opción A: Despliegue con Docker (Recomendado para Producción - R10)

Este es el método ideal para el despliegue en la nube o la programación automatizada (Cron Jobs). 

#### **A.1. Construcción de la Imagen**

1.  Asegúrate de que Docker Desktop esté corriendo.
2.  Desde la carpeta raíz del proyecto, ejecuta:
    ```bash
    docker build -t etl-proyecto .
    ```

#### **A.2. Ejecución CLI (Calendarización)**

La ejecución sin interfaz, para ser programada:

| Tipo de Carga | Comando de Docker |
| :--- | :--- |
| **Carga Incremental** | `docker run etl-proyecto python main.py operador 2` |
| **Carga Completa** | `docker run etl-proyecto python main.py dev 1` |

#### **A.3. Ejecución Web UI (Monitoreo Streamlit)**

Para acceder al tablero de control y monitoreo:

1.  **Ejecutar el Contenedor:**
    ```bash
    docker run -p 8501:8501 etl-proyecto
    ```
2.  **Acceder a la Web UI:** Abre tu navegador en: `http://localhost:8501`

### Opción B: Ejecución para Desarrollo Local (Python)

Este modo es crucial para la depuración y desarrollo rápido.

#### **B.1. Configuración del Entorno**

1.  Activar el entorno virtual:
    ```bash
    python -m venv venv
    source venv/bin/activate # o .\venv\Scripts\activate en Windows
    ```
2.  Instalar dependencias:
    ```bash
    pip install -r requirements.txt
    ```

#### **B.2. Ejecución CLI (Comandos Python)**

Desde la terminal en la carpeta raíz, ejecuta:

| Tipo de Carga | Comando de Ejecución Python |
| :--- | :--- |
| **Carga Incremental** | `python main.py operador 2` |
| **Carga Completa** | `python main.py dev 1` |

#### **B.3. Interfaz Web (Streamlit UI)**

Para la ejecución local y monitoreo:

```bash
streamlit run app.py

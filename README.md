# Calculadora web con API REST

Interfaz web desarrollada con **HTML, CSS y JavaScript** que consume una API REST de calculadora mediante `fetch()`. Permite realizar operaciones aritméticas utilizando los métodos HTTP **GET** y **POST**, mostrar respuestas en formato JSON y consultar el estado de conexión con la API.

## Tecnologías utilizadas

- **HTML5:** estructura de la página y formulario.
- **CSS3:** estilos y presentación.
- **JavaScript:** validación de entradas, eventos, solicitudes HTTP y actualización del DOM.
- **Fetch API:** comunicación con el servidor REST.
- **FastAPI (Python):** backend utilizado en el ejercicio; se ejecuta por separado.

## Archivos

```text
calculadora-frontend/
├── index.html       # Interfaz de la calculadora
├── app.css          # Estilos de la página
├── app.js           # Funciones JavaScript y solicitudes HTTP
└── README.md        # Instrucciones del proyecto
```

> **Importante:** el archivo JavaScript entregado se llama `app(2).js`, pero `index.html` carga `app.js`. **Renombrar `app(2).js` a `app.js`** antes de ejecutar la página.

## Requisitos previos

- Navegador web moderno.
- Servidor de la API REST en ejecución (por ejemplo, el backend de FastAPI del ejercicio).
- Opcionalmente, Python para servir el frontend por HTTP.

## Configuración y ejecución

### 1. Iniciar el backend

Si se utiliza el backend FastAPI del ejercicio anterior, desde su carpeta ejecutar:

```bash
pip install -r requirements.txt
uvicorn main:app --reload
```

La API quedará disponible en `http://127.0.0.1:8000`. Su documentación interactiva puede consultarse en `http://127.0.0.1:8000/docs`.

### 2. Configurar la dirección de la API

En `app.js`, modificar la constante `base`:

```javascript
// Pruebas locales
const base = "http://127.0.0.1:8000";
```

**Atención:** en el archivo original aparece `const base="localhost:8000";`. Para efectuar peticiones correctamente desde `fetch()`, se requiere una dirección absoluta con `http://` o `https://`.

Si se publica el frontend detrás de un proxy inverso que redirige `/api` hacia FastAPI, puede usarse:

```javascript
const base = "/api";
```

Esta segunda opción **requiere que el servidor web tenga configurado el proxy**; no basta con cambiar la constante.

### 3. Abrir el frontend

Desde la carpeta con `index.html`, `app.css` y `app.js`, iniciar un servidor local:

```bash
python3 -m http.server 5500
```

Después, visitar `http://127.0.0.1:5500` en el navegador.

## Funcionalidades

La página recibe dos números (**A** y **B**), valida que sean numéricos y ejecuta la operación seleccionada. El resultado aparece en el apartado **Respuesta**.

| Operación | Método | Ruta | Datos enviados |
|---|---|---|---|
| Suma | GET | `/sumar` | Parámetros `a` y `b` en la URL |
| Resta | GET | `/restar` | Parámetros `a` y `b` en la URL |
| Multiplicación | POST | `/multiplicar` | Objeto JSON `{ "a": 2, "b": 3 }` |
| División | POST | `/dividir` | Objeto JSON `{ "a": 2, "b": 3 }` |

**Ejemplo de GET:**

```http
GET http://127.0.0.1:8000/sumar?a=2&b=3
```

**Ejemplo de POST:**

```javascript
fetch("http://127.0.0.1:8000/multiplicar", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ a: 2, b: 3 })
});
```

**Respuesta esperada** en ambos ejemplos:

```json
{"resultado":5.0}
```

## Funciones JavaScript

| Función | Descripción |
|---|---|
| `obtenerValores()` | Lee los campos de entrada y verifica que contengan números. |
| `mostrarSalida(texto)` | Muestra texto o datos convertidos a JSON en el elemento `out`. |
| `sumar()` / `restar()` | Envían solicitudes GET con parámetros en la URL. |
| `multiplicar()` / `dividir()` | Envían solicitudes POST con un cuerpo JSON. |
| `checkHealth()` | Consulta `/health` para mostrar el estado de conexión de la API. |

Se utiliza `fetch()` junto con promesas (`.then()` y `.catch()`), `JSON.stringify()` para convertir el objeto enviado y `textContent` para actualizar el contenido de la página.

## Consideraciones y solución de problemas

- **No carga JavaScript:** comprobar que el archivo se llame `app.js`, como indica `index.html`.
- **Fallan las peticiones:** comprobar que `base` incluya el protocolo (`http://`) y que FastAPI esté ejecutándose.
- **El indicador de conexión muestra un error:** el frontend consulta `GET /health`, pero el backend FastAPI entregado anteriormente **no define esa ruta**. Para utilizar el indicador, agregar al backend:

  ```python
  @app.get("/health")
  def health():
      return {"status": "ok"}
  ```

- **Problemas de CORS:** si frontend y backend usan orígenes diferentes, comprobar que el backend permita el origen correspondiente.
- **División entre cero:** el backend responde con un mensaje de error (`detail`), que la función `dividir()` muestra en pantalla.

## Objetivo de aprendizaje

Practicar la conexión entre un frontend y un backend mediante una API REST, diferenciando solicitudes GET y POST, el envío de parámetros y JSON, el uso de promesas y la manipulación del DOM.

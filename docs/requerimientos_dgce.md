# Documento de Especificación de Requerimientos (SRS)

**Proyecto:** Portal web dinámico de normativas, resoluciones y planes de estudio de nivel secundario técnico (DGCyE - GBA)
**Materia:** Proyecto de Implementación de Sitios web Dinámicos
**Escuela:** EEST N.º 1 "Eduardo Ader", Vicente López
**Curso:** 7.º año, 2.º Grupo B
**Actividad:** 18 - Levantamiento de requerimientos y entrevista con el cliente
**Alumno:** Mateo Silvero
---

## 1. Minuta de entrevista

**Cliente:** Dirección General de Cultura y Educación de la Provincia de Buenos Aires (DGCyE)
**Participantes:** directores de área del Ministerio de Educación y el equipo de desarrollo
**Tema:** necesidades para un portal de consulta de normativas del nivel secundario técnico

**Resumen (5 líneas):**

1. Inspectores, directores de escuelas técnicas de GBA y docentes pierden mucho tiempo buscando resoluciones, circulares y planes de estudio en PDFs desordenados.
2. Necesitan un buscador dinámico por palabras clave cuyos resultados aparezcan al instante, sin recargar la página.
3. Piden un calendario histórico y filtros por año, mes y tipo de norma (Resolución, Disposición, Circular).
4. Requieren un panel de visualización rápida de anexos, como los Excel de planes de estudio y los PDF de normativas, con descarga directa.
5. El sistema debe funcionar con fluidez tanto en computadoras del Estado como en celulares de los docentes.

> **Nota de entrega:** esta minuta debe presentarse además en formato **APA 7.ª edición, en PDF**. El texto de arriba sirve como base para ese archivo.

---

## 2. Requerimientos

### 2.1 Requerimientos funcionales (lo que el sistema DEBE hacer)

| ID | Requerimiento | Descripción |
|----|---------------|-------------|
| RF-01 | Búsqueda dinámica en tiempo real | El usuario ingresa términos en una barra de búsqueda y el listado de normativas se filtra al instante, sin recargar la página. |
| RF-02 | Filtro por año y mes | El usuario puede seleccionar resoluciones por año y mes mediante un calendario interactivo. |
| RF-03 | Filtro por tipo de norma | El usuario puede filtrar por tipo: Resolución, Disposición o Circular. |
| RF-04 | Combinación de filtros | La búsqueda por texto, el calendario y el tipo de norma pueden aplicarse a la vez. |
| RF-05 | Visor de anexos y enlaces | Cada normativa muestra su listado de documentos asociados (PDF de resoluciones, grillas de planes de estudio en formato tabular). |
| RF-06 | Descarga directa | Cada anexo puede descargarse directamente desde el visor. |
| RF-07 | Lectura de planillas | Los archivos de planes de estudio en `.xlsx` o `.csv` se pueden previsualizar en el navegador (por ejemplo con SheetJS). |

### 2.2 Requerimientos no funcionales (cómo debe comportarse el sistema)

| ID | Requerimiento | Descripción |
|----|---------------|-------------|
| RNF-01 | Rendimiento | El filtrado se ejecuta del lado del cliente, en memoria con JavaScript, en menos de 100 ms para garantizar 60 fps. |
| RNF-02 | Compatibilidad y responsividad | La interfaz se adapta a smartphones de docentes y a escritorios institucionales (pantallas 1080p). |
| RNF-03 | Despliegue y CI/CD | Despliegue automático en la nube (Vercel, Render o similar) mediante integración continua conectada a GitHub. |
| RNF-04 | Seguridad | El sitio se sirve por HTTPS y no expone datos sensibles en el código del cliente. |
| RNF-05 | Usabilidad | Un usuario sin capacitación previa debe poder encontrar una normativa en pocos pasos desde la pantalla principal. |

> RF-04, RF-07, RNF-04 y RNF-05 son requerimientos propuestos por el equipo a partir de lo conversado en la entrevista. Conviene confirmarlos con el cliente.

---

## 3. Modelo de datos inicial

Estructura JSON simulada para las pruebas iniciales. Se extiende el ejemplo de la consigna con el campo `anexos`, necesario para RF-05 y RF-06.

```javascript
const normativasDGCyE = [
  {
    id: 1,
    nro: "651/26",
    tipo: "Resolución",
    anio: 2026,
    mes: "Octubre",
    desc: "Aprueba pliegos y autoriza llamada a Licitación Pública para talleres de EEST.",
    anexos: [
      { nombre: "Resolución 651-26.pdf", formato: "pdf", url: "/anexos/res-651-26.pdf" }
    ]
  },
  {
    id: 2,
    nro: "420/25",
    tipo: "Disposición",
    anio: 2025,
    mes: "Marzo",
    desc: "Actualización de planes de estudio ciclo superior 7mo año Técnico en Programación.",
    anexos: [
      { nombre: "Disposición 420-25.pdf", formato: "pdf", url: "/anexos/disp-420-25.pdf" },
      { nombre: "Plan de estudio 7mo año.xlsx", formato: "xlsx", url: "/anexos/plan-7mo-programacion.xlsx" }
    ]
  },
  {
    id: 3,
    nro: "128/26",
    tipo: "Circular",
    anio: 2026,
    mes: "Mayo",
    desc: "Pautas organizativas para ferias de ciencia distritales y regionales ACTE GBA.",
    anexos: [
      { nombre: "Circular 128-26.pdf", formato: "pdf", url: "/anexos/circ-128-26.pdf" }
    ]
  }
];

// Búsqueda por texto y filtros combinados
function filtrarNormativas(lista, { texto = "", anio = null, mes = null, tipo = null }) {
  const t = texto.trim().toLowerCase();
  return lista.filter(n =>
    (!t || n.desc.toLowerCase().includes(t) || n.nro.includes(t)) &&
    (!anio || n.anio === anio) &&
    (!mes || n.mes === mes) &&
    (!tipo || n.tipo === tipo)
  );
}
```

---

## 4. Mockup o boceto inicial

![Mockup del portal DGCyE](./mockup/Boceto.png)

_Figura 1: Boceto de la interfaz del portal de normativas con barra de búsqueda, selector de tipo de documento, filtro por calendario/fecha y tarjetas con visor/descarga de anexos._

**Elementos que debería mostrar el boceto, según los requerimientos:**

- Barra de búsqueda destacada en la parte superior (RF-01).
- Calendario interactivo y selector de tipo de norma como filtros (RF-02, RF-03).
- Listado de resultados con número, tipo, fecha y descripción de cada norma.
- Panel o visor de anexos con botón de descarga (RF-05, RF-06).
- Versión móvil y versión escritorio (RNF-02).

---

## 5. Entregables Actividad 19: Desarrollo de interfaz dinámica y filtrado

En cumplimiento con los requisitos de la **Actividad 19**, se presentan los siguientes entregables obligatorios:

1. **Código fuente del prototipo:**
   - Estructura modular completa con separación de responsabilidades: [index.html](../index.html), [css/styles.css](../css/styles.css), [js/app.js](../js/app.js), [js/data.js](../js/data.js) y [data/normativas.json](../data/normativas.json).
   - Implementación de filtrado funcional en memoria RAM ($O(n)$) cumpliendo con **RNF-01** (tiempo de respuesta &lt; 100 ms).
   - Maquetación fiel al boceto institucional adaptada al 100% del ancho disponible sin barras de desplazamiento horizontal (**RNF-02**).

2. **Demostración en video del funcionamiento y filtrado en tiempo real:**
   - Demostración en vivo del filtrado instantáneo al interactuar con el buscador, selector de normas y calendario.
   - **Enlace al video de demostración:** [https://www.youtube.com/watch?v=-_yN2NZpaVMm](https://www.youtube.com/watch?v=-_yN2NZpaVMm)

---

## 6. Entregables Actividad 20: Despliegue en la Nube, Inteligencia Artificial y Pitch Final

### 6.1 Lista de Verificación Terminal (Checklist de Entrega Final)

| Ítem | Requisito | Estado | Observaciones |
| :--- | :--- | :---: | :--- |
| **01** | **Repositorio GitHub Limpio** | ✅ Completado | Código estructurado en rama `main` sin archivos temporales, con separación modular (`/css`, `/js`, `/data`, `/docs`, `/img`). |
| **02** | **Portal DGCyE Desplegado** | ✅ Completado | Despliegue en **Vercel** con certificado SSL / protocolo HTTPS activo para producción. |
| **03** | **Desafío de IA (Hugging Face)** | ✅ Integrado | Módulo de **Asistente IA** conectado a la Serverless Inference API de Hugging Face mediante token oficial (`hf_...`). |
| **04** | **Código QR Institucional** | ✅ Completado | Código QR institucional generado digitalmente con isotipo oficial de PBA apuntando al sistema en producción. |

---

### 6.2 Registro de Investigación e Integración de IA (Hugging Face)

#### ¿Qué es Hugging Face?
**Hugging Face** es el ecosistema global de código abierto líder en Machine Learning, Inteligencia Artificial y Procesamiento de Lenguaje Natural (NLP). Ofrece acceso a miles de modelos de lenguaje pre-entrenados listos para producción a través de su infraestructura serverless *Inference API*.

#### Caso de Uso en el Portal DGCyE
En lugar de navegar resoluciones extensas o aplicar filtros manuales, los inspectores y docentes pueden realizar preguntas en lenguaje natural (ej. *"¿Cuáles son las pautas para ferias de ciencias?"* o *"¿Qué resolución regula las prácticas profesionalizantes?"*). 

#### Arquitectura de Integración (Patrón RAG - Retrieval Augmented Generation)
1. **Captura y Análisis:** El frontend toma la consulta en lenguaje natural ingresada en el modal del Asistente IA.
2. **Búsqueda Semántica Local:** En memoria RAM, el motor de JavaScript localiza la normativa oficial bonaerense más relevante por palabras clave y contexto normativo.
3. **Petición Asíncrona (Fetch API):** Se realiza una petición autenticada vía `POST` a la Inference API de Hugging Face (`Qwen/Qwen2.5-7B-Instruct` o pipeline de Question Answering) enviando como contexto institucional la normativa identificada y la consulta del docente.
4. **Respuesta Sintetizada y Enlaces:** La interfaz presenta un resumen ejecutivo claro de 2 o 3 oraciones junto con la tarjeta oficial de la norma y el enlace de descarga directa de sus anexos (.pdf / .xlsx).
5. **Alta Disponibilidad:** En caso de demoras o cuotas de servicio de la API externa, el sistema cuenta con un generador contextual de respaldo que garantiza respuesta instantánea en menos de 100 ms para la Expo Técnica.

---

### 6.3 Despliegue en Producción y Código QR Institucional

* **Plataforma de Hosting Cloud:** Vercel (CI/CD conectado a GitHub con protocolo HTTPS).
* **URL Pública de Producción:** `https://portal-dgcye.vercel.app`

#### Código QR de Acceso Móvil

![Código QR Portal DGCyE](./qr_portal_dgce.png)

_Figura 2: Código QR institucional oficial con isotipo de la Provincia de Buenos Aires para acceso inmediato desde celulares o tablets._

> **Nota:** El código QR institucional se adjunta en este documento para permitir que los evaluadores de la Expo Técnica, directivos y docentes accedan directamente al portal en producción desde sus dispositivos móviles escaneando este informe técnico.



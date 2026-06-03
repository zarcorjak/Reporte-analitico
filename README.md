# Analítica — Automatización y Visualización de Reportes SOX

Proyecto de gestión de controles SOX que automatiza el proceso completo desde la transformación de datos hasta la notificación de hallazgos, incluyendo visualización interactiva en Power BI y un dashboard con análisis asistido por IA.

---

## Estructura del proyecto

```
Analítica-Automatización y Visualización/
│
├── ejercicio1_etl.py               # Pipeline ETL: construcción de la Base Maestra
├── ejercicio3_notificaciones.py    # Automatización de notificaciones por correo
├── ejercicio3_evidencia.html       # Evidencia visual del sistema de notificaciones
├── bonus_ia_analisis.html          # Dashboard con análisis IA de controles
│
├── Base_Maestra_Controles.xlsx     # Salida generada por el ETL (6 hojas)
├── log_notificaciones.xlsx         # Registro de envíos de notificaciones
├── notificaciones_controles.log    # Log de ejecución del sistema de notificaciones
│
└── Power Bi/
    ├── Visualización Estratégica en Power BI.pbix   # Reporte interactivo
    └── Captura de pantalla 2026-04-23 170015.png    # Vista previa del dashboard
```

---

## Componentes

### 1. Pipeline ETL — `ejercicio1_etl.py`

Construye la **Base Maestra de Controles SOX** a partir de un archivo fuente Excel con tres hojas: `Riesgos y controles`, `Resultados pruebas` y `Estructura org`.

**Pasos del proceso:**

1. **Carga** de las tres hojas del archivo fuente con pandas.
2. **Limpieza y estandarización** — renombrado de columnas, corrección de tipos, normalización de fechas y valores nulos.
3. **Integración organizacional** — cruce con la estructura de comités y gerencias por `ID_Proceso`.
4. **Consolidación de resultados** — join con el resultado más reciente por control (`Efectivo`, `Inefectivo`, `Pendiente`).
5. **Campos derivados:**
   - `Semaforo` — clasificación visual (VERDE / ROJO / ROJO CRÍTICO / GRIS / AMARILLO).
   - `Nivel_Auto_Simplificado` — Alto / Medio / Bajo según tipo de automatización.
   - `Control_Clave` — controles que cubren 2 o más riesgos.
   - `Causa_Raiz_Limpia` — causa raíz depurada de prefijos CIRF.
6. **Asignación de responsables** desde un catálogo interno por `ID_Control`.
7. **Exportación a Excel** con 6 hojas formateadas:

| Hoja | Contenido |
|------|-----------|
| `01_Controles_Original` | Datos fuente de riesgos y controles |
| `02_Resultados_Original` | Datos fuente de resultados de pruebas |
| `03_Estructura_Org` | Estructura organizacional |
| `04_Base_Maestra` | Vista consolidada con todos los campos derivados |
| `05_Resumen_Proceso` | Tasa de efectividad y exposición al riesgo por proceso |
| `06_Controles_Inefectivos` | Listado de controles que requieren acción |

**Dependencias:**

```bash
pip install pandas openpyxl xlsxwriter numpy
```

**Uso:**

```bash
# Coloque el archivo fuente en la misma carpeta y ejecute:
python ejercicio1_etl.py
```

> El archivo fuente debe llamarse `Base para prueba tecnica - Analista gestion de información.xlsx` y ubicarse en la misma carpeta que el script.

---

### 2. Automatización de Notificaciones — `ejercicio3_notificaciones.py`

Lee la hoja `06_Controles_Inefectivos` de la Base Maestra y envía correos HTML de alerta a cada responsable de control inefectivo.

**Características:**

- **Modo DEMO** activado por defecto (`MODO_DEMO: True`) — imprime en consola el correo que se enviaría sin hacer envíos reales.
- **Correo HTML** con diseño corporativo: tabla de detalles del control, causa raíz, badge de complejidad y lista de acciones requeridas.
- **CC automático** a `auditoria.interna@empresa.com.co` en cada envío.
- **Log de ejecución** en `notificaciones_controles.log` y registro de resultados en `log_notificaciones.xlsx`.
- Configuración SMTP mediante variables de entorno.

**Variables de entorno para producción:**

```bash
export SMTP_HOST=smtp.gmail.com
export SMTP_PORT=587
export SMTP_USER=gestion.controles@empresa.com.co
export SMTP_PASS=tu_contraseña
```

**Activar modo producción:**

En el archivo, cambiar:
```python
'MODO_DEMO': False
```

**Dependencias:**

```bash
pip install pandas openpyxl
```

**Uso:**

```bash
# Requiere que ejercicio1_etl.py haya generado la Base Maestra primero
python ejercicio3_notificaciones.py
```

---

### 3. Dashboard con Análisis IA — `bonus_ia_analisis.html`

Dashboard web interactivo que presenta un análisis inteligente del estado de los controles SOX por proceso. Incluye:

- Estado de controles por proceso (Efectivo / Inefectivo / Sin resultado).
- Nivel de cobertura de controles por proceso.
- Identificación de procesos con mayor exposición al riesgo.
- Patrones en controles inefectivos y concentración de fallas.
- Priorización de procesos críticos.

Construido con Chart.js. Se abre directamente en el navegador sin servidor.

---

### 4. Visualización en Power BI — `Power Bi/`

Reporte estratégico en Power BI (`.pbix`) con visualizaciones para la toma de decisiones sobre el programa SOX. Se conecta a la Base Maestra generada por el ETL.

---

### 5. Evidencia de Notificaciones — `ejercicio3_evidencia.html`

Página HTML que muestra una vista previa del correo de alerta generado por el sistema de notificaciones. Útil para revisión y aprobación del diseño antes de activar el modo producción.

---

## Flujo de ejecución recomendado

```
1. Preparar el archivo fuente Excel con las 3 hojas requeridas
         ↓
2. Ejecutar ejercicio1_etl.py
   → Genera Base_Maestra_Controles.xlsx
         ↓
3. Ejecutar ejercicio3_notificaciones.py (modo DEMO primero)
   → Genera log_notificaciones.xlsx y notificaciones_controles.log
         ↓
4. Revisar bonus_ia_analisis.html en el navegador
         ↓
5. Abrir el .pbix en Power BI Desktop para visualización estratégica
```

---

## Requisitos

- Python 3.8+
- Librerías: `pandas`, `numpy`, `openpyxl`, `xlsxwriter`
- Power BI Desktop (para el archivo `.pbix`)
- Servidor SMTP válido (solo para envíos reales de notificaciones)

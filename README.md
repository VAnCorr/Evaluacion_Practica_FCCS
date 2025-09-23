# Rúbrica de Evaluación Práctica

Proyecto para la evaluación automatizada de residentes en el curso "Reconocimiento y abordaje inicial del paciente grave".

## Estructura
- `index.html`: Interfaz principal de la rúbrica.
- `assets/`: Imágenes, iconos y recursos.
- `.vscode/settings.json`: Configuración recomendada para VS Code.

## Uso
1) Abre `index.html` en tu navegador.
2) Completa: Nombre del residente, Tutor evaluador y Fecha (la actividad está fija como "Evaluacion Practica Curso Reconocimiento del paciente Grave").
3) Para cada criterio, selecciona el nivel de desempeño y agrega observaciones si aplica.
4) Revisa la puntuación total y el nivel global.
5) Opciones:
	- "Guardar evaluación (JSON)": descarga un archivo con los resultados.
	- "Imprimir / Guardar PDF": usa la impresora "Microsoft Print to PDF" para generar el informe.
	 - "Generar PDF y subir a Drive": crea el PDF en el navegador y lo sube a tu carpeta de Google Drive.

Notas
- El nombre del tutor evaluador se guarda en el navegador para facilitar futuras evaluaciones.
- Puedes ajustar pesos/criterios editando `rubricaConfig` dentro de `index.html`.

## Integración con Google Sheets (Apps Script)

- URL Web App: `https://script.google.com/macros/s/AKfycbz1vmXlCqKx3aqUjRMkpN1a3--mvzGb-Ode0DOInItYKzc8BIJek3Vy4y84GkETepk7Hg/exec`
- Pestaña/Sheet destino: `Evaluacion Practica`

El botón "Enviar a Google Sheets" envía un `POST` JSON al Web App con el siguiente payload:

```
{
	sheetName: "Evaluacion Practica",
	timestamp: "YYYY-MM-DD HH:mm:ss",    // Columna A
	nombre_residente: string,             // Columna B
	tutor_evaluador: string,              // Columna C
	fecha_evaluacion: "YYYY-MM-DD",      // Columna D
	actividad_evaluada: string,           // Columna E
	c1..c14: number|string,               // Columnas F..S (puntuaciones por criterio, padding si faltan)
	puntuacion_total: number,             // Columna T
	nivel_desempeno: "Insuficiente|Suficiente|Bueno|Excelente", // Columna U
	observaciones: string                 // Columna V (observaciones concatenadas)
}
```

Notas Apps Script
- Configura el despliegue como Web App (ejecutar como tú; acceso: cualquiera con el enlace) si deseas consumir desde navegador.
- Si recibes error CORS, puedes ajustar el `ContentService` para permitir `Access-Control-Allow-Origin` o usar `mode: 'no-cors'` (sin lectura de respuesta).
- La función del servidor debe tomar los campos anteriores y hacer `appendRow` en la hoja `Evaluacion Practica` respetando el orden de columnas A..V.

### Subida de PDF a Drive

- El cliente genera un PDF con `html2pdf.js` y envía `pdf_base64`, `pdf_filename` y `folder_id` en el mismo POST.
- El Apps Script crea el archivo dentro de la carpeta y devuelve `fileUrl` en la respuesta.

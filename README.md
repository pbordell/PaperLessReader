# PaperLess Reader 📰✈️📘

Una aplicación nativa de Android desarrollada en **Java** que transforma los complejos diseños en múltiples columnas de los periódicos en formato PDF en una experiencia de lectura limpia, fluida y sin distracciones, similar a la de un libro electrónico.

A diferencia de la extracción de texto local tradicional (que rompe el orden de las columnas y mezcla la publicidad), esta aplicación delega el análisis visual y la estructuración del contenido a la **API de Visión de OpenAI**, la cual interpreta la retícula de la página, aísla cada artículo y unifica el texto de forma secuencial.

---

## 🛠️ Arquitectura y Flujo de Trabajo

1. **Selección del Archivo:** El usuario elige el PDF del periódico utilizando el explorador de archivos nativo de Android (*Storage Access Framework*).
2. **Renderizado Local:** La aplicación utiliza **PdfBox-Android** para renderizar las páginas seleccionadas del PDF en imágenes PNG de alta resolución directamente en la memoria del dispositivo.
3. **Análisis por IA:** La imagen se codifica en Base64 y se envía de forma asíncrona mediante **OkHttp** a los servidores de OpenAI (`gpt-4o`).
4. **Respuesta Estructurada:** La IA procesa el diseño visual, descarta la publicidad, los números de página o banners, y devuelve un objeto JSON estricto con los artículos ordenados.
5. **Interfaz de Lectura:** El JSON se deserializa con **Gson** para llenar una lista (`RecyclerView`). Al pulsar sobre cualquier noticia, se abre un visor de texto optimizado tipográficamente para una lectura cómoda.

## 📦 Dependencias Principales

El proyecto gestiona sus librerías mediante la sintaxis moderna de Kotlin DSL (`build.gradle.kts`):

*   **[PdfBox-Android]:** Una adaptación ligera de Apache PDFBox optimizada específicamente para el entorno gráfico y de memoria de Android.
*   **[OkHttp 4]:** Cliente HTTP encargado de gestionar las peticiones de red asíncronas con tiempos de espera (*timeouts*) personalizados.
*   **[Gson]:** Librería de Google que mapea y convierte automáticamente las respuestas JSON de la IA en objetos de Java.

---

## 📝 Formato JSON Esperado de la IA

La aplicación obliga al modelo de lenguaje a responder utilizando exclusivamente una estructura de objeto JSON limpia, evitando explicaciones adicionales o formatos markdown externos:

```json
{
  "news": [
    {
      "title": "Titular principal de la noticia",
      "subtitle": "Breve subtítulo o contexto editorial.",
      "body": "Cuerpo completo de la noticia reconstruido de forma lineal e hilada a través de las columnas originales...",
      "category": "Economía"
    }
  ]
}
```

# Equipo 3 — MongoDB

Exposición de la Unidad II para **Fundamentos de Arquitectura de Software y Desarrollo
Backend** (TSU en Desarrollo e Innovación de Software).

**Tema:** MongoDB — documentos cuando la fila no es la forma natural de los datos.
**Integrantes:** Heidi, Scarlet, Damian.
**Exposición:** lunes 26 de octubre de 2026.

---

## Contenido del repositorio

| Carpeta | Archivo | Qué es |
|---|---|---|
| `borrador/` | `equipo3_mongodb_borrador.md` / `.pdf` | Borrador de la presentación (entrega del 21 de octubre) |
| `reporte/` | `equipo3_mongodb_reporte.md` / `.pdf` | Reporte final, 4 páginas (máximo 5 sin contar portada) |
| `slides/` | `equipo3_mongodb_slides.pdf` / `.pptx` | Presentación de 11 diapositivas para la exposición |
| `guion/` | `equipo3_mongodb_guion.md` | Guion palabra por palabra, con tiempos y preguntas probables |

El reporte y los slides responden lo mismo con distinto formato: qué problema resuelve
MongoDB, las piezas con las que se trabaja, un ejemplo pequeño relacionado con la API del
curso, qué se gana y qué cuesta, y las fuentes consultadas.

---

## La idea en una frase

MongoDB no reemplaza a MySQL: guarda lo que no tiene forma de fila. En el Sistema General de
Ingresos, cada trámite trae campos distintos; en lugar de hacer `ALTER TABLE` por cada tipo
nuevo, el trámite se guarda como un documento completo dentro de una colección.

---

## Demo

```bash
docker run -d --name mongo -p 27017:27017 mongo:7
docker exec -it mongo mongosh
```

```javascript
use ingresos

db.tramites.insertOne({
  tipo: "licencia",
  folio: "L-1042",
  fecha: "2026-10-20",
  datos: { giro: "restaurante", m2: 85 }
})

db.tramites.insertOne({
  tipo: "constancia",
  folio: "C-2087",
  fecha: "2026-10-21",
  datos: { rfc: "XAXX010101000", adeudos: 0 }
})

db.tramites.find({ "datos.giro": "restaurante" })
```

Dos documentos con campos distintos en la misma colección, sin `ALTER TABLE`.

---

## Fuentes

1. MongoDB. *MongoDB Manual*. https://www.mongodb.com/docs/manual/
2. MongoDB. *Data Modeling Introduction*. https://www.mongodb.com/docs/manual/core/data-modeling-introduction/
3. Docker Hub. *mongo (official image)*. https://hub.docker.com/_/mongo

---

## Cómo abrir los archivos

- Los `.pdf` se abren con cualquier lector; los slides están en formato 16:9.
- Los `.md` se leen directamente en GitHub.
- El `.pptx` se edita con PowerPoint, Keynote o LibreOffice Impress.

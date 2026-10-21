# Guion de la exposición — MongoDB

**Equipo 3** · Heidi, Scarlet, Damian  
Unidad II · 15 minutos (objetivo real: 14:00)

---

## Antes de entrar

- [ ] PDF de las diapositivas abierto en modo presentación, probado en el proyector.
- [ ] Imagen `mongo:7` ya descargada (`docker pull mongo:7`).
- [ ] Contenedor arriba y `mongosh` conectado, **o** saber usar `docker exec -it mongo mongosh`.
- [ ] Los dos `insertOne` listos para pegar (no escribirlos en vivo).
- [ ] Video de respaldo de la demo en el escritorio.
- [ ] Cronómetro visible para quien expone al final.
- [ ] Acordar quién controla la laptop: **Damian** cambia las diapositivas.

**Regla de oro:** si se retrasan, recortan la demo, nunca el cierre.

---

## Reparto

| Tiempo | Quién | Parte |
|---|---|---|
| 0:00–0:40 | Scarlet | Apertura y orden |
| 0:40–5:30 | Heidi | Problema + qué es MongoDB |
| 5:30–7:30 | Scarlet | Cómo se usa |
| 7:30–11:00 | Damian | Ejemplo + comparación |
| 11:00–13:30 | Scarlet | Costos y límites |
| 13:30–14:30 | Damian | Demo |
| 14:30–15:00 | Heidi | Cierre |

---

## 0:00–0:40 · Apertura — Scarlet

**Diapositiva 1 → 2**

> Buenos días. Somos el Equipo 3: Heidi, Scarlet y Damian. Hoy vamos a hablar de MongoDB.
> No venimos a decir que MongoDB es mejor que MySQL, ni que deberíamos cambiar el sistema.
> Venimos a ver un problema muy concreto: qué pasa cuando un dato no tiene forma de fila.
> Vamos a ver el problema, qué es MongoDB, cómo se usa, un ejemplo con trámites del sistema,
> sus costos y una demo corta. Son quince minutos.

**Hace:** avanza a la diapositiva 2, señala las seis partes y pasa a la 3.

---

## 0:40–3:30 · El problema — Heidi

**Diapositiva 3**

> Empiezo con algo que ya nos pasó. En el Sistema General de Ingresos hay trámites. Una
> licencia de funcionamiento pide giro y metros cuadrados. Una constancia de no adeudo pide
> RFC y cuántos adeudos tiene. Son dos trámites, pero no tienen los mismos campos.
>
> Si eso lo guardamos en MySQL, tenemos dos caminos. Uno: una tabla por tipo de trámite.
> Dos: una tabla gigante con todas las columnas posibles. Y en los dos casos, cada trámite
> nuevo nos obliga a hacer `ALTER TABLE`. La tabla crece, la mayoría de las columnas quedan
> en `NULL` para la mayoría de los registros, y para leer un solo trámite tenemos que juntar
> varias tablas con `JOIN`.
>
> No es que MySQL esté mal. Es que le estamos pidiendo que guarde algo que no tiene forma de
> tabla. La pregunta no es qué base de datos es mejor, sino qué forma tiene el dato.
>
> Y aquí el dato tiene forma de documento: un trámite es un folio, una fecha, un estado y un
> bloque de datos que cambia según el tipo. Eso es exactamente lo que guarda MongoDB.

**Hace:** pausa después de «qué forma tiene el dato». Es la frase que abre el resto.

---

## 3:30–5:30 · Qué es MongoDB — Heidi

**Diapositiva 4**

> MongoDB es una base de datos orientada a documentos. Cada registro es un documento JSON
> —técnicamente BSON— y los documentos se agrupan en colecciones. Una colección es como una
> tabla, pero sin columnas fijas: cada documento puede traer campos distintos.
>
> Las piezas son cuatro. El servidor, `mongod`, que escucha en el puerto 27017. El cliente,
> que es la librería dentro de nuestra API: `pymongo` si es Python, `mongoose` si es Node.
> La consola, `mongosh`, para mirar y probar. Y opcionalmente Atlas, que es MongoDB en la
> nube sin instalar nada.
>
> Una aclaración importante: MongoDB no reemplaza a MySQL. Los pagos, la contabilidad y las
> relaciones entre entidades siguen en MySQL. MongoDB guarda lo que no tiene forma de fila.
> Si MongoDB se cae, la API tiene que seguir respondiendo desde MySQL.

**Hace:** al decir «no reemplaza», señala la franja verde de abajo.

---

## 5:30–7:30 · Cómo se usa — Scarlet

**Diapositiva 5**

> Se usa en cuatro pasos. Primero elegimos base y colección: `ingresos`, `tramites`.
> Segundo, armamos el documento: un JSON con tipo, folio, fecha y un objeto `datos` que
> cambia según el trámite. Tercero, insertamos con `insertOne`. No hay que declarar columnas
> antes: el documento entra tal cual. Cuarto, consultamos con `find`.
>
> Y aquí está lo interesante: podemos buscar dentro del objeto anidado. Por ejemplo,
> `datos.giro` igual a `restaurante`.
>
> Hay un detalle que no se puede saltar: el índice. Sin índice, MongoDB recorre toda la
> colección. Con `createIndex` sobre `datos.rfc`, la búsqueda es directa. Es el mismo concepto
> que el índice en MySQL; si no lo ponemos, el documento se vuelve lento.

**Hace:** no leer el código completo; solo señalar `find` y `createIndex`.

---

## 7:30–9:30 · Ejemplo concreto — Damian

**Diapositiva 6**

> Este es el ejemplo concreto. A la izquierda, una licencia: tipo licencia, folio L-1042,
> fecha, y dentro de `datos`, giro y metros cuadrados. A la derecha, una constancia: los
> mismos campos generales, pero dentro de `datos` trae RFC y adeudos. Son documentos
> distintos y viven en la misma colección. No hubo `ALTER TABLE`, no hubo columnas `NULL`,
> no hubo dos tablas.
>
> La consulta es `db.tramites.find` con `datos.giro` igual a `restaurante`. MongoDB entra al
> objeto y compara. Ese es el punto: el trámite se lee completo en una sola consulta, sin
> `JOIN`.
>
> Y ojo: esto no significa que todo vaya en un solo documento gigante. Si un dato se repite
> en miles de trámites y cambia seguido, conviene separarlo. Eso es diseño de documentos, y
> es la decisión más importante.

---

## 9:30–11:00 · Comparación — Damian

**Diapositiva 7**

> ¿Dónde encaja con lo que ya vimos? MySQL guarda filas y columnas y consulta con SQL;
> MongoDB guarda documentos y consulta por clave o por campo. Docker: ya levantamos la API y
> MySQL; ahora es un contenedor más, `mongo:7` en el 27017. GraphQL: eso es el contrato con
> el cliente, qué datos pide; MongoDB es almacenamiento interno, el cliente ni se entera. Y
> el token y los permisos: siguen igual, los valida la API; MongoDB no autentica a nadie.
>
> La frase que quiero que se lleven es esta: MongoDB no cambia la API, cambia cómo se guarda
> un dato que no es tabla.

---

## 11:00–13:30 · Costos y límites — Scarlet

**Diapositiva 8**

> Ahora lo que cuesta, porque no todo es bonito. Gana: un trámite se lee completo, el esquema
> es flexible, hay menos migraciones y escala horizontal con réplicas. Cuesta: duplicar
> datos, porque lo que se repite hay que mantenerlo en varios documentos; no hay `JOIN`, así
> que las relaciones se resuelven en la aplicación; las transacciones son más limitadas
> cuando se tocan varias colecciones; y hay otro modelo que aprender, porque el equipo ya
> piensa en SQL.
>
> Y el costo más grande: el esquema no desaparece. Se mueve a la aplicación. Si nadie diseña
> el documento, en seis meses hay cinco formas distintas del mismo trámite y nadie sabe cuál
> es la buena.

**Diapositiva 9**

> Entonces, ¿cuándo no conviene? Cuando los datos están muy relacionados: pagos, contabilidad,
> nómina. Cuando los reportes cruzan muchas entidades: sumar por sucursal, mes y tipo es más
> simple en SQL. Cuando un movimiento toca cinco entidades a la vez. Y cuando el esquema ya es
> fijo y simple: si cabe en una tabla, MySQL ya funciona.
>
> La pregunta para decidir es: ¿este dato se lee completo y cambia por bloques?

**Hace:** esta es la diapositiva más importante para el jurado. No correrla.

---

## 13:30–14:30 · Demo — Damian

**Diapositiva 10**

> Para cerrar, una demo de tres minutos. Levanto MongoDB con Docker, abro `mongosh`, inserto
> dos trámites con campos distintos en la misma colección y busco por el campo anidado. Sin
> `ALTER TABLE`.

**Guion técnico (ensayado):**

1. `docker run -d --name mongo -p 27017:27017 mongo:7` (si ya está: `docker start mongo`).
2. `docker exec -it mongo mongosh` → `use ingresos`.
3. Pegar el `insertOne` de la licencia.
4. Pegar el `insertOne` de la constancia.
5. `db.tramites.find({ "datos.giro": "restaurante" })`.
6. Frase final: «Misma colección, dos formas. Ningún `ALTER TABLE`.»

**Si falla:** «La demo está grabada; la pasamos y seguimos» → video de 3 minutos. No arreglar
Docker frente al grupo.

---

## 14:30–15:00 · Cierre — Heidi

**Diapositiva 11**

> Recapitulemos. Uno: el documento sigue al dato, no al revés. Dos: el esquema se diseña, no
> se deja al azar. Tres: no es para todo; las relaciones siguen en SQL.
>
> MongoDB no elimina el esquema: lo mueve a la aplicación. Eso es una ventaja si lo hacemos
> bien y un problema si lo ignoramos.
>
> Gracias. ¿Preguntas?

---

## Preguntas probables

**¿MongoDB es más rápido que MySQL?**
> No siempre. Para leer un documento completo por clave es muy rápido; para cruzar muchas
> entidades, MySQL con `JOIN` suele ganar. La velocidad depende del patrón de acceso, no del
> logo.

**¿Se pierden datos si se cae?**
> Depende de la configuración. Con journaling y un replica set se puede tener durabilidad; un
> solo servidor sin réplica es un punto de falla. Por eso no lo usamos como fuente de verdad.

**¿Y las transacciones?**
> MongoDB tiene transacciones multi-documento desde la versión 4.0, pero no son gratis. Si el
> caso es altamente transaccional, una base relacional es más simple.

**¿Por qué no guardar todo en MongoDB?**
> Porque pagos, contabilidad y reportes necesitan relaciones y consistencia fuerte. Forzarlo
> obliga a duplicar y a resolver `JOIN` en la aplicación.

**¿Cómo migrarían el sistema?**
> No migraríamos todo. Empezaríamos por una colección nueva, por ejemplo `tramites`,
> conviviendo con MySQL; la API decide qué lee de cada uno.

**¿Y los archivos?**
> Van a almacenamiento de objetos (S3 o MinIO). En Mongo se guarda el metadato y la
> referencia, no el PDF completo. GridFS existe, pero no es la primera opción.

**Si no saben la respuesta:**
> «No lo tenemos medido. Lo que sí podemos afirmar es… y lo dejaría como punto a revisar.»

---

## Si algo se sale de control

| Problema | Qué hacer |
|---|---|
| Van 2 minutos tarde | Demo a 40 segundos: solo el segundo `insert` y el `find`. |
| Se cae la demo | Video de respaldo, sin pedir disculpas dos veces. |
| Se cae el proyector | Seguir con el borrador impreso y el ejemplo de los dos documentos. |
| Pregunta que no saben | Frase de arriba; nunca inventar. |
| Se quedan sin tiempo | Cierre directo: frase final y gracias. |

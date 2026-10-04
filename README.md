# FixLab | Ecosistema de atención técnica con IA

**Entrega final - Valentín Matrangolo**

Sistema de prueba para consultas de equipos comerciales: Gmail recibe una consulta, n8n la valida y deduplica, Airtable almacena clientes y casos, OpenAI redacta un borrador apoyado en una base de conocimiento, y una persona debe aprobarlo antes de responder en el hilo original.

## Archivos

- `FixLab-workflow.json`: exportación importable de n8n, sin credenciales asociadas.
- `FixLab-arquitectura.pdf`: diagrama, estructuras de datos, ejemplos JSON, costos, seguridad y pruebas.
- `evidencias/`: capturas de flujo, aprobación, error y panel sin datos personales.

## Enlaces

- **Base de datos / dashboard Airtable (vista de lectura):** https://airtable.com/appfI6i5iPrr7IwZd/shrvm8lKql6BjInc9

## Funcionamiento

1. Gmail Trigger consulta la bandeja cada 5 minutos con `in:inbox subject:"[FIXLAB]"`, máximo un resultado.
2. El código verifica remitente, cuerpo, IDs y que el mensaje no provenga de la cuenta receptora.
3. Airtable busca por `Gmail ID` para evitar duplicados, crea la consulta y vincula un cliente existente o nuevo.
4. Se incorporan hasta 10 artículos vigentes de `Base de conocimiento` al prompt dinámico.
5. GPT-4o-mini redacta un borrador con límite de 350 tokens; la salida se mapea desde `output[0].content[0].text`.
6. Gmail `Send and Wait` envía una aprobación interna. Un rechazo marca la consulta `Rechazada`; una aprobación permite responder con `Reply` al ID de mensaje original y marcar `Respondida`.
7. Si falla OpenAI, el puerto `Error` registra un evento vinculado y cambia el estado de la consulta a `Error`.

## Instalación

Importar el JSON en n8n; conectar credenciales propias para Gmail, Airtable y OpenAI; seleccionar la base y sus tablas si cambian los IDs; revisar la dirección de aprobación que se deriva de `Get a message.to`; comprobar que los nombres de campos coinciden con el PDF. Configurar el trigger sólo para la bandeja de prueba. Publicar el workflow tras verificar las ramas de aprobación y error.

**Seguridad:** el archivo no contiene tokens ni contraseñas. No subir capturas con correos personales, borradores privados o claves. Los IDs de base y tablas se conservan como referencias de configuración; no conceden acceso por sí mismos.

## Alcance real

Integrado y probado: n8n, Gmail, Airtable y OpenAI; aprobación humana, respuesta en hilo, validación de vacío y registro de fallo de IA. La búsqueda de conocimiento es por registros vigentes, no un índice vectorial. Claude y la API de lotes aparecen como estrategia de escalado; no están conectados al flujo en vivo. La vista pública está enlazada arriba. El video de demostración está pendiente.

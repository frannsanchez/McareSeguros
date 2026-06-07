# McareSeguros
# 1. Resumen ejecutivo
En el siguiente proyecto se analizará el caso de MCare, se propone una arquitectura orientada a eventos, con procesamiento asincrónico, colas de trabajo, servicios especializados y una capa de reporting separada de la operación diaria. 
El problema principal del caso de estudio no es únicamente leer documentos mediante OCR, sino coordinar un flujo completo de reintegros médicos: carga del evento, almacenamiento y chequeo documental, OCR, validaciones manuales, validaciones automáticas, aprobación, pago e informes. Dado que la solución propuesta no implica reemplazar todos los sistemas actuales, el sistema de eventos, SharePoint, el cluster OCR y el módulo de procesamiento de archivos se conservan, pero estos deben integrarse mediante eventos, colas, contratos de datos claros, trazabilidad y una base operacional consistente. Además, el sistema se diseñará para adaptarse automáticamente a los picos repentinos de trámites (como en épocas de gripe).
Asimismo, se identificarán las etapas críticas que requieren funcionamiento en tiempo real para agilizar los reintegros, diferenciándolas de aquellos informes consolidados y análisis de mejora que se procesarán por lotes (batch).
# 2. Contexto y problema a resolver
MCare recibe eventos de reintegro médico con documentación asociada en PDF, BMP o JPG. Cada certificado médico representa un evento de atención, y el asegurado debe presentar certificados, facturas, recetas, órdenes, estudios y comprobantes asociados dentro de plazos específicos. El proceso debe escalar porque se reciben aproximadamente 5000 eventos diarios y existen picos abruptos, por ejemplo en épocas de gripe.
Los principales desafíos son:
- Procesar grandes volúmenes de documentación médica lo antes posible.
- Evitar que el portal web de cara al usuario colapse o se ralentice debido al alto consumo de procesamiento que requiere el motor de OCR.
- Detectar automáticamente qué documentos requieren intervención manual.
- Validar reglas de negocio sobre montos, fechas, póliza vigente, saldo disponible y matrícula médica.
- Permitir revisión humana con trazabilidad antes de aprobar o rechazar.
- Generar informes operativos en tiempo real y reportes batch diarios.
- Mantener seguridad, auditoría y control sobre datos médicos sensibles.
- Establecer etapas de implementación del proyecto.
# 3. Arquitectura propuesta
La propuesta es aplicar una arquitectura híbrida: arquitectura orientada a eventos, pipelines de procesamiento de datos y servicios desacoplados. Cada componente publica eventos cuando completa una etapa del proceso, y otros componentes consumen esos eventos para continuar el flujo.
<img width="1116" height="1872" alt="Event Processing Ecosystem-2026-06-07-190456" src="https://github.com/user-attachments/assets/86be0412-a02e-4c74-889a-c93dc352afe1" />

Los beneficios principales de este tipo de arquitectura son:
- Desacoplar el portal, OCR, base de datos, revisión manual, aprobación, pagos e informes.
- Permitir que las colas absorban picos de carga sin perder eventos.
- Escalar los workers de OCR y validación de forma independiente.
- Separar los datos transaccionales de los datos de reporting.
- Registrar trazabilidad completa de cada paso y facilitar auditoría.




  

# 🤖 n8n Workflow: Human-in-the-Loop (HITL) con Bots (simulados) y Notificaciones

Este repositorio contiene una implementación avanzada de un patrón **Human-in-the-Loop (HITL)** desarrollado en **n8n**. El flujo simula la gestión de casos clínicos donde una IA (simulada) propone una recomendación y un humano debe validar, editar o rechazar la acción antes de su ejecución final.

> ⚠️ **Nota Educativa:** Este proyecto es parte de una práctica técnica. No debe utilizarse para decisiones clínicas reales en entornos de producción.

## 📋 El Concepto (HITL)
En sistemas de misión crítica o alta sensibilidad (como la salud o finanzas), la automatización total presenta riesgos éticos y operativos. Este workflow implementa una **política de supervisión humana** que:
1. **Anonimiza** datos sensibles mediante hashing.
2. **Evalúa el riesgo** del caso para definir tiempos de espera.
3. **Escala a un supervisor** automáticamente si el revisor inicial no responde (Fallback seguro).

## 🛠️ Arquitectura del Workflow
El flujo está organizado de forma lógica en columnas para asegurar la mantenibilidad y escalabilidad:

- **Validación & Seguridad:** Webhook de entrada -> Validación de campos -> Anonimización de PatientID (Hashing).
- **Capa de Decisión IA:** Evaluación de términos sensibles y asignación de políticas de riesgo (Critical, High, Medium, Low).
- **Intervención Humana:** Notificación al revisor (vía Webhook/Slack) -> Generación de `approvalUrl` única -> Nodo **Wait** (espera asincrónica).
- **Resolución & Fallback:** Procesamiento de decisión (Aprobado/Editado/Rechazado) o escalamiento automático por **Timeout**.

## 📊 Métricas Operativas (Observabilidad)
El sistema no solo procesa el caso, sino que genera un log estructurado para auditoría con las siguientes métricas:
- `latencyMs`: Tiempo total del proceso.
- `humanResponseMs`: Tiempo de reacción del revisor humano.
- `fallbackUsed`: Booleano que indica si se activó el escalamiento automático.
- `riskScore`: Nivel de riesgo detectado por la lógica de IA.

## 🚀 Configuración
El entorno corre sobre **Docker** y utiliza variables de entorno para una fácil portabilidad.

### Requisitos
- n8n (Docker Compose)
- Postman (para simulación de carga)

### Variables de Entorno (.env)
```env
NOTIFICATION_WEBHOOK_URL=[https://tu-url-de-notificacion.com](https://tu-url-de-notificacion.com)
SUPERVISOR_WEBHOOK_URL=[https://tu-url-de-escalamiento.com](https://tu-url-de-escalamiento.com)
REVIEW_BASE_URL=http://localhost:5684
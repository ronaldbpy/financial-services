---
name: agente-whatsapp
description: Levanta un agente de WhatsApp con IA para un negocio, usando AgentKit. Usar cuando pida "armar un agente de WhatsApp", "un bot para el negocio", "que conteste los mensajes solo", "chatbot de WhatsApp", o cuando un cliente quiera automatizar la atención por WhatsApp.
---

# Agente de WhatsApp con AgentKit

AgentKit genera un agente de WhatsApp completo a partir de una entrevista. No lo escribís
vos: contestás diez preguntas y Claude Code arma el proyecto.

**Repo:** https://github.com/Hainrixz/whatsapp-agentkit — MIT, de @soyenriquerocha.

---

## El flujo

```bash
git clone https://github.com/Hainrixz/whatsapp-agentkit.git
cd whatsapp-agentkit
bash start.sh          # chequea Python 3.11+ y Claude Code
claude
```

Y adentro de Claude Code:

```
/build-agent
```

Ahí arranca la entrevista.

---

## Las diez preguntas — preparalas antes

Si las tenés contestadas de antemano, el armado baja de 30 minutos a 10. Pedíselas al
cliente **antes** de sentarte:

1. Nombre del negocio
2. A qué se dedica
3. Para qué quiere el agente — contestar preguntas, agendar, tomar pedidos
4. Nombre del agente (el que van a ver los clientes)
5. Tono — profesional, amigable, vendedor, empático
6. Horario de atención
7. Archivos del negocio: menú, precios, FAQ → van en `/knowledge`
8. API key de Anthropic
9. Proveedor de WhatsApp — Meta o Twilio
10. Credenciales de ese proveedor

---

## Qué genera

```
agent/
  main.py         servidor que recibe los mensajes
  brain.py        la conexión con Claude
  memory.py       historial por cliente
  tools.py        las herramientas del negocio
  providers/      Meta o Twilio
config/
  business.yaml   los datos del negocio
  prompts.yaml    la personalidad del agente
knowledge/        los archivos que subiste
tests/test_local.py   simulador de chat en la terminal
```

---

## Antes de dárselo a un cliente

- [ ] **Probalo en el simulador** (`tests/test_local.py`) antes de conectarlo a WhatsApp
      de verdad.
- [ ] **La API key va en `.env`**, nunca en el código ni en `business.yaml`.
- [ ] **Un reclamo tiene que salir a un humano.** Definí la derivación antes de prender.
- [ ] **Meta cobra por mensaje** desde julio de 2025. Lo que entra es gratis, y las
      respuestas dentro de las 24 horas también. Las plantillas de marketing y todo lo
      que salga fuera de esa ventana se paga.
- [ ] Si usás la API oficial de Meta, el número **no puede estar activo en la app de
      WhatsApp** al mismo tiempo.

---

## Lo que este kit NO resuelve

- La aprobación del número y las plantillas en Meta. Eso lleva días y es aparte.
- El seguimiento comercial. El agente contesta; vender lo sigue haciendo una persona.

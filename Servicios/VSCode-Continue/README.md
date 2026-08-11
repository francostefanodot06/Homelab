# 💻 VS Code + Continue (Qwen 2.5 Coder)

Documentación de la integración entre **Visual Studio Code** y el servicio de inferencia local de **Ollama** ejecutado en el homelab.

Esta configuración permite utilizar **Qwen 2.5 Coder 14B** directamente desde el entorno de desarrollo como asistente de código, agente autónomo, autocompletado en tiempo real (*Tab completion*) y refactorización inline, delegando toda la carga de procesamiento a la GPU del servidor.

---

## 🎯 Objetivo

El objetivo de esta integración es disponer de un entorno de asistencia de desarrollo tipo "GitHub Copilot" 100% privado, autohospedado y sin restricciones ni cuotas de uso de servicios en la nube.

Al delegar la inferencia a la GPU dedicada (**AMD Radeon RX 9060 XT de 16 GB**) del homelab, se libera espacio de VRAM y memoria RAM en la workstation local.

---

## 🏗️ Arquitectura

La arquitectura conecta las extensiones cliente dentro de VS Code directamente con el socket de la API REST de Ollama en el servidor local.

```text
Workstation (VS Code)
  │
  ├── Extension: Continue (Chat, Inline, Autocomplete)
  └── Extension: Roo Code / Cline (Agente autónomo)
        │
        ▼  [HTTP / API REST :11434]
Homelab (LXC Ubuntu + GPU Passthrough)
  │
  ├── systemd: ollama.service (OLLAMA_HOST=0.0.0.0)
  └── GPU: AMD Radeon RX 9060 XT (16 GB VRAM)
        │
        ▼
  Modelos: qwen2.5-coder:14b / qwen2.5-coder:1.5b

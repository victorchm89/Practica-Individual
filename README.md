# Tarea Individual – Módulo 6 (Exploit the Binary)

> Práctica basada en el repositorio *Exploit-the-Binary* y el temario del módulo.

## Objetivo
Documentar una **metodología** de análisis/explotación de binarios y **2 casos prácticos** (mínimo), más una **aproximación a 0-days** (fuzzing/diffing/reversing) en un entorno de laboratorio controlado.

## Estructura
```text
.
├── README.md
├── cases/
│   ├── 01_stack_overflow_x86/
│   │   ├── inputs/
│   │   ├── scripts/
│   │   └── screenshots/
│   └── 02_format_string/
│       ├── inputs/
│       ├── scripts/
│       └── screenshots/
├── lab/
└── scripts/

[200~


#!/usr/bin/env bash
set -euo pipefail

# === Te pedirá tu usuario de GitHub (no email) ===
read -rp "👉 Tu usuario de GitHub: " victorchm89
REPO_NAME="modulo6-exploit-the-binary"
REMOTE="https://github.com/${victorchm89}/${REPO_NAME}.git"

# Asegúrate de estar dentro del repo recién inicializado
if [ ! -d ".git" ]; then
  echo "No veo .git aquí. Entra en la carpeta del repo e inténtalo de nuevo."
  exit 1
fi

# 1) Estructura
mkdir -p cases/01_stack_overflow_x86/{inputs,scripts,screenshots}
mkdir -p cases/02_format_string/{inputs,scripts,screenshots}
mkdir -p lab scripts

# 2) README principal
cat > README.md <<'EOF'
# Tarea Individual – Módulo 6 (Exploit the Binary)

> Práctica basada en el repositorio *Exploit-the-Binary* y el temario del módulo.

## Objetivo
Documentar una **metodología** de análisis/explotación de binarios y **2 casos prácticos** (mínimo), más una **aproximación a 0-days** (fuzzing/diffing/reversing) en un entorno de laboratorio controlado.

## Estructura
```text
.
├── README.md
├── cases/
│   ├── 01_stack_overflow_x86/
│   │   ├── inputs/
│   │   ├── scripts/
│   │   └── screenshots/
│   └── 02_format_string/
│       ├── inputs/
│       ├── scripts/
│       └── screenshots/
├── lab/
└── scripts/
````

## Metodología (resumen)

1. Detección (fuzzing/inputs límite).
2. Contexto (SO/arch, mitigaciones).
3. Triage y reproducción del crash.
4. Análisis raíz (reversing/flujo de datos).
5. Explotación (primitivas → control de flujo → payload).
6. Validación (fiabilidad/variantes).
7. Documentación y recomendaciones.

## Casos previstos

* **01 – Stack-based buffer overflow (x86, Windows)**.
* **02 – Format string + info leak**.

## Aproximación a 0-days (pipeline)

Fuzzing (WinAFL/AFL++/boofuzz) → minimización → triage → root-cause → PoC limpia + mitigaciones.

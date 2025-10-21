# Práctica Individual – Módulo 6 (Exploit the Binary)

Trabajo individual basado en el repo **Exploit-the-Binary** y el temario del módulo. El objetivo es **documentar metodología**, **dos casos prácticos** de explotación de binarios y una **aproximación a 0-days** (fuzzing/diffing/reversing), todo en **laboratorio controlado**.

---

## 1) Alcance y ética
- Todo el trabajo se realiza en **VM de laboratorio** con binarios vulnerables o diseñados para prácticas.
- **Prohibido** atacar sistemas reales sin permiso. La entrega incluye **recomendaciones de mitigación**.

## 2) Entorno de laboratorio
**SO recomendado:** Windows 10/11 (x86/x64) en VM con snapshot.  
**Herramientas base:**
- Depuración/Reversing: **x64dbg**, **Ghidra/IDA Free**.
- Python 3 + **pwntools** (automatización de exploits).
- **ROPgadget** / herramientas ROP.
- Fuzzing: **WinAFL / AFL++ / boofuzz** (según caso).
- Utilidades: Process Monitor/Explorer, `checksec`-like, **BinDiff/Diaphora** (diffing).
- Control de versiones: **git**.

> Mantén una nota en `lab/` con versiones, protecciones detectadas (DEP/ASLR/CFG/Canaries), y snapshots.

## 3) Estructura del repositorio
```text
.
├── README.md
├── cases/
│   ├── 01_stack_overflow_x86/
│   │   ├── inputs/         # corpus/payloads de prueba
│   │   ├── scripts/        # exploit.py, helpers, harness
│   │   └── screenshots/    # evidencias (crash, EIP/RIP, etc.)
│   └── 02_format_string/
│       ├── inputs/
│       ├── scripts/
│       └── screenshots/
├── lab/                    # notas de entorno, mitigaciones, versiones
└── scripts/                # utilidades generales (pattern.py, etc.)

## 4) Metodología (pipeline)
Detección: fuzzing/inputs límite, revisión estática ligera.

Contexto: SO/arquitectura, mitigaciones activas, superficie de entrada.

Triage: crash reproducible y estable, firmas de excepción, call stack.

Control: medir offset, confirmar control de EIP/RIP/SEH y estabilidad.

Root cause: reversing guiado, análisis de flujo de datos/punteros.

Explotación: construir primitivas (RW/exec), ROP/JOP/ret2libc según mitigaciones.

Validación: fiabilidad, variantes del entorno (ASLR aleatorio, reinicios).

Documentación: PoC clara, impacto, mitigación y riesgos residuales.

5) Casos prácticos
Caso 01 — Stack-based Buffer Overflow (x86, Windows)
Objetivo: controlar EIP y ejecutar un payload de prueba (p.ej., calc) o ROP con DEP.
Pasos sugeridos:

Fuzzing de la entrada → crash.

Generar patrón cíclico y hallar offset:

bash
Copiar código
python scripts/pattern.py 600
Confirmar control de EIP y estabilidad del crash.

Enumerar módulos/gadgets (ASLR, SafeSEH, DEP/CFG).

Construir payload mínimo → versión robusta (ROP si procede).

Evidencias: capturas (crash, registros, ejecución controlada) en screenshots/.

Mitigación: validación de longitudes, safe APIs, compilación con protecciones.

Entrega mínima del caso:

cases/01_stack_overflow_x86/scripts/exploit.py

cases/01_stack_overflow_x86/inputs/*

cases/01_stack_overflow_x86/screenshots/*

cases/01_stack_overflow_x86/README.md con cómo reproducir.

Caso 02 — Format String + Info Leak
Objetivo: abusar de %x/%p/%n para leer/escribir memoria. Obtener leaks que permitan derrotar ASLR y pivotar a ROP.
Pasos sugeridos:

Forzar lecturas con %x/%p y mapear offsets de stack.

Identificar punteros útiles → calcular base de módulos.

Construir ROP/ret2libc si DEP/ASLR activos.

Evidencias: leak → cálculo de direcciones → ejecución controlada.

Mitigación: eliminar inputs como formato, usar snprintf/vsnprintf, validación estricta.

Entrega mínima del caso:

cases/02_format_string/scripts/exploit.py

cases/02_format_string/inputs/*

cases/02_format_string/screenshots/*

cases/02_format_string/README.md con cómo reproducir.

6) Aproximación a 0-days (fuzzing/diffing/reversing)
Fuzzing: elegir target y protocolo/CLI; diccionarios semánticos; medir cobertura.
Minimización de crashes: cmin/tmin o reducción manual de inputs.
Triage: agrupar por firma de excepción y backtrace.
Root cause: reversing / taint manual / diffing (BinDiff/Diaphora) entre versiones.
Exploitability: convertir crash en primitiva (info leak / RW / control de flujo).
Reporte: PoC mínima, impacto, mitigaciones y repro steps claros.

Incluye logs/capturas de fuzzing y un mini-informe en lab/.

7) Bypass de mitigaciones (hoja de ruta)
DEP/NX → ROP/JOP, VirtualProtect/VirtualAlloc vía gadgets.

ASLR → info leak, módulos no aleatorizados, cálculos relativos.

Stack Canaries → evitar sobrescritura directa / leak de canario (solo en lab).

CFG/SafeSEH → gadgets válidos, llamadas permitidas, diferencias x86/x64.

8) Evidencias y criterios de corrección
Reproducibilidad: pasos “cómo reproducir” por caso + scripts listos.

Pruebas: capturas de crash, control de registros, ejecución del payload.

Calidad: PoC clara, comentarios en código, manejo de errores.

Mitigación: propuesta técnica y justificación.

9) Cómo ejecutar (ejemplos)
bash
Copiar código
# Generar patrón cíclico de 600 bytes
python scripts/pattern.py 600

# Ejecutar exploit de un caso
python cases/01_stack_overflow_x86/scripts/exploit.py
10) Bibliografía y recursos
Write-ups y documentación de herramientas utilizadas (añadir enlaces).

Referencias del temario del módulo.

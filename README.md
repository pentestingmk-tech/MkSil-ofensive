# MKSIL V7.8 — OFFENSIVE MASTER

<p align="center">
<img src="logo.png" alt="MKSIL" width="200"/>
<br>
<strong>Framework ofensivo autónomo de reconocimiento y evaluación de superficie de ataque</strong>
</p>

MKSIL es un orquestador ofensivo con interfaz gráfica tipo terminal que
**automatiza el ciclo completo de reconocimiento**: escaneo de puertos,
fingerprinting de servicios, detección de vulnerabilidades, fuzzing de
directorios, correlación de exploits y análisis estratégico asistido por IA —
todo desde una única ventana, con salida en vivo y reporte final exportable.

Diseñado para pruebas de penetración, auditorías de seguridad y *Red Team*,
combina las mejores herramientas de la industria bajo un flujo de trabajo
unificado y sin intervención manual entre fases.

---

## ✨ Características

- **Escaneo dual de puertos**:
  - `FAST` — detección rápida de servicios (`nmap -sV -T4`)
  - `STEALTH` — modo sigiloso con fragmentación de paquetes (`nmap -sS -T2 -f`)
- **Detección de vulnerabilidades** con **Nuclei** en dos pasadas:
  - Pasada 1: plantillas `critical, high, medium` sobre cada servicio web.
  - Pasada 2: plantillas específicas `cve, rce, lfi, sqli, xss, ssrf, wordpress`.
- **Fuzzing de directorios** con **Feroxbuster** sobre rutas web detectadas.
- **Correlación de exploits** con **SearchSploit** (`--nmap` para mapear
  versiones de servicio a exploits existentes).
- **Agente táctico** (`TacticalAgent`): puntúa cada servicio detectado,
  clasifica la exposición (`LOW/MEDIUM/HIGH/CRITICAL`) y produce un
  **SENIOR PENTEST STRATEGY REPORT** ordenado por riesgo.
- **AI ADVISOR**: análisis de estrategia de ataque generado por IA local
  (**Ollama + llama3**) con el contexto real del escaneo.
- **Detección del SO por TTL**: calcula el sistema operativo del objetivo a
  partir del TTL del ping (Linux/*BSD=64, Windows=128, Solaris/Cisco=255) e
  incorpora la clasificación en vivo en la barra de estado, en el reporte del
  agente táctico y en el informe HTML. Si el host no responde a ICMP, cae
  limpio sin errores.
- **Ejecución 100 % en paralelo/background**: la UI nunca se bloquea.
- **Reporte HTML profesional** autocontenido, listo para entregar como
  evidencia de auditoría (SO, puertos, servicios, vulns, rutas, exploits,
  estrategia IA, risk score).

---

## 🔧 Requisitos

| Herramienta | Propósito |
|---|---|
| Python 3.8+ | Motor principal |
| `tkinter` | Interfaz gráfica |
| `Pillow` (`PIL`) | Carga del logo |
| `nmap` | Escaneo de puertos y versiones |
| `nuclei` | Detección de vulnerabilidades |
| `feroxbuster` | Fuzzing de directorios web |
| `searchsploit` | Correlación de exploits |
| `wordlists/dirb/common.txt` | Wordlist de fuzzing (default) |
| `ollama` (opcional) | Advisor IA local (modelo `llama3`) |

### Instalación

```bash
# Dependencias de Python
pip install Pillow ollama

# Herramientas de sistema (Debian/Kali/Ubuntu)
sudo apt update && sudo apt install -y nmap feroxbuster exploitdb dirb
# nuclei:
#   go install -v github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest

# Instalar el logo en la ruta esperada (opcional)
sudo mkdir -p /usr/local/share/mksil
sudo cp logo.png /usr/local/share/mksil/

# (Opcional) Advisor IA local
curl -fsSL https://ollama.com/install.sh | sh
ollama pull llama3
```

---

## 🚀 Uso

```bash
python3 mksil
```

1. Escribí el **TARGET** (IP o dominio).
2. Elegí el modo: `FAST` (rápido) o `STEALTH` (sigiloso).
3. Presioná **DEPLOY MISSION**.
4. Seguí el progreso en vivo por pestañas y esperá el reporte final.

> Si `ollama` no está instalado o el modelo no está disponible, el **AI ADVISOR**
> cae automáticamente a un análisis táctico local heurístico (sin errores).

### Modos de escaneo

| Modo | Comando invisible | Uso recomendado |
|---|---|---|
| `FAST` | `nmap -n -Pn -sV -T4 --open` | Auditorías autorizadas, entornos estables |
| `STEALTH` | `nmap -n -Pn -sS -T2 -f --data-length 24` | Evasión elemental, entornos sensibles |

En modo `STEALTH` también se aplica `rate-limit` a nuclei y limitación de
hilos/rate a feroxbuster, manteniendo un perfil bajo.

---

## 🗂️ Pestañas de resultados

| # | Pestaña | Contenido |
|---|---|---|
| 1 | `PUERTOS` | Salida del escaneo nmap en vivo |
| 2 | `SERVICIOS` | Puertos abiertos + producto/versión |
| 3 | `NUCLEI` | Hallazgos de vulnerabilidad (critical/high/medium) |
| 4 | `FEROX` | Rutas y directorios descubiertos |
| 5 | `EXPLOITS` | Correlación searchsploit por puerto |
| 6 | `AGENT` | Reporte de estrategia con risk score |
| 7 | `AI ADVISOR` | Plan de ataque generado por IA |
| 8 | `REPORT` | Exporta el informe HTML |

La barra de estado muestra en vivo: tiempo transcurrido, tamaño del XML de
nmap, cantidad de puertos abiertos, el **SO detectado por TTL** y el estado del
proceso.

---

## 📊 Scoring táctico

El agente asigna un **riesgo por puerto/servicio** según reglas de senior:

| Regla | Puntos |
|---|---|
| Servicio HTTP | +5 |
| SMB / puerto 445 | +8 |
| Base de datos expuesta | +6 |
| SSH | +3 |
| Exploit conocido para el puerto | +2 c/u (máx +10) |
| Vuln crítica de nuclei coincidente | +10 |

La exposición se clasifica como `CRITICAL ≥ 20`, `HIGH ≥ 15`, `MEDIUM ≥ 8`,
`LOW` en otro caso; y se resume en un **RISK SCORE** global del objetivo.

---

## 🚨 Advertencia de uso responsable

MKSIL está destinado **exclusivamente a auditorías de seguridad y pruebas de
penetración con autorización previa por escrito** del propietario del sistema.

- Escanear infraestructura sin autorización es ilegal en la mayoría de las
  jurisdicciones y constituye un delito.
- En muchos entornos (por ejemplo, infraestructura crítica, organismos
  públicos u operadores de servicios esenciales) aplican normativas
  específicas que exigen habilitación previa del ente titular.
- El autor no se hace responsable por el uso indebido de esta herramienta.
  Usuarios internacionales: verifiquen la legislación local aplicable a su
  país y jurisdicción antes de ejecutar cualquier prueba.

**Nada de lo que haga MKSIL debe ejecutarse contra sistemas que no sean de
tu propiedad o que no cuentes con autorización expresa para auditar.**

---

## 🛡️ Permisos (payload) y alcance

MKSIL respeta el principio de mínima intrusión:

- No instala drivers ni payloads.
- No explota vulnerabilidades por sí mismo: **reconoce, detecta y correlaciona**
  para orientar al pentester.
- El reporte exportado es autocontenido (un único `.html`) para facilitar el
  archivo y la revisión del cliente/auditor.

---

## 📁 Reporte de salida

El informe final se guarda en:

```
~/mksil_output/AUDIT_<target>.html
```

Incluye: objetivo, **RISK SCORE**, estrategia priorizada, inventario
puerto→servicio→exposición, hallazgos nuclei, rutas de feroxbuster, exploits
correlacionados y la estrategia del AI advisor.

---

## ⚙️ Configuración

Los valores por defecto pueden ajustarse editando las constantes al inicio
del script:

```python
REPORT_DIR = os.path.expanduser("~/mksil_output")          # carpeta de reportes
WORDLIST   = "/usr/share/wordlists/dirb/common.txt"         # wordlist de fuzzing
IMAGE_PATH = "/usr/local/share/mksil/logo.png"              # logo
```

---

## 📌 Roadmap sugerido

- [ ] Módulo de escalada de privilegios post-explotación.
- [ ] Soporte multi-target (batch/host list).
- [ ] Tema claro/oscuro y exportación PDF.
- [ ] Integración de credenciales/bruteforce (hydra) opcional.
- [ ] Modo `SAFE` (solo fingerprinting sin fuzzing).

---

## 👤 Autor

MKSIL fue desarrollado y probado en entornos de auditoría autorizados como
parte de un flujo profesional de pentesting y Red Team.

**Licencia:** uso educativo y auditorías autorizadas. Mantener los créditos.
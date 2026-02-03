# VULNEX
- https://github.com/vulnex/BlueTeamWireless

# 🛡️ Workshop: Detección de Ataques Wireless con IDS
## Guía de Preparación para Estudiantes

---

## 📅 Información del Workshop

**Duración:** 2 horas  
**Nivel:** Intermedio  
**Tema:** Detección de ataques WiFi usando sistemas IDS (Kismet y Nzyme)

---

## 🎯 Objetivos del Workshop

Al finalizar este workshop serás capaz de:
- Configurar y utilizar Kismet como IDS wireless
- Configurar y utilizar Nzyme 2.x para defensa WiFi
- Detectar ataques de deautenticación (deauth flood)
- Detectar ataques de Evil Twin (puntos de acceso falsos)
- Identificar dispositivos de ataque como Flipper Zero y WiFi Pineapple

---

## 💻 Requisitos del Sistema

### Sistema Operativo
| SO | Compatibilidad | Notas |
|----|----------------|-------|
| Linux (Ubuntu 22.04+) | ✅ Recomendado | Mejor soporte para wireless |
| Linux (Kali) | ✅ Excelente | Herramientas preinstaladas |
| Linux (Debian 12+) | ✅ Compatible | Funciona correctamente |
| Windows con WSL2 | ⚠️ Limitado | Sin acceso a USB WiFi |
| macOS | ❌ No compatible | No soporta monitor mode |

### Hardware Mínimo
- **RAM:** 4 GB (8 GB recomendado)
- **CPU:** 2 cores (4 cores recomendado)
- **Disco:** 10 GB libres
- **Puerto USB:** Para adaptador WiFi externo

---

## 📡 Adaptador WiFi USB (OBLIGATORIO)

⚠️ **IMPORTANTE:** Las tarjetas WiFi internas de los portátiles generalmente NO soportan modo monitor. Necesitas un adaptador USB externo.

### Adaptadores Recomendados

| Chipset | Modelo | Precio Aprox. | 2.4 GHz | 5 GHz | Dónde Comprar |
|---------|--------|---------------|---------|-------|---------------|
| Atheros AR9271 | Alfa AWUS036NHA | 25-35€ | ✅ | ❌ | Amazon, AliExpress |
| Ralink RT3070 | Alfa AWUS036NH | 20-30€ | ✅ | ❌ | Amazon, AliExpress |
| Realtek RTL8812AU | Alfa AWUS036ACH | 45-60€ | ✅ | ✅ | Amazon |
| MediaTek MT7612U | Panda PAU09 | 35-45€ | ✅ | ✅ | Amazon |

### Opción Económica
Si tienes presupuesto limitado, busca en AliExpress adaptadores con chipset **RT3070** o **AR9271**. Cuestan entre 8-15€ y funcionan perfectamente para el workshop.

**Palabras clave para buscar:** "RT3070 USB WiFi", "AR9271 monitor mode", "Kali Linux WiFi adapter"

### Verificar Compatibilidad
Si ya tienes un adaptador USB WiFi, puedes verificar si soporta modo monitor:

```bash
# En Linux, conecta el adaptador y ejecuta:
iw list | grep -A 10 "Supported interface modes"

# Debe mostrar "monitor" en la lista
```

---

## 🐳 Instalación de Docker (ANTES del Workshop)

### ⚠️ IMPORTANTE: Docker Engine, NO Docker Desktop

**Docker Desktop NO funcionará** para este workshop porque ejecuta los contenedores en una máquina virtual que no puede acceder a los adaptadores WiFi USB.

Debes instalar **Docker Engine** nativo en Linux.

### Instalación en Ubuntu/Debian

```bash
# 1. Eliminar Docker Desktop si lo tienes instalado
sudo apt remove docker-desktop

# 2. Actualizar repositorios
sudo apt update

# 3. Instalar Docker Engine
sudo apt install -y docker.io docker-compose-v2

# 4. Añadir tu usuario al grupo docker
sudo usermod -aG docker $USER

# 5. IMPORTANTE: Cerrar sesión y volver a entrar
#    O reiniciar el ordenador

# 6. Verificar instalación
docker --version
docker compose version
```

### Instalación en Kali Linux

```bash
# Docker ya viene en los repositorios de Kali
sudo apt update
sudo apt install -y docker.io docker-compose-v2

sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar

# Verificar
docker --version
```

### Verificar que Docker Funciona

```bash
# Ejecutar contenedor de prueba
sudo docker run hello-world

# Si ves "Hello from Docker!" la instalación es correcta
```

---

## 🔧 Herramientas Adicionales (Opcionales)

Instala estas herramientas en tu sistema Linux para diagnóstico:

```bash
sudo apt update
sudo apt install -y \
    iw \
    wireless-tools \
    aircrack-ng \
    net-tools \
    tcpdump
```

---

## 📋 Checklist Pre-Workshop

Marca cada punto cuando lo hayas completado:

```
[ ] Sistema operativo Linux instalado (Ubuntu, Debian, o Kali)
[ ] Mínimo 4 GB de RAM disponible
[ ] Mínimo 10 GB de disco libre
[ ] Adaptador WiFi USB con soporte para modo monitor
[ ] Docker Engine instalado (NO Docker Desktop)
[ ] Comando "docker --version" funciona correctamente
[ ] Comando "sudo docker run hello-world" funciona
[ ] Puerto USB disponible para el adaptador WiFi
```

---

## ❓ Preguntas Frecuentes

### ¿Puedo usar una máquina virtual?
**No recomendado.** Las máquinas virtuales tienen problemas para pasar dispositivos USB WiFi al sistema guest. Si es tu única opción, usa VMware Workstation (no VirtualBox) con USB passthrough habilitado.

### ¿Funciona en Windows con WSL2?
**No.** WSL2 no puede acceder a adaptadores WiFi USB. Necesitas Linux nativo o dual-boot.

### ¿Puedo usar la WiFi interna de mi portátil?
**Probablemente no.** La mayoría de tarjetas internas (Intel, Broadcom, Qualcomm) no soportan modo monitor o tienen soporte muy limitado. Usa un adaptador USB externo.

### ¿Qué pasa si no tengo adaptador WiFi?
Podrás seguir la parte teórica del workshop, pero no podrás hacer los ejercicios prácticos de captura. Considera comprar un adaptador económico (~10-15€ en AliExpress).

### ¿Necesito conexión a Internet durante el workshop?
**Sí**, para descargar la imagen Docker (~2-3 GB) al inicio del workshop. Después, la mayor parte del trabajo es local.

---

## 🆘 Problemas Comunes

### "Permission denied" al ejecutar Docker
```bash
# Solución: Añadir usuario al grupo docker
sudo usermod -aG docker $USER
# Luego cerrar sesión y volver a entrar
```

### El adaptador WiFi no aparece en Linux
```bash
# Verificar que el sistema lo detecta
lsusb | grep -i wireless

# Si no aparece, prueba otro puerto USB
# Si aparece pero no funciona, puede faltar el driver
```

### "Docker Desktop is running" en Linux
```bash
# Docker Desktop interfiere con Docker Engine
# Desinstalar Docker Desktop:
sudo apt remove docker-desktop
```

---

## 📧 Contacto

Si tienes problemas con la preparación, contacta al instructor antes del workshop para resolverlos con tiempo.

https://github.com/vulnex/BlueTeamWireless

---

## 📚 Recursos Adicionales (Lectura Opcional)

Si quieres prepararte más, puedes leer sobre:

- **Kismet:** https://www.kismetwireless.net/docs/
- **Nzyme:** https://www.nzyme.org/
- **Ataques WiFi comunes:** Deauth, Evil Twin, Karma Attack
- **802.11 Basics:** Beacons, Probe Requests, Management Frames

---

**¡Nos vemos en el workshop!** 🚀

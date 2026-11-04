# 🛡️ Blue Team Wireless Workshop

Workshop materials for wireless network security and intrusion detection using Kismet and Nzyme IDS.

**Author:** Simon Roses Femerling / VULNEX  
**Website:** https://www.vulnex.com

---

## 📖 About This Workshop

This is a comprehensive 2-hour intermediate-level workshop focused on detecting wireless attacks using IDS (Intrusion Detection Systems). Participants will learn to configure and use Kismet and Nzyme 2.x to detect common WiFi attacks including:

- Deauthentication flood attacks
- Evil Twin access points
- Rogue access points
- Attacks from devices like Flipper Zero and WiFi Pineapple

## 📁 Repository Structure

```
├── configs/
│   └── kismet_site.conf          # Kismet configuration for detection
├── labs/
│   ├── LAB1-kismet-setup.md      # Kismet installation and configuration
│   ├── LAB2-nzyme-setup.md       # Nzyme IDS setup
│   ├── LAB3-detect-deauth.md     # Deauth attack detection
│   ├── LAB4-detect-evil-twin.md  # Evil Twin detection
│   └── LAB5-detect-rogue-ap.md   # Rogue AP detection
├── reference/
│   └── CHEATSHEET.md             # Quick reference guide
├── nzyme-docker-workshop.zip      # Docker setup for Nzyme
├── PREPARACION_WORKSHOP_WIRELESS_IDS.md  # Workshop preparation guide (Spanish)
├── VULNEX_BlueTeamWireless.pdf   # Workshop slides
└── README.md                      # This file
```

## 🎯 Prerequisites

### System Requirements
- **Operating System:** Linux (Ubuntu 22.04+, Debian 12+, or Kali Linux recommended)
- **RAM:** Minimum 4 GB (8 GB recommended)
- **CPU:** 2 cores minimum (4 cores recommended)
- **Disk Space:** 10 GB free
- **USB Port:** For external WiFi adapter

### Required Hardware
⚠️ **IMPORTANT:** An external USB WiFi adapter with monitor mode support is **required**. Internal laptop WiFi cards generally do NOT support monitor mode.

**Recommended chipsets:**
- Atheros AR9271 (e.g., Alfa AWUS036NHA)
- Ralink RT3070 (e.g., Alfa AWUS036NH)
- Realtek RTL8812AU (e.g., Alfa AWUS036ACH)
- MediaTek MT7612U (e.g., Panda PAU09)

### Software Requirements
- **Docker Engine** (NOT Docker Desktop - must be native Linux Docker)
- Basic wireless tools: `iw`, `wireless-tools`, `aircrack-ng`

## 🚀 Quick Start

### 1. Install Docker Engine (Linux native)

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install -y docker.io docker-compose-v2
sudo usermod -aG docker $USER
# Log out and log back in

# Verify
docker --version
docker compose version
```

### 2. Install Wireless Tools

```bash
sudo apt update
sudo apt install -y iw wireless-tools aircrack-ng net-tools tcpdump
```

### 3. Verify WiFi Adapter

```bash
# Check if your adapter supports monitor mode
iw list | grep -A 10 "Supported interface modes"
# Should show "monitor" in the list
```

### 4. Follow the Labs

Start with `labs/LAB1-kismet-setup.md` and proceed sequentially through the labs.

## 📚 Documentation

- **[Workshop Preparation Guide (Spanish)](PREPARACION_WORKSHOP_WIRELESS_IDS.md)** - Detailed setup instructions
- **[Workshop Slides](VULNEX_BlueTeamWireless.pdf)** - Complete workshop presentation
- **[Cheat Sheet](reference/CHEATSHEET.md)** - Quick reference for commands and alerts

## 🔗 Additional Resources

- **Kismet Documentation:** https://www.kismetwireless.net/docs/
- **Nzyme Documentation:** https://www.nzyme.org/
- **VULNEX:** https://www.vulnex.com
- **GitHub Repository:** https://github.com/vulnex/BlueTeamWireless

## 📝 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.

---
---

# 🛡️ Workshop Blue Team Wireless

Materiales de workshop sobre seguridad de redes inalámbricas y detección de intrusiones usando Kismet y Nzyme IDS.

**Autor:** Simon Roses Femerling / VULNEX  
**Sitio web:** https://www.vulnex.com

---

## 📖 Sobre Este Workshop

Este es un workshop integral de 2 horas de nivel intermedio enfocado en la detección de ataques inalámbricos usando IDS (Sistemas de Detección de Intrusiones). Los participantes aprenderán a configurar y utilizar Kismet y Nzyme 2.x para detectar ataques WiFi comunes incluyendo:

- Ataques de inundación de deautenticación (deauth flood)
- Puntos de acceso Evil Twin (gemelos malignos)
- Puntos de acceso no autorizados (Rogue AP)
- Ataques desde dispositivos como Flipper Zero y WiFi Pineapple

## 📁 Estructura del Repositorio

```
├── configs/
│   └── kismet_site.conf          # Configuración de Kismet para detección
├── labs/
│   ├── LAB1-kismet-setup.md      # Instalación y configuración de Kismet
│   ├── LAB2-nzyme-setup.md       # Configuración de Nzyme IDS
│   ├── LAB3-detect-deauth.md     # Detección de ataques deauth
│   ├── LAB4-detect-evil-twin.md  # Detección de Evil Twin
│   └── LAB5-detect-rogue-ap.md   # Detección de Rogue AP
├── reference/
│   └── CHEATSHEET.md             # Guía de referencia rápida
├── nzyme-docker-workshop.zip      # Configuración Docker para Nzyme
├── PREPARACION_WORKSHOP_WIRELESS_IDS.md  # Guía de preparación del workshop
├── VULNEX_BlueTeamWireless.pdf   # Diapositivas del workshop
└── README.md                      # Este archivo
```

## 🎯 Requisitos Previos

### Requisitos del Sistema
- **Sistema Operativo:** Linux (Ubuntu 22.04+, Debian 12+, o Kali Linux recomendado)
- **RAM:** Mínimo 4 GB (8 GB recomendado)
- **CPU:** Mínimo 2 núcleos (4 núcleos recomendado)
- **Espacio en Disco:** 10 GB libres
- **Puerto USB:** Para adaptador WiFi externo

### Hardware Requerido
⚠️ **IMPORTANTE:** Se **requiere** un adaptador WiFi USB externo con soporte para modo monitor. Las tarjetas WiFi internas de los portátiles generalmente NO soportan modo monitor.

**Chipsets recomendados:**
- Atheros AR9271 (ej., Alfa AWUS036NHA)
- Ralink RT3070 (ej., Alfa AWUS036NH)
- Realtek RTL8812AU (ej., Alfa AWUS036ACH)
- MediaTek MT7612U (ej., Panda PAU09)

### Requisitos de Software
- **Docker Engine** (NO Docker Desktop - debe ser Docker nativo en Linux)
- Herramientas inalámbricas básicas: `iw`, `wireless-tools`, `aircrack-ng`

## 🚀 Inicio Rápido

### 1. Instalar Docker Engine (nativo en Linux)

```bash
# Ubuntu/Debian
sudo apt update
sudo apt install -y docker.io docker-compose-v2
sudo usermod -aG docker $USER
# Cerrar sesión y volver a entrar

# Verificar
docker --version
docker compose version
```

### 2. Instalar Herramientas Inalámbricas

```bash
sudo apt update
sudo apt install -y iw wireless-tools aircrack-ng net-tools tcpdump
```

### 3. Verificar Adaptador WiFi

```bash
# Verificar si tu adaptador soporta modo monitor
iw list | grep -A 10 "Supported interface modes"
# Debe mostrar "monitor" en la lista
```

### 4. Seguir los Laboratorios

Comienza con `labs/LAB1-kismet-setup.md` y procede secuencialmente a través de los laboratorios.

## 📚 Documentación

- **[Guía de Preparación del Workshop](PREPARACION_WORKSHOP_WIRELESS_IDS.md)** - Instrucciones detalladas de configuración
- **[Diapositivas del Workshop](VULNEX_BlueTeamWireless.pdf)** - Presentación completa del workshop
- **[Hoja de Referencia](reference/CHEATSHEET.md)** - Referencia rápida para comandos y alertas

## 🔗 Recursos Adicionales

- **Documentación de Kismet:** https://www.kismetwireless.net/docs/
- **Documentación de Nzyme:** https://www.nzyme.org/
- **VULNEX:** https://www.vulnex.com
- **Repositorio GitHub:** https://github.com/vulnex/BlueTeamWireless

## 📝 Licencia

Este proyecto está licenciado bajo la Licencia Apache 2.0 - ver el archivo [LICENSE](LICENSE) para más detalles.

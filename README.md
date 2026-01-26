# 📡 ESPectre
### by tech_logix

🎥 **Enlace al reel:**  
_(añadir aquí)_

Este repositorio recopila los **pasos mínimos** para instalar y poner en funcionamiento **ESPectre**, un sistema **open-source de detección de movimiento** basado en **perturbaciones de señales Wi-Fi**, ejecutado sobre microcontroladores ESP32.

---

## 🔗 Repositorio original del proyecto

👉 https://github.com/francescopace/espectre

---

## ⚙️ Requisitos

### Hardware
- Placa compatible:
  - ESP32
  - ESP32-C3
  - ESP32-S3
- Cable USB (USB-C o Micro-USB según la placa)
- Red Wi-Fi 2.4 GHz

### Software
- Docker instalado (para usar ESPHome en contenedor)
- Navegador moderno (Chrome / Edge recomendados)

---

## 🛠️ Instalación básica

### 1️⃣ ESPHome Web (flasheo inicial)

Accede al flasher web oficial de ESPHome:

👉 https://web.esphome.io/

Desde ahí podrás:
- Conectar la placa por wifi
- Conectar la placa por USB
- Flashear un binario
- Recuperar placas en blanco

> **Nota**: Darle al boton de Logs una vez este conectado a la wifi para extraer la direccion ip
---

### 2️⃣ Descargar el YAML de ESPectre y el docker-compose para levantar los servicios

> **Nota**: El docker-compose lo tienes en los archivos del proyecto

Descarga el archivo de configuración que corresponda a tu modelo de ESP32:

👉 https://github.com/francescopace/espectre/blob/main/SETUP.md#2-download-a-configuration-file

Guarda el archivo `.yaml` dentro de la carpeta que tengas montada como volumen en tu contenedor de ESPHome (normalmente `/config`).

---

### 3️⃣ Compilar el binario con ESPHome en Docker

Desde tu máquina, ejecuta:

```bash
docker exec -it NOMBRE_DEL_CONTENEDOR esphome run /config/tu_archivo.yaml
```


Ejemplo:
```bash
docker exec -it esphome esphome run /config/espectre-c3.yaml
```

📌 Importante:
El archivo .yaml debe estar dentro del volumen montado en el contenedor (normalmente /config), o ESPHome no podrá compilarlo.

---

### 4️⃣ Localizar el firmware generado

Una vez termine la compilación, el binario se guarda en:

```bash
.esphome/build/<nombre_del_nodo>/.pioenvs/<nombre_del_nodo>/
```

El archivo que debes usar es:

```bash
firmware.factory.bin
```

---

### 5️⃣ Flashear el binario

Vuelve a ESPHome Web:

👉 https://web.esphome.io/

Selecciona:

 - “Install”

- Carga el archivo firmware.factory.bin

- Flashea la placa y espera a que termine.

---

### ✅ Resultado

A partir de aquí ya tendrás:

- ESPectre instalado en tu ESP32

- Acceso a la placa por red

- Sensores de movimiento Wi-Fi activos

## Integracion con Home Assintant

---

### Pasos

1. **Abre tu panel de Home Assistant.**

2. **Ve a Ajustes → Dispositivos y servicios.**

3. **Arriba a la derecha, pulsa el botón “+ Añadir integración”.**

4. **Busca ESPHome.**

5. **Selecciónalo e introduce la IP de la placa.**

6. **Acepta y espera unos segundos.**

Home Assistant detectará el dispositivo y añadirá automáticamente todos los sensores y controles de ESPectre.

---

A partir de ese momento ya podrás ver:

- Movement score

- Estado IDLE / MOTION

- Calidad del movimiento

- Botones de control

- Calibración de la habitación

---

## Calibrar sensor

> **Nota**: Antes de usar ESPectre es muy recomendable calibrar el entorno. Esto le permite aprender cómo se comporta la señal Wi-Fi cuando no hay movimiento.

**Pasos mínimos para calibrar**

1. **Deja la habitación vacía y sin movimiento.**

2. **Espera unos segundos a que los valores se estabilicen.***

3. **Pulsa el botón Calibrate.**

4. **No entres ni te muevas hasta que termine.**

5. **Al finalizar, ese estado se tomará como referencia base.**

6. **A partir de ese momento, cualquier cambio se comparará contra esa referencia.**

---
## ¿Para qué sirve el botón Calibrate?

El botón Calibrate hace que ESPectre analice la señal Wi-Fi durante unos segundos y construya un modelo de cómo es la habitación cuando está en reposo.

Define lo que el sistema considera “estado normal”.
Todo el movimiento posterior se mide en relación a esa referencia.

---

## ¿Para qué sirve el Threshold?

El threshold es el umbral dinámico que separa el ruido normal del entorno del movimiento real.

Si la perturbación Wi-Fi supera el threshold → ESPectre marca MOVIMIENTO.

Si no lo supera → sigue considerándose IDLE, aunque haya pequeñas variaciones.

No es un número fijo: se ajusta automáticamente según el entorno y la calibración.

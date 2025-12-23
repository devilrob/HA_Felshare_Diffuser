# Felshare (Cloud MQTT) — Home Assistant Custom Integration

> **Unofficial / Unaffiliated / Not Endorsed**  
> This is a community-made *custom integration* for Home Assistant. It is **not** an official Felshare product and is **not** endorsed by Felshare or Home Assistant.

This integration helps you control **Felshare smart diffusers** (and compatible devices) via the **Felshare Cloud**:
- Sign-in via Felshare cloud API to obtain a session token
- Connect to **MQTT over WebSockets (TLS)** (cloud)
- Publish/receive device status and commands

---

## Legal & Responsible Use (READ FIRST)

### No affiliation / trademarks
- “Felshare” and related names/logos are **trademarks of their respective owners**.  
- This project is **independent** and provided for interoperability/automation purposes only.  
- Do not claim sponsorship, partnership, or endorsement by Felshare or Home Assistant.

### No warranty / limitation of liability
This project is provided **“AS IS”**, without warranty of any kind (express or implied), including (but not limited to) merchantability, fitness for a particular purpose, and non‑infringement.

To the maximum extent permitted by law, the authors/contributors shall not be liable for any claim, damages, or other liability arising from the use of this software (including but not limited to device malfunction, property damage, lost profits, data loss, service outages, or security incidents).

### Compliance & acceptable use
By using this integration, **you** are responsible for ensuring your use:
- Complies with all applicable laws and regulations
- Complies with Felshare’s terms of service, acceptable use policy, and privacy policy (if any)
- Is limited to devices and accounts that you **own or are authorized to control**
- Does not violate any contractual obligations (e.g., reverse engineering restrictions)

> **Note:** This integration may use endpoints and message formats that are undocumented and may change at any time. If Felshare modifies their cloud platform, the integration may stop working.

### Safety notice
This integration is for **convenience automation** only. Do **not** rely on it for any safety‑critical use cases. You are responsible for safe operation of the diffuser (oils, ventilation, fire safety, pets/children, etc.).

### Privacy & credentials
- You provide your Felshare account credentials to Home Assistant during configuration.  
- Home Assistant stores credentials and tokens according to its internal storage mechanisms.  
- You are responsible for securing your Home Assistant instance and network.
- **Recommendation:** use a dedicated Felshare account with minimal privileges if possible, and avoid sharing logs that contain tokens/IDs.

---

## Features

- Diffuser **Power** On/Off
- **Fan** control (if your model supports it)
- **Oil Name** / fragrance label
- Oil metrics:
  - **Consumption (ml/h)**
  - **Oil Capacity (ml)**
  - **Remaining Oil (ml)**
  - **Liquid Level (%)** sensor
- **WorkTime** scheduling:
  - Enable/disable schedule
  - Start/end time (HH:MM)
  - Work duration (seconds) and pause (seconds)
  - Days of the week (individual switches)
- Diagnostics:
  - **MQTT Status**
  - **Work Schedule Info** (schedule summary text)

---

## Requirements

- Home Assistant (Core / Supervised / OS)
- A Felshare account (the same one used in the mobile app)
- Your device added to that account in the Felshare app
- Outbound internet access to `app.felsharegroup.com` on **443/TCP**

> **Important:** This is **cloud-based** (not local). If the cloud service goes down or changes, this integration may stop working.

---

## Installation (Manual)

1. Copy:
   ```
   custom_components/felshare
   ```
   into:
   ```
   <your-home-assistant-config>/custom_components/felshare
   ```

2. Restart Home Assistant.

---

## Configuration (UI)

1. Go to **Settings → Devices & Services → Add Integration**
2. Search for **Felshare (Cloud MQTT)**
3. Enter:
   - **Email**
   - **Password**
4. Select your **Device ID** from the list

Home Assistant will create a device “Felshare <device_id>” with entities.

---

## Created Entities

### Switches
- `switch.<...>_power` — **Power**
- `switch.<...>_fan` — **Fan**
- `switch.<...>_00_work_schedule` — **Work Schedule**
- `switch.<...>_05_work_day_mon` … `switch.<...>_05_work_day_sun` — **Work Day <Day>**

### Text
- `text.<...>_oil_name` — **Oil Name**
- `text.<...>_01_work_start` — **Work Start (HH:MM)**
- `text.<...>_02_work_end` — **Work End (HH:MM)**

### Number
- `number.<...>_consumption` — **Consumption (ml/h)**
- `number.<...>_capacity` — **Oil Capacity (ml)**
- `number.<...>_remain_oil` — **Remaining Oil (ml)**
- `number.<...>_03_work_run_s` — **Work Run (seconds)**
- `number.<...>_04_work_stop_s` — **Work Stop (seconds)**

### Sensor
- `sensor.<...>_liquid_level` — **Liquid Level (%)**
- `sensor.<...>_mqtt_status` — **MQTT Status** (diagnostic)
- `sensor.<...>_work_schedule` — **Work Schedule Info** (diagnostic)

---

## Using “WorkTime” Scheduling

1. Enable the **Work Schedule** switch
2. Set:
   - **Work Start (HH:MM)** (e.g., `09:00`)
   - **Work End (HH:MM)** (e.g., `21:00`)
3. Set:
   - **Work Run (seconds)** (e.g., `30`)
   - **Work Stop (seconds)** (e.g., `190`)
4. Enable the desired **Work Day** switches

> The device typically expects a full schedule payload when any value changes. The integration re-sends a complete payload to apply changes consistently.

---

## Troubleshooting

### Entities show `unknown` or don’t update
- Confirm the diffuser is **online** in the Felshare app
- Confirm HA has working internet (DNS + outbound 443)
- Open the Felshare app and enter the device screen at least once; the integration may “learn” the initial sync payload and store it under `.storage`
- Restart Home Assistant or reload the integration

### Enable debug logs
Add to `configuration.yaml`:

```yaml
logger:
  default: info
  logs:
    custom_components.felshare: debug
```

Restart Home Assistant and check **Settings → System → Logs**.

---

## Technical Summary (for transparency)

- Cloud API (Login): `http://app.felsharegroup.com:7001/login`
- Device List: `http://app.felsharegroup.com:7001/device` (`token` header)
- MQTT over WebSockets (TLS):
  - Host: `app.felsharegroup.com`
  - Port: `443`
  - Path: `/mqtt`
  - Topics:
    - RX: `/device/rxd/<device_id>`
    - TX: `/device/txd/<device_id>`

---

## Support

This is a community project. Support is best-effort:
- Please open issues with logs (**redact tokens/emails/device IDs**)
- PRs are welcome

---

## License

See the `LICENSE` file in this repository.  
If no license file is present yet, **add one** (e.g., MIT/Apache-2.0) before publishing/distributing the code.

---

---

# Felshare (Cloud MQTT) — Integración Custom para Home Assistant

> **No oficial / No afiliado / Sin respaldo**  
> Esta integración es comunitaria y **no** es un producto oficial de Felshare, ni está respaldada por Felshare o Home Assistant.

Esta integración permite controlar **difusores inteligentes Felshare** (y compatibles) usando el **cloud de Felshare**:
- Login por API cloud para obtener token
- Conexión a **MQTT por WebSockets (TLS)** en el cloud
- Publicación/recepción de estado y comandos

---

## Aviso legal y uso responsable (LEER PRIMERO)

### Sin afiliación / marcas registradas
- “Felshare” y nombres/logos relacionados son **marcas registradas de sus dueños**.
- Este proyecto es **independiente** y existe solo para interoperabilidad/automatización.
- No afirmes patrocinio, asociación o respaldo por parte de Felshare o Home Assistant.

### Sin garantía / limitación de responsabilidad
Este proyecto se ofrece **“tal cual”**, sin garantía de ningún tipo (expresa o implícita), incluyendo (entre otras) comerciabilidad, idoneidad para un propósito particular y no infracción.

En la máxima medida permitida por la ley, los autores/colaboradores no serán responsables por daños o pérdidas derivadas del uso del software (incluyendo fallos del dispositivo, daños materiales, pérdidas económicas, pérdida de datos, caídas del servicio o incidentes de seguridad).

### Cumplimiento y uso permitido
Al usar esta integración, **tú** eres responsable de asegurar que tu uso:
- Cumpla con las leyes y regulaciones aplicables
- Cumpla con los términos/condiciones y políticas de Felshare (si aplican)
- Sea solo con dispositivos/cuentas que **te pertenecen o tienes autorización para controlar**
- No viole obligaciones contractuales (por ejemplo, restricciones de ingeniería inversa)

> **Nota:** El cloud y sus endpoints/formats pueden cambiar sin aviso. Si Felshare cambia la plataforma, la integración puede dejar de funcionar.

### Seguridad
Esta integración es solo para **automatización de conveniencia**. No la uses para escenarios críticos. Eres responsable del uso seguro del difusor (aceites, ventilación, seguridad contra incendios, mascotas/niños, etc.).

### Privacidad y credenciales
- Proporcionas tus credenciales de Felshare a Home Assistant durante la configuración.
- Home Assistant almacena credenciales/tokens según sus mecanismos internos.
- Tú eres responsable de asegurar tu instancia de Home Assistant y tu red.
- **Recomendación:** usa una cuenta dedicada de Felshare con permisos mínimos si es posible, y no compartas logs con tokens/IDs.

---

## Funciones

- Encendido/apagado (**Power**)
- Control de ventilador (**Fan**) si el modelo lo soporta
- **Oil name** (fragancia)
- Métricas de aceite:
  - **Consumption (ml/h)**
  - **Oil capacity (ml)**
  - **Remain oil (ml)**
  - **Liquid level (%)**
- Horario **WorkTime**:
  - Activar/desactivar
  - Hora inicio/fin (HH:MM)
  - Tiempo de trabajo y pausa (segundos)
  - Días de semana (switch por día)
- Diagnóstico:
  - **MQTT status**
  - **Work schedule info**

---

## Requisitos

- Home Assistant (Core / Supervised / OS)
- Cuenta Felshare
- Dispositivo agregado en la app Felshare
- Salida a internet hacia `app.felsharegroup.com` por **443/TCP**

> **Importante:** Es cloud (no local). Si el cloud cae o cambia, puede dejar de funcionar.

---

## Instalación (manual)

1. Copia:
   ```
   custom_components/felshare
   ```
   dentro de:
   ```
   <tu_config_de_homeassistant>/custom_components/felshare
   ```

2. Reinicia Home Assistant.

---

## Configuración (UI)

1. **Settings → Devices & Services → Add Integration**
2. Busca **Felshare (Cloud MQTT)**
3. Ingresa Email/Password
4. Selecciona el **Device ID**

---

## Soporte

Proyecto comunitario (best‑effort).  
Al reportar problemas, **oculta tokens/emails/device IDs**.

---

## Licencia

Revisa el archivo `LICENSE`.  
Si aún no existe, **agrega una licencia** (MIT/Apache‑2.0) antes de publicar/distribuir.

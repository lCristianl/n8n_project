# Proyecto n8n - ChatBot WhatsApp

Este proyecto contiene workflows de n8n para un chatbot de WhatsApp y sistema de notificaciones.

## 📋 Requisitos previos

- Docker y Docker Compose instalados
- Dominio configurado apuntando al servidor
- Meta Developer Account con WhatsApp Business API configurada
- Google Cloud Console Account con API configurada
- Gemini con Token configurado

### 1. Configurar variables de entorno

Editar el archivo `docker-compose.yml`:

**Reemplazar las siguientes variables:**
- `cambiar-dominio.com` → Tu dominio real (ej: `chatbot.tikee.com`)
- `contraseña_segura` → Una contraseña segura para el admin

### 2. Ejecutar el contenedor

```bash
# Levantar el servicio
docker-compose up -d

# Verificar que esté funcionando
docker ps
docker logs n8n
```

### 3. Importar workflows y activarlos

**⚠️ IMPORTANTE: Ejecutar estos comandos después de que el contenedor esté corriendo:**

```bash
# Importar workflows desde la carpeta /workflows
docker compose exec -u node n8n n8n import:workflow --separate --input=/workflows

# Activar todos los workflows importados
docker compose exec -u node n8n n8n update:workflow --all --active=true
```

## 🔧 Configuración de Credenciales WhatsApp

**👇👇Video de ayuda para configurar las credenciales de los nodos "WhatsApp Trgigger" y "WhatApp Send Message"👇👇**

`https://www.youtube.com/watch?v=jYnqJmTHGtI`

### En Meta for Developers:

1. Ve a tu aplicación de WhatsApp Business
2. En la sección **WhatsApp /Configuracion de la API**, configura:
   - **Número de Prueba**: (en caso de querer otro del que viene por defecto)
   - **Para**: Este será el número al cual se le entregarán los mensajes
   - **URL del Webhook**: `https://tu-dominio.com/webhook/321b4b98-9ede-4cf9-a7c5-e01edaaa4791`
3. En el nodo de WhatsApp trigger agrega

## 🔧 Configuración de Gemini Credenciales
En el nodo de Google Gemini Chat Model agregar la credencial con el token de Gemini.

## 🔧 Configuración de Google Drive Credenciales

**👇👇Video de ayuda para configurar las credenciales del nodo "Google Drive"👇👇**

`https://www.youtube.com/watch?v=H6Xkc9cfdrE`

Agregar las credenciales de Google Drive y cambiar el siguiente parametro del nodo:
    
- **File (By ID)**: (agregar el id del archivo excel de drive)

**Para sacar el id del archivo excel en Drive se debe abrir el archivo y en la url se debe sacar la siguiente parte de la url: (ID_DEL_ARCHIVO)**

`https://docs.google.com/spreadsheets/d/ID_DEL_ARCHIVO/edit`

**Nota:**

No importa en qué cuenta esté el archivo, lo importante es que la cuenta con la que conectaste n8n tenga acceso (como Editor o al menos Lector).

Si el archivo está en otra cuenta de Drive, simplemente compártelo con la cuenta que usaste para autenticar n8n (con el correo de esa cuenta de Google).

## 🔧 Configuración de Gmail Credenciales
    En las credenciales del nodo Send a message de Gmail agregar el mismo "Client ID" y "Client ID" que se agrego en el nodo Drive y en los parametros del nodo cambiar el correo electronico de la persona que le llegará el Email.

### Verificar conectividad:

```bash
# Probar webhook desde el servidor
curl -X GET https://tu-dominio.com/webhook-test/321b4b98-9ede-4cf9-a7c5-e01edaaa4791
```

## 📱 Acceso a la interfaz

- **URL**: `https://tu-dominio.com`
- **Usuario**: `admin`
- **Contraseña**: La que configuraste en `N8N_BASIC_AUTH_PASSWORD`

## 🔍 Comandos útiles de mantenimiento

```bash
# Ver logs en tiempo real
docker logs -f n8n

# Reiniciar el contenedor
docker-compose restart

# Parar el servicio
docker-compose down

# Backup de datos
docker run --rm -v proyecto_n8n_n8n_data:/data -v $(pwd):/backup alpine tar czf /backup/n8n_backup.tar.gz /data

# Restaurar backup
docker run --rm -v proyecto_n8n_n8n_data:/data -v $(pwd):/backup alpine tar xzf /backup/n8n_backup.tar.gz -C /

# Ver estado del contenedor
docker ps
docker-compose ps
```

## 📂 Estructura del proyecto

```
Proyecto_n8n/
├── docker-compose.yml      # Configuración de Docker
├── workflows/             # Workflows de n8n
│   ├── ChatBot.json       # Workflow del chatbot WhatsApp
│   └── Notificaciones.json # Workflow de notificaciones
└── README.md             # Este archivo
```

## 🛠️ Solución de problemas comunes

### Error "Invalid parameter" en WhatsApp Trigger
- Verificar que la URL del webhook sea accesible públicamente
- Confirmar que el dominio apunte correctamente al servidor
- Revisar logs: `docker logs n8n`

### Webhook no responde
```bash
# Verificar conectividad
curl -I https://tu-dominio.com/healthz

# Verificar puerto abierto
sudo netstat -tlnp | grep :5678
```

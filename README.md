# 🐧 Ejercicio de Ubuntu - Directivas, Usuarios, Servicios y Permisos

---

## 1. 🔐 Directiva de contraseñas

Lo primero que vamos a hacer para tener una contraseña robusta es instalar `pam_pwquality`, que es un módulo de PAM (Pluggable Authentication Module) que se encarga de verificar la calidad de las contraseñas cuando se cambian o crean nuevas.

> Está integrado con PAM, que es el sistema que gestiona la autenticación en Linux.

### 🧩 Instalación

```bash
sudo apt install libpam-pwquality
```

> Como estamos en modo normal `$`, tenemos que usar `sudo`. Si estuviéramos en modo root `#`, no haría falta.

---

### 🛠️ Configurar directiva de contraseñas

Editamos el archivo `/etc/security/pwquality.conf`, que es la configuración central de `pam_pwquality`.

```bash
sudo nano /etc/security/pwquality.conf
```

Cambia o añade las siguientes líneas:

```ini
minlen = 11            # Mínimo 11 caracteres
ucredit = -1           # Al menos 1 mayúscula
lcredit = -1           # Al menos 1 minúscula
dcredit = -1           # Al menos 1 número
retry = 3              # Número de intentos
reject_username        # No permitir usar el nombre de usuario
badwords = password contrasena   # Palabras prohibidas
```

Guarda con `Ctrl+O`, Enter, y sal con `Ctrl+X`.

---

### ✅ Comprobar que se aplica

Edita el archivo `/etc/pam.d/common-password` y asegúrate de que contiene esta línea:

```bash
sudo nano /etc/pam.d/common-password
```

Busca una línea como:

```bash
password requisite pam_pwquality.so retry=3
```

> Si no está, las reglas de `pwquality.conf` **no se aplican**.

---

## 2. 👤 Crear usuario `Usuario2`

### Paso 2.1: Crear usuario con home `cpu2`

```bash
sudo useradd -m -d /home/cpu2 -s /bin/bash Usuario2
```

Explicación:

- `useradd`: Crea un nuevo usuario
- `-m`: Crea la carpeta personal
- `-d /home/cpu2`: Define `/home/cpu2` como su carpeta personal
- `-s /bin/bash`: Usa Bash como shell
- `Usuario2`: Nombre del usuario

---

### Paso 2.2: Darle permisos de `sudo`

```bash
sudo usermod -aG sudo Usuario2
```

Explicación:

- `usermod`: Modifica un usuario existente
- `-aG sudo`: Añade (append) al grupo sudo sin quitar los grupos existentes

---

### Paso 2.3: Establecer fecha de expiración

```bash
sudo chage -E 2025-06-30 Usuario2
```

Explicación:

- `chage`: Cambia propiedades de expiración
- `-E`: Define la fecha de expiración (AAAA-MM-DD)

---

### Paso 2.4: Probar contraseñas inválidas

```bash
sudo passwd Usuario2
```

Contraseñas que deben fallar:

- `usuario2` (es igual al nombre de usuario)
- `password` (palabra prohibida)

Contraseña que debería funcionar:

- `Segura123Xy` (más de 10 caracteres, mayúscula, minúscula, número, sin palabras prohibidas)

![Captura de pantalla 2025-04-15 233637](https://github.com/user-attachments/assets/23c6d5c9-4114-4dc5-8505-828faf232325)

---

## 3. ⚙️ Comandos de servicios

### Paso 3.1: Iniciar MySQL
```bash
sudo systemctl start mysql
```

### Paso 3.2: Ver el estado de MySQL
```bash
sudo systemctl status mysql
```

### Paso 3.3: Parar MySQL
```bash
sudo systemctl stop mysql
```

### Paso 3.4: Activar e iniciar Bluetooth (un solo comando)
```bash
sudo systemctl enable --now bluetooth
```
> "Inicio automático" al activar el sistema

### Paso 3.5: Parar y desactivar servicio de red
```bash
sudo systemctl disable --now NetworkManager
```

---

## 4. 🔢 Completar tabla de permisos

| Formato texto | Formato numérico |
|---------------|------------------|
| rw- r-- r--   | 644              |
| rwx r-x ---   | 750              |
| r-x --x -w-   | 512              |
| rw- -wx ---   | 630              |

> El orden de los permisos es: **Usuario, Grupo y Otros**.
> r = lectura, w = escritura, x = ejecución.

---

## 5. 📄 Archivo `/etc/profile`

### a) Cambiar permisos para escribir como usuario normal

**Numérico:**
```bash
sudo chmod 644 /etc/profile
```

**Texto:**
```bash
sudo chmod o+w /etc/profile
```

### b) ¿Es buena idea permitirlo?

**No.**

- `/etc/profile` es un archivo **global del sistema**.
- Sirve para **cargar variables de entorno** y **ejecutar scripts** que afectan a **todos los usuarios**.

> Si cualquier usuario puede modificarlo, podría ejecutar scripts maliciosos al iniciar sesión.

**❌ Es un gran riesgo de seguridad.**



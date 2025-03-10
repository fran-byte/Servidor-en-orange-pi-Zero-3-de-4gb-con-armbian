# orange_pi_zero_3_4gb_armbian_server (in progress...)
Instalación de pequeño servidor Nginx en una Orange pi Zero 3 de 4gb

Vamos a seguir los pasos correctos para preparar la tarjeta SD para tu Orange Pi Zero 3 con 4GB de RAM usando la imagen de Armbian y los archivos de U-Boot y DTB. de los repositorios: https://github.com/leeboby/armbian-images

# Descargar la imagen:
oizero3 1GB2GB. 	Servidor de debian12  https://github.com/leeboby/armbian-images/releases/download/opizero3/Armbian_23.08.0-trunk_Orangepizero3_bookworm_current_6.1.31-1GB-2GB.img.xz

# Descargar los archivos archivos necesarios para instalar un bootloader y el árbol de dispositivos (DTB) :
OPi Zero3 1.5gb-4g.

4g.u-boot.bin: 4gb.u-boot.bin. https://github.com/leeboby/opizero3-uboot-kernel/blob/main/u-boot-sunxi-with-spl-opizero3-4gb.bin
4gb.dtb.: 4gb.dtb.  https://github.com/leeboby/opizero3-uboot-kernel/blob/main/sun50i-h616-orangepi-zero3-4gb.dtb



### Resumen de los pasos a seguir:

1. **Descomprimir la imagen `.xz`**.
2. **Grabar la imagen en la tarjeta SD**.
3. **Montar la tarjeta SD y copiar los archivos** de configuración.
4. **Escribir el bootloader (`u-boot`)** en la tarjeta SD.
5. **Desmontar y poner la tarjeta SD en la Orange Pi Zero 3**.

### Paso 1: Descomprimir la imagen `.xz`

Tienes la imagen comprimida **`Armbian_23.08.0-trunk_Orangepizero3_bookworm_current_6.1.31-1GB-2GB.img.xz`**, así que primero debes descomprimirla:

1. Abre una terminal y ve a la carpeta donde está la imagen `.xz`.
2. Descomprime la imagen con el siguiente comando:

   ```bash
   unxz Armbian_23.08.0-trunk_Orangepizero3_bookworm_current_6.1.31-1GB-2GB.img.xz
   ```

   Esto generará un archivo **`Armbian_23.08.0-trunk_Orangepizero3_bookworm_current_6.1.31-1GB-2GB.img`**.

### Paso 2: Grabar la imagen en la tarjeta SD

Ahora que tienes la imagen descomprimida, puedes grabarla en la tarjeta SD. Utiliza **`dd`** o una herramienta gráfica como **Raspberry Pi Imager**.

Si prefieres usar la terminal, primero identifica la tarjeta SD (asegurándote de que no es el disco duro principal) con el siguiente comando:

```bash
lsblk
```

Para mi tarjeta e 32gb y mi unidad sdb debería mostrarte algo como:

```
/dev/sdb  29.5G
```

Aquí, **`/dev/sdb`** es tu tarjeta SD. **¡Ten mucho cuidado con este paso!** Si seleccionas el disco equivocado, podrías sobrescribir tus datos importantes.

Luego, graba la imagen con **`dd`**: Importante Modifica sdb (por tu unidad, si no fuera esta)

```bash
sudo dd if=Armbian_23.08.0-trunk_Orangepizero3_bookworm_current_6.1.31-1GB-2GB.img of=/dev/sdb bs=4M status=progress conv=fsync
```

### Paso 3: Montar la tarjeta SD y copiar los archivos de configuración

Una vez que la imagen esté grabada en la tarjeta SD, **monta la partición de arranque** para copiar los archivos de **U-Boot** y **DTB**.

1. Monta la partición de arranque de la tarjeta SD: (cambia sdb1 por tu unidad)

   ```bash
   sudo mount /dev/sdb1 /mnt
   ```

   (Asegúrate de que `/dev/sdb1` es la partición correcta, que normalmente es la **partición de arranque** de la tarjeta SD).

2. Copia el archivo **`sun50i-h616-orangepi-zero3-4gb.dtb`** a la carpeta **`/boot/dtb/allwinner/`**:

   ```bash
   sudo cp sun50i-h616-orangepi-zero3-4gb.dtb /mnt/boot/dtb/allwinner/sun50i-h616-orangepi-zero3.dtb
   ```

### Paso 4: Escribir el bootloader (`u-boot`) en la tarjeta SD

1. **Escribe el archivo de bootloader** `u-boot-sunxi-with-spl-opizero3-4gb.bin` en la tarjeta SD usando **`dd`**:
2.  Reemplaza **`/dev/sdb`** por el dispositivo correcto

   ```bash
   sudo dd bs=1k seek=8 if=u-boot-sunxi-with-spl-opizero3-4gb.bin of=/dev/sdb
   ```

  
   - **El parámetro `seek=8` asegura que el bootloader se grabe correctamente** a partir del lugar adecuado en la tarjeta SD.

### Paso 5: Desmontar la tarjeta SD y ponerla en la Orange Pi Zero 3

Una vez que hayas copiado los archivos y actualizado el bootloader:

1. Desmonta la tarjeta SD:

   ```bash
   sudo umount /mnt
   ```

2. **Coloca la tarjeta SD en tu Orange Pi Zero 3** y enciéndelo.

Ahora deberías poder arrancar tu **Orange Pi Zero 3** utilizando la imagen de **Armbian** y los archivos de configuración correctos para **4GB de RAM**.


# Arrancando el sistema

## Si no deseas conectar un monitor configuraremos a traves de SSH desde nuestro PC

---

### **1. Configurar la conexión de red (Wi-Fi o Ethernet)**
- **Si usas Ethernet**: Conecta la Orange Pi a tu router con un cable. Obtendrá IP automáticamente (DHCP).
- **Si usas Wi-Fi**:
  - Inserta la tarjeta microSD en tu computadora.
  - En la partición `boot` (primera partición), crea un archivo llamado `wpa_supplicant.conf` con este contenido:
     ```conf
     ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
     update_config=1
     country=TU_CODIGO_PAIS (ej: ES, MX, AR)

     network={
         ssid="NOMBRE_RED_WIFI"
         psk="CONTRASEÑA_WIFI"
         key_mgmt=WPA-PSK
     }
     ```
  - Guarda el archivo y extrae la tarjeta SD.

---

### **2. Obtener la IP de la Orange Pi**
- **Opción A (Recomendada)**: Revisa la lista de dispositivos conectados en tu router (busca "Orange Pi" o direcciones MAC que empiecen con `02:81` o similar).
- **Opción B**: Usa un escáner de IP en tu computadora:
  - **Linux/macOS**: 
    ```bash
    nmap -sn 192.168.1.0/24 # Ajusta la red según tu router
    ```

---

### **3. Conectar por SSH**
- **Usuario y contraseña predeterminados** de Armbian:
  - Usuario: `root` / Contraseña: `1234` (te pedirá cambiarla en el primer login).
  - Otra opción: `orangepi`/`orangepi` (depende de la imagen).
  
  ```bash
  ssh root@IP_DE_LA_ORANGE_PI
  ```
  Ejemplo:
  ```bash
  ssh root@192.168.1.144
  ```

---

### **4. Solucionar problemas comunes**
- **"Connection refused"**: Asegúrate de que el servicio SSH está activo. Crea un archivo vacío llamado `ssh` en la partición `boot` de la SD.
- **"Password incorrecto"**: Si no funciona `1234`, prueba con `orangepi` o revisa la documentación de tu imagen de Armbian.
- **No encuentra la IP**: Usa el comando `ping orangepi.local` (si tu red soporta mDNS).

---

### **5. Configuraciones posteriores (opcional)**
- **Cambiar contraseña**: Obligatorio tras el primer login.
- **Crear un usuario nuevo**:
  ```bash
  adduser nombre_usuario
  usermod -aG sudo nombre_usuario
  ```
- **Habilitar acceso sin contraseña (SSH Keys)**:
  ```bash
  ssh-copy-id nombre_usuario@IP_DE_LA_ORANGE_PI
  ```

---
# Instalanado servidor 

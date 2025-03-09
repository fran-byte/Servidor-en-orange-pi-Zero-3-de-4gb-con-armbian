# orange_pi_zero_3_4gb_armbian_server
Instalación de pequeño servidor Nginx en una Orange pi Zero 3 de 4gb


Vamos a seguir los pasos correctos para preparar la tarjeta SD para tu **Orange Pi Zero 3** con **4GB de RAM** usando la imagen de **Armbian** y los archivos de **U-Boot** y **DTB**. de los repositorios:
https://github.com/leeboby/armbian-images

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

Reemplaza **`/dev/sdX`** por el dispositivo correcto (por ejemplo, `/dev/sdb`).

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

¡Listo! Ahora deberías poder arrancar tu **Orange Pi Zero 3** con **4GB de RAM** utilizando la imagen de Armbian y los archivos de configuración correctos para **4GB de RAM**.

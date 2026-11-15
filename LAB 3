# lab-3 ==================================================================
 COMANDOS DE LAS PRACTICAS 1, 2 Y 3
 Kali Linux en VirtualBox (usuario: belen) + host Windows
 Cambia IP_DE_KALI por la IP real de tu VM (ejemplo: 192.168.1.50)
==================================================================


##################################################################
 PRACTICA 1
##################################################################

------------------------------------------------------------------
 1.1 GRUB con tiempo de espera de 20 segundos
------------------------------------------------------------------

# Abrir el archivo de configuracion de GRUB (necesita sudo porque es archivo del sistema)
sudo nano /etc/default/grub

# Dentro del archivo (Ctrl+W para buscar "GRUB_TIMEOUT"), dejar estas lineas:
#   GRUB_TIMEOUT=20
#   GRUB_TIMEOUT_STYLE=menu
# Guardar: Ctrl+O, Enter. Salir: Ctrl+X

# Aplicar el cambio (regenera /boot/grub/grub.cfg). Sin esto no se aplica
sudo update-grub

# Mostrar el resultado: muestra las lineas de timeout
grep -E "GRUB_TIMEOUT" /etc/default/grub

# Reiniciar para ver el menu de GRUB durante 20 segundos
sudo reboot


------------------------------------------------------------------
 1.2 Recuperar la contrasena de root desde GRUB
------------------------------------------------------------------

# Paso 1: reiniciar la VM. En el menu de GRUB seleccionar la primera
#         entrada y presionar la tecla "e" para editar.
#
# Paso 2: con las flechas, buscar la linea que empieza con "linux"
#         (contiene "ro quiet splash"). Ir al final (tecla End) y agregar:
#
#         rw init=/bin/bash
#
#         (si la linea tiene "ro", cambiarlo por "rw")
#
# Paso 3: presionar Ctrl+X (o F10) para arrancar.
#         Queda una consola: root@(none):/#

# Asegurar que el disco es escribible
mount -o remount,rw /

# Cambiar la contrasena de root
passwd root

# (Opcional) cambiar la contrasena de tu usuario
passwd belen

# Continuar el arranque normal
exec /sbin/init

# Si no funciona, reiniciar forzado
reboot -f


##################################################################
 PRACTICA 2
##################################################################

------------------------------------------------------------------
 2.1 Script de backup con TAR (nombre con fecha dd-mm-yyyy:hh:mm)
------------------------------------------------------------------

# Crear el script
nano ~/backup.sh

# ---- CONTENIDO DEL SCRIPT (copiar dentro de nano) ----
#!/bin/bash
# Script de backup de la carpeta /home/usuario

# Fecha y hora actual en formato dd-mm-yyyy:hh:mm
FECHA=$(date +"%d-%m-%Y:%H:%M")

# Ruta del backup (en /tmp para que no se incluya a si mismo)
DESTINO="/tmp/backup_$(whoami)_$FECHA.tar.gz"

# Crear el comprimido
tar -czvf "$DESTINO" -C /home "$(whoami)"

echo "Backup creado en: $DESTINO"
# ---- FIN DEL SCRIPT ----

# Explicacion:
#   date +"%d-%m-%Y:%H:%M"  -> dia, mes, ano, hora, minuto
#   $(whoami)               -> usuario actual (belen)
#   tar -czvf               -> c=crear, z=comprimir gzip, v=mostrar progreso, f=nombre del archivo
#   -C /home "belen"        -> entra a /home y comprime solo la carpeta del usuario

# Dar permisos de ejecucion
chmod +x ~/backup.sh

# Ejecutar el script
./backup.sh

# Mostrar el resultado (lista el backup con tamano y nombre con fecha y hora)
ls -lh /tmp/backup_*


------------------------------------------------------------------
 2.2 Script que guarda el resultado de ifconfig en el escritorio
------------------------------------------------------------------

# Instalar ifconfig si no existe (paquete net-tools)
sudo apt update && sudo apt install net-tools -y

# Crear el script
nano ~/ifconfig_guardar.sh

# ---- CONTENIDO DEL SCRIPT (copiar dentro de nano) ----
#!/bin/bash
# Guarda el resultado de ifconfig en un archivo del escritorio

# Preguntar el nombre del archivo
read -p "Que nombre desea ponerle al archivo? " NOMBRE

# Ruta del escritorio (si tu Kali esta en espanol, cambia Desktop por Escritorio)
ESCRITORIO="$HOME/Desktop"

# Ejecutar ifconfig y guardar el resultado
ifconfig > "$ESCRITORIO/$NOMBRE.txt"

echo "Archivo creado en: $ESCRITORIO/$NOMBRE.txt"
# ---- FIN DEL SCRIPT ----

# Explicacion:
#   read -p "..." NOMBRE  -> pregunta y guarda lo escrito en la variable NOMBRE
#   ifconfig > archivo    -> redirige la salida del comando al archivo

# Dar permisos y ejecutar
chmod +x ~/ifconfig_guardar.sh
./ifconfig_guardar.sh

# Mostrar el resultado (cambiar NOMBRE por el que escribiste)
cat ~/Desktop/NOMBRE.txt


##################################################################
 PRACTICA 3
##################################################################

------------------------------------------------------------------
 3.1 Modo Bridge y ping desde Windows
------------------------------------------------------------------

# En VirtualBox (con la VM apagada):
#   Configuracion -> Red -> Adaptador 1
#   Conectado a: "Adaptador puente (Bridged Adapter)"
#   Nombre: la tarjeta fisica de tu PC (Wi-Fi o Ethernet)
#   Aceptar y encender la VM

# Apagar la VM desde Kali (para cambiar la configuracion)
sudo poweroff

# [Kali] Ver la IP de la VM. Antes estaba en 10.0.2.15 (NAT).
# En Bridge debe ser del rango de tu red real, ej. 192.168.x.x
ip a

# [Kali] Alternativa para ver la IP
ifconfig

# [Kali] Si la IP no cambia, renovarla (libera la IP anterior y pide una nueva)
sudo dhclient -r eth0 && sudo dhclient eth0

# [Windows - PowerShell] Ver la IP de tu PC y confirmar que esta en el mismo rango
ipconfig

# [Windows - PowerShell] Probar ping hacia la VM
ping IP_DE_KALI


------------------------------------------------------------------
 3.2 Habilitar SSH y conectarse con usuario y contrasena
------------------------------------------------------------------

# [Kali] Habilitar SSH al inicio (enable) y arrancarlo ahora (--now)
sudo systemctl enable --now ssh

# [Kali] Verificar que este "active (running)". Salir con q
sudo systemctl status ssh

# [Windows - PowerShell] Conectarse a la VM (escribir yes la primera vez, luego la contrasena)
ssh belen@IP_DE_KALI

# Para salir de la sesion SSH
exit

# Si falla: en Kali verificar que exista "PasswordAuthentication yes"
sudo nano /etc/ssh/sshd_config
sudo systemctl restart ssh


------------------------------------------------------------------
 3.3 Generar par de llaves en Windows (passphrase en blanco)
------------------------------------------------------------------

# [Windows - PowerShell] Crear la carpeta .ssh si no existe
mkdir $env:USERPROFILE\.ssh

# Generar las llaves RSA de 4096 bits y definir el destino con -f
# Cuando pida "Enter passphrase", presionar Enter (en blanco) dos veces
ssh-keygen -t rsa -b 4096 -f $env:USERPROFILE\.ssh\id_rsa

# Mostrar que se crearon:
#   id_rsa      = llave PRIVADA (nunca se comparte)
#   id_rsa.pub  = llave PUBLICA (la que se copia al servidor)
dir $env:USERPROFILE\.ssh


------------------------------------------------------------------
 3.4 Copiar la llave publica al servidor Kali
------------------------------------------------------------------

# [Windows - PowerShell] Todo en una sola linea. Pedira la contrasena por ultima vez
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh belen@IP_DE_KALI "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"

# Explicacion:
#   type ... id_rsa.pub     -> lee la llave publica y la manda por el pipe |
#   mkdir -p ~/.ssh         -> crea la carpeta .ssh si no existe
#   chmod 700 / chmod 600   -> permisos estrictos (sin ellos SSH rechaza la llave)
#   cat >> authorized_keys  -> agrega la llave a la lista de llaves autorizadas


------------------------------------------------------------------
 3.5 Probar conexion sin contrasena
------------------------------------------------------------------

# [Windows - PowerShell] Debe entrar directo, sin pedir credenciales
ssh belen@IP_DE_KALI

# Si todavia pide contrasena, revisar en Kali:
ls -la ~/.ssh
cat ~/.ssh/authorized_keys


==================================================================
 FIN
==================================================================

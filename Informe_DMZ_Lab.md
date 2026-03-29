
# Informe de configuración de DMZ con Cisco Packet Tracer


### 1. Objetivo del laboratorio

> Diseñar y fortificar una arquitectura de red mediante la segmentación de zonas (LAN, DMZ y WAN). Se implementaron **ACLs (Access Control Lists) avanzadas** para controlar el flujo de tráfico. permitiendo únicamente peticiones web legítimas hacia la DMZ y bloqueando cualquier intento de comunicación no iniciada desde la zona de riesgo hacia la red interna.

**Ejemplo:**  
Configurar una DMZ segura usando un router Cisco ISR, aplicando NAT y ACLs para controlar el tráfico entre LAN, DMZ y red externa. 

---

### 2. Topología implementada.

> La red diseñada cuenta con una topología de **Estrella**, distribuida de la siguiente manera

<img width="653" height="402" alt="image" src="https://github.com/user-attachments/assets/0044d458-4558-4493-b5d6-58ee0a01a47c" />

- **Cantidad de redes**: 3
- **Dispositivos usados**: 7 dispositivos totales:
    - 2 PC´s, Internal y External.
    - 3 switch´s, uno para cada red.
    - 1 Servidor para la red DMZ.
    - 1 router.
- **Descripción de la función de las zonas que encontramos en nuestra red**:
    - *LAN*: Permitir el trabajo de los usuarios internos y la gestión de los recursos de la red.
    - *DMZ*: Exponer los servicios al exterios sin permitir acceso directo a la red interna por parte de un atacante.
    - *WAN*: Proveer conectividad global y permitir acceso de clientes externos al servidor web.


### 3. Plan de direccionamiento IP.


| Dispositivo             | IP              | Máscara           | Gateway           |
|-------------------------|------------------|-------------------|-------------------|
| PC_Internal             | 192.168.1.10     | 255.255.255.0     | 192.168.1.1       |
| Server_DMZ              | 192.168.2.10     | 255.255.255.0     | 192.168.2.1       |
| PC_External             | 192.168.3.10     | 255.255.255.0     | 192.168.3.1       |
| Router_FW Gi0/0 (LAN)   | 192.168.1.1      | 255.255.255.0     | N/A               |
| Router_FW Gi0/1 (DMZ)   | 192.168.2.1      | 255.255.255.0     | N/A               |
| Router_FW Gi0/2 (Ext)   | 192.168.3.1      | 255.255.255.0     | N/A               |


### 4. Configuración aplicada.

Se aplicaron las siguientes configuraciones. 

- Interfaces configuradas con `IP configuration`.

    - *PC_Internal*:
        - `IP Address`: 192.168.1.10
        - `Subnet Mask`: 255.255.255.0
        - `Default Gateway`: 192.168.1.1
    - *Server-PT Web_DMZ*: 
        - `IP Address`: 192.168.2.10
        - `Subnet Mask`: 255.255.255.0
        - `Default Gateway`: 192.168.2.1 
    - *PC_External*:
        - `IP Address`: 192.168.3.10
        - `Subnet Mask`: 255.255.255.0
        - `Default Gateway`: 192.168.3.1

- Interfaces configuradas con `CLI`.
```bash
    Router_FW# configure terminal
    !
    ! --- Configuración de la Interfaz LAN ---
    Router_FW(config)# interface GigabitEthernet0/0
    Router_FW(config-if)# ip address 192.168.1.1 255.255.255.0
    Router_FW(config-if)# no shutdown
    Router_FW(config-if)# exit
    !
    ! --- Configuración de la Interfaz DMZ ---
    Router_FW(config)# interface GigabitEthernet0/1
    Router_FW(config-if)# ip address 192.168.2.1 255.255.255.0
    Router_FW(config-if)# no shutdown
    Router_FW(config-if)# exit
    !
    ! --- Configuración de la Interfaz WAN (EXT) ---
    Router_FW(config)# interface GigabitEthernet0/2
    Router_FW(config-if)# ip address 192.168.3.1 255.255.255.0
    Router_FW(config-if)# no shutdown
    Router_FW(config-if)# exit
    !
    Router_FW(config)# end
    Router_FW# write memory
```

- Configuración de `ACLs`:
```bash
    Extended IP access list 101
        permit tcp any host 192.168.3.1 eq www
    Extended IP access list 102
        permit tcp 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255 established
        deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
        permit ip any any
```



### 5. Verificaciones realizadas

> Después de aplicar las configuraciones estos fueron los resultados de las verificaciones

- Acceso web desde PC_External: ✅
  <img width="606" height="399" alt="image" src="https://github.com/user-attachments/assets/3b346fdb-310f-4252-b735-392b5c408e30" />

- Bloqueo de ping externo: ✅
  <img width="589" height="340" alt="image" src="https://github.com/user-attachments/assets/a135c4d2-1ea1-4078-bbc0-8cd1ac71f04f" />

- Aislamiento de la DMZ: ✅
  <img width="473" height="471" alt="image" src="https://github.com/user-attachments/assets/6bd975aa-81ae-42a9-85e1-f3e66b03bc5c" />


### 6. Conclusiones y recomendaciones

> Aprendimos a configurar una red con los siguientes puntos clave:
- **Configuración de IP´s**: Utilizando IP estáticas y creando un subnetting de 3 segmentos, LAN, DMZ y WAN.
- **Aislamiento de Redes**: Se implemento un aislamiento que garantiza la integridad de la red *LAN interna* con las reglas de la **ACL 102**.
- **Seguridad Perimetral**: Se restringió el acceso desde la red externa únicamente al puerto **80(HTTP)**, mitigando riesgos de ataque.
- **Gestión de Configuración**: Comprendimos el uso de comandos como `show access-lists` y `write memory`para la gestión de configuraciones, asegurando un despliegue robusto.
- **RECOMENDACIONES**: Se recomienda revisar y comprobar que las reglas se van aplicando correctamente una por una para poder tener un control en el caso de que alguna falle o no se aplique adecuadamente.


### 7. Capturas de evidencia

> Las capturas solicitadas se encuentran a lo largo del informe con su respectivo paso de configuración.

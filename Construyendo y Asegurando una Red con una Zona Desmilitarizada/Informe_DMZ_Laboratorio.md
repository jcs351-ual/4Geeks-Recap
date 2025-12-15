# Informe de configuración de DMZ con Cisco Packet Tracer


> ### 1. Objetivo del laboratorio
>
> - Este laboratorio tiene como objetivo aprender a configurar una zona desmilitarizada (*DMZ*) haciendo uso de equipos de CISCO, así como la aplicación de listas de control de acceso (*ACLs*) y una red NAT, con objetivo de controlar el tráfico y/o nivel de acceso entre las redes interna y externa
>

---

> ### 2. Topología implementada
>
> Esta red consta de los siguientes equipos físicos.
>
> - Cantidad de redes: En este laboratorio la topología se ha dividido en 3 redes
> - Dispositivos usados: En este esquema de red se han usado los siguientes dispositivos:
>> - Dispositivos finales(*x3*), un ordenador en la red interna y otro en la red externa, así como un servidor web en la red de la zona desmilitarizada.
>>
>> - Switches Cisco 2960(*x3*), en cada una de las redes del esquema.
>> - Router Cisco ISR 2911(*x1*)
> - Breve descripción de la función de cada zona (LAN, DMZ, Externa).



### 3. Plan de direccionamiento IP

El direccionamiento que se ha usado en este laboratorio ha sido el siguiente:

| Dispositivo             | IP               | Máscara           | Gateway           |
|-------------------------|------------------|-------------------|-------------------|
| PC_Internal             | 192.168.1.10     | 255.255.255.0     | 192.168.1.1       |
| Server_DMZ              | 192.168.2.10     | 255.255.255.0     | 192.168.2.1       |
| PC_External             | 192.168.3.10     | 255.255.255.0     | 192.168.3.1       |
| Router_FW Gi0/0 (LAN)   | 192.168.1.1      | 255.255.255.0     |                   |
| Router_FW Gi0/1 (DMZ)   | 192.168.2.1      | 255.255.255.0     |                   |
| Router_FW Gi0/2 (Ext)   | 192.168.3.1      | 255.255.255.0     |                   |


### 4. Configuración aplicada (resumen)

La configuración realizada para conseguir el objetivo del laboratorio ha sido la siguiente:

- Interfaces configuradas con `ip address`:
```bash
interface Gig0/0
ip address 192.168.1.1 255.255.255.0

interface Gig0/1 
ip address 192.168.2.1 255.255.255.0

interface Gig0/2
ip address 192.168.3.1 255.255.255.0
```
- Configuración de la red NAT:
```bash
ip nat inside source static 192.168.2.10 192.168.3.1
```
- Configuración de las ACLs:
```bash
access-list 100 permit tcp any host 192.168.3.1 eq 80
access-list 100 deny icmp any 192.168.2.0 0.0.0.255
access-list 100 deny ip any 192.168.1.0 0.0.0.255
access-list 100 deny ip any 192.168.2.0 0.0.0.255

access-list 110 permit tcp any any established
access-list 110 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
access-list 110 permit ip 192.168.2.0 0.0.0.255 any
```


> ### 5. Verificaciones realizadas
>
> #### Desde *Pc_External*:
>
> - `ping` hacia *192.168.3.1* con resultado Fallido, porque se bloquea ICMP.
>
> - Acceso web al servidor DMZ (*192.168.3.1*) con resultado satisfactorio, la página carga.
>
> #### Desde *PC_Internal*:
>
> - Acceso web hacia el servidor DMZ (*192.168.2.10*) con resultado satisfactorio.
>
> #### Desde *Server-PT Web_DMZ*:
>
> - `ping` hacia *192.168.1.10* con resultado fallido, tal como se esperaba



> ### 6. Conclusiones y recomendaciones
>
> ¿Qué aprendiste con este ejercicio? ¿Qué mejorarí­as?

**Ejemplo:**
Aprendí­ a aplicar NAT y ACLs en un entorno simulado. Recomiendo verificar conectividad básica antes de aplicar reglas de firewall, ya que un error en la IP puede bloquear todo.


### 7. Capturas de evidencia

> Las capturas de pantalla de las pruebas realizadas se van a adjuntar en `ConnectionTests.pdf`.
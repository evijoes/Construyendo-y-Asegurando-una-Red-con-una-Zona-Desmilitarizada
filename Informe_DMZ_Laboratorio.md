
# Informe de configuración de DMZ con Cisco Packet Tracer


### 1. Objetivo del laboratorio

Configurar una DMZ segura utilizando un router Cisco ISR 2911 como firewall, implementando NAT estático para exponer servicios públicos de forma controlada y aplicando ACLs para establecer políticas de seguridad estrictas entre las tres zonas de red: LAN interna, DMZ y red externa. El objetivo principal es garantizar que solo el tráfico legítimo y necesario pueda fluir entre zonas, protegiendo la red interna de accesos no autorizados desde el exterior mientras se mantiene disponible el servicio web público.

---

### 2. Topología implementada

> Describe la red. 
[PC_External] -- (SW_External) -- [Gi0/2 Router_FW Gi0/1] -- (SW_DMZ) -- [Server_DMZ]
                                      |
                                    [Gi0/0]
                                      |
                                  (SW_Internal)
                                      |
                                [PC_Internal]


- Cantidad de redes: 3 subredes distintas (LAN, DMZ, Externa)
- Dispositivos usados: 
    >1 Router Cisco ISR 2911 (Router_FW)
    >3 Switches Cisco 2960 (SW_Internal, SW_DMZ, SW_External)
    >2 PCs (PC_Internal, PC_External)
    >1 Servidor Web Server-PT (Web_DMZ)
- Breve descripción de la función de cada zona (LAN, DMZ, Externa).
    >LAN (Internal): Red privada interna donde reside el usuario corporativo. Requiere máxima seguridad y acceso saliente a internet, pero ningún acceso entrante desde externa.
    >DMZ (Demilitarized Zone): Zona intermedia que aloja servicios públicos (servidor web). Accesible desde el exterior pero aislada de la LAN para limitar daños en caso de compromiso.
    >Externa (External): Simula internet o red pública. Contiene usuarios externos que deben acceder únicamente a servicios específicos de la DMZ.


### 3. Plan de direccionamiento IP


| Dispositivo             | IP              | Máscara           | Gateway           |
|-------------------------|------------------|-------------------|-------------------|
| PC_Internal             |192.168.1.10      |255.255.255.0      |192.168.1.1        |
| Server_DMZ              |192.168.2.10      |255.255.255.0      |192.168.2.1        |
| PC_External             |192.168.3.10      |255.255.255.0      |192.168.3.1        |
| Router_FW Gi0/0 (LAN)   |192.168.1.1       |255.255.255.0      |192.168.1.1        |
| Router_FW Gi0/1 (DMZ)   |192.168.2.1       |255.255.255.0      |192.168.2.1        |
| Router_FW Gi0/2 (Ext)   |192.168.3.1       |255.255.255.0      |192.168.3.1        |


### 4. Configuración aplicada (resumen)

> Resume los comandos o pasos más relevantes.

Configuración de interfaces:

-interface GigabitEthernet0/0
 description LAN_INTERNAL
 ip address 192.168.1.1 255.255.255.0
 ip nat inside
 no shutdown

-interface GigabitEthernet0/1
 description DMZ_ZONE
 ip address 192.168.2.1 255.255.255.0
 ip nat inside
 no shutdown

-interface GigabitEthernet0/2
 description EXTERNAL_NETWORK
 ip address 192.168.3.1 255.255.255.0
 ip nat outside
 no shutdown


- Interfaces configuradas con `ip address`
- NAT:
```bash
ip nat inside source static 192.168.2.10 192.168.3.1
```
- ACLs:
```bash
access-list 101 permit tcp any host 192.168.3.1 eq 80
access-list 100 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
```



### 5. Verificaciones realizadas

- `ping` desde PC_Internal al router: ✅
- Acceso web desde PC_External: ✅
- Bloqueo de acceso desde DMZ a LAN: ✅


### 6. Conclusiones y recomendaciones

Aprendizajes obtenidos:
1- Comprensión práctica de la arquitectura DMZ como capa de protección intermediaria entre internet y la red corporativa.
2- Dominio de NAT estático para publicación segura de servicios sin exponer topología interna.
3- Implementación efectiva de ACLs como firewall de capa de red, comprendiendo el procesamiento top-down y la importancia del orden de las reglas.
4- Verificación de que la seguridad funciona tanto en el plano de datos (tráfico permitido/bloqueado) como en el plano de control (conectividad administrativa).

Recomendaciones
-Habilitar logging en ACLs (permit/deny ... log) para detectar intentos de intrusión.
-Implementar HTTPS (puerto 443) además de HTTP para cifrar tráfico externo.
-Usar VLANs adicionales en la DMZ si se agregan más servidores, aislando servicios entre sí.


### 7. Capturas de evidencia

> Adjunta aquí (o en un PDF anexo) las capturas solicitadas: pings, navegador, comandos `show`, etc.
Topologia de la red ![alt text](image-1.png)
Configuracion IP PC_Internal ![alt text](image-2.png)
Configuracion Web_DMZ ![alt text](image-3.png) ![alt text](image-4.png)
Configuracion IP PC_External ![alt text](image-5.png)
Ping exitoso PC_Internal -> Gateway ![alt text](image-6.png)
Navegador PC_External accediendo a 192.168.3.1 ![alt text](image-7.png)
Resultado de show ip nat translations ![alt text](image-8.png)
Prueba de bloqueo ACL ![alt text](image-9.png)




Fin del informe
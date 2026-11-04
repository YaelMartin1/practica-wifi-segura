# Informe de Auditoría de Red Wi-Fi Insegura

**Autor:** Yael Martin
**Fecha:** 03/11/2026
**Repositorio:** practica-wifi-segura

---

## 1. Introducción

Las redes Wi-Fi públicas (cafeterías, aeropuertos, plazas) son entornos donde cualquier persona conectada a la misma red puede intentar observar el tráfico de los demás. En este informe actúo como auditor de seguridad junior: analizo una solicitud realizada por HTTP, identifico qué información queda expuesta y explico cómo una VPN reduce ese riesgo.

Los principales ataques en este tipo de redes son:

- **Sniffing:** captura de los paquetes de datos que viajan por la red.
- **Man-in-the-Middle:** el atacante se interpone entre el dispositivo y el router, y la información pasa primero por su equipo.
- **Evil Twin:** un hotspot falso con un nombre parecido al de la red legítima, creado para robar datos.

---

## 2. Sitio analizado

| Dato | Valor |
|---|---|
| Sitio visitado | `http://neverssl.com` |
| Host | `neverssl.com` |
| Método HTTP | `GET` |
| Protocolo | HTTP (sin cifrado) |
| Herramienta | DevTools del navegador (F12 → pestaña Network) |

Se eligió este sitio porque está pensado para funcionar solo por HTTP, lo que permite observar qué se ve en una conexión sin cifrar.

---

## 3. Evidencia observada
Captura de DevTools con la solicitud a neverssl.com

*Pestaña Network de DevTools mostrando la primera solicitud al host `neverssl.com`.*

**¿Qué protocolo utiliza el sitio?**
El sitio utiliza **HTTP**, no HTTPS. Se nota porque la URL empieza con `http://` y el navegador la marca como "No seguro".

**Información visible durante la solicitud:**

- **URL:** `http://neverssl.com/`
- **Host:** `neverssl.com`
- **Método:** `GET`
- **User-Agent:** `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/157.0.0.0 Safari/537.36`
- **Accept-Language:** `es-US,es;q=0.9,fr-FR;q=0.8,fr;q=0.7,es-419;q=0.6`
- **Otros headers:** `Accept`, `Accept-Encoding`, `Cache-Control`, `Connection`, `If-Modified-Since`, `If-None-Match`, `Referer`, `Upgrade-Insecure-Requests`
Todos estos datos viajan en texto plano: cualquiera que capture el tráfico puede leerlos tal cual.

---

## 4. Riesgos encontrados

Navegar por HTTP desde una red Wi-Fi pública expone la información a quien esté en la misma red:

- **Identidad del dispositivo:** el User-Agent revela el navegador, el sistema operativo y la versión, lo que ayuda a preparar ataques dirigidos.
- **Hábitos de navegación:** un atacante ve qué sitios y páginas visitas.
- **Contenido de la comunicación:** formularios, búsquedas y datos enviados por HTTP pueden leerse o modificarse.
- **Sesiones abiertas:** las cookies enviadas sin cifrar pueden robarse para secuestrar una sesión (session hijacking).
- **Alteración del contenido:** en un ataque MitM, el atacante puede modificar la página que recibes o redirigirte a una falsa.

Además, que la red Wi-Fi tenga contraseña no la hace segura: si todos los clientes conocen la misma clave, cualquiera puede intentar espiar o atacar a los demás.

---

## 5. Cómo ayuda una VPN

Una VPN (red privada virtual) crea un túnel seguro entre el dispositivo y un servidor de la VPN:

- Cifrado: los datos se cifran antes de salir del dispositivo. Si un atacante los captura en la red pública, solo ve ruido ilegible.
- Encapsulamiento y túnel seguro: los paquetes originales se encapsulan dentro de otros paquetes cifrados que viajan por el túnel, por lo que el contenido real queda oculto durante todo el trayecto por la red pública.
- Protección del tráfico: el atacante de la misma red no puede leer ni modificar el contenido que pasa por el túnel.
- Privacidad: la red Wi-Fi solo ve que existe una conexión hacia el servidor de la VPN, no los sitios que visitas. Tu dirección IP real también queda oculta para los sitios de destino.

Límite importante: la VPN protege el trayecto entre el dispositivo y el servidor de la VPN. Después de ese punto, el tráfico HTTP sigue sin cifrar hasta el sitio destino, por lo que sigue siendo preferible usar HTTPS.

---

## 6. Las 3 Reglas de Oro para redes Wi-Fi públicas

1. Usa siempre una VPN. Activa la VPN antes de abrir cualquier sitio o aplicación. Así tu tráfico viaja cifrado dentro de un túnel.
2. Navega solo por HTTPS. Verifica el candado en la barra de direcciones y evita sitios que empiecen con `http://`, sobre todo si vas a iniciar sesión o ingresar datos.
3. Verifica la red y no hagas operaciones sensibles. Confirma con el personal el nombre exacto de la red para evitar Evil Twins, y no hagas operaciones bancarias ni ingreses contraseñas importantes si no estás con VPN.

---

## Conclusión

El análisis con DevTools muestra que una solicitud HTTP expone, en texto plano, el host, la URL, el método y los headers. En una red Wi-Fi pública, esa información puede ser capturada por cualquiera. Una VPN agrega una capa de cifrado y un túnel seguro que protege el tráfico, y las buenas prácticas (HTTPS, verificar la red, no hacer operaciones sensibles) completan la protección.

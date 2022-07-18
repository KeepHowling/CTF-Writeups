## wormonetics Part - 1

###  Statement  
Bienvenido a nuestra organización wormonetics. Nuestro equipo de I + D está trabajando en un proyecto secreto Proyecto Σ. Pero debido a una amenaza interna nuestro proyecto está en riesgo [ Según la ley de la empresa sí es interno ]. Después de hacer un montón de análisis que tenemos la persona final que es responsable, pero nos encontramos con una fuerte evidencia por lo que la captura de la imagen de su disco PC y para la investigación adicional que necesitamos su ayuda a cambio le proporcionará puntos 😄.  

Así que ahora usted tiene que encontrar qué nombre nuestro interno está utilizando para ponerse en contacto con los Hackers y será grande si usted también encontrar la dirección de correo electrónico de quien está en contacto. Flag : wormcon{name_email} <br>

 ````
 Example: Name is Bob and email is hacker@gmail.com
 Sample Flag : wormcon{bob_hacker@gmail.com}
 ````
  
[Descargar](https://mega.nz/file/kltyhCaD#QoRhdAuU0rPBGWDdsw9cXF22YOZqG6hj1TdsJziHdGY)
`Password: DiskF0r3ns1cswormc0nCTFh4ckS`

`Author : l3v1ath4n`
<br>

### Lets solve it!

Se nos pide un correo electrónico y un nombre, esto significa que no necesitamos buscar procesos ni vertederos ni nada por el estilo. No necesitamos Volatility para esta tarea. Sólo necesitamos buscar strings.
<br>
Uno de los mejores editores de texto que he encontrado para hacer esta tarea es IntelliJ (raro, lo sé):<br>
Hacemos `Ctrl+F`<br><br>

![[Pasted image 20210829200325.png]]<br>
![[Pasted image 20210829200549.png]]<br>
y no sólo se nos da una barra de progreso de la búsqueda sino también una vista previa no sólo de lo que encontramos sino también de su "entorno" lo que significa que no perderemos tiempo revisando TODOS los resultados.

Mira el primer resultado, se parece MUCHO a un correo electrónico, vamos a hacer doble clic y ver lo que está sucediendo aquí:<br>

 ````
 Date: Fri, 06 Aug 2021 23:09:26 +0530
From: apt@vulnfreak.org
To: fr3akhacker@gmail.com
Subject: Get Your Offer
User-Agent: Roundcube Webmail/1.4.11
Message-ID: <0dbcb67bca6e55cf09a43d8b9c3fee2e@vulnfreak.org>
X-Sender: apt@vulnfreak.org
Content-Type: multipart/mixed;
 boundary="=_49651a3336ec1062112b60a7050cf0a4"

--=_49651a3336ec1062112b60a7050cf0a4
Content-Transfer-Encoding: 7bit
Content-Type: text/plain; charset=US-ASCII;
 format=flowed

Hello,

We are communicating to you as we find your details on our secret forum. 
To join us you have to prove you are eligible for our group. So now you 
have to figure out how to reach us further.

Happy Hacking :)
 ````
  
Uh, ¡algo está pasando ahí! Así que ahora sabemos que hay un volcado de correos en este archivo sin procesar y, no sólo eso, sino que conocemos el formato de los mismos. <br>

Podemos buscar "Asunto: " (Subject) y ver los resultados:<br>

![[Pasted image 20210829201101.png]]<br>

Uno a uno miramos los diferentes correos, hasta que pisamos este:<br>
 ````
To: hackwithdark@outlook.com
From: Harry Hack  <harrythehacker1337@outlook.com>
Subject: Regarding The opening
Message-ID: <b247f130-4958-e7c4-278c-fa62e1de08a7@outlook.com>
Date: Fri, 6 Aug 2021 23:38:25 +0530
User-Agent: Mozilla/5.0 (Windows NT 6.3; Win64; x64; rv:78.0) Gecko/20100101
Thunderbird/78.12.0
MIME-Version: 1.0
Content-Type: text/plain; charset=utf-8; format=flowed
Content-Transfer-Encoding: 7bit
Content-Language: en-US

Hello Dark,

Hi want to confirm If I was selected or not
````
  
  
<br>
Parece la respuesta al primer correo (¡parece que ha descubierto cómo llegar a los hackers!). Así que ahora tenemos el correo de los hackers: `hackwithdark@outlook.com` y no sólo eso sino que también tenemos el nombre del remitente, nuestro "traidor": `Harry`

The flag is: `wormcon{harry_hackwithdark@outlook.com}`

 
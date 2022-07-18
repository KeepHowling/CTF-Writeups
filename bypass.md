Inspeccionamos qué tipo de archivo es:

> file Bypass.exe
Bypass.exe: PE32 executable (console) Intel 80386 Mono/.Net assembly, for MS Windows

Vamos a ver si tiene algún tipo de compresion con DIE

![[Pasted image 20210707003814.png]]

Genial, no tiene nada. Vamos a ejecutarlo.

![[Pasted image 20210707003859.png]]

Pide un user y contraseña. Vamos a investigar qué pasa por dentro. Como es un archivo .NET:
	- DNSPy

![[Pasted image 20210707005027.png]]

Vemos que hay 8 funciones, del 0 al 7. Vamos a verlas poco a poco.

cada una tiene funciones sencillas. La funcion 0 es la más interesanter. Hace un if flag2, con dnspy podemos poner un breakpoint y settearlo a true

![[Pasted image 20210707005257.png]]

![[Pasted image 20210707005309.png]]
sale una nueva pregunta. Vams a ver las comparaciones que podemos encontrarnos por aquí, bajamos un poco y encontramos otro if(flag), hacemos lo mismo. Breakpoint y debuggeamos

![[Pasted image 20210707005433.png]]

Vaya, ese string parece la llave, podemos probar a poner la flag a true a ver si nos da la flag del reto

![[Pasted image 20210707005515.png]]

Conseguido!
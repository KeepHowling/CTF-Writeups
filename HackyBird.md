Lo analizamos con die

![[Pasted image 20210707202257.png]]

Vale, no esta empaquetado ni nada. Vamos a abrirlo.

Es un flappy bird normal y corriente. Imagino que habra que sacar una puntuación imposible para un humano. 

Como es un juego, voy a probar a modificar la puntuación a un número absurdo

![[Pasted image 20210707202929.png]]

con el new scan - next scan encuentro la direccion de lo que quiero cambiar

![[Pasted image 20210707202952.png]]

la añado y con doble click la cambio

![[Pasted image 20210707203020.png]]

la he puesto a 10.000 y ha salido esto xd, vamos a analizar bien que hace con este número. Si hacemos click derecho sobre el valor --> "find out what accesses this address" se abre un debugger que no hace nada. tampoco podemos jugar. Cerramos y volvemos a empezar.
 HAcemos lo mismo y dejamos el debugger mientras jugamos.
 ![[Pasted image 20210707203314.png]]
 
 hay un compare en la instrucción 00403133. Vamos a ghidra a esa dirección a ver qué hace.
 
 ![[Pasted image 20210707203350.png]]
 
 lo compara con 999. Vamos a poner ese número a ver qué pasa.
 
 ![[Pasted image 20210707203446.png]]
 
 pasamos una tubería y BINGO!
 
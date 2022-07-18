### ¿Reversing o no reversing? Esa es la cuestión.
Hemos encontrado un programa que verifica si la flag que has introducido es correcta o no.
1. no hay extension, vamos a ver qué hace file  
	``````
	❯ file holaKeAseEntraOKeHase
	holaKeAseEntraOKeHase: Python script, ASCII text executable, with very long lines
	``````
2. wow es un script directamente vamos a ver cómo es
``````
#!/usr/bin/env python
print("Enter the flag and I will check it for you.")
gqvHmYuMFcJZIgGlcaCijvOCFVVRxaAHznpdHZSvPOosbBsBARzcQqzxlqGwkWHQgJisBtyoNoGakfPXrYrzQPmEeDZYmiKBhKYYlBZsvcRetTpKhVLpdCNOVEpAnERv = raw_input()
tLwXLmpmvtaFnOXyUVawvhuPXUkOkPaOyATJbvCFnNLLJstBPXzkFLsoPtWjWmXCELoVqtwVMcUejFbrDGIfrmGtayUhcGVBUSxFidlIUVdmjcgrCpGZaCdQAbMGFnNO = [[[], [], []], [[], [], []], [[], [], []]]
if len(gqvHmYuMFcJZIgGlcaCijvOCFVVRxaAHznpdHZSvPOosbBsBARzcQqzxlqGwkWHQgJisBtyoNoGakfPXrYrzQPmEeDZYmiKBhKYYlBZsvcRetTpKhVLpdCNOVEpAnERv) == 27:
    for FanDUiNixUXFyymvDOppNjqGUtJzGVTFCmDECCFiAqqksvhBDmKhExTIXtJJYKWhGQaQkCNszRHdJrBPpkJRtyQRQTvTECrczrFMeiOTRIxCSJjGYoEwtIgtPBivsKWv in range(3):
        for AWThnRgGmxNIxvPijzPqVQHSOTRMOrodGHBMRskKTznwymAXOQblVxldzGYGvAmWLMjVQmfsWdPCsSkiSUodxCfNDErGyOJKlJcQApFPlhmgOLMOsnJGZkHIwGYNFDTq in range(3):
            [******************************************************]
    if RECGXHmnXfHWkqSibXniUTAxBtCVWxeUjyQtktOPZaeiSuBtDcsykbrLUfSIfibeUINCRliGptxkvobsRXOgyuiISEUtpdOcSgTxIzOymZpQNvsjZhUzNSxFtRgvGCCq == "z}zzzeo_dbr{OnkacHiasmae_do":
        print("Your flag is correct!")
    else:
        print("Your flag is incorrect. :(")
else:
    print("Your flag is incorrect. :(")

``````
3. Es un código obfuscado aparte de "z}zzzeo_dbr{OnkacHiasmae_do" que parece la flag removida.  
4. Vamos a ver cómo funciona:  
	``````
	❯ ./holaKeAseEntraOKeHase
	Enter the flag and I will check it for you.
	AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
	Your flag is incorrect. :(
	``````
5. Para poder leer mejor vamos a desobfuscarlo.  
6. Vamos a probar esto:  
	https://github.com/sleeyax/PyDeobfuscator  
	Tiene 2 modos:
	- intensio
	- pyminifier  
Probamos el primero:  

``````
	❯ python3 deobfuscator.py -i ../out/holaKeAseEntraOKeHase -o ../codigoDesobfuscado.py -d intensio
``````
  
6. Vamos a ver si nos ha deobfuscado el código, si no, probamos  
![[Pasted image 20220209120558.png]]
  
7. Vale. Lo que hace es coger el input y darle muchas vueltas para que termine de una forma concreta.
8. Podemos tener 2 formas de resolverlo: a la cuenta de la vieja o con zmt solvers (z3 pro ejemplo). Yo he decidido resolverlo a manita y con lógica.
	1. Como sé la longitud voy a hacer que var1 sea "abcdefghijklmnopqrstuvwxyz("
	2. hago un print de var3 al final de toda la mezcla para ver cómo se ha transformado el string inicial:  
		![[Pasted image 20220209121923.png]]
	3. ahora sabemos cómo varía el string. Es decir. Tenemos la posición de cada una de las letras de la flag.
	6. vamos a recolocar las letras (Tip: dale la vuelta al abecedario para ir de atrás adelante y no tener que mover el cursor)  
	7. ![[Pasted image 20220209122326.png]]  
	8. ![[Pasted image 20220209122419.png]] }  
	9. ![[Pasted image 20220209122438.png]] z}  
	10. ![[Pasted image 20220209122524.png]] zz}  
	11. ![[Pasted image 20220209122548.png]] zzz}  
	12. Así hasta terminar el abecedario.  
	   
La flag es: ``HackOn{bro_demasiado_ezzzz}``
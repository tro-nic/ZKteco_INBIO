# ZKteco_INBIO
Estudio de la comunicación entre lectoras FR1200 y la controladora InBio260
TRONIC SPA.
REV0

Información obtenida en la investigación del sistema de comunicación 485 de ZKTECO, sin conclusión.


Sistema analizado: CCAA ZKTECO

Controladora: INBIO260
		Mainboard: C3-260-MAIN 2.0-11AV
		MIcro PC: 565HB-Inbio 1.1-52A-V
		
Lectora FR1200 (125KHZ) conexion via 485

Software ZKACCESS 3.5.3 build0008
Mainboard: FR1200_ZLM30-V1.3-151023

TRONIC

Prototipado en ANDUINO
 Arduino PROMINI
			D2 = RE/DE
			D3 = R0
			D4 = DI
			
 Sketch: 485_zk_tarjeta_nueva.ino

Módulo_485: transceiver modificado
		CI: LM3085 ( se extrajo de otra lectora FR1200)

Conexiones del bus:
		A = 485+	(amarillo)
		B = 485- 	(purpura)

Control de datos y lectora de muestra:

		FR1200: R0, DI, RE/DE
		Inbio: : R0, DI, RE/DE
		
Analisis del trama: Tramas.xlxs

Alimentacion y comunicación: 12v 1amp / ETh (PaP)

Se contó con 5 tajetas 125Khz ZK para el analisis de información, formato y secuencia.

NOtas:

La comunicación 485 es sincrrona y va directametne al driver LM3085 en el caso de la controladora la info va al mini PC.

Se determino que el sentido de la trama es invertido con respecto a la lectura decimal de la tarjeta

1 bit 9 Us
2 bit 18 Us
3 bit 25Us
4 Bit 34Us
5 bit 43Us
8 bit 70Us

Se logro con exito duplicar las tarjetas leidas desde la lectora, se replicaron y enviaron desde el prototipo.

La lectura de la trama desde el arduino no se ha logrado al 100%, al parecer el tiempo de lectura debe ser menor que la capacidad que tiene el uC. Se salta 1 bit por cada bit leido.

NO se logro ententder el final de la trama. Se asume que esta deriva de algun calculo realizado entre la trama del canal y el valor de la tarjeta, ya que cada final es distinto para cada tarjeta 
y cambia en cada canal. (Se suspende la creacción de una trama nueva), por ahora solo se puede replicar y enviar la trama de una tarjeta existente.

No se hizo analisis de la trama la lectura de Huella, sobre el Osciloscopio se apriecia que este valor es muy largo.

El dato de la tarjeta al igual que en WD es de 24 bit

El envio de datos desde consola se desarrollo para realizar 4 intentos de sincronización, por dificultades de lectura en ocaciones la sincronización no se da.
Es importante sincronizar con la trama del canal correspondiente ya que la trama a enviar debe ir 50mSeg despues de la trama del canal que le correspode
El osc debe estar seteado en Single, 250Us, 2v Trigger Vertical desplazado a la izq.

---------------------
Existe dieferncias en el funcionamiento de los integrados LM3085 y MAX485
--------------------

Silencio = HI

EL inicio de cada trama de canal:
00101010110 + XXXXXXXX (canal)

Canal = 1 -> 10000000
Canal = 4 -> 00100000

Cada 8 Bit "10"

La lectura de datos esta invertida. El MSB esta a la derecha

Los espacio de mas de 70uS son separadores de datos o no aportan mayor infromación descubiertos hasta la fecha.
		
***********************************************************************************

Sincronizacion y marco de control de datos

Secuencia de respuesta:
 _______________________________________________________________________________________________________________
| |													|		|							|	|					|__fin___________________________________________________________|
TRama	 				25 mS							5mS				18mS			Trama     		10 mS										140mS
de canal (750 uS aprox) 																respuesta lectora(1250 uS aprox)

|---------------------------------------------------------------------------------------------------------------|
											50mS Aprox (respuesta)

|------------------------------------------------------------|---------------------------------------------------->
						30mS tiempo de DE controladora						30mS tiempo de RE controladora

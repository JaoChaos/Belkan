# Los extraños mundos de Belkan

El método *think* comienza con Belkan parado. Se comprueba qué se hizo en la ejecución
anterior y se actualizan los sensores, después se decide cuál será la siguiente acción.
En el constructor, al empezar, se pone *mapaResultado* con todas las celdas a ’T’,
suponiendo que todo es tierra.

## Actualización
Si avanzó, se actualizan fila o columna.  
Si giró, se actualiza hacia dónde y se decida hacia dónde se girará la próxima vez de
forma aleatoria.  
Y si hizo alguna acción relacionada con deshacerse de objetos, se actualizarán las
variables auxiliares que controlan la mochila y el objeto activo.

## Decisión
A continuación, después de actualizar la situación de Belkan, se pasa a decidir la
siguiente acción.  
Primero se comprueba que la variable de giro aleatorio no haya llegado a cero, si se da el
caso de que no se haya chocado con nada ni nadie y la variable haya llegado a cero,
Belkan girará. En el caso en el que vaya avanzando justo al lado de un precipicio, siempre
girará hacia el lado correcto, ahorrando así algo de vida.  
A continuación, si lo que tiene delante es terreno o superficie, Belkan avanzará. Si no,
comprobará si tiene que girar o si tiene que usar un objeto.  

## Objetos
Si se encuentra de frente con un lobo y tiene un hueso como objeto activo, se le dará a
este.  
En el resto de casos de uso de los objetos, se comprueba si se tiene activo el objeto, si no
es así, Belkan busca en la mochila y si está empezará a tirar objetos hasta que salga el
deseado.  
Cuando se encuentre con un bosque, usará las zapatillas; cuando se encuentre con agua,
usará el bikini; y cuando se encuentre con una puerta, usará la llave.  

## Actualización del mapa
El mapa se irá guardando en *mapaResultado*, recibiendo la información de todos los
sensores, una vez que haya pasado por una casilla de GPS.  
Estas casillas, si no las ha encontrado ya, serán buscadas por Belkan. Cuando una casilla
de GPS entra en los sensores de terreno 1, 3, 5, 7, 11 o 13 de Belkan, éste comenzará a
dirigirse hacia ellos para así tener el mapa guardado cuanto antes.  
Esta función de búsqueda también se usa para los objetos y las puertas. En el caso de los
objetos, solo los busca cuando ya ha encontrado el GPS y hay espacio en la mochila, y en
el de las puertas, cuando el objeto activo sea una llave.  

## Funciones auxiliares

### Girar
En esta función se engloba todo el código necesario para decidir hacia dónde girar y para
actualizar la variable de giro aleatorio.  
Cuando queden 100 puntos de vida, o menos, la variable de giro aleatorio tendrá valores
más pequeños para girar más veces y descubrir más mapa con los últimos puntos de
vida.

```C++
Action ComportamientoJugador::Girar(Sensores sensores){
	Action accion;

	if(!girar_derecha){
		accion = actTURN_L;

		girar_derecha = (rand() % 2 == 0);
		pasosGirar = (rand() % 50) + 1;
		/*
			Si gira, ya sea a la izquierda o a la derecha, pasosGirar tendrá que
			volver a tomar un valor nuevo
		*/
	}
	else{
		accion = actTURN_R;

		girar_derecha = (rand() % 2 == 0);
		pasosGirar = (rand() % 50) + 1;
	}

	if(sensores.vida < 100){	//Cuando le queden menos de 100 puntos de vida empezará a girar más
		pasosGirar = (rand() % 10) + 1;
	}

	return accion;
}
```

### guardarVisitado
Con esta función se guarda toda la información de los sensores de terreno (desde 0 hasta
15) en mapaResultado.

```C++
void ComportamientoJugador::guardarVisitado(Sensores sensores){
	mapaResultado[fil][col] = sensores.terreno[0];

	if (brujula == 0){
		mapaResultado[fil-1][col-1] = sensores.terreno[1];
		mapaResultado[fil-1][col] = sensores.terreno[2];
		mapaResultado[fil-1][col+1] = sensores.terreno[3];

		mapaResultado[fil-2][col-2] = sensores.terreno[4];
		mapaResultado[fil-2][col-1] = sensores.terreno[5];
		mapaResultado[fil-2][col] = sensores.terreno[6];
		mapaResultado[fil-2][col+1] = sensores.terreno[7];
		mapaResultado[fil-2][col+2] = sensores.terreno[8];

		mapaResultado[fil-3][col-3] = sensores.terreno[9];
		mapaResultado[fil-3][col-2] = sensores.terreno[10];
		mapaResultado[fil-3][col-1] = sensores.terreno[11];
		mapaResultado[fil-3][col] = sensores.terreno[12];
		mapaResultado[fil-3][col+1] = sensores.terreno[13];
		mapaResultado[fil-3][col+2] = sensores.terreno[14];
		mapaResultado[fil-3][col+3] = sensores.terreno[15];
	}
	else
		if (brujula == 1){
			mapaResultado[fil-1][col+1] = sensores.terreno[1];
			mapaResultado[fil][col+1] = sensores.terreno[2];
			mapaResultado[fil+1][col+1] = sensores.terreno[3];

			mapaResultado[fil-2][col+2] = sensores.terreno[4];
			mapaResultado[fil-1][col+2] = sensores.terreno[5];
			mapaResultado[fil][col+2] = sensores.terreno[6];
			mapaResultado[fil+1][col+2] = sensores.terreno[7];
			mapaResultado[fil+2][col+2] = sensores.terreno[8];

			mapaResultado[fil-3][col+3] = sensores.terreno[9];
			mapaResultado[fil-2][col+3] = sensores.terreno[10];
			mapaResultado[fil-1][col+3] = sensores.terreno[11];
			mapaResultado[fil][col+3] = sensores.terreno[12];
			mapaResultado[fil+1][col+3] = sensores.terreno[13];
			mapaResultado[fil+2][col+3] = sensores.terreno[14];
			mapaResultado[fil+3][col+3] = sensores.terreno[15];
		}
		else
			if (brujula == 2){
				mapaResultado[fil+1][col+1] = sensores.terreno[1];
				mapaResultado[fil+1][col] = sensores.terreno[2];
				mapaResultado[fil+1][col-1] = sensores.terreno[3];

				mapaResultado[fil+2][col+2] = sensores.terreno[4];
				mapaResultado[fil+2][col+1] = sensores.terreno[5];
				mapaResultado[fil+2][col] = sensores.terreno[6];
				mapaResultado[fil+2][col-1] = sensores.terreno[7];
				mapaResultado[fil+2][col-2] = sensores.terreno[8];

				mapaResultado[fil+3][col+3] = sensores.terreno[9];
				mapaResultado[fil+3][col+2] = sensores.terreno[10];
				mapaResultado[fil+3][col+1] = sensores.terreno[11];
				mapaResultado[fil+3][col] = sensores.terreno[12];
				mapaResultado[fil+3][col-1] = sensores.terreno[13];
				mapaResultado[fil+3][col-2] = sensores.terreno[14];
				mapaResultado[fil+3][col-3] = sensores.terreno[15];
			}
			else
				if (brujula == 3){
					mapaResultado[fil+1][col-1] = sensores.terreno[1];
					mapaResultado[fil][col-1] = sensores.terreno[2];
					mapaResultado[fil-1][col-1] = sensores.terreno[3];

					mapaResultado[fil+2][col-2] = sensores.terreno[4];
					mapaResultado[fil+1][col-2] = sensores.terreno[5];
					mapaResultado[fil][col-2] = sensores.terreno[6];
					mapaResultado[fil-1][col-2] = sensores.terreno[7];
					mapaResultado[fil-2][col-2] = sensores.terreno[8];

					mapaResultado[fil+3][col-3] = sensores.terreno[9];
					mapaResultado[fil+2][col-3] = sensores.terreno[10];
					mapaResultado[fil+1][col-3] = sensores.terreno[11];
					mapaResultado[fil][col-3] = sensores.terreno[12];
					mapaResultado[fil-1][col-3] = sensores.terreno[13];
					mapaResultado[fil-2][col-3] = sensores.terreno[14];
					mapaResultado[fil-3][col-3] = sensores.terreno[15];
				}
}
```

### Buscar
A esta función se le pasan los sensores y el elemento que se quiere buscar. Este
elemento puede ser un punto de GPS, una puerta o un objeto.  
Se comprueban los sensores de terreno 1, 3, 5, 7, 11 y 13, y según en cuál se encuentre
elemento a buscar se activarán unos bool u otros.  
En el caso del GPS, lo buscará cuando todavía no lo haya encontrado, para empezar a
guardar mapa cuanto antes.  
Los objetos los buscará cuando tenga sitio en la mochila y ya haya encontrado el GPS,
dándole así más prioridad al GPS.  
Y las puertas las buscará siempre que tenga una llave activa, ya que si reaparece
después de morir dentro de una habitación cerrada por una puerta, cuando consiga la
llave, podrá salir y buscar un GPS.  

```C++
void ComportamientoJugador::Buscar(Sensores sensores, char QueBuscas){
	switch(QueBuscas){
		case 'k':
		case 'K':	//GPS
			if(sensores.terreno[1] == 'K'){
				girarEnIzUno = true;
			}
			else{
				if(sensores.terreno[3] == 'K'){
					girarEnDerUno = true;
				}
				else{
					if(sensores.terreno[5] == 'K'){
						girarEnIzDos = true;
					}
					else{
						if(sensores.terreno[7] == 'K'){
							girarEnDerDos = true;
						}
						else{
							if(sensores.terreno[11] == 'K'){
								girarEnIzTres = true;
							}
							else{
								if(sensores.terreno[13] == 'K'){
									girarEnDerTres = true;
								}
							}
						}
					}
				}
			}
			break;
		case 'o':
		case 'O':	//Objeto
			if(sensores.terreno[1] == '0' || sensores.terreno[1] == '1' || sensores.terreno[1] == '2' || sensores.terreno[1] == '3'){
				girarEnIzUno = true;
			}
			else{
				if(sensores.terreno[3] == '0' || sensores.terreno[3] == '1' || sensores.terreno[3] == '2' || sensores.terreno[3] == '3'){
					girarEnDerUno = true;
				}
				else{
					if(sensores.terreno[5] == '0' || sensores.terreno[5] == '1' || sensores.terreno[5] == '2' || sensores.terreno[5] == '3'){
						girarEnIzDos = true;
					}
					else{
						if(sensores.terreno[7] == '0' || sensores.terreno[7] == '1' || sensores.terreno[7] == '2' || sensores.terreno[7] == '3'){
							girarEnDerDos = true;
						}
						else{
							if(sensores.terreno[11] == '0' || sensores.terreno[11] == '1' || sensores.terreno[11] == '2' || sensores.terreno[11] == '3'){
								girarEnIzTres = true;
							}
							else{
								if(sensores.terreno[13] == '0' || sensores.terreno[13] == '1' || sensores.terreno[13] == '2' || sensores.terreno[13] == '3'){
									girarEnDerTres = true;
								}
							}
						}
					}
				}
			}
			break;
		case 'd':
		case 'D':	//Puerta
			if(sensores.terreno[1] == 'D'){
				girarEnIzUno = true;
			}
			else{
				if(sensores.terreno[3] == 'D'){
					girarEnDerUno = true;
				}
				else{
					if(sensores.terreno[5] == 'D'){
						girarEnIzDos = true;
					}
					else{
						if(sensores.terreno[7] == 'D'){
							girarEnDerDos = true;
						}
						else{
							if(sensores.terreno[11] == 'D'){
								girarEnIzTres = true;
							}
							else{
								if(sensores.terreno[13] == 'D'){
									girarEnDerTres = true;
								}
							}
						}
					}
				}
			}
			break;
	}

	//Se añaden también las minúsculas al switch por si acaso
}
```

### HayObjeto
Simplemente comprueba si hay un objeto justo delante de Belkan.

```C++
bool ComportamientoJugador::HayObjeto(Sensores sensores){
	return (sensores.superficie[2] == '0' || sensores.superficie[2] == '1' || sensores.superficie[2] == '2' || sensores.superficie[2] == '3');
}
```

### PuedeAvanzar
Se comprueba si delante de Belkan hay terreno por el que pueda avanzar.

```C++
bool ComportamientoJugador::PuedeAvanzar(Sensores sensores){
	return ((sensores.terreno[2] == 'T' || sensores.terreno[2] == 'S' || sensores.terreno[2] == 'K') && (sensores.superficie[2] == '_'));
}
```

### BuscarEnMochila
Cuando se encuentre con agua, bosque o una puerta delante, Belkan buscará en su mochila si tiene el objeto necesario. Si tiene el objeto necesario en su
mochila, comenzará a tirar objetos hasta que se active el deseado y pueda avanzar.

```C++
bool ComportamientoJugador::BuscarEnMochila(Sensores sensores){
	bool tirarObjetos = false;
	queue<char> mochilaExtra = mochila;

	if(!mochila.empty()){
		if(sensores.terreno[2] == 'A' && objetoMano != '1'){
			for(int i = 0; !mochilaExtra.empty() && !tirarObjetos; i++){
				if(mochilaExtra.front() == '1'){
					tirarObjetos = true;
				}
				else{
					mochilaExtra.pop();
				}
			}
		}
		else{
			if(sensores.terreno[2] == 'B' && objetoMano != '2'){
				for(int i = 0; !mochilaExtra.empty() && !tirarObjetos; i++){
					if(mochilaExtra.front() == '2'){
						tirarObjetos = true;
					}
					else{
						mochilaExtra.pop();
					}
				}
			}
			else{
				if(sensores.terreno[2] == 'D' && objetoMano != '3'){
					for(int i = 0; !mochilaExtra.empty() && !tirarObjetos; i++){
						if(mochilaExtra.front() == '3'){
							tirarObjetos = true;
						}
						else{
							mochilaExtra.pop();
						}
					}
				}
			}
		}
	}

	return tirarObjetos;
}
```

### SituacionIdonea
Se da cuando tiene un objeto activo que puede usar para avanzar por el agua o el bosque
o abrir una puerta. Esta función únicamente comprueba que se de esa situación.

```C++
bool ComportamientoJugador::SituacionIdonea(Sensores sensores){
	return (sensores.terreno[2] == 'A' && objetoMano == '1') || (sensores.terreno[2] == 'B' && objetoMano == '2') || (sensores.terreno[2] == 'D' && objetoMano == '3');
}
```

### reinicio
Devuelve a todas las variables a su valor inicial y vacía la mochila.

```C++
void ComportamientoJugador::reinicio(Sensores sensores){
	brujula = 0;
	fil = 99;
	col = 99;
	pasosGirar = (rand() % 50) + 1;	//Se le da un nuevo valor
	objetoMano = '-';	//Se vacían las manos
	bien_situado = false;
	girarEnIzUno = girarEnIzDos = girarEnIzTres = girarYaIz = false;
	girarEnDerUno = girarEnDerDos = girarEnDerTres = girarYaDer = false;
	girar_derecha = false;
	estaAbierta = false;
	ultimaAccion = actIDLE;

	for(int i = 0; !mochila.empty(); i++){	//Se vacía la mochila
		mochila.pop();
	}
}
```

## Variables utilizadas
- *fil, col y brujula*: son usadas para determinar la posición y la orientación en el mapa de
Belkan.
- *pasosGirar*: variable que tomará valores aleatorios y que obligará a Belkan a girar una
vez que llegue a cero.
- *girar_derecha, bien_situado*: la primera decidirá de forma aleatoria hacia dónde girar y
la segunda determina si ha encontrado o no una casilla de GPS.
- *girarEnIzUno, girarEnIzDos, girarEnIzTres, girarYaIz, girarEnDerUno, girarEnDerDos,
girarEnDerTres, girarYaDer*: son usadas por el método de búsqueda y determinan si
seguir buscando o si ya ha encontrado el elemento que se estaba buscando.
- *estaAbierta*: una vez que se abre una puerta, se pone a true, haciendo que Belkan
tenga que avanzar y no siga intentando usar la llave. Una vez que se ha cruzado la
puerta, ésta se cierra y se vuelve a poner a false esta variable.
- *objetoMano*: determina cuál es el objeto que se encuentra activo en cada momento,
teniendo ‘_’ cuando no hay ninguno.
- *mochila*: determina qué objetos hay guardados en la mochila.
- *ultimaAccion*: determina cuál fue la última acción realizada por Belkan para poder
actualizar la situación de éste.

# Los extraños mundos de Belkan II

Para empezar, comenzaré con la explicación de los cambios realizados a la parte que se 
entregó en la práctica anterior. Más adelante pasaré a explicar el
método deliberativo, su algoritmo y los métodos auxiliares asociados a él.

## Cambios introducidos
Lo más importante en esta parte (que se centra especialmente en el método think) es toda
la parte relacionada con la ejecución de los planes.  
Lo primero que hace con los planes, si nos guiamos por el orden en el que está escrito, es
que si hay un obstáculo (un objeto, un aldeano o un lobo) modifique el plan en lo
necesario para adaptarse a la situación. Lo siguiente que se hace con los planes es
generarlos, éstos son generados tanto para buscar reyes como para buscar regalos. La
búsqueda de regalos solo se hará si no tiene ningún regalo, no está ejecutando ningún
plan y, por supuesto, si tiene algún conocimiento de su entorno. La de reyes se hace de
una forma similar, solo la hará cuando tenga regalos, no esté ejecutando ningún plan,
tenga conocimiento del entorno y que entre ese conocimiento esté la posición de un rey.
Lo último relacionado con los planes de este método es la ejecución.  
A parte de los planes, el otro cambio en el método think ha sido que ya no busca objetos
ni puertas, solo puntos de GPS.  
En reiniciar también ha habido un cambio significativo, a parte de la reinicialización de las
nuevas variables introducidas. En esta práctica, cuando se entre en reiniciar por última
vez comprueba el mapa y si hay un elemento que predomine en más de un 75%, pintará
el resto del mapa que no haya sido descubierto a ese mismo color.  ######
Finalmente, el último cambio viene en el método Buscar, que ahora hace uso de los
planes y ha extendido su capacidad a todos los sensores de Belkan

## pathFinding
Para el método pathFinding se ha utilizado el algoritmo de búsqueda con información A*.
Este algoritmo se basa en consultar el coste que presenta viajar desde un nodo hasta el
destino. El coste se basa en una heurística que cuenta las casillas que hay que andar
para llegar a destino.  
Primero guardamos el nodo con menor coste en cerrados (la lista con la solución) y lo
borramos de abiertos (la lista de nodos posibles). A continuación asignamos el coste a los
nodos vecinos y comprobamos si son candidatos para entrar en abiertos, esto sucede
cuando no están en ninguna de las dos listas y a la vez son transitables.  
A la vez, se va generando una lista de acciones que terminará siendo el plan que seguirá
Belkan para llegar a su destino por el camino más óptimo posible.  

```C++
bool ComportamientoJugador::pathFinding(const estado &origen, const estado &destino, list<Action> &plan){
	list< Nodo > abiertosLista, cerrados;
	priority_queue< Nodo, vector<Nodo>, functorNodo> abiertos;
	list< Nodo > vecinos;	//izquierda, delante y derecha
	list< Action > chachiGuay;
	Nodo insercion;
	Nodo current;
	Nodo original, target;
	estado moverDer, moverDel, moverIzq;
	char queHay[3];		//0 -> izquierda; 1 -> delante; 2 -> derecha
	int costeG, costeH;

	//Borro la lista
	plan.clear();

	//Insertar nodo incial en abiertos
	original.setEstado(origen);
	original.setCosteF(0);
	original.setCosteG(0);
	original.setCosteH(0);

	abiertos.push(original);
	abiertosLista.push_back(original);

	//Crear un nodo para el destino
	target.setEstado(destino);

	//Comienza el algoritmo A*
	while(!abiertos.empty()){
		current.setEstado(abiertos.top().getEstado());
		current.setCosteF(abiertos.top().getCosteF());
		current.setCosteH(abiertos.top().getCosteH());
		current.setCosteG(abiertos.top().getCosteG());
		
		list<Action> caminoNuevoCerrado = abiertos.top().getCamino();

		abiertosLista.erase(find(abiertosLista.begin(), abiertosLista.end(), abiertos.top()));

		abiertos.pop();

		//Guardamos su camino en la lista chachiGuay
		for(int i = 0; !caminoNuevoCerrado.empty(); i++){
			chachiGuay.push_back(caminoNuevoCerrado.front());
			caminoNuevoCerrado.pop_front();
		}

		//Guardamos current en cerrados y lo borramos de abiertos
		cerrados.push_back(current);

		if(current.getFila() == destino.fila && current.getColumna() == destino.columna){
			/*
				Como en el bucle las acciones se hacen para colocarse encima de la
				casilla de destino, después de darle la vuelta al plan, le borramos
				la última acción, que siempre será actFORWARD (aunque esto no tiene
				nada que ver)
			*/

			plan = chachiGuay;

			if(!plan.empty()){
				plan.pop_back();
			}

			return true;		//Si hemos llegado a destino, salimos del bucle, es decir, hay un camino
		}

		//Comprueba qué hay en cada nodo visitable para asignarle el coste más adelante
		switch(current.getBrujula()){
			case 0:
				...
			case 1:
				...
			case 2:
				...
			case 3:
				...
		}

		//Asigno los costes
		vecinos.clear();
		for(int i = 0; i < 3; i++){
			switch(i){
				case 0:
					insercion.setEstado(moverIzq);

					insercion.addAccion(actTURN_L);
					insercion.addAccion(actFORWARD);

					costeG = 20;

					break;
				case 1:
					insercion.setEstado(moverDel);

					insercion.addAccion(actFORWARD);

					costeG = 10;

					break;
				case 2:
					insercion.setEstado(moverDer);

					insercion.addAccion(actTURN_R);
					insercion.addAccion(actFORWARD);

					costeG = 20;

					break;
			}

			switch(queHay[i]){
				case 'A':	//Agua
				case 'B':	//Bosque
				case 'P':	//Precipicio
				case 'M':	//Muro
				case 'D':	//Puerta
				case '?':	//Inexplorado
					costeG = 5000;
					
					break;
				//El resto de casos (cuando es sencillo caminar por el terreno)
				//se ha hecho ya en el switch anterior
			}			


			costeH = distanciaNodos(original, insercion);

			insercion.setCosteG(costeG);
			insercion.setCosteH(costeH);
			insercion.setCosteF(costeG + costeH);
			
			vecinos.push_back(insercion);

		}

		//Aquí viene la chicha del algoritmo
		for(auto it = vecinos.begin(); it != vecinos.end(); ++it){
				//La casilla es transitable							//La casilla no está en cerrados
			if((*it).getCosteG() < 5000 && find(cerrados.begin(), cerrados.end(), (*it)) == cerrados.end()){
				int nuevoMovimientoVecino = current.getCosteG() + distanciaNodos(current, (*it));
				auto estaEnAb = find(abiertosLista.begin(), abiertosLista.end(), (*it));	//Para comprobar si está ya en abiertosLista

						//Cuesta menos llegar así					//No está en abiertos
				if(nuevoMovimientoVecino < (*it).getCosteG() || estaEnAb == abiertosLista.end()){
					// (*it).setCosteG(nuevoMovimientoVecino);
					// (*it).setCosteH(distanciaNodos((*it), target));

					if(estaEnAb == abiertosLista.end()){
						abiertos.push(*it);
						abiertosLista.push_back(*it);
					}
				}
			}
		}
	}

	return false;	//No ha conseguido encontrar un camino al rey
}
```

# Nota obtenida en ambas prácticas
- Belkan reactivo: 9,5 / 10
- Belkan deliberativo: 6,5 / 10

# Colliders y Triggers 

## Parecidos y diferencias
Un ***collider*** define una superficie en la que se detectaran colisiones. Si se detecta una colision, el collider emite el evento `OnCollisionEnter`, durante el tiempo que dura la colisión se emite el evento `OnCollisionStay` en cada fotograma y cuando la colisión termina se emite el evento `OnCollisionExit`.

Además, los colliders son la piedra angular del motor de física de Unity. Son los que hacen que los gameObjects se comporten como objetos sólidos y no se puedan atravesar unos a otros.


Un ***trigger***, en cambio, no es sólido, deja que los objetos lo atraviesen y define un volumen en el que si entra otro collider o trigger, se disparará el evento `OnTriggerEnter`, durante el tiempo que dura el contacto se emite el evento `OnTriggerStay` en cada fotograma y cuando deja de solapar con otro collider emite el evento `OnTriggerExit`.

Por tanto, la principal diferencia entre *colliders* y *triggers* es que los primeros son sólidos y no pueden atravesar a otros colliders y los segundos son como  fantasmas que pueden atravesar y ser atravesados por otros colliders y triggers.  

<i class="icon-info block"></i><div class="important">En Unity, *colliders *y *triggers* se añaden utilizando el componente *Collider*. Para que un collider se convierta en un trigger hay que marcar la casilla *isTrigger* del componente en el inspector.
<figure class="center" style="width:555px">
![](img/collider.gif)  ![](img/trigger.gif)       <figcaption style="width:555px">Por defecto *isTrigger* está desactivado y el componente se comporta como un *collider* (izquierda). Si marcamos *isTrigger* el componente se comporta como un *trigger* (derecha).<figcaption>
</figure></div>


### Cuando usar un *collider*
Cuando el gameObject que lo contiene ha de chocar con otros objetos y no atravesarlos o ser atravesado por ellos.	
 
En este caso, si queremos detectar y reaccionar a los choques, podemos detectar los eventos `OnCollisionEnter`, `OnCollisionStay` y `OnCollisionExit` utilizando funciones *listener* o receptoras con estos mismos nombres. Por ejemplo:
```js
		function onCollisionEnter(colision) {
			var controlDanyo = colision.collider.GetComponent(ControlDanyo) 
			vida -= controlDanyo.danyo;
		}
```

### Cuando usar un *trigger*
Cuando queremos detectar si un gameObject entra en una determinada zona, y que nuestro programa actue en consecuencia, pero no queremos que *choque* con nada. 
 
Para detectar cuando esto ocurre deberemos utilizar en este caso los eventos `OnTriggerEnter`, `OnTriggerStay` u `OnTriggerExit`. Por ejemplo:
```js
		function onTriggerEnter(colisionador) {
			var puerta = colisionador.GetComponent(ControlPuerta) 
			puerta.abrir();
		}
```

<i class="icon-info block"></i><div class="important">Fíjate en que los *listeners* de los eventos de un collider reciben un parámetro de tipo [`Collision`](http://docs.unity3d.com/ScriptReference/Collision.html), mientras que los *listeners* de un trigger reciben un parámetro de tipo collider. No, no es un trabalenguas ;P</div>


## Colliders estáticos, cinemáticos y dinámicos
Independientemente de que un componente *collider* esté configurado como *trigger* o no, distinguimos tres tipos de colliders para entender bien como se comportan:

* **Colliders estáticos**
	Son aquellos cuyo gameObject **no tiene** un componente *rigidbody*. 

	En cuanto a la física, no reaccionan a los choques. Son sólidos y no se pueden atravesar. Pero permanecerán estáticos por mucho que los empujemos. De ahí el nombre.

	En cuanto a los eventos, no recibirán niguno de los relacionados con las colisiones o los triggers, a no ser que choque o entre en ellos un gameObject con un collider dinámico (es decir ,que tenga un componente *rigidbody* con `isKinematic = false`). 
	
	En el caso de los eventos de trigger, sí que recibirán los producidos por un trigger cinemático.
* **Colliders dinámicos**
	Son aquellos cuyo gameObject **tiene** un componente *rigidbody* (con `isKinematic = false`). 
	
	Estos están completamente controlados por el motor de física. Pueden ser empujados por otros colliders, tengan rigidbody o no.
	
	En cuanto a los eventos, los scripts que coloquemos en sus gameObjects recibiran los eventos relacionados con las colisiones con cualquier otro collider, y con los triggers si entran en un collider configurado como trigger (independientemente de que este último sea estático, cinemático o dinámico) 
	
	Si están configurados como trigger, cualquier collider (estático, dinámico o cinemático) que entre en ellos dispara sus eventos de trigger.

* **Colliders cinemáticos**
	Son aquellos cuyo gameObject **tiene** un componente *rigidbody*, en el que se ha ha marcado la opción isKinematic (`isKinematic = true`).
	
	En cuanto a la física, están a medio camino entre los dinámicos y los estáticos. Empujan a otros colliders dinámicos cuando chocan con ellos, pero no son empujados por otros colliders. Sólo se pueden mover utilizando su componente *transform*.
	
	En cuanto a los eventos, sus scripts sólo reciben los eventos de colisión si el collider con el que han chocado es dinámico.

	Si está configurado como trigger, sí que recibe los eventos de trigger sea cual sea el tipo de collider que entre en él.

## Colliders, triggers y eventos
Si has leido con atención el apartado anterior te habrás dado cuenta de que el tema de cuando y donde se pueden detectar eventos de colisión o de trigger es un poco enrevesado. Esa es la razón por la que establecemos esta terminología de estático, dinámico y cinemático. 
Además, aunque un trigger no es más que un collider con la propiedad `isTrigger` puesta a `true`, vamos a referirnos a ellos como triggers para no tener que aclarar este punto cada vez que hablemos de ellos. Así que a efectos de los eventos tenemos:

* Colliders estáticos
* Colliders dinámicos
* Colliders cinemáticos
* Triggers estáticos
* Triggers dinámicos
* Triggers cinemáticos 

Teniendo esta terminología clara y lo que significa cada uno, las tablas del apartado siguiente deberían simplificar tu trabajo a la hora de saber que hacer para detectar una colisión o la entrada en un trigger.

### Hace falta un Rigidbody para que se puedan detectar los eventos
Aparte de hacer falta para que el motor de física actúe sobre el gameObject, el papel del *rigidbody* es determinante a la hora de emitir y detectar eventos de colisión o de trigger, como hemos visto en el apartado anterior.

<i class="icon-info block"></i><div class="important">La regla general es que si queremos que se emitan eventos para detectar una colisión o la entrada en un trigger, uno de los dos gameObjects implicados ha de tener un *Rigidbody*, además de su *collider* o su *trigger*.</div>

<i class="icon-halt block"></i><div class="important">Pero sólo es una regla general burda. Es necesario tener en cuenta como está configurado ese Rigidbody (su propiedad `isKinematic`) y el collider (su propiedad `isTrigger`) para establecer cual es el tipo del collider y considerar también el tipo de collider de los gameObjects con los que interacciona.

Como este tema es un poco enrevesado, pero al mismo tiempo muy importante (si no te quieres pasar horas dando cabezazos al teclado porque tu trigger no funciona), te lo resumo en estas dos tablas para que lo tengas más claro:</div>


| Tipo de collider	  | Collider <br> Estático  | Collider<br>Dinámico    | Collider <br>Cinemático |
|--------		      |--------			        |-------			      |-------			        |
| Collider Estático   | &nbsp;					| <i class="icon-ok"></i> |  &nbsp;                 |
| Collider Dinámico   | <i class="icon-ok"></i> | <i class="icon-ok"></i> |	<i class="icon-ok"></i> |
| Collider Cinemático |  &nbsp;                 | <i class="icon-ok"></i> |  &nbsp;                 |
<div class="table-caption" style="width:400px">**Tabla resumen eventos de colisión**:  que colliders reciben eventos de colisión según su tipo y el del tipo del collider con el que colisionan. No se incluyen los triggers porque estos no disparan eventos de colisión.</div>




| Tipo de collider	  | Collider<br>Estático    | Collider<br>Dinámico    | Collider<br>Cinemático  | Trigger<br>Estático     | Trigger<br>Dinámico     | Trigger<br>Cinemático   |
|--------		      |--------			        |-------			      |-------			        |--------			      |-------			        |-------			      |
| Collider Estático   |  &nbsp;                 |   &nbsp;    			  |  &nbsp;                 |  &nbsp;                 | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
| Collider Dinámico   |  &nbsp;                 |  &nbsp;                 |  &nbsp;                 | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
| Collider Cinemático |  &nbsp;                 |  &nbsp;                 |  &nbsp;                 | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
| Trigger Estático    |  &nbsp;                 | <i class="icon-ok"></i> | <i class="icon-ok"></i> |  &nbsp;                 | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
| Trigger Dinámico    | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
| Trigger Cinemático  | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> | <i class="icon-ok"></i> |
<div class="table-caption" style="width:620px">**Tabla resumen eventos de trigger**:  que triggers reciben eventos de trigger según su tipo y el del tipo del collider con el que colisionan.</div>


 




# Protocolo voto secreto y verificable

## TL;DR Explicación muy simple 
Se trata de habilitar una *suerte de habitación* anónima. 
En la que dejamos nuestro voto ofuscado (producto función hash de este) de forma que
sea imposible saber que opción hemos votado pero que a su vez podamos verificar 
que esa fue nuestra opción.
Desde fuera de la habitación, podemos mirar dentro y decir: Sí, mi voto está ahí.
De esta forma nadie podrá saber a quien pertenece o a quién se vota.


## Paso 1.- Anuncio de la ronda de votaciones
> Dado un grupo de personas identificadas y que se reconocen con derecho a votar.

Se distribuye un documento que contiene:
* url de la *habitación oscura de voto* 
* distintas opciones (URI)
* modalidades de voto aceptadas `voto único`, `voto preferente`, etc
* funciones hash aceptadas, métodos de firma, etc.
* Plazos para *reclamar el derecho al voto* y *votar*
La url es privada y aleatoria. De forma que solo la conocerán los votantes y
el organizador.

## Paso 2.- Reclamar el derecho al voto
Dentro del plazo indicado en el [anuncio de la ronda de votaciones](#anuncio-de-la-ronda-de-votaciones)
Se deberá reclamar el derecho al voto. Esto consiste en enviar al servicio web el producto de 
la función hash de un código aleatorio generado por el cliente además del voto. 
El servicio web anexará este hash a la URI de la habitación.
Creando así un documento denominda *inscripción de voto*

## Paso 3.- Verificación 
Todos aquellos que reclamaron su voto deberán verificar el documento resultante.
Deberán verificar que el producto de la función hash de su código aleatorio figura en 
la inscripción. Hasta que no lo verifiquen no podrá revelarse el voto

## Paso 4.- Revelado del voto
De nuevo los clientes accederán a la habitación a través de una red anónima.
Esta vez enviarán un mensaje que contendrá:
* El producto de la función hash con el que reclamaron su derecho al voto
* El código aleatorio del que origina el producto de la función hash.
* Su voto

## Paso 5.- Difusión de los resultados
Los resultados serán publicados.

## Habitación oscura de voto
Se trata de un servicio web accesible solo mediante algún protocolo que permita
el uso anónimo. Tal como [TOR](tor.org), [Freenet](freenet.org) o similar.

## Notas
Este protocolo depende de que los votantes interactuen más de una vez.
Los teléfonos móviles están conectados a internet permanentemente. Habilitar
un servicio que se preocupe por validar el voto en el móvil es viable.
Así como habilitarlo en un ordenador que pueda conectarse de forma permanente.
Por ejemplo una dispositivo como el raspberry PI.

Nada previene que una persona vote más de una vez. Si tenemos más votos que 
personas que hayan validado su voto deberemos anular la habitación y crear otra.
En caso de que vuelva a suceder se puede dividir la habitación o redistribuirla.
Esto también es un problema ya que a menor número de votantes por documento más posibilidades 
de que todos hayan votado lo mismo con lo que se destaparía el voto. 


## Voto distribuido
Las entidades organizativas pueden verse como nodos hipotética implementación
mediante couchdb se podría utilizar el mecanismo de replicación para unir nodos.

Ejemplos:
A tres (funciona igual a dos)
O1 <-> O2 <-> O3 <-> O1

Centralizado
Central ← O1
Central ← O..
Central ← ON


##Ejemplo de voto

Paso 1
[Organizador → votante] 
```
	{
		url: '$url-dark-room',
		type: 'preference',
		options:[$uri1, $uri2, $uri3]
		claimBefore: '2014-12-24T12:33:54.424Z',
		revealBefore:'2014-12-30T12:33:54.424Z'
		accept:'hash/bcrypt-10;signature/rsa192'
	}
```

Paso 2
[Votante → $url-dark-room]
`curl -F hash=$2a$10$TxCfmG7Zx8ZziKoC6B//p.5LwWrcoMW6gUIwGgbgIZlLU1WvlFMhS" $url-dark-room`

Paso 3
Antes de pasar al paso 4 habrá que verificar que nuestro hash está ahí.
Para esto habrá que enviar el producto de la función hash del listado firmado con nuestra clave pública.

	firma(hash(listado))

Ejemplo de listado:
```
$url-dark-room
#hashes
$2a$10$TxCfmG7Zx8ZziKoC6B//p.5LwWrcoMW6gUIwGgbgIZlLU1WvlFMhS"
```
Y difundir nuestra opción.

Paso 4 
Una vez que todo el mundo ha verificado el documento podemos revelar nuestro voto.
Accedemos otra vez a la habitación oscura:
`curl -F hash=$2a$10$TxCfmG7Zx8ZziKoC6B//p.5LwWrcoMW6gUIwGgbgIZlLU1WvlFMhS;vote=2036e0a9-b95b-4a62-be30-918a1b214f29;$uri1" $url-dark-room`



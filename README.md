Requerimientos 
	Wamp Server 2.5 (Apache : 2.4.9 PHP : 5.5.12).

	Cuenta en desarrollador en twitter.

	Generar una nueva aplicación con (consumer key y token).

	Editor de código multiplataforma o editor de texto plano. 


Introducción 
Actualmente Twitter es una de las mayores fuentes de información en tiempo real de Internet alimentada por millones de usuarios. 
Twitter ofrece tres APIs:   Streaming API,  REST API y  Search API aplicables a necesidades diferentes. ElStreaming API proporciona un subset de tweets en casi tiempo real. Se establece una conexión permanente por usuario con los servidores de Twitter y mediante una petición http  se recibe un flujo continúo de tweets en formato json.  
Se puede obtener una muestra aleatoria (statuses/sample), un filtrado (statuses/filter) por palabras claves o por usuarios. Sin embargo, los métodos más interesantes cómo obtener todo el caudal de tweets (statuses/firehose) o sólo los tweets que tienen enlaces (statuses/links) o los tweets conretweets (statuses/retweet) “Is not a generally available resource.
El REST API ofrece a los desarrolladores el acceso al core de los datos de Twitter. Todas las operaciones que se pueden hacer vía web son posibles realizarlas desde el API.  Dependiendo de la operación requiere o no autenticación, con el mismo criterio que en el acceso web. Sopota los formatos: xml,json, rss, atom. El Search API ofrece una información más limitada del tweet,  en concreto sobre los datos del autor en el que solo indica el Id,  el screen_name y la url de su avatar. Los otros dos APIs si ofrecen el perfil completo del autor en el momento de la escritura del tweet.



Como usar la API de twitter en PHP 
Crear una cuenta de desarrollador y una nueva aplicación
Para crear una cuenta de desarrollador tenemos que dirigirnos a la siguiente dirección:
https://dev.twitter.com/
Iniciar sesión con nuestra cuenta de usuario. Seguidamente pulsamos el siguiente enlace:
https://apps.twitter.com/
Y creamos una nueva aplicación: 
 




Llenar el formulario
Hacer el llenado del formulario que se muestra  con el nombre de la nueva aplicación, una breve descripción y una url de un sitio. 
 
Crear el token de acceso
Los Tokens de acceso son las claves que vamos a necesitar para comunicarnos con la API de Twitter, vamos a necesitar cuatro Tokens o claves:
	The api key
	The api secret
	The access token
	The access token secret
Para obtener estas claves o Tokens tenemos que dirigirnos a la pestaña API Keys.
 
Y vemos que se nos han generado dos Tokens “Access token” y el “Access token secret” los cuales tenemos que guardar



Conexión API de twitter con PHP 
Una vez que tenemos todas las claves necesarias para hacer funcionar la API de Twitter vamos a ir a lo bueno, en primer lugar vamos a ver como poder autentificarnos mediante PHP en la API de Twitter.
Existen distintas librerías hechas en PHP para gestionar la API de Twitter, yo he decidido usar una que me parece la más sencilla, ya que no solo requiere la importación de un solo archivo PHP.  La librería en cuestión se llama Abraham y la podéis descargar desde el mismo GitHub del autor.
https://github.com/abraham/twitteroauth

 
Creamos un archivo PHP que será nuestro index este debe estar en un carpeta junto con el paquete de la librería que descargamos de la página del autor. 
Importaremos la librería que se encuentra en un archivo PHP 
require "api/autoload.php";
use Abraham\TwitterOAuth\TwitterOAuth;

El siguiente paso a realizar se conforma en la conexión ya que pondremos nuestros consumer key y de igual manera los token que se crearon al inicio de la aplicación. 
$consumer_key = "2RtrXnM8W3zkQDLWr0ByZAp0A";
$consumer_secret = "zXNhsi7KiuouJlKmRflMiN3isGhvxdH7D34Q8C0GO4NRaVgcOg";
$token = "612271270-F4D3CIWaFd2L7gpUlBGDmnXJZJYEdhmueKyeOTLY";
$token_secret = "fJY5kkJwXakYSsqaLePwTviVLiFUmQSeT0zY4vSF3A9VB";

Hasta ahora solo lo hemos conectado, ahora veremos cómo podemos consumir datos de twitter
$conexion = new TwitterOAuth($consumer_key, $consumer_secret, $token, $token_secret);
$contenido = $conexion->get("followers/list");
var_dump($contenido); //Le indicamos que nos imprima lo de contenido

Con este código mostraremos información de mi lista de seguidores, utilizando el método get.
Nos arroja un arreglo que contiene a todos mis amigos los cuales son 17  me muestra su información como se puede visualizar.  
 
Hasta el momento ya hemos consumido datos de twitter, y todos ellos están en un arreglo llamado “users” el cual podemos ver toda la información pública de nuestros amigos.
Datos específicos de mi lista de amigos 
Como se puede visualizar le estamos indicando que del arreglo que nos arrojó anteriormente llamado “users” nos imprima su nombre, imagen de perfil, numero de amigo y seguidores.



foreach ($contenido-> users as $follower){
    echo "<img src='".$follower->profile_image_url."'>";
    echo $follower->screen_name;
    echo "<br>";
    echo "<span style='color: #".$follower->profile_background_color."'>" . $follower->description . "</span>";
    echo "<br>";
    echo "Tiene:" .$follower->friends_count ."amigos";
    echo "<br>";
    echo "Tiene:" .$follower->followers_count ."seguidores";
    echo "<br>";
    echo "<h4>Ultimo Tweet:</h4>";
    echo $follower->status->text;
    echo $follower->created_at;

    echo "<br>";
    //echo $follower->Retweet_count;
    echo "<br>";
    echo "<hr>";

}


Con este código se mostrara así la parte visual del proyecto, ya definiendo la información que nos interesa y la imprimimos. 
 
Búsqueda de tweets de un usuario en específico 
Se utilizara la misma librería y los accesos al igual que los token’s. 
El primer paso será crear un nuevo archivo PHP en cual se llamara búsqueda.php este archivo contendrá el formulario que nos pedir el nombre del usuario de twitter que queremos saber sus tweets al igual que la cantidad. 


<form action="tweets.php" method="post">
  <center>
  <table width="332" border="1">
  <tr>
    <td width="263">Nombre de Usuario</td>
    <td width="239"><label for="nom2"></label>
      <input type="text" name="nom" id="nom2" /></td>
  </tr>
  <tr>
    <td>Numero de Tweets</td>
    <td><label for="num"></label>
      <input type="text" name="num" id="num" /></td>
  </tr>
  <tr>
    <td>&nbsp;</td>
    <td><input type="submit" name="Buscar" id="Buscar" value="Enviar" /></td>
  </tr>
</table>
</center>
</form>

Estos datos los enviaremos a un nuevo archivo PHP llamado tweets con el método post, el archivo contendrá lo mismo la librería y las variables de accesos y token.
Tweets.php
<?php

$conexion = new TwitterOAuth($consumer_key, $consumer_secret, $token, $token_secret);
$contenido = $conexion->get("statuses/user_timeline",["screen_name" => $nombre = $_POST['nom'] , "count" => $numero = $_POST['num'] ]);

$html = "";
$i = 1;

foreach($contenido as $tweet){
   
    $html .= $i . "->";
    $html .= $tweet->text;
    $html .= "<hr>";
    $i ++;
    
}
 echo "<div class='follower'>";
echo $html;
echo "</div>";


?>




Visualmente 
Con el mismo estilo que utilizamos lo implementamos en tweets y se vera de la siguiente manera.
 
Este sería el formulario que se tendrá que llenar con la información que nosotros nos interese, por ejemplo en el nombre del usuario pondremos el de Aristegui que es “aristeguionline” y en el número de tweets quiero los últimos 3 y enviaremos los datos.
 

Y es así como se realiza la búsqueda es recomendable que se tengan bien los datos de la aplicación que se crean. Ya que esa información es la que nos dará toda la información en formato JSON nosotros nos tocara jugar con el código para obtener lo que a notros nos importa y también darle un mejor aspecto visual.

Estilo que se utilizo 
Ahora bien ya tenemos la información de nuestros amigos lo que sigue es darle un toque en la parte visual según nuestro gusto en este caso se utilizó el siguiente estilo, a la hora de imprimir mandarlos a traer con class seguido del nombre de la clase.

 
<style>
body{
    font-family:Verdana, Geneva, sans-serif;
}
.follower{
    width: 350px;
    min-height: 320px;
    float: left;
    margin: 10px;
    border-radius: 8px;
    border: 2px solid gainsboro;
    padding: 10px;
}
img {
    float:left;
    margin: 5px;
    border: 6px #FFF solid;
    border-radius: 2px;
}
.nombre {
    float: left;
    font-size: 20px;

}
.separador{
    min-height: 80px;
}
.clr{
    clear: both;
}
</style>
 




Anexo
Busqueda.php  
<body>
  <form action="tweets.php" method="post">
  <center>
  <table width="332" border="1">
  <tr>
    <td width="263">Nombre de Usuario</td>
    <td width="239"><label for="nom2"></label>
      <input type="text" name="nom" id="nom2" /></td>
  </tr>
  <tr>
    <td>Numero de Tweets</td>
    <td><label for="num"></label>
      <input type="text" name="num" id="num" /></td>
  </tr>
  <tr>
    <td>&nbsp;</td>
    <td><input type="submit" name="Buscar" id="Buscar" value="Enviar" /></td>
  </tr>
</table>
</center>
</form>
</body> 


Seguidores.php
<?php

require "api/autoload.php"; //ubicacion de la libreria

use Abraham\TwitterOAuth\TwitterOAuth; //libreria

$consumer_key = "2RtrXnM8W3zkQDLWr0ByZAp0A";
$consumer_secret = "zXNhsi7KiuouJlKmRflMiN3isGhvxdH7D34Q8C0GO4NRaVgcOg";
$token = "612271270-F4D3CIWaFd2L7gpUlBGDmnXJZJYEdhmueKyeOTLY";
$token_secret = "fJY5kkJwXakYSsqaLePwTviVLiFUmQSeT0zY4vSF3A9VB";

$conexion = new TwitterOAuth($consumer_key, $consumer_secret, $token, $token_secret);
$contenido = $conexion->get("followers/list"); //muestra la lista de los seguidores 
//informacion especifica con class del diseño 

foreach($contenido->users as $follower){    
    echo "<div class='follower'>";
    echo "<img src='".$follower->profile_image_url."'>";
    echo "<span class='nombre'>" . $follower->screen_name . "</span>";
    echo "<div class='clr'></div>";
    echo "<span class='nombre'>" . $follower->location . "</span>";
    echo "<br>";
    echo "<div class='separador' style='background: url(".$follower->profile_background_image_url.")'></div>" ;
    echo "<br>";
    echo "Tiene : <b>" . $follower->friends_count . "</b> amigos, y <b>".$follower->statuses_count."</b> tweets";
    echo "<hr>";
    echo "<span style='color: #".$follower->profile_background_color."'>" . $follower->description . "</span>";
    echo "<h4>Ultimo Tweet:</h4>";
    echo $follower->status->text;
    echo "</div>";
}
?>
Tweets.php 
<?php

require "api/autoload.php";

use Abraham\TwitterOAuth\TwitterOAuth;

$consumer_key = "2RtrXnM8W3zkQDLWr0ByZAp0A";
$consumer_secret = "zXNhsi7KiuouJlKmRflMiN3isGhvxdH7D34Q8C0GO4NRaVgcOg";
$token = "612271270-F4D3CIWaFd2L7gpUlBGDmnXJZJYEdhmueKyeOTLY";
$token_secret = "fJY5kkJwXakYSsqaLePwTviVLiFUmQSeT0zY4vSF3A9VB";


$conexion = new TwitterOAuth($consumer_key, $consumer_secret, $token, $token_secret);
$contenido = $conexion->get("statuses/user_timeline",["screen_name" => $nombre = $_POST['nom'] , "count" => $numero = $_POST['num'] ]);



$html = "";
$i = 1;

foreach($contenido as $tweet){
   
    $html .= $i . "->";
    $html .= $tweet->text;
    $html .= "<hr>";
    $i ++;
    
}
 echo "<div class='follower'>";
echo $html;
echo "</div>";


?>


 

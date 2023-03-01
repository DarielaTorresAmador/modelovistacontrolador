# modelovistacontrolador

# Investigacion_MVC
MVC es un patrón en el diseño de software comúnmente utilizado para implementar interfaces de usuario, datos y lógica de control. Enfatiza una separación entre la lógica de negocios y su visualización.
# FUNCIONALIDADES
# El modelo es el responsable de:
Acceder a la capa de almacenamiento de datos. Lo ideal es que el modelo sea independiente del sistema de almacenamiento.
Define las reglas de negocio (la funcionalidad del sistema). Un ejemplo de regla puede ser: "Si la mercancía pedida no está en el almacén, consultar el tiempo de entrega estándar del proveedor".
Lleva un registro de las vistas y controladores del sistema.
Si estamos ante un modelo activo, notificará a las vistas los cambios que en los datos pueda producir un agente externo (por ejemplo, un fichero por lotes  que actualiza los datos, un temporizador que desencadena una inserción, etc.).
 

# El controlador es responsable de:
 Recibe los eventos de entrada (un clic, un cambio en un campo de texto, etc.).
Contiene reglas de gestión de eventos, del tipo "SI Evento Z, entonces Acción W". Estas acciones pueden suponer peticiones al modelo o a las vistas. Una de estas peticiones a las vistas puede ser una llamada al método "Actualizar()". Una petición al modelo puede ser "Obtener_tiempo_de_entrega ( nueva_orden_de_venta )". 
 

# Las vistas son responsables de:
Recibir datos del modelo y los muestra al usuario.
Tienen un registro de su controlador asociado (normalmente porque además lo instancia).
Pueden dar el servicio de "Actualización()", para que sea invocado por el controlador o por el modelo (cuando es un modelo activo que informa de los cambios en los datos producidos por otros agentes)

# EJEMPLO
#VISTA
<html>
 <head>
   <title>LIBRERIA UAZON</title>
 </head>
 <body>
  <h1>Libros dados de alta en nuestra libreria</h1>
  <table border="1">
   <tr>
    <th>TITULO</th>
    <th>PRECIO</th>
   </tr>
   <?php foreach ($libros as $libro): ?>
    <tr>
      <td><?php echo $libro['titulo']?></td>
      <td><?php echo number_format($libro['precio'],2)?></td>
    </tr>
  <?php endforeach; ?>
  </table>
</body>
</html>

# MODELO 
<?php
   $user = 'comprador';
   $pwd = 'proweb2013';
   $db = new PDO('mysql:host=localhost;dbname=uazon', $user, $pwd);
   $result = $db->query('SELECT titulo, precio FROM libros');
   $libros = array();
   while ($libro = $result->fetch())
      $libros[] = $libro;
?>

# CONTROLADOR
<?php
   //Se incluye el modelo
   require 'model.php';
   //En $libros tenemos un array con todos los libros gracias al modelo
   //La vista recibe un array para mostrarlo por pantalla
   include 'view.php';
?>

# Almacenamiento y recuperacion de datos
las acciones e información procedentes del usuario serán recogidas exclusivamente por los Controladores. Ningún componente de otra capa debe acceder a los datos generados desde el cliente, de la misma forma que sólo los componentes de la Vista estarán autorizados a generar interfaces de usuario con las que enviar información de retorno.

Destaca también el papel central del Controlador. Tiene acceso bidireccional al Modelo, es decir, será capaz tanto de actualizar su estado, invocando por ejemplo métodos o acciones incluidos en su lógica de negocio, como de consultar la información que sea necesaria para completar sus tareas. Sin embargo, en ningún caso el Modelo será consciente o mostrará acoplamiento alguno respecto a las clases Controlador que lo están utilizando, ni conocerá las distintas representaciones (Vistas) que pueden realizarse de él cara al usuario.

Por otra parte, el Controlador es el encargado de seleccionar la Vista más apropiada en función de la acción llevada a cabo por el usuario, suministrándole toda la información que necesite para componer la interfaz. Para pasar esta información, el Controlador puede usar clases del Modelo o, lo que es más habitual, clases específicamente diseñadas para ello, denominadas View-Models, que contendrán toda la información que la Vista necesite para maquetarse y mantendrá a ésta aislada de los cambios en el Modelo.

La responsabilidad de la Vista, por tanto, se reduce a generar la interfaz partiendo de los datos que le suministre el controlador.



# INVESTIGACION 2 MVC
#Definir las vistas en las cuales se reciben y envían los datos del modelo y los muestra al usuario.
# Modelo
Gestiona todo lo relacionado con la información y la iteración con los datos de nuestra aplicación. Todas las peticiones de acceso a los datos pasará por esta capa.

# Controlador
Une la vista y el modelo. El usuario solicitará información por medio de la vista y esta hará la petición al controlador. Posteriormente, este, realizará la petición al modelo.

# Vista
Es la capa que nos mostrará la información formateada. También desde dónde el usuario puede solicitar más información.  
# Modelo
<?php

class Service {
    
    private $servicio;
    private $db;

    public function __construct() {
        $this->servicio = array();
        $this->db = new PDO('mysql:host=localhost;dbname=ejemplo_mvc', "root", "");
    }

    private function setNames() {
        return $this->db->query("SET NAMES 'utf8'");
    }

    public function getServicios() {

        self::setNames();
        $sql = "SELECT id, nombre, precio FROM servicio";
        foreach ($this->db->query($sql) as $res) {
            $this->servicio[] = $res;
        }
        return $this->servicio;
    }

    public function setServicio($nombre, $precio) {

        self::setNames();
        $sql = "INSERT INTO servicio(nombre, precio) VALUES ('" . $nombre . "', '" . $precio . "')";
        $result = $this->db->query($sql);

        if ($result) {
            return true;
        } else {
            return false;
        }
    }
}
?>

# Vista
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>Ejemplo MVC con PHP</title>
        <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet" type="text/css" />
        <link href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css" rel="stylesheet" >
        <script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
        <script type="text/javascript" src="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/js/bootstrap.min.js"></script>
    </head>
    <body>
        <div class="container">
            <header class="text-center">
                <h1>Ejemplo MVC con PHP</h1>
                <hr/>
                <p class="lead">Creamos una base de datos de los servicios <br/>
                    que podría realizar un taller y <br/>
                    operamos con ella utilizando el paradigma MVC</p>
            </header>
            <div class="col-lg-6 text-center">
                <hr/>
                <h3>Listado de servicios</h3>
                <table class="table">
                    <tr>
                        <td><strong>SERVICIO</strong></td>
                        <td><strong>PRECIO</strong></td>
                    </tr>
                    <?php
                    for ($i = 0; $i < count($datos); $i++) {
                        ?>
                        <tr>
                            <td><?php echo $datos[$i]["nombre"]; ?></td>
                            <td><?php echo $datos[$i]["precio"]; ?> €</td>
                        </tr>
                        <?php
                    }
                    ?>
                </table>
                <a href="../index.php"> <i class="fa fa-arrow-circle-left"></i> Volver a la página principal</a>
                <hr/>
            </div> 
            <footer class="col-lg-12 text-center">
                Adaweb - <?php echo date("Y"); ?>
            </footer>
        </div>
    </body>
</html>
# cocntrolador
<?php
    require_once("../models/modelo.php");
    $services = new Service();
    $datos = $services->getServicios();
    require_once("../views/vista.php");
?>


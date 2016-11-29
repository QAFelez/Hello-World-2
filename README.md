# FirstProject

#Iteración 1:
Búsqueda de código, subirlo a Git y compilación en Jenkins.

##Paso 1: Código

Hemos buscado un código sencillo para la primera prueba. Para ello hemos optado por un *Hello World*.

##Paso 2: Git

Se ha optado por subir el código a **Github**. Cada miembro del grupo se ha creado su propia cuenta con el usuario de UST Global.
Elena ha subido el primer código a este repositorio en el cual estamos todos de colaboradores (Todos tenemos acceso de lectura/escritura).
Hemos configurado Github tanto de consola como de escritorio. 


##Paso 3: Jenkins

Se ha instalado Jenkins en una máquina virtual que actúa como servidor. En el proceso de instalación se han llevado a cabo los siguientes pasos.




En Jenkins tenemos que incluir los plugins necesarios para cargar el proyecto desde Github (Manage Jenkins->Manage Plugins->Available->Install):

*Git Parameter Plug-In
*GitHub Branch Source Plugin
*GitHub plugin

Dentro de la configuración del proyecto, en Source Code Management hay que añadir el repositorio donde tenemos nuestro proyecto. Repository URL: *https://github.com/QAElena/FirstProject.git* y damos a Apply and Save.

##Paso 4 : Integrar Jenkins y Github

Una vez está creado el repositorio hay que configurarlo para que se enlace con Jenkins, permitiendo así, que al realizar un *push* automáticamente se lance el trabajo en Jenkins. 
**En Github->**Para ello es necesario ir a: Settings->Weebhooks y en PayLoad URL añadir la URL que se obtiene del plugin de Github en Jenkins (para obtener esta URL de Jenkins es necesario ir a Manage Jenkins->Configure System->Github->Advanced->Specify another hook url for GitHub configuration y copiar esa URL)

>And, **VERY IMPORTANT**, since you are installing your jenkins server in your localhost, please be aware that you **shouldn't** fill in above Jenkins hook url like *http://localhost:8080/github-webhook* because Github is not able to recognize *localhost* or *127.0.0.1* or *192.168.~.~.*

Para conseguir que cada vez que se realice un push se compile la aplicación en Jenkins: dentro del proyecto Configure-> Build Triggers, seleccionamos Poll SCM y en el cuadro de texto incluimos * * * * *.

#Iteración 2:

Búsqueda de código, subirlo a Github, compilación en Jenkins y realización de pruebas unitarias.

##Paso 1: Código

Se vuelve a utilizar el mismo código *Hello World* que en el caso anterior. *Se añade el código de las pruebas unitarias*

##Paso 2:

Se actualizan los ficheros del repositorio.

##Paso 3:

Jenkins toma los archivos y realiza la compilación de los mismos cuando se realiza el *push* de forma automática.

##Paso 4:

……

#Iteración 3:

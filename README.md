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


Se vuelve a utilizar el mismo código *Hello World* que en el caso anterior. *Se añade las clases correspondiente a las pruebas de unidad*, en el POM.xml se añade el plugin que permite la ejecución de pruebas de unidad con Maven. Plugin *surfire*

```
 <plugin>      
       <groupId>org.apache.maven.plugins</groupId>
       <artifactId>maven-surefire-plugin</artifactId>
       <version>2.16</version>
</plugin>
```

##Paso 2:

Se actualizan los ficheros del repositorio.


##Paso 3:

Jenkins toma los archivos y realiza la compilación de los mismos cuando se realiza el *push* de forma automática.


##Paso 4:

Jenkins ejecuta las pruebas de unidad


#Iteración 3:


##Paso 1: Código
Se vuelve a utilizar el mismo código *Hello World* que en el caso anterior. *Se añade el código de las pruebas de integración*. Se añaden los plugins para ejecución de pruebas de integración (failsafe) al POM.xml, y se modifican los plugins de unitarias e integración para que por defecto NO ejecute ninguna prueba, mediante el flag skipTests.
Se actualizan los ficheros del repositorio.
```
<properties> 
  <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
	<!-- Posibilidad de evitar todo tipo de tests (por defecto evitar) -->
   <skipTests>true</skipTests>
 </properties>

  (...)
  
   <!-- Unit tests are run by surefire. -->
        <!-- Classes under src/test/java called *Test are included automatically. -->
        <!-- Integration tests are run during the test phase. -->
      <plugin>
         <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.16</version>
        <configuration>
		<!-- Evitar tests, controlado por un flag que se puede cambiar desde línea de comando -->		
          <skipTests>${skipTests}</skipTests>
        </configuration>		
      </plugin>
        <!-- Integration tests are run by failsafe. -->
        <!-- Classes under src/test/java called *IT are included automatically. -->
        <!-- Integration tests are run during the verify phase. -->
      <plugin>
      <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-failsafe-plugin</artifactId>
        <version>2.16</version>
        <configuration>
		 <!-- Evitar tests, controlado por un flag que se puede cambiar desde línea de comando -->
          <skipTests>${skipTests}</skipTests>
        </configuration>		
        <executions>
          <execution>
            <goals>
              <goal>integration-test</goal>
              <goal>verify</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
```


##Paso 2:

Se ejecuta Jenkins por etapas, mediante un script de Groovy. En la primera de ellas se compilan los módulos desde GitHub, en la segunda se ejecutan las pruebas unitarias y en la tercera las de integración.

```
//Aquí se separan las pruebas de integración de componentes de las unitarias
// En este caso el POM.xml incluía todas las pruebas. Opcionalmente se pueden
// usar comodines para descartar o aceptar ficheros (p.e. excluir ficheros acabados
// en IT de las pruebas unitarias)
node {
   def mvnHome
   // El primer Stage coje el código del repositorio
   stage('Preparation') {
      // Get some code from a GitHub repository
      git 'https://github.com/QAFelez/Hello-World-2.git' 
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'Maven'
   }
   // El segundo Stage corre las unitarias, con el plugin surefire, y en este caso
   // solamente de la clase HelloAppTest
   stage('Build with unitary test') {
      // Run the maven build
      if (isUnix()) {
       //  sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
          sh "'${mvnHome}/bin/mvn' -DskipTests=false surefire:test -Dtest=HelloAppTest"
      } 
   }
   // El tercer stage corre las pruebas de integración, con el plugin failsafe, y 
   // solamente de la clase HelloWithTestsIT
    stage('Build with integration test') {
      // Run the maven build
      if (isUnix()) {
       //  sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
          sh "'${mvnHome}/bin/mvn' -DskipTests=false failsafe:integration-test -Dtest=HelloWithTestsIT"
      } 
   }
   stage('Results') {
      //TODO: Aquí se controla el deploy, de momento simplemente de se
      junit '**/target/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
}
```
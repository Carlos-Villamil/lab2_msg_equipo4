## Laboratorio 2 — MSG Foundation: una ventana JavaFX sin tocar el núcleo

## Datos generales

Cada equipo le pone una ventana JavaFX al sistema MSG del Laboratorio 1 sin tocar una sola línea de domain ni de application. Las cuatro piezas nuevas se acuerdan primero en un contrato y después se construyen en paralelo, cada una en su rama.

| Campo | Valor |
| --- | --- |
| Curso | Ingeniería de Software I (603501) — Unillanos |
| Unidad | Implementación |
| Duración | 2 horas (una sesión de práctica por grupo de 20) |
| Modalidad | Equipos de 4 con roles rotados respecto al Laboratorio 1 |
| Caso | MSG Foundation (Schach), nombres de clases en inglés |
| Herramientas | Java 21, JavaFX 21, Maven, JUnit 5, Git y GitHub |
| Material | lab2-msg.zip en el aula virtual al iniciar la sesión |
| Punto de partida | La solución de referencia del Laboratorio 1, igual para todos: Main imprime 1160.00 y el pom.xml todavía no tiene JavaFX |
| Prerrequisito conceptual | Clase de JavaFx respetando el diseño: los seis pasos de una evolución, el contrato, la raíz de composición y la diferencia entre el controlador de JavaFX y el Controller de GRASP |

Desarrollo en 2 horas. El núcleo ya está terminado y probado. Cada rol escribe una pieza pequeña de la capa externa, de 10 a 30 líneas. Como el contrato se acuerda en papel antes de programar, nadie tiene que esperar a otro para empezar.

## Resultados de aprendizaje

Al terminar la sesión, cada estudiante podrá:

- 1. Agregar un framework (JavaFX) a un proyecto Maven existente y explicar en qué capa queda.

- 2. Acordar un contrato entre piezas antes de programar y usarlo para trabajar en paralelo.

- 3. Conectar una vista JavaFX a un caso de uso existente mediante un controlador de JavaFX que recibe sus dependencias, sin modificar domain ni application.

- 4. Distinguir el controlador de JavaFX (un adaptador) del Controller de GRASP (el caso de uso).

- 5. Demostrar con Git que el núcleo no cambió.

- 6. Auditar código de interfaz generado por IA e identificar, con principio y consecuencia, dónde viola el diseño.


## Lo que se construye

Una ventana con el título «MSG Foundation — Fondos de la semana», un botón Calcular fondos y una etiqueta que muestra \$ 1160.00 al pulsarlo. La ventana usa el mismo caso de uso que la consola: Main sigue funcionando igual.

| Archivo | Ubicación | En el proyecto base | Lo escribe |
| --- | --- | --- | --- |
| pom.xml | raíz del proyecto | Sin JavaFX | Integrador |
| Launcher.java, App.java | co.edu.unillanos.msg | No existen | Integrador |
| funds-view.fxml | src/main/resources/c o/edu/unillanos/msg/ infrastructure/ui | No existe | Vista |
| FundsController.java infrastructure.ui |   | No existe | Controlador |
| MoneyFormat.java y MoneyFormatTest.java | infrastructure.ui (código y prueba) | No existen | Formato y estilo |
| funds-view.css | junto a funds- view.fxml | No existe | Formato y estilo |
| Todo domain, todo application, InMemory*Repository, Main | — | Completos | Nadie: no se tocan |

En el proyecto el dinero se maneja con double, como en la solución del Laboratorio 1:

EstimateFundsForWeek.execute() devuelve un double.

## El contrato

El contrato fija los nombres que comparten las piezas nuevas. Si la vista dice fx:id="resultado" y el controlador espera totalLabel, la ventana falla al abrir; acordarlo primero evita ese error y permite que cada uno programe sin esperar a los demás. Esta es la propuesta: el equipo la confirma o la ajusta en la Parte A.

| Elemento | Valor propuesto | Lo usan |
| --- | --- | --- |
| Archivo de la vista | infrastructure/ui/funds-view.fxml, en src/main/resources/co/edu/unillanos/msg | Vista, Integrador |
| Controlador declarado en la vista | fx:controller="co.edu.unillanos.msg.infras tructure.ui.FundsController" | Vista, Controlador |
| Etiqueta del total | Un Label con fx:id="totalLabel" | Vista, Controlador |
| Acción del botón | onAction="#onEstimate" | Vista, Controlador |


| Elemento | Valor propuesto | Lo usan |
| --- | --- | --- |
| Constructor del controlador | FundsController(EstimateFundsForWeek estimateFunds) | Controlador, Integrador |
| Formato del dinero | MoneyFormat.format(double) devuelve, por ejemplo, "$ 1160.00" | Formato y estilo, Controlador |
| Hoja de estilos | funds-view.css, con las clases title y total | Vista, Formato y estilo |

Un valor del contrato solo cambia si los cuatro lo acuerdan y lo corrigen en BITACORA.md.

## Roles y flujo de trabajo

Los roles rotan para que nadie repita lo que hizo en el Laboratorio 1. Si el equipo es el mismo: Hipotecas → Integrador, Inversiones → Vista, Registro → Controlador, Integrador → Formato y estilo. Si el equipo cambió, eligen libremente, pero el Integrador no puede ser quien ya lo fue.

| Rol | Rama | Escribe | Lo demuestra con | Revisa el PR de |
| --- | --- | --- | --- | --- |
| Integrador | feature/javafx- setup y después feature/app | JavaFX en el pom.xml, Launcher, App; fusiona los PR | La ventana abre con mvn javafx:run y desde el IDE | Controlador |
| Vista | feature/view | funds-view.fxml | La ventana muestra título, botón y etiqueta con los nombres del contrato | Formato y estilo |
| Controlador | feature/control ler | FundsController | El botón muestra $ 1160.00 | Integrador |
| Formato y estilo | feature/format | MoneyFormat, MoneyFormatTest, funds-view.css | MoneyFormatTest en verde | Vista |

Si el equipo queda de tres, el Integrador asume también Formato y estilo.

El orden de fusión importa. Todo lo que importa javafx compila solo cuando JavaFX está en el pom.xml, y App necesita la vista y el controlador. Por eso el orden es:

- 1. feature/javafx-setup (el Integrador lo abre en los primeros 10 minutos de la Parte B).

- 2. feature/format, que no depende de nadie y que el controlador usa.

- 3. feature/view y feature/controller, en cualquier orden.

- 4. feature/app, al final.

Después de cada fusión, quien todavía está trabajando trae main a su rama con git fetch origin y

git merge origin/main.


## Reglas del flujo (las mismas del Laboratorio 1):

- Nadie hace commit en main; todo entra por PR con una aprobación.

- Cada rama toca solo los archivos de su rol.

- Los mensajes de commit dicen qué cambió.

- Quien revisa deja al menos un comentario y verifica que mvn test pasa en la rama y que el PR no toca domain ni application.

## Reglas de implementación:

- Ningún archivo de domain ni de application cambia. Si alguien siente que debe abrirlo, lo discute primero con el equipo.

- FundsController no hace new de casos de uso ni de repositorios: los recibe.

- Ningún archivo de infrastructure.ui contiene una fórmula, ni el número 52, ni 0.28.

## Agenda

| Minuto | Actividad | IA | Control |
| --- | --- | --- | --- |
| 0–15 | Parte 0: equipos, roles, repositorio, mvn test y Main en la consola | No | Quien no logre autenticarse avisa al monitor de inmediato |
| 15–30 | Parte A: contrato y decisiones, en papel | No |   |
| 30–75 | Parte B: desarrollo en ramas, en el orden de fusión | No | Cada rama tiene commits de su dueño |
| 75–95 | Parte C: integración y guion de aceptación No |   |   |
| 95–110 | Parte D: auditoría individual de un método escrito por IA | Se audita, no se usa | Hoja individual |
|   | 110–120 Cierre y entrega | — | Etiqueta lab2-entrega |

## Parte 0 — Arranque (15 min)

Mientras el Integrador crea el repositorio, los demás leen el contrato y la Parte A.

Integrador. Descomprime el .zip, llena en BITACORA.md la tabla del equipo y sube el proyecto. La etiqueta base marca el punto de partida: al final sirve para demostrar que el núcleo no cambió.

```
git init
git add .
git commit -m "Proyecto base del Laboratorio 2"
git branch -M main
git remote add origin https://github.com/USUARIO/lab2-msg-equipoN.git
git push -u origin main
git tag base
git push origin base
```


Después, en GitHub, invita a los otros tres (Settings → Collaborators) y protege main con una regla que exija un pull request con una aprobación (Settings → Branches).

Los demás. Aceptan la invitación, clonan y verifican que el proyecto base funciona:

git clone https://github.com/USUARIO/lab2-msg-equipoN.git cd lab2-msg-equipoN mvn test

Resultado esperado: BUILD SUCCESS con las pruebas del Laboratorio 1. Luego ejecutan Main desde el IDE: la consola imprime 1160.00. Un COMPILATION ERROR no es normal: avise al profesor.

## Parte A — Contrato y decisiones (15 min, sin IA, en papel)

A1. El contrato. El equipo revisa la tabla del contrato y la confirma o la ajusta. El Integrador la copia en BITACORA.md, sección «Contrato», dentro de su rama feature/javafx-setup, así entra a main con su primer PR.

- A2. Decisiones de diseño. La justificación nombra la información o la relación concreta, no solo el principio.

| # | Rol | Decisión | Estado | Lo que escribe el equipo |
| --- | --- | --- | --- | --- |
| D1 | Controlador | FundsController se llama «controlador», pero ¿es el Controller de GRASP? | Tomada | Quién cumple ese papel y por qué importa la diferencia |
| D2 | Integrador | FundsController recibe el caso de uso por su constructor, desde App |   | Tomada Qué se pierde si el controlador hiciera new EstimateFundsForWeek(...) con sus propios repositorios |
| D3 | Formato y estilo | MoneyFormat va en infrastructure.ui y no en domain |   | Tomada Qué tiene que ver el formato $ 1160.00 con la ventana y no con las reglas del negocio |
| D4 | Vista | Los nombres del contrato (fx:id, onAction) se acuerdan antes de escribir la vista | Tomada | Qué pasaría si la vista y el controlador los eligieran cada uno por su cuenta |
| D5 | Integrador | ¿En qué orden se fusionan los cinco PR? | Abierta | El orden y la dependencia que lo obliga |
| D6 | Equipo | Predicción: ¿qué archivos de domain y de application van a cambiar? | Abierta | La lista, aunque esté vacía; al final se compara con lo que realmente cambió |

La hoja se agrega a una carpeta docs dentro del proyecto para su entrega.


## Parte B — Desarrollo en ramas (45 min, sin IA)

Cada miembro crea la rama de su rol, completa su parte, sube la rama y abre un PR hacia main con el revisor que indica la tabla de roles.

Integrador (feature/javafx-setup, después feature/app)

- 1. Agrega al pom.xml las dependencias org.openjfx:javafx-controls y org.openjfx:javafx- fxml (versión 21.0.5) y el plugin org.openjfx:javafx-maven-plugin (versión 0.0.8) con <mainClass>co.edu.unillanos.msg.App</mainClass>.

- 2. Crea App (extiende Application) con un start mínimo que solo muestra una ventana vacía con el título «MSG Foundation», y Launcher, cuyo main llama a Application.launch(App.class, args).

- 3. Verifica que la ventana vacía abre con mvn javafx:run y desde el IDE ejecutando Launcher. Copia el contrato en BITACORA.md y abre el PR: todos lo necesitan.

- 4. Revisa y fusiona los PR en el orden acordado.

- 5. Con main actualizado, en feature/app completa App.start: crea los tres repositorios y los datos de ejemplo igual que Main, crea EstimateFundsForWeek, carga funds-view.fxml con un FXMLLoader y le entrega el caso de uso al controlador con setControllerFactory. Ninguna regla de negocio va en App.

## Vista (feature/view)

Escribe funds-view.fxml en la carpeta del contrato: un contenedor VBox con el título (clase title), el botón Calcular fondos y la etiqueta del total (clase total). Declara el controlador, el fx:id y el onAction exactamente como dice el contrato, y enlaza la hoja funds-view.css. Puede escribirla a

mano o con Scene Builder. Mientras el controlador no exista, la vista no carga: pruébela cuando su rama tenga main actualizado.

## Controlador (feature/controller)

Escribe FundsController en infrastructure.ui: el campo @FXML para totalLabel, el campo con el

caso de uso, el constructor del contrato y el método @FXML onEstimate(). El método pide el total al caso de uso y lo muestra con MoneyFormat.format(...). Si el caso de uso lanza una IllegalStateException, muestra su mensaje en un Alert de error. Si MoneyFormat todavía no está en

main, traiga main cuando se fusione feature/format.

## Formato y estilo (feature/format)

- 1. Escribe MoneyFormat.format(double): devuelve "\$ " seguido del monto con dos decimales y punto decimal. Use String.format(Locale.ROOT, "\$ %.2f", monto): sin Locale.ROOT, un Windows configurado en español escribe 1160,00.

- 2. Escribe MoneyFormatTest con al menos tres casos: 1160 → \$ 1160.00, 0.5 → \$ 0.50 y −750 → \$ -750.00.

- 3. Escribe funds-view.css con las clases title y total: el total más grande y en negrita.

- 4. Abra el PR apenas pase la prueba: el controlador lo necesita.

Tener en cuenta. En GitHub cada rama tiene commits de su dueño y ningún PR toca domain ni

application.


## Parte C — Integración y guion de aceptación (20 min)

Con los cinco PR fusionados, todos actualizan main, corren mvn test (esperado: BUILD SUCCESS, con las pruebas del Laboratorio 1 y MoneyFormatTest) y ejecutan el guion en este orden:

|   | Paso Acción | Resultado esperado |
| --- | --- | --- |
| 1 | mvn javafx:run | Abre la ventana con el título, el botón y la etiqueta vacía |
| 2 | Ejecutar Launcher desde el IDE | Abre la misma ventana |
| 3 | Pulsar Calcular fondos | La etiqueta muestra $ 1160.00 |
| 4 | Ejecutar Main desde el IDE | La consola sigue imprimiendo 1160.00 |
| 5 | git diff --stat base main -- src/main/java/co/edu/unillanos/msg/d omain src/main/java/co/edu/unillanos/msg/a pplication | No imprime nada: el núcleo no cambió |
| 6 | Abrir FundsController | No hay new de casos de uso ni de repositorios, ni 52, ni 0.28 |

Completen en BITACORA.md el resultado de cada paso y la sección «Lo que realmente cambió», y compárenla con la predicción D6. Como main está protegida, la bitácora entra con un último PR desde la rama docs/bitacora, que aprueba cualquier otro miembro.

Entrega de ejecución. Alguien del equipo ejecuta los pasos 3 y 5 y toma capturas de pantalla que lleva a la carpeta docs.

## Parte D — Auditoría individual (15 min, en papel)

Esta parte es individual y se hace con los computadores cerrados. Un estudiante le pidió a un asistente de IA «el método del botón Calcular fondos para la ventana de la MSG» y obtuvo esto. Compila sin errores.

```
@FXML
private void onEstimate() {
MortgageRepository mortgages = new InMemoryMortgageRepository();
InvestmentRepository investments = new InMemoryInvestmentRepository();
double total = 0;
for (Investment i : investments.findAll()) {
total += i.weeklyReturn();
}
for (Mortgage m : mortgages.findAll()) {
total += m.weeklyCoupleContribution();
total -= m.weeklyEscrow();
}
total -= 39000.0 / 52;
totalLabel.setText("$ " + total);
}
```


- 1. Encuentre cuatro problemas. Para cada uno escriba la línea, el principio o regla que viola y qué le pasaría al usuario o al equipo por culpa de él.

- 2. El estudiante abre la ventana y pulsa el botón. ¿Qué número ve y por qué no es 1160.00?

- 3. Reescriba el método en cinco líneas o menos usando las clases del laboratorio.

## Cierre y entrega (10 min)

El docente pide a dos equipos que comparen su predicción D6 con lo que realmente cambió, y discute con el grupo:

- 1. ¿Cuántos archivos de domain y application tocaron? Si la respuesta es cero, ¿qué dice eso del diseño del Laboratorio 1?

- 2. Si en la próxima clase los datos pasan a una base de datos H2, ¿qué archivos creen que cambian?

- 3. En la Parte D, la IA escribió código que compila. ¿Por qué eso no basta para aceptarlo, y quién responde por lo que entra al repositorio?

El Integrador fusiona el PR de la bitácora y marca la versión entregada:

```
git switch main
git pull
git tag lab2-entrega
git push origin lab2-entrega
```

## Extensiones para equipos rápidos

No son requisito para el laboratorio.

- Un dato menos repetido. App y Main cargan los mismos datos de ejemplo. Proponga cómo evitarlo sin que ninguna de las dos dependa de la otra.

- Fondos negativos. Si el total es negativo, la etiqueta se pone en rojo. ¿Dónde va esa decisión: en el controlador, en MoneyFormat o en el caso de uso? Justifíquelo.

- Integración continua. El archivo .github/workflows/pruebas.yml del Laboratorio 1 sirve tal cual: las pruebas no abren ventanas.

## Entregables y evaluación

Todo se entrega antes de salir del aula; no hay trabajo para la casa. El historial de GitHub muestra el autor y la hora de cada commit.


## Problemas frecuentes con JavaFX

| Síntoma | Causa probable | Solución |
| --- | --- | --- |
| «JavaFX runtime components are missing» al ejecutar desde el IDE | Se ejecutó App, que extiende Application | Ejecutar Launcher, o usar mvn javafx:run |
| «package javafx... does not exist» | La rama no tiene el pom.xml con JavaFX | git fetch origin y git merge origin/main después de fusionar feature/javafx-setup |
| «Location is not set» | La ruta de getResource no coincide con la carpeta del FXML | El FXML va en src/main/resources/co/edu/unill anos/msg/infrastructure/ui |
| LoadException al abrir la ventana | fx:id, onAction o fx:controller distintos a los del contrato | Comparar la vista y el controlador con la tabla del contrato |
| Error al crear el controlador (no encuentra un constructor sin parámetros) | App no llamó a setControllerFactory antes de load() | Registrar la fábrica antes de cargar la vista |
| La hoja de estilos no se aplica | Ruta del .css o nombre de clase distintos | El .css va junto al FXML y se enlaza con @funds-view.css |

## Código de referencia

## funds-view.fxml:

```
<?xml version="1.0" encoding="UTF-8"?>
<?import java.net.URL?>
<?import javafx.scene.control.Button?>
<?import javafx.scene.control.Label?>
<?import javafx.scene.layout.VBox?>
<VBox xmlns:fx="http://javafx.com/fxml"
fx:controller="co.edu.unillanos.msg.infrastructure.ui.FundsController"
spacing="16" alignment="CENTER" prefWidth="420" prefHeight="240">
<stylesheets>
<URL value="@funds-view.css"/>
</stylesheets>
<Label text="MSG Foundation — Fondos de la semana" styleClass="title"/>
<Button text="Calcular fondos" onAction="#onEstimate"/>
<Label fx:id="totalLabel" styleClass="total"/>
</VBox>
FundsController:
public class FundsController {
@FXML private Label totalLabel;
private final EstimateFundsForWeek estimateFunds;
```


```
public FundsController(EstimateFundsForWeek estimateFunds) {
this.estimateFunds = estimateFunds;
}
@FXML
private void onEstimate() {
try {
totalLabel.setText(MoneyFormat.format(estimateFunds.execute()));
} catch (IllegalStateException e) {
Alert alert = new Alert(Alert.AlertType.ERROR, e.getMessage());
alert.showAndWait();
}
}
}
```

## MoneyFormat:

```
public final class MoneyFormat {
private MoneyFormat() { }
/** Devuelve el monto con dos decimales y punto decimal, por ejemplo "$ 1160.00".
*/
public static String format(double amount) {
return String.format(Locale.ROOT, "$ %.2f", amount);
}
}
```

App.start (la creación de repositorios y datos es la misma de Main):

```
@Override
public void start(Stage stage) throws Exception {
// ... los tres repositorios y los datos de ejemplo, igual que en Main ...
EstimateFundsForWeek estimateFunds =
new EstimateFundsForWeek(mortgages, investments, expenses);
FXMLLoader loader = new FXMLLoader(
App.class.getResource("infrastructure/ui/funds-view.fxml"));
loader.setControllerFactory(type -> new FundsController(estimateFunds));
stage.setScene(new Scene(loader.load()));
stage.setTitle("MSG Foundation");
stage.show();
}
```

# visualloopmuinuddin

package visualLoop2;
import java.util.ArrayList;
import javafx.application.Application;
import static javafx.application.Application.launch;
import javafx.event.EventHandler;
import javafx.scene.Group;
import javafx.scene.Scene;
import javafx.scene.canvas.Canvas;
import javafx.scene.canvas.GraphicsContext;
import javafx.scene.image.Image;
import javafx.scene.input.KeyEvent;
import javafx.scene.input.MouseEvent;
import javafx.scene.paint.Color;
import javafx.stage.Stage;
/**
*
* @author SUGIARTO COKROWIBOWO
*/
public class VisualLoop2 extends Application implements Runnable {
//Loop Parameters
private final static int MAX_FPS = 60;
private final static int MAX_FRAME_SKIPS = 5;
private final static int FRAME_PERIOD = 1000 / MAX_FPS;
//Thread
private Thread thread;
private volatile boolean running = true;
//Canvas
Canvas canvas = null;

//Atribut Kotak
float cx=0f;


//KEYBOARD HANDLER
ArrayList<String> inputKeyboard = new ArrayList<String>();
public VisualLoop2() {
resume();
}
@Override
public void start(Stage primaryStage) {
Group root = new Group();
Scene scene = new Scene(root);
canvas = new Canvas(1024, 700);
root.getChildren().add(canvas);
//HANDLING KEYBOARD EVENT
scene.setOnKeyPressed(
new EventHandler<KeyEvent>() {
public void handle(KeyEvent e) {
String code = e.getCode().toString();
if (!inputKeyboard.contains(code)) {
inputKeyboard.add(code);
System.out.println(code);
}
}
}
);
scene.setOnKeyReleased(
new EventHandler<KeyEvent>() {
public void handle(KeyEvent e) {
String code = e.getCode().toString();
inputKeyboard.remove(code);
}
}
);
//HANDLING MOUSE EVENT
scene.setOnMouseClicked(
new EventHandler<MouseEvent>() {
public void handle(MouseEvent e) {
}
}
);
//primaryStage.getIcons().add(new Image(getClass().getResourceAsStream("logo.jpg")));
primaryStage.setTitle("Visual Loop");
primaryStage.setScene(scene);
primaryStage.show();
}
public static void main(String[] args) {
launch(args);
}
//THREAD
private void resume() {
reset();
thread = new Thread(this);
running = true;
thread.start();
}
//THREAD
private void pause() {
running = false;
try {
thread.join();
} catch (InterruptedException e) {
e.printStackTrace();
}
}
//LOOP
private void reset() {
}
//LOOP
private void update() { 
        
    if (inputKeyboard.contains("SPACE")){
        cx+=0; //FUNGSI BERHENTI
    }else{
        cx+=2;
    }
   
}
//LOOP
private void draw() {
try {
if (canvas != null) {
GraphicsContext gc = canvas.getGraphicsContext2D();
gc.clearRect(0, 0, canvas.getWidth(), canvas.getHeight());
//CONTOH MENGGAMBAR KOTAK
gc.save();


gc.setFill(Color.DEEPSKYBLUE);
gc.fillRect(cx,10,100,100);
gc.restore();
//CEK INPUT KEYBOARD
if (inputKeyboard.contains("SPACE")) {
gc.setFill(Color.RED);
gc.fillText("SPACE", 300, 300);
}
}
} catch (Exception e) {
e.printStackTrace();
}
}
@Override
public void run() {
long beginTime;
long timeDiff;
int sleepTime = 0;
int framesSkipped;
//LOOP WHILE running = true;
while (running) {
try {
synchronized (this) {
beginTime = System.currentTimeMillis();
framesSkipped = 0;
update();
draw();
}
timeDiff = System.currentTimeMillis() - beginTime;
sleepTime = (int) (FRAME_PERIOD - timeDiff);
if (sleepTime > 0) {
try {
Thread.sleep(sleepTime);
} catch (InterruptedException e) {
}
}
while (sleepTime < 0 && framesSkipped < MAX_FRAME_SKIPS) {
update();
sleepTime += FRAME_PERIOD;
framesSkipped++;
}
} finally {
}
}
}
}

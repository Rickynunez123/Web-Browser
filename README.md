# Web-Browser
I created a web Browser using JavaFX
import java.util.List;
import javafx.application.Application;
import javafx.geometry.Insets;
import javafx.scene.Scene;
import javafx.scene.control.Button;
import javafx.scene.control.TextField;
import javafx.scene.layout.BorderPane;
import javafx.scene.layout.HBox;
import javafx.scene.layout.Priority;
import javafx.scene.web.WebEngine;
import javafx.scene.web.WebView;
import javafx.stage.Stage;
import javafx.concurrent.Worker.State;


public class WebBrowserRicardo extends Application {
    private static final String DEFAULT_TITLE = "Ricardo Nunez";


    private Stage stage = null;
    private BorderPane borderPane = null;
    private WebView view = null; //It is capable of showing web pages (HTML, CSS, SVG, JavaScript) inside a JavaFX application
    private WebEngine webEngine = null; //is a non-visual object capable of managing one Web page at a time. It loads Web pages,
    // creates their document models, applies styles as necessary, and runs JavaScript on pages.
    private TextField statusBar = null;
    private TextField addressField = null;


    private String getParameter( int index ) {
        Parameters params = getParameters();
        List<String> parameters = params.getRaw();
        return !parameters.isEmpty() ? parameters.get(index) : "";
    }

    private HBox makeAddressBar(){
        HBox addressBarPane = new HBox(); //add to a pane pane1.setBottom(hBox);
        addressBarPane.setPadding(new Insets(5,4,5,4));
        addressBarPane.setSpacing(10);
        addressBarPane.setStyle("-fx-background-color: #336699");

        Button backButton = new Button("<");
        backButton.setOnAction( event -> {
            webEngine.executeScript("history.back()");
        });

        Button nextButton = new Button( ">" );
        nextButton.setOnAction( event -> {
            webEngine.executeScript("history.forward()");
        });



        addressField = new TextField();
        HBox.setHgrow(addressField, Priority.ALWAYS);
        addressField.setOnAction( event -> {
            // TODO: validate data - verify protocol
            webEngine.load( addressField.getText() );
        });

        Button helpButton = new Button( "?" );
        helpButton.setOnAction( event -> {
//			webEngine.load( "file://myHelp.html" );
            webEngine.loadContent(
                    "<HTML><BODY>My help page!</BODY></HTML>"
            );
        });

        addressBarPane.getChildren().addAll(backButton, nextButton,addressField,helpButton);


        return addressBarPane;
    }


    private WebView makeHtmlView(){
        view = new WebView();
        webEngine = view.getEngine();
        webEngine.getLoadWorker().stateProperty().addListener((ov, oldState, newState) -> {
            if (newState == State.SUCCEEDED){
                String title = webEngine.getTitle();
                stage.setTitle(title == null || title.isEmpty() ? DEFAULT_TITLE : title);
                addressField.setText(webEngine.getLocation());
            }
        });
        webEngine.setOnStatusChanged((ev -> {
            statusBar.setText(ev.getData());
        }));
        return view;
        }





    private HBox makeStatusBar(){
        HBox statusBarPane = new HBox();
        statusBarPane.setPadding(new Insets(5,4,5,4));
        statusBarPane.setSpacing(10);
        statusBarPane.setStyle("-fx-background-color: #336699;");
        statusBar = new TextField();
        HBox.setHgrow(statusBar, Priority.ALWAYS);
        statusBarPane.getChildren().addAll(statusBar);
        return statusBarPane;
    }


    @Override
    public void start(Stage primaryStage){
        primaryStage.setTitle("Ricardo's Web browser");
        borderPane = new BorderPane();
        borderPane.setTop(makeAddressBar());
        borderPane.setCenter(makeHtmlView());
        borderPane.setBottom(makeStatusBar());
        webEngine.load(getParameter(0).isEmpty() ?"http://www.google.com" :getParameter(0));

        statusBar.setText("Default text ");
        statusBar.setFocusTraversable(false);

        Scene scene = new Scene(borderPane,800,600);
        stage = primaryStage;
        stage.setScene(scene);
        stage.show();

    }
}

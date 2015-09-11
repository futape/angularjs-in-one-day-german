# AngularJS in einem Tag





## Vorwort

Dies ist eine deutsche Übersetzung des Artikels [*Ultimate guide to learning AngularJS in one day*](http://toddmotto.com/ultimate-guide-to-learning-angular-js-in-one-day/) von Todd Motto, veröffentlicht am 2. Oktober 2013.  
Dieses Dokument wurde am 11. September 2015 unter einer [Creative Commons BY­ 4.0 Lizenz](http://creativecommons.org/licenses/by/4.0/) veröffentlicht.





## Was ist AngularJS?

Angular ist in JavaScript geschriebenes klientseitiges MVC/MVVM Framework, welches für die Entwicklung morderner Single Page Web Applications (und Websites) eingesetzt wird. Dieser Artikel stellt einen vollständigen Crash Kurs dar, welcher auf meinen Erfahrungen, Empfehlungen und Best Practices, welche ich bei meiner Arbeit mit Angular gesammelt habe, basiert.





## Terminologie

Angular hat eine anfängliche kurze Lernkurve, welche beim Lernen über die Basics hinaus von Höhe und Tiefen durchzogen sein wird. Das wichtigste ist erst einmal sich mit der Terminologie vertraut zu machen und zu verstehen was es heißt im Sinne von "MVC zu denken". MVC steht für *Model-View-Controller*. Im Folgenden erläutere ich die grundlegenden APIs, welche dem Anwender von Angular bereitgestellt werden, sowie einige Informationen zur Terminologie.



### MVC

Wahrscheinlich hast du schon mal von MVC als ein von vielen Programmiersprachen verwendetes Mittel zur Strukturierung und Architektur von Applications/Software gehört. Hier ist eine kurze Zusammenfassung der Bedeutungen der einzelnen Bestandteile:

+   *Model*: Die Daten(-struktur) hinter einem bestimmten Bereich der Application. Wird in Angular gewöhnlich als JSON ausgetauscht. Falls dir JSON noch fremd ist, informiere dich am besten zuerst darüber, da es in Angular ein fundamentales Mittel zum Austausch von Informationen zwischen Server und View ist. Zum Beispiel könnte eine Sammlung von *User IDs* als folgendes Model dargestellt werden:

        {
          "users" : [{
            "name": "Joe Bloggs",
            "id": "82047392"
          },{
            "name": "John Doe",
            "id": "65198013"
          }]
        }

    Diese Daten können entweder über XHR (XMLHttp Request) von dem Server abgefragt werden - wie du in jQuery die `$.ajax` Funktion benutzen würdest, stellt dir Angular `$http` bereit - oder direkt in den HTML Code der Webseite eingebettet werden (zum Beispiel beim Verarbeiten der Seite durch PHP und auslesen der Informationen aus einer Datenbank). Anschließend können Änderungen am Model vollzogen werden und zurück zum Server bzw. zur Datenbank gesendet werden.
+   *View*: Die Beschreibung des Views ist einfach: Es ist dein HTML Code und/oder der (von Angular) gerendete Output. Bei der Verwendung eines MVC Frameworks werden Model Daten heruntergeladen und analysiert, auf deren Basis dann der View/HTML und die angezeigten Informationen aktualisiert werden.
+   *Controller*: Genau wie der Name schon sagt - sie kontrollieren. Um genauer zu sein kontrollieren sie Daten. Controllers sind die direkte Verbindung zwischen *Server* und *View*, der *Middle Man*, welcher über die Kommunikation zwischen Server und Client für die Verarbeitung und Aktualisierung der Daten sorgt.



### Ein AngularJS Projekt vorbereiten - Der grundlegende Aufbau

Zuerst muss der grundlegende Aufbau des Angular Projekts eingerichtet werden. Dieser setzt sich aus mehreren Komponenten zusammen, dazu zählen die `ng-app` Deklaration um die Application zu definieren, ein *Controller*, welcher mit dem View kommuniziert, und einige DOM Bindings sowie das Einbindung von Angular selbst. Es folgt ein exemplarischer grundlegender Aufbau:

HTML mit `ng-*` Angaben:

    <div ng-app="myApp">
        <div ng-controller="MainCtrl">
            <!-- Controller Logik -->
        </div>
    </div>

Ein Angular Module und Controller:

    var myApp = angular.module('myApp', []);
    
    myApp.controller('MainCtrl', ['$scope', function ($scope) {
      // Controller Funktion
    }]);

Bevor mit der Arbeit an der App begonnen werden kann muss ein *Angular Module* erstellt werden, welches die gesamte Logik der App beherbergen wird. Es gibt mehrere Wege um Modules zu deklarieren. Mehrere Methodenaufrufe können auf folgende Weise miteinander verkettet werden (ich mag diese Art der Notation nicht besonders):

    angular.module('myApp', [])
    .controller('MainCtrl', ['$scope', function ($scope) {...}])
    .controller('NavCtrl', ['$scope', function ($scope) {...}])
    .controller('UserCtrl', ['$scope', function ($scope) {...}]);

Stattdessen ein globales Module zu definieren hat sich in Angular Projekten, an denen ich arbeitete, als beste bzw. bessere Vorgehensweise erwiesen. Die fehlenden Semikolons und versehentliches Abbrechen der "Kette" stellte sich als kontraproduktiv und anfällig für unnötige Kompilierungsfehler heraus. Definiere stattdessen ein globales Module:

    var myApp = angular.module('myApp', []);
    myApp.controller('MainCtrl', ['$scope', function ($scope) {...}]);
    myApp.controller('NavCtrl', ['$scope', function ($scope) {...}]);
    myApp.controller('UserCtrl', ['$scope', function ($scope) {...}]);

Jede neue Datei greift auf diesen `myApp` Namespace zu, um sich in die Application zu integrieren. Ja, ich erstelle für jeden Controller, Directive, Factory und alles andere eine neue Datei (du wirst das zu schätzen lernen). Die Dateien werden zu einer einzigen Datei, welche später in die Webseite eingeunden wird, zusammengefasst (*concatenated*) und minified (zum Beispiel mittels Grunt oder Gulp).



### Controllers

Nachdem zuvor das Konzept von MVC und der grundlegende Aufbau eines Angular Projekts vermittelt wurde, wird nun die Verwendung von Controllern in Angular behandelt.

Am oben verwendeten Beispiel erfordert es nur einen kleinen Schritt um Daten vom Controller aus ins DOM einzufügen. Angular verwendet eine Templating-style `{{ handlebars }}` Syntax um Placeholders in das HTML einzufügen und Daten an bestimmter Stelle ins DOM einzufügen. Das HTML selbst sollte (im Idealfall) gar keinen tatsächlichen Text oder hartkodierte Werte enthalten um Angular so gut wie möglich zu nutzen. Das folgende Beispiel zeigt wie ein einfacher String ins DOM eingefügt werden kann:

HTML:

    <div ng-app="myApp">
        <div ng-controller="MainCtrl">
             {{ text }}
        </div>
    </div>

JavaScript:

    var myApp = angular.module('myApp', []);

    myApp.controller('MainCtrl', ['$scope', function ($scope) {
        
        $scope.text = 'Hello, Angular fanatic.';
        
    }]);

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/mN7QB/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Das wichtigste oben dargestellte Konzept ist das `$scope` Konzept, welches in jeder Funktion eines Controllers verwendet wird. Die `$scope` Variable verweist auf das aktuelle Element/Bereich im DOM (dies ist nicht dasselbe wie `this`) und bietet eine sehr praktische Art des Scopings, die Daten und Logik vollständig auf ein Element beschränkt (*scoped*). Damit bietet Angular quasi JavaScripts Konzept des *public* und *private* Scopings von Variablen für das DOM an - das ist wirklich großartig.

Im ersten Augenblick mag das `$scope` Konzept ein wenig einschüchternd wirken, aber es ist für Daten in Angular der Weg vom Server (oder als statische Daten) ins DOM. Die Demo oben stellt das grundlegende Vorgehen dar, um Daten in das DOM einzufügen.

Als nächstes folgt ein ähnliches Beispiel, allerdings mit etwas typischeren Daten, welche rein hypothetisch von einem Server gesendet worden sein könnten, um Login Informationen eines Users anzuzeigen. Im Beispiel werden die Daten noch statisch ins JavaScript eingebettet; Später wird gezeigt wie dynamische JSON Daten abgefragt werden können.

JavaScript:

    var myApp = angular.module('myApp', []);

    myApp.controller('UserCtrl', ['$scope', function ($scope) {
        
        // Die Nutzerdaten namespacen.
        // Auch praktisch als visuelles DOM Hilfsmittel.
        $scope.user = {};
        $scope.user.details = {
          "username": "Todd Motto",
          "id": "89101112"
        };
        
    }]);

HTML:

    <div ng-app="myApp">
        <div ng-controller="UserCtrl">
            <p class="username">Welcome, {{ user.details.username }}</p>
            <p class="id">User ID: {{ user.details.id }}</p>
        </div>
    </div>

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/425KU/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Es ist wichtig sich dessen bewusst zu sein, dass Controller nur zur Kontrolle von *Daten*, und zum Erstellen von Funktionen (auch Event-spezifische Funktionen), welche mit dem Server kommunizieren und JSON Daten senden und empfangen, gedacht sind. DOM Manipulation sollte an dieser Stelle nicht vollzogen werden - kein Ort für jQuery Code. Für DOM Manipulation werden Directives verwendet. Diese werden im Folgenden berschrieben.

Pro-Tipp: In der gesamten Angular Documentation (Stand: 2. Oktober 2013) werden in Code Beispielen Controller folgendermaßen erstellt:

    var myApp = angular.module('myApp', []);

    function MainCtrl ($scope) {
      //...
    };

Dies sollte vermieden werden, da auf diese Weise alle Funktionen im globalen Scope definiert werden würden und somit nur sehr lose an die App gebunden wären. Es bedeuted außerdem, dass der Code nicht minified werden kann oder einfach Tests ausgeführt werden können. Der globale Namespace sollte nicht überfüllt werden und Controller sollten *in* der App definiert werden.



### Directives

Eine Directive ([lies auch meinen Artikel über Directives von bestehenden Scripts/Plug-ins](http://toddmotto.com/creating-an-angularjs-directive-from-one-of-your-existing-plugins-scripts)) in ihrer einfachsten Form ist ein kleiner Abschnitt templated HTML, welcher möglichst an mehreren Stellen in der Application benötigt und verwendet wird. Directives stellen eine einfache Möglichkeit dar, ohne großen Aufwand und Interaktionen mit dem DOM, DOM Elemente zur Application hinzuzufügen. Directives sind allerdings nicht nur *einfach* und sie zu meistern setzt eine haarsträubenden Lernkurve voraus. Der nächste Abscnitt, jedoch, vermittelt die wesentlichen Grundlagen um mit ihnen zu arbeiten.

Directives sind für eine Vielzahl von Dingen, welche DOM Komponenten/Elemente wie zum Beispiel Tabs oder Navigationselemente umfassen, nützlich - praktisch hängt es also davon ab welche Elemente im UI der App genutzt werden. Anders gesagt: Falls du jemals mit `ng-show` oder `ng-hide` herumgespielt hast, *das* sind Directives (auch wenn sie keine DOM Elemente einfügen).

Das folgende Beispiel ist wieder sehr einfach: Das Markup für den nutzerdefinierten Button vom Typ *customButton* soll nicht immer wiederholt werden müssen, deshalb wird eine Directive für `customButton` angelegt, welche das Markup für den Button definiert. Es gibt mehrere Wege um eine Directive im DOM/HTML anzugeben:

    <!-- 1: Als Attribut Deklaration -->
    <a custom-button>Click me</a>

    <!-- 2: Als nutzerdefiniertes Element -->
    <custom-button>Click me</custom-button>

    <!-- 3: Als Klasse (für Kompatibilität mit alten IEs genutzt) -->
    <a class="custom-button">Click me</a>

    <!-- 4: Als HTML Kommentar -->
    <!-- directive: custom-button -->

Ich bevorzuge die erste Variante, Attribute. Nutzerdefinierte Elemente sind dank Web Components Bestandteil von HTML5, doch Angular bezeichnet diese als recht buggy in einigen älteren Browsern.

Nachdem nun vermittelt wurde wie Directives an bestimmten Stellen im HTML verwendet werden, wird im Folgenden am Beispiel des Buttons erklärt wie die Directives erstellt werden. Wie auch zuvor wird dazu auf den Namespace der Application, `myApp`,  zugegriffen. In ihrere einfachsten Form sieht eine Directive folgendermaßen aus:

    myApp.directive('customButton', function () {
      return {
        link: function (scope, element, attrs) {
          // DOM Manipulation/Events hier ergänzen
        }
      };
    });

Die Directive wird mittels der `.directive()` Methode definiert, welche den Namen der Directive, hier *customButton*, als Parameter erwartet. Directive Namen in Camel-Case Notation sind gleichzusetzen mit Namen im HTML, welche einen Bindestrich als Trennsymbol verwenden (siehe voriges Beispiel), wobei die Großbuchstaben im Camel-Case-Namen zu Kleinbuchstaben tranformiert werden und ihnen ein Bindestrich vorangestellt wird.

Eine Directive definiert sich selbst als ein Object, welches eine Vielzahl an Parametern definieren kann, und von der Directive Funktion zurückgegeben wird. Die wichtigsten Parameter, welche es zuerst zu meistern gilt, sind `restrict`, `replace`, `transclude`, `template` und `templateUrl`, sowie die `link` Property. Diese wurden dem vorigen Beispiel hinzugefügt:

    myApp.directive('customButton', function () {
      return {
        restrict: 'A',
        replace: true,
        transclude: true,
        template: '<a href="" class="myawesomebutton" ng-transclude>' +
                    '<i class="icon-ok-sign"></i>' +
                  '</a>',
        link: function (scope, element, attrs) {
          // DOM Manipulation/Events hier ergänzen
        }
      };
    });

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/VC4H2/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Es ist hilfreich einmal das eingefügte Markup zum Beispiel mittels den *Chrome Entwicklertools* oder *Firebug* zu betrachten. Das Icon wird nicht geladen und angezeigt, da *Font Awesome* nicht in die Wesbeite eingebunden wurde, aber es veranschaulicht die Funktionsweise von Directives. Nachfolgend werden die einzelnen Parameter der Directive erläutert:

+   `restrict`: Diese Option bezieht sich auf die Verwendungsweise der Directive, und wie diese eingeschränkt werden soll. Falls das Projekt *legacy IE Support* erfordert, werden wahrscheinlich Attribut/Klassen Deklarationen von Nöten sein. Das Einschränken als "A" bedeutet, dass die Directive auf *Attribute* begrenzt wird. Ähnlich steht "E" für *Elemente*, "C" für *Klassen* (Classes) und "M" für *Kommentare*. Die Standardkonfiguration ist "EA", welche zeigt, dass die Optionen auch kombiniert werden können.
+   `replace`: Gibt an ob das DOM Element, welches die Directive aufgerufen hat, mit dem Template der Directive ersetzt werden soll. Eine Demonstration bietet das Beispiel oberhalb.
+   'transclude`: Einfach ausgedrückt erlaubt es diese Option, festzulegen ob der Inhalt des aufrufenden DOM Elements in die Directive (Template) kopiert werden soll. Beispielweise ist im oberen Ergebnis zu sehen, dass beim Rendern die Wörter "Click Me" aus dem ursprünglichen Markup in das Template der Directive übertragen worden sind.
+   `template`: Der Inhalt (Markup), welcher ins DOM eingefügt werden soll. Diese Option sollte nur für kleine Abschnitte HTML verwendet werden. Das eingefügte Template wird von Angular kompiliert, was die Verwendung von *Handlebar Template Tags* erlaubt, um Bindings im Template einzurichten.
+   `templateUrl`: Erlaubt die Angabe eines Templates, welches in eine separaten Datei *oder* ein `<script>` Tag ausgelager ist. Mittels einer Template URL kann zum Beispiel auf eine Template Datei, welche eine überschaubare Menge HTML Code enthält, aber als eigenständige Datei, vorzugsweise in einem extra *Templates* Verzeichnis, existieren muss, verwiesen werden, indem der Pfad der Datei angegeben wird. Ein Verwendungsbeispiel:

    myApp.directive('customButton', function () {
      return {
        templateUrl: 'templates/customButton.html'
        // Weitere Directive Optionen hier ergänzen
      };
    });

`customButton.html` Template Datei (der Dateiname muss nicht mit dem Namen der Directive übereinstimmen):

    <a href="" class="myawesomebutton" ng-transclude>
      <i class="icon-ok-sign"></i>
    </a>

Der Vorteil an dieser Variante ist, dass der Browser die ausgelesenen HTML Template Datei *cacht*. Eine weitere Alternative, welche allerdings nicht gecacht wird, ist die Deklaration des Templates in einem `<script>` Tag:

    <script type="text/ng-template" id="customButton.html">
    <a href="" class="myawesomebutton" ng-transclude>
      <i class="icon-ok-sign"></i>
    </a>
    </script>

Die `text/ng-template` Typen-Angabe und die ID teilen Angular mit, dass es sichum ein Angular Template handelt. Angular wird dann sowohl nach einer entsprechenden Template Datei als auch nach einem eingebetteten `ng-template` suchen und je nachdem was gefunden wurde (bzw. vom Entwickler verwendet wurde) nutzen. Ich bevorzuge die Verwendung von `.html` Dateien, da sie sehr leicht zu verwalten sind, die Performance steigern und das Markup übersichtlich halten. Egal ob 1 oder 100 Directives, der Code bleibt auf diese Weise gurt lesbar und überschaubar.



### Services

Services sind häufig die Ursache für Verwirrung. Meiner Erfahrung und Recherche nach, bieten sie viel mehr einfach einen anderen (komfortableren) Weg Dinge umzusetzen als *besonders viele* neue Funktionalitäten zu implementieren. Ein Blick in die Angular Source zeigt, dass sie denselben Compiler durchlaufen und eine menge Funktionalität teilen. Aufgrund meiner Recherche empfehle ich, Services für *Singletons*, und Factories für etwas komplexere Aufgaben/Funktionen, wie das Arbeiten mit Object Literalen, und in anderen komplizierteren Anwendungsfällen, zu verwenden.

Das folgende Beispiel zeigt einen Service, welcher zwei Zahlen miteinander multipliziert:

    myApp.service('Math', function () {
      this.multiply = function (x, y) {
        return x * y;
      };
    });

Diesser kann dann folgendermaßen in einem Controller verwendet werden:

    myApp.controller('MainCtrl', ['$scope', function ($scope) {
        var a = 12;
        var b = 24;

        // Gibt 288 zurück
        var result = Math.multiply(a, b);
    }]);

Zugegeben, Multiplikation ist sehr einfach und erfordert nicht unbedingt einen eigenen Service, aber die Funktionsweise solcher wurde damit vermittelt.

Bei der Verwendung eines Services (oder Factories) ist zu beachten, dass Angular die Abhängigkeit (*Dependency*) von diesem mitgeteilt wird, sodass es die notwendige Komponente (der Service oder Factory) bereitstellen kann, anderenfalls wird es zu einem Kompilierungsfehler kommen und der Controller wird nicht mehr benutzbar sein. Die Abhängigkeiten werden Angular mittels einfacher *Dependency Injection* mitgeteilt. Dies geschieht beim Definieren des Controllers in dem Bereich `function ($scope)`. Die Aufgabe des `['$scope']`-Teils davor, wird später erläutert. Nachfolgend wird demonstriert wie Angular über die Abhängigkeit eines Services mittels Dependency Injection unterrichtet werden kann:

    // `Math` an Controller übergeben
    myApp.controller('MainCtrl', ['$scope', 'Math', function ($scope, Math) {
        var a = 12;
        var b = 24;

        // Gibt 288 zurück
        var result = Math.multiply(a, b);
    }]);



### Factories

Der Sprung von Services zu Factories sollte nun ein leichter sein. Methoden können einfach oder mittels einer Object Literale zur Factory hinzugefügt werden: 

    myApp.factory('Server', ['$http', function ($http) {
      return {
        get: function(url) {
          return $http.get(url);
        },
        post: function(url) {
          return $http.post(url);
        },
      };
    }]);

Im obigen Beispiel wird ein nutzerdefinierter Wrapper (`Server`) für Angulars XHR Methoden erstellt. Nach der Dependency Injection in einen Controller kann die Factory in diesem benutzt werden:

    myApp.controller('MainCtrl', ['$scope', 'Server', function ($scope, Server) {
        var jsonGet = 'http://myserver/getURL';
        var jsonPost = 'http://myserver/postURL';
        Server.get(jsonGet);
        Server.post(jsonPost);
    }]);

Für andere Anwendungsfälle wie das *Polling* eines Servers für Änderungen, oder für die Verwendung von Sockets könnten noch zusätzliche Methoden wie `Server.poll(jsonPoll)` oder `Server.socket(jsonSocket)` definiert werden. Dies eröffnet Möglichkeiten zur Modularisierung des Code und sogar zum Entwickeln von Tools und Libraries um den Code in den Controllern auf ein Minimum zu reduzieren.



### Filters

Filters werden in Verbindung mit Arrays von Daten, aber auch außerhalb von Loops verwendet. Für das Filtern von Daten in einem Array ist dies das richtige Werkzeug, allerdings können Filters auch verwendet werden um, zum Beispiel, die Eingabe eines Nutzers in ein `<input>` Feld zu analysieren. Filter können auf mehrere Arten definiert werden: Innerhalb von Controllern, oder als Methode. Das folgende Beispiel veranschaulicht die Verwendung eines, als Methode definierten, Filters:

JavaScript:

    myApp.filter('reverse', function () {
        return function (input, uppercase) {
            var out = '';
            for (var i = 0; i < input.length; i++) {
                out = input.charAt(i) + out;
            }
            if (uppercase) {
                out = out.toUpperCase();
            }
            return out;
        }
    });

    // Controller nur enthalten um Daten zur Verfügung zu stellen
    myApp.controller('MainCtrl', ['$scope', function ($scope) {
        $scope.greeting = 'Todd Motto';
    }]);

Verwendung im Markup:

    <div ng-app="myApp">
        <div ng-controller="MainCtrl">
            <p>No filter: {{ greeting }}</p>
            <p>Reverse: {{ greeting | reverse }}</p>
        </div>
    </div>

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/pmh4s/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Die Daten (`greeting`) wurden mithilfe der Pipe (`|`) an den Filter (`reverse`) übergeben, welcher daraufhin auf die Daten angewandt wurde und die Reihenfolge der Zeichen umkehrte.

Ein Beispiel für die Verwendung von Filters in Kombination mit `ng-repeat`:

    <ul>
      <li ng-repeat="number in myNumbers |filter:oddNumbers">{{ number }}</li>
    </ul>

Zuletzt ein kurzes Beispiel für die Definition eines Filters *in* einem Controller und die anschließende Verwendung mit `ng-repeat`:

JavaScript:

    myApp.controller('MainCtrl', ['$scope', function ($scope) {
        
        $scope.numbers = [10, 25, 35, 45, 60, 80, 100];
        
        $scope.lowerBound = 42;
        
        // Den Filter definieren
        $scope.greaterThanNum = function (item) {
            return item > $scope.lowerBound;
        };
        
    }]);

Markup:

    <li ng-repeat="number in numbers | filter:greaterThanNum">
      {{ number }}
    </li>

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/cZbCf/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Das waren die wesentlichen Grundlagen von AngularJS und dessen APIs. Die Möglichkeiten von Angular wurden nur angekratzt, das vermittelte Wissen reicht jedoch aus um die ersten Applications mit Angular zu erstellen.





## *Two-Way Data-Binding*

*Two-Way Data-Binding*, zu Deutsch: *Bidirektionale/Zwei-Wege Datenverbindung*, beschreibt eine vollständige Synchronisierung der Daten, mit Austausch von aktualisierten Daten in beide Richtungen. Dadurch entsteht ein vollständiger Kreislauf von synchronisierten Daten: Das aktualisierte *Model* aktualisiert den *View*, und der aktualisierte *View* aktualisiert das *Model*. Dies gewährleistet synchronisierte Daten ohne Umstände. Wenn zum Beispiel ein Model mittels `ng-model` mit einem `<input>` Element verbunden wird und Daten in dieses Feld eingetragen werden, wird automatisch das dazugehörige Model erstellt bzw. aktualisiert.

Im folgenden Beispiel wird ein `<input>` Feld erstellt und mit einem Model namens *myModel* verbunden. Mittels der *Curly Handlebars Syntax* können die Daten des Models gleichzeitig auch noch an anderer Stelle ausgegeben werden, und die Anzeige im View wird ebenfalls synchronisiert.

HTML:

    <div ng-app="myApp">
        <div ng-controller="MainCtrl">
            <input type="text" ng-model="myModel" placeholder="Start typing..." />
            <p>My model data: {{ myModel }}</p>
        </div>
    </div>

JavaScript:

    myApp.controller('MainCtrl', ['$scope', function ($scope) {
        // Die Model Daten abfragen und/oder
        // Initialwerte zuweisen (hier ein leerer String)
        $scope.myModel = '';
    }]);

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/qrr3q/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>





## XHR/Ajax/`$http` Abfragen und JSON Anbindung

Bisher wurde in diesem Artikel sowohl dargestellt wie einfache Daten im `$scope` zur Verfügung gestellt werden können, als auch wie *Two-Way Data-Binding* funktioniert. Als nächstes soll vermittelt werden wie XHR Abfragen ausgeführt werden können. Diese sind für Websites nicht zwangsläufig relevant, sofern diese nicht Ajax in irgendeiner Form verwenden, sind aber essenziell für Web Applications um Daten von einem Server abzufragen.

In einer lokalen Entwicklungsumgebung ist es wahrscheinlich, dass für den Betrieb eines lokalen Servers Java ASP .NET, PHP oder Ähnliches verwendet wird. Dabei ist es egal ob der Server mit einer lokalen Datenbank kommuniziert oder ob er als tatsächliche Schnittstelle zu einer anderen Resource fungiert, das Vorgehen ist dasselbe.

*"Dollar HTTP"* - Die `$http` Methode ist ein großartiger Angular Wrapper zum Zugriff auf Daten auf einem Server, und so einfach zu nutzen, dass sie quasi blind bedient werden könnte. Das nachfolgende Beispiel zeigt einen einfachen `GET` Request um Daten von einem Server *zu erhalten* (*get*). Die Syntax ist der von jQuery sehr ähnlich, der Wechsel zu Angular sollte also ein Leichtes sein.

    myApp.controller('MainCtrl', ['$scope', '$http', function ($scope, $http) {
      $http({
        method: 'GET',
        url: '//localhost:9000/someUrl'
      });
    }]);

Die Funktion gibt ein sogenanntes *Promise Object* zurück, welches beim Umgang mit Callbacks durch seine Effizienz und gute Lesbarkeit überzeugt. Jede Metode eines Promise Objects, welche einen Event Handler (Callback) hinzufügt, gibt wieder das aufrufende Promise Object zurück, sodass mehrere Methoden mittels *Dot Notation* verkettet werden können. Zwei übliche Methoden sind `success` und `error`.

    myApp.controller('MainCtrl', ['$scope', function ($scope) {
      $http({
        method: 'GET',
        url: '//localhost:9000/someUrl'
      })
      .success(function (data, status, headers, config) {
        // Daten erfolgreich empfangen
      })
      .error(function (data, status, headers, config) {
        // Fehler beim Request
      });
    }]);

Wie bereits gesagt, ist diese Art der Event Handler Zuweisung sehr gut lesbar. Durch das Verbinden von Model und View bzw. Aktualisieren des Views auf Basis der Model Daten, werden auch Model und Server miteinander vereint. Nachfolgend wird die Anzeige eines Usernames, welcher über einen Ajax Request abgefragt wurde, demonstriert.

Im Idealfall wird zuerst die Struktur der JSON Daten entworfen, diese beeinflussen wie die Daten in die Application eingebunden werden. Eine einfach gestaltete JSON Response sähe zum Beispiel wie folgt aus:

    {
      "user": {
        "name": "Todd Motto",
        "id": "80138731"
      }
    }

Das vom Server zurückgegebene Object ist in den Promise/Event Handlers über den Parameter `data` verfügbar, der Username (in diesem Fall "Todd Motto") kann also mittels `data.user.name` ausgelesen werden. Die Kommentare im Code unterhalb erläutern das Vorgehen zum Empfangen der JSON Daten:

JavaScript:

    myApp.controller('UserCtrl', ['$scope', '$http', function ($scope, $http) {

      // Erstellen eines `user` Objects
      $scope.user = {};

      // Das Model mit Initialwerten füllen,
      // hier ein leerer String
      $scope.user.username = '';

      // Den Ajax Request ausführen
      // und den Username aus der Response auslesen
      $http({
        method: 'GET',
        url: '//localhost:9000/someUrlForGettingUsername'
      })
      .success(function (data, status, headers, config) {
        // Den Username in das bestehende Model eintragen
        $scope.user.username = data.user.name;
      })
      .error(function (data, status, headers, config) {
        // Fehler beim Request
      });
    }]);

Markup:

    <div ng-controller="UserCtrl">
      <p>{{ user.username }}</p>
    </div>

Als Ergebnis wird der Username angezeigt. Als nächstes wird dieses Konzept mit dem *Declarative Data-Binding* weiter ausgebaut, was das Ganze noch spannender macht.





## *Declarative Data-Binding*

Der Gedanke hinter Angular ist es, dynamisches HTML zu Erstellen, welches reich an Funktionalität ist und eine derartige Menge an Aufgaben nahtlos erledigt, die man sich nie von einem klientseitigen Web Framework nie hätte erträumen lassen - und das ist genau das was Angular tut.

Man stelle sich beispielweise einmal vor, dass mittels Ajax eine Liste von E-Mails, mit Betreff, Absender und Datum, abgefragt wird, und diese gerendet werden soll (bzw. ins DOM eingefügt werden soll). Dies ist ein klassisches Beispiel für eine Situation in der die Stärke von Angular zum Vorschein erstrahlt. In dem Beispiel wird zuerst ein Controller für die E-Mails erstellt:

    myApp.controller('EmailsCtrl', ['$scope', function ($scope) {

      // Erstellen eines `emails` Objects
      $scope.emails = {};

      // Vom Server empfangene Daten emulieren,
      // ein Array von Objects
      $scope.emails.messages = [{
            "from": "Steve Jobs",
            "subject": "I think I'm holding my phone wrong :/",
            "sent": "2013-10-01T08:05:59Z"
        },{
            "from": "Ellie Goulding",
            "subject": "I've got Starry Eyes, lulz",
            "sent": "2013-09-21T19:45:00Z"
        },{
            "from": "Michael Stipe",
            "subject": "Everybody hurts, sometimes.",
            "sent": "2013-09-12T11:38:30Z"
        },{
            "from": "Jeremy Clarkson",
            "subject": "Think I've found the best car... In the world",
            "sent": "2013-09-03T13:15:11Z"
        }];

    }]);

Als nächstes müssen die Model Daten in den View bzw. das DOM eingefügt werden. An dieser Stelle kommen *Declarative Bindings* zum Einsatz, um *festzulegen* (*declare*) was die Application tun soll - um den ersten Anteil dynamischen HTMLs zu erstellen. Dafür wird im Beispiel Angulars built-in Directive `ng-repeat` verwendet, welche die Daten durchläuft und die Ausgabe rendert, ohne jegliche Callbacks oder Zustandsänderungen, frei von viel Aufwand:

    <ul>
      <li ng-repeat="message in emails.messages">
        <p>From: {{ message.from }}</p>
        <p>Subject: {{ message.subject }}</p>
        <p>{{ message.sent | date:'MMM d, y h:mm:ss a' }}</p>
      </li>
    </ul>

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/TAVQc/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Das Beispiel demonstriert außerdem die Verwendung des `date` Filters und zeigt wie UTC Daten verarbeitet werden können.

Angular bietet eine Vielzahl mehr an `ng-*` Directives, welche durchaus einen Blick würdig sind um die volle Stärke von Declarative Bindungs nutzen zu können. Das Experimentieren mit diesen Directives festigt das Verständnis vom Weg der Daten vom Server zum Model, bis hin zum View, um die Daten schließlich zu rendern.





## Scope Functions

Als Weiterführung von Declarative Binding sind Scope Functions der nächste Schritt beim Erstellen von Applications mit Funktionalität. Als Beispiel zeigt der folgende Code eine Funktion um eine der E-Mails zu *löschen*:

    myApp.controller('MainCtrl', ['$scope', function ($scope) {

      $scope.deleteEmail = function (index) {
        $scope.emails.messages.splice(index, 1)
      };

    }]);

Pro-Tipp: Es ist wichtig sich bewusst zu machen, dass man Daten *aus dem Model* entfernt, anstatt tatsächliche DOM Elemente zu löschen. Als ein MVC Framework, wird Angular, dank des Two-Way Data-Bindings und seiner Callback-freien Beschaffenheit, all dies selbstständig erledigen, solange der Code strukturiert wurde um auf die Daten zu reagieren.

Das Einbinden der Funktionen in den Scope (HTML) wird ebenfalls mittels `ng-*` Directives umgesetzt. In diesem Beispiel wird die `ng-click` Directive verwendet:

    <a ng-click="deleteEmail($index)">Delete email</a>

Dies ist etwas völlig anderes als *Inline Click Handlers* (`onclick` Attribut), die Unterschiede werden später erläutert. Im Beispiel wird außerdem eine `$index` Variable als Parameter an die Funktion übergeben. Angular wird automatisch den Index des zu löschenden Elements bestimmen und ihn dieser Variable zuweisen, dies erspart dem Entwickler eine Menge Code und Programmlogik.

Ergebnis (Versuche einige E-Mails zu löschen):

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/BgZmt/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>





## Declarative DOM Methods

Als nächstes werden *DOM Methods* behandelt. Dies sind ebenfalls Directives und implementieren Funktionalität im DOM, welche gewöhnlich noch mehr Programmlogik als im vorherigen Beispiel erfordern würde. Ein gutes Beispiel dafür wäre eine einfache umschaltbare Navigation. Mithilfe von `ng-show` und `ng-click` kann auf einfache Weise eine tadellose *Toggling Navigation* erstellt werden:

    <a href="" ng-click="toggle = !toggle">Toggle nav</a>
    <ul ng-show="toggle">
      <li>Link 1</li>
      <li>Link 2</li>
      <li>Link 3</li>
    </ul>

An dieser Stelle kommt MVVM ins Spiel; Im Beispiel wird kein Controller erstellt. Mehr Informationen zu MVVM folgen später.

Ergebnis (Versuche die Navigation umzuschalten):

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/ZUyW5/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>





## Expressions

Dies ist ein großartiges Feature von Angular, für dessen Funktion anderenfalls JavaScript und eine Menge sich wiederholender Code erforderlich wäre.

Ein klassisches Beispiel:

    elem.onclick = function (data) {
      if (data.length === 0) {
        otherElem.innerHTML = 'No data';
      } else {
        otherElem.innerHTML = 'My data';
      }
    };

Der Code könnte zum Beispiel ein Callback für einen `GET` Request sein, welches das DOM auf Basis der empfangenen Daten aktualisiert. Mit Angular ist dies ohne großartgen Aufwand möglich, als Inline-Notation und ohne JavaScript:

    <p>{{ data.length > 0 && 'My data' || 'No data' }}</p>

Dieses Markup wird die angezeigten Daten ohne die Verwendung von Callbacks dynamisch aktualisierten wann immer die Application neue Daten einholt. Wenn die Response leer ist, wird dies mit "No data" mitgeteilt, anderenfalls wird "My data" angezeigt. Es gibt unzählige Anwendungsfälle für dieses Feature und Angular regelt das alles voll automatisch durch Two-Way Binding.

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/XCwcr/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>





## Dynamische Views und Routing

Der Gedanke hinter Single-Page Web Applications (und auch Websites): Die Seite besteht aus einem Header, einem Footer, einer Sidebar und dem Bereich in der Mitte, welcher abhängig von der aufgerufenen URL mit Inhalt gefüllt wird.

Angular macht die Umsetzung dieses Gedankens zum Kinderspiel und formt damit die sogenannten *Dynamischen Views*. Dynamische Views (zu Englisch: *Dynamic views*) zeigen bestimmte Inhalte (Views), auf Basis der URL an: Umgesetzt werden sie mittels des `$routeProvider`s. Ein Beispiel für eine einfache Konfiguration:

    myApp.config(['$routeProvider', function ($routeProvider) {

      /**
       * $routeProvider
       */
      $routeProvider
      .when('/', {
        templateUrl: 'views/main.html'
      })
      .otherwise({
        redirectTo: '/'
      });

    }]);

Der Code zeigt, dass *wenn* (`when`) die URL "/" ist, `main.html` geladen wird. Es ist ratsam den ersten View "main.html" anstelle von "index.html" zu nennen, sodass letzterer Dateiname für den Code der Single-Page Application selbst verwendet werden kann. Weitere URL-spezifische Views zu ergänzen ist ein Leichtes:

    myApp.config(['$routeProvider', function ($routeProvider) {

      /**
       * $routeProvider
       */
      $routeProvider
      .when('/', {
        templateUrl: 'views/main.html'
      })
      .when('/emails', {
        templateUrl: 'views/emails.html'
      })
      .otherwise({
        redirectTo: '/'
      });

    }]);

`emails.html` kann dann einfach mit dem Markup zum Rendern der E-Mails (siehe oben) gefüllt werden. Das Ergebnis ist eine fortschrittliche Application, deren Entwicklung nur wenig Aufwand erforderte.

Mit dem `$routeProvider` Service kann noch vieles mehr umgesetzt werden, und es lohnt sich auf jeden Fall sich damit noch etwas näher zu befassen, die aufgezeigten Beispiele reichen aber schon aus um ihn bei der Entwicklung von Applications einzusetzen. Beispielweise könnten `$http` Events überwacht werden um zum Beispiel einen Ladekreis anzuzeigen wenn eine Ajax Abfrage ausgeführt wird um neue Daten zu erhalten.





## *Global Static Data* - Globale statische Daten

In Gmail wird ein Großteil der anfänglichen Daten geladen indem das JSON direkt in die Seite eingebettet wird (man betrachte einmal den Quellcode). Beim Einbinden von Daten auf diese Weise wird Angulars Rendering beschleunigt und die Seite ist einsatzbereit sobald geladen.

Beim Entwickeln unserer Apps werden Java-Tags in das Markup eingesetzt, welche beim Generieren des HTMLs durch die vom Back-end produzierten Daten in das HTML integrieren. *(Da ich keinerlei Erfahrung mit Java habe, wurde der Java-Tag im Beispiel durch einen einfachen Platzhalter ersetzt. Statt Java kann natürlich auch jede andere Sprache auf dem Server verwendet werden.)* Dieses Beispiel zeigt wie JSON Daten direkt in die Seite eingebettet werden können und anschließend für unmittelbare Anbindung an den Controller übergeben werden können:

    <!-- In index.html (natürlich am Ende des Markups bzw. der Seite) -->
    <script>
    window.globalData = {};
    globalData.emails = <javaTagHereToGenerateMessages>;
    </script>

Ein vorhandener Java-Tag würde dann beim Parsen des Markups zu den E-Mail-Daten aufgelöst werden sodass Angular die E-Mails sofort rendern könnte. Die Daten müssen nur noch von einem Controller verarbeitet werden:

    myApp.controller('EmailsCtrl', ['$scope', function ($scope) {

        $scope.emails = {};
        
        // Die ersten Daten zuweisen
        $scope.emails.messages = globalData.emails;
        
    }]);





## Minification

Nun soll noch ein wenig auf die Minification von Angular Code eingegenagen werden. Bei den ersten Versuchen einen Angular Code durch einen Minifier laufen zu lassen, ist es wahrscheinlich, dass ein Error auftritt.

Tatsächlich ist die Minification von AngularJS Code ganz einfach, es muss einfach nur der Inhalt der Dependency Injection vor der Funktion in einem Array angegeben werden:

    myApp.controller('MainCtrl',
    ['$scope', 'Dependency', 'Service', 'Factory',
    function ($scope, Dependency, Service, Factory) {

      // Code

    }]);

Nach der Minification:

    myApp.controller('MainCtrl',
    ['$scope', 'Dependency', 'Service', 'Factory',
    function (a,b,c,d) {

      // a = $scope
      // b = Dependency
      // c = Service
      // d = Factory

      // Benutzung des `$scope` Alias
      a.someFunction = function () {...};

    }]);

Es ist lediglich darauf zu achten, dass die Angaben in derselben Reihenfolge wie in der Dependency Injection erfolgen, anderenfalls wird es zu einer nervenaufreibenden Fehlersuche kommen.





## Unterschiede zwischen MVC und MVVM

Bevor dieser Artikel zu Ende ist, sollen noch einmal kurz die Unterschiede zwischen MVC und MVVM erläutert werden:

+   *MVC*: Das Model und der View kommunizeren mit einem Controller, Model-View-Controller
+   *MVVM*: Umfasst das *Declarative Data-Binding*, welches technisch mit sich selbst kommuniziert. Model-View-View-Model; Das Model spricht den View an, und der View kann wiederum das Model ansprechen. Dank Angulars *Two-Way Data-Binding* werden alle Daten, ohne jeden weiteren Aufwand, synchronisiert. Außerdem erlaubt es dem Entwickler, ohne die Verwendung eines Controllers, Programmlogik anzuwenden.

Ein kurzes Beispiel, welches veranschaulicht wie `ng-repeat` verwendet werden kann, ohne dass ein Controller die Daten zur Verfügung stellt:

    <li ng-repeat="number in [1,2,3,4,5,6,7,8,9]">
      {{ number }}
    </li>

Dies dient nur zur Demonstration. Wenn es nicht nur zu einem schnellen Test dient, würde ich immer die Verwendung von Controllern empfehlen, um den Alles etwas übersichtlicher zu gestalten.

Ergebnis:

<iframe width="100%" height="300" src="http://jsfiddle.net/toddmotto/C9GHF/embedded/result,js,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>





## HTML5 Web Components

Wie bereits zuvor angedeutet, erlaubt Angular das Erstellen von nutzerdefinierten Elementen:

    <myCustomElement></myCustomElement>

Dies macht zukünftige Funktionen von HTML5 sogar schon jetzt im Web verfügbar. HTML5 definiert die *Web Components* und das `<template>` Element, mit Angular lassen sich diese schon heute verwenden. Web Components, bestehend aus nutzerdefinierten Elementen, und durch dynamische JavaScript Einbindung vervollständigt, sind für die Befüllung des Views ein wichtiges Thema - und mit Angular können wir diese schon jetzt praktisch nutzen. Das Team hinter Angular denkt damit voraus und sichert so kommende Web Technologien.





## Scope Comments

*Scope Comments* sind meiner Meinung nach wirklich eine gute Ergänzung in meinem Workflow, anstatt mein HTML mit Kommentaren wie diesen zu versehen:

    <!-- header -->
    <header>
      Stuff.
    </header>
    <!-- /header -->

Wenn es um Angular geht, ist die Rede immer von Views und Scopes, nicht vom DOM. Scopes bzw. Gültigkeitsbereiche sind tatsächlich *scoped* (zu Deutsch: *begrenzt*), was bedeutet, dass, solange die Daten nicht bewusst mit anderen Controllern ausgetauscht werden, diese auf *diesen einen* Controller begrenzt sind und nicht von irgendwo anders aus erreichbar sind. Ich erkannte, dass das kennzeichnen von gescopten Bereichen ein großartiges Hilfsmittel ist:

    <!-- scope: MainCtrl -->
    <div class="content" ng-controller="MainCtrl">

    </div>
    <!-- /scope: MainCtrl -->





## AngularJS debuggen

Es existiert eine großartige Chrome Extension, welche von Google für das Entwickeln und Debuggen von Angu8lar Applications empfohlen wird - *AngularJS Batarang* kann [hier](https://chrome.google.com/webstore/detail/angularjs-batarang/ighdmehidhipcmcojjgiloacoafjmpfk) heruntergeladen werden.



*Happy coding.*





## Mehr zum Thema

+   Controller strukturieren: [*Rethinking AngularJS Controllers*](http://toddmotto.com/rethinking-angular-js-controllers)
+   Nutzerdefinierte Filter: [*Everything about custom filters in AngularJS*](http://toddmotto.com/everything-about-custom-filters-in-angular-js)
+   Die `Controller as` Sytax: [*Digging into Angular’s “Controller as” syntax*](http://toddmotto.com/digging-into-angulars-controller-as-syntax)
+   Die `$emit`, `$broadcast` und `$on` Events des `$scope` und `$rootScope` Event Systems: [*Understanding Angular’s $scope and $rootScope event system $emit, $broadcast and $on*](http://toddmotto.com/all-about-angulars-emit-broadcast-on-publish-subscribing)
+   Kleinst-Module mithilfe von IIFE: [*Minimal Angular module/syntax approach using an IIFE*](http://toddmotto.com/minimal-angular-module-syntax-approach-using-an-iife)
+   Englischsprachiger Originalartikel: [*Ultimate guide to learning AngularJS in one day*](http://toddmotto.com/ultimate-guide-to-learning-angular-js-in-one-day/)
+   Französische Übersetzung des Originalartikels: [*Apprendre Angular en un jour, le guide ultime*](http://vfsvp.fr/article/apprendre-angular-en-un-jour-le-guide-ultime)
+   Originalartikel als Präsentation: [*AngularJS in one day*](https://speakerdeck.com/toddmotto/angularjs-in-one-day)
+   Eine Directive aus einem bestehenden Script oder Plug-in erstellen: [*Creating an AngularJS Directive from one of your existing plugins/scripts*](http://toddmotto.com/creating-an-angularjs-directive-from-one-of-your-existing-plugins-scripts)

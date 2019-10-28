_Übungsaufgabe zur Veranstaltung [IT Systeme](https://hsro-wif-oop.github.io) im [Bachelorstudiengang Wirtschaftsinformatik](https://www.th-rosenheim.de/technik/informatik-mathematik/wirtschaftsinformatik-bachelor/) an der [Hochschule Rosenheim](http://www.th-rosenheim.de)._


# 04 - Betriebssysteme

In dieser Übung werden wir uns weiter ein wenig mit Schaltnetzwerken beschäftigen.

**Die Lösung befindet sich im Branch Musterloesung.**


## Aufgabe 1: Prozesse

Gegeben ist folgendes Java Programm. Es ruft einen Prozess aus einem Java Programm auf. Dabei wird das Resultat auf `System.out` ausgeben.

```java
import java.io.*;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;
import java.util.function.Consumer;

public class Proc {

    private static class StreamGobbler implements Runnable {
        private InputStream inputStream;
        private Consumer<String> consumer;

        public StreamGobbler(InputStream inputStream, Consumer<String> consumer) {
            this.inputStream = inputStream;
            this.consumer = consumer;
        }

        @Override
        public void run() {
            new BufferedReader(new InputStreamReader(inputStream)).lines().forEach(consumer);
        }
    }

    public static void main(String[] args)  {
        // bestimmt, um welches OS es sich handelt
        boolean isWindows = System.getProperty("os.name")
                .toLowerCase().startsWith("windows");

        ProcessBuilder builder = new ProcessBuilder();
        if (isWindows) {
            builder.command("cmd.exe", "/c", "dir");
        } else {
            builder.command("sh", "-c", "ls");
        }
        // Setzen spezifischer Einstellungen
        builder.directory(new File(System.getProperty("user.home")));

        try {
            Process process = builder.start();
            StreamGobbler streamGobbler = new StreamGobbler(process.getInputStream(), System.out::println);
            Executors.newSingleThreadExecutor().submit(streamGobbler);
            process.waitFor(1000, TimeUnit.MILLISECONDS);
        }
        catch (IOException ex) {
            System.out.println(ex.getMessage());
        }
        catch (InterruptedException iex) {
            System.out.println(iex.getMessage());
        }

    }
}
```
## a)
Legen Sie zunächst ein Projekt an, compilieren das Projekt und führen das Program aus.

- Welche Ausgabe bekommen Sie?
- Welches Systemprogramm wird ausgeführt und was bedeuten die Parameter?

## b)
Können Sie das Verzeichnis ändern, das ausgeben wird?

## c)
Ändern Sie das Programm so ab, dass Sie ein  beliebiges Command über die Commandline beim Programmaufruf als Argument übergeben können, z.b.

Beispiele (auf Windows):

 ```cmd
 // Verzeichnis 'test' anlegen
java Proc "mkdir test"  

// das kennen Sie schon
java Proc "dir"

// löscht da Verzeichnis 'test' wieder
java Proc "rmdir test"

// das kennen Sie schon
java Proc "dir"
```

## d)

Ändern Sie die Ausgabe so ab, dass stets die Systemzeit der Zeile vorgestellt wird, z.B.:

```
1572294004867: 03/15/2019  08:40 AM    <DIR>          Saved Games
1572294004868: 05/14/2019  11:05 AM    <DIR>          Searches
1572294004868: 05/20/2019  10:38 AM    <DIR>          source
1572294004868: 10/16/2019  07:02 PM             2,695 test.pf
1572294004868: 10/02/2019  12:08 PM             1,069 text.txt
```

> Note: Hierzu können Sie die komplette `run()`-Methode umschreiben oder einfach eine `map`-Methode in den Stream einfügen, mit einem entsprechenden Lambda-Ausdruck.

## e)   

Ändern Sie die Ausgabe so um, dass diese in eine Datei 'dat.txt' geschrieben wird und diese kontinuierlich weitergeschrieben wird.
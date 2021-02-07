
# Struttura del progetto EjLog

La struttura del progetto EjLog è a due livelli

* la parte "custom"
* la parte "base" (cartella Wmsbase2)

L'idea di fondo è che nel progetto base siano contenute tutte le classi di utilizzo standard. Nel progetto custom vengono inserite le classi specifiche per un impianto (esempio: sinottico) e tutte quelle classi del progetto base che necessitano di essere ridefininte (esempio: IconTokenRenderer per visualizzare sul sinottico le icone delle Udc in modo particolare).

Al momento della compilazione e della creazione del pacchetto jar complessivo del progetto, le classi custom sovrascrivono quelle base (in generale sono inserite prima nel [classpath][1] e quindi sono le classi ad essere istanziate al posto di quelle base).



  [1]: https://dev.to/autonomousapps/build-compile-run-a-crash-course-in-classpaths-f4g


# Importazione Progetto in Eclipse

L'importazione del progretto in Ecipse avviene per mezzo dei seguenti passaggi

1. Avviare Eclipse
2. Selezionare uno workspace (Esempio: c:\ws\EjLogTrunk)
3. Selezionare File - Import - Existing Gradle Project
4. Premere next e poi finish

TODO: Completare con videate

```{error}
ATTENZIONE NON E' UNA BUONA PRATICA INFILARE LO WORKSPACE IN MEZZO AI SORGENTI !!!!

DEFINIRE UNA CARTELLA A PARTE RISERVATA esempio c:\ws

C'E' IL FORTE RISCHIO DI COMMITTARE LO WORKSPACE COME PARTE DEI SORGENTI !!!

NON FARLO !
```

```{warning}
Uno workspace è un concetto / strumento legato all'IDE Eclipse. Nulla ha a che vedere con la compilabilità del programma.

Se un progetto non compila da command line con Gradle, non ci sono speranze di farlo funzionare da Eclipse !
```

```{note}
Come convenzione per il nome workspace utilizziamo la seguente regola

*nome progetto + nome ramo di sviluppo*

Esempio: *EjLogTrunk*  oppure *EjLogTag2.3.12.2* oppure *EjLogBranchTestFunzionalitàXYZ*

Mai utilizzare spazi !
```

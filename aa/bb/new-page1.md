---
title: Ma Che Mi Hai Portato
description: 
published: true
date: 2021-02-01T14:18:54.045Z
tags: 
editor: undefined
dateCreated: 2021-02-01T13:18:59.230Z
---

CIAO

# Sistema di build (Gradle)

Il sistema di build del progetto EjLog è [Gradle][1].

Gradle si occupa della risoluzione delle dipendenze delle libreria e della compilazione (invocando il compilatore java dietro le quinte).

La struttura delle cartelle del progetto EjLog NON riflette quella standard di un progetto Gradle (che poi riflette la struttura standard di un progetto Maven). Le motivazioni sono storiche: quando si è partiti, il progetto era gestito tramite Ant e la struttura delle cartelle era più snella.

[home](/home)

[stockimport_fix.wsdl](/stockimport_fix.wsdl)

## Compilazione del progetto Wmsbase2

> Quanto contenuto nella cartella Wmsbase2 è una entità a se stante, **che deve risultare compilabile**.
{.is-warning}



Quindi, aprendo un prompt dei comandi nella cartella, **deve** funzionare il sequente comando

```
gradlew build
```

Cosa accade invocando questo comando **(ATTENZIONE ALLA W che sta per Wrapper !!!!!)**

1. **gradlew.bat** è un file batch per Windows (gradle senza estensione è l'equivalente per Linux)
2. eseguendolo (aprire il file con un editor per capire meglio) viene cercata la virtual machine Java puntata dalla variabile di ambiente **JAVA_HOME**, che esegue il jar **gradle-wrapper**

```
...
set CLASSPATH=%APP_HOME%\gradle\wrapper\gradle-wrapper.jar

@rem Execute Gradle
"%JAVA_EXE%" %DEFAULT_JVM_OPTS% %JAVA_OPTS% %GRADLE_OPTS% "-Dorg.gradle.appname=%APP_BASE_NAME%" -classpath "%CLASSPATH%" org.gradle.wrapper.GradleWrapperMain %CMD_LINE_ARGS% --no-daemon
...
```
3. il wrapper scarica sul PC il file binary della corretta versione di gradle **(indicata in ...\Wmsbase2\gradle\wrapper\gradle-wrapper.properties)**

4. viene finalmente invocato il jar del sistema di build Gradle che è stato scarico e viene eseguito il task **build**

---

5. il task di build è un task predefinito di gradle che esegue una serie di compiti base, tra cui compilare i sorgenti (vedere per completezza la documentazione sul sito gradle)

```{warning}
L'utilizzo del gradle wrapper permette a tutti i PC che andranno a compilare il progetto di utilizzare la stessa versione di Gradle. Questa cosa è fondamentale.

Se si invocasse, per errore, **gradle** invece di **gradlew** l'effetto sarebbe quello di cercare nel path di Windows l'installazione di Gradle (package binari del tool), installazione che può esistere ed essere differente da quella puntata dal wrapper.
```

## Compilazione del progetto Wmsbase3

prova



  [1]: https://gradle.org/

---
title: 20_dependencies
description: 
published: true
date: 2021-02-07T14:54:47.455Z
tags: 
editor: markdown
dateCreated: 2021-02-07T14:53:48.500Z
---


# Dipendenze!

Nel seguito sono descritte le cose che sono necessarie affinchè sia possibile lanciare l'applicativo o compilarlo. Chiaramente è necessario compilarlo per poterlo avviare.

## Dipendenze di esecuzione

Per essere solamente eseguito le dipendenze sono:

* JRE 1.8 Oracle (comprensiva dalle librerie JavaFX)
* Sistema operativo in grado di eseguire la JRE del punto precedente
* Microsoft SQL Server versioni dalla 2012 in avanti (2019 l'ultima disponibile al momento)
* Pacchetto di librerie/file di configurazione derivante dall'operazione di compilazione/deploy

**Nota**: l'application launcher WmsLauncher necessita di [7-Zip][2].

Per le differenze tra Java JRE e JDK consultare ad esempio il seguente [link][1]

## Dipendenze di compilazione

Per la compilazione del progetto sono necessari:

* JDK 1.8 Oracle (comprensiva dalle librerie JavaFX)
* [Gradle][3]

[1]: https://www.redhat.com/it/topics/cloud-native-apps/what-is-a-Java-runtime-environment
[2]: https://www.7-zip.org/download.html
[3]: /ejlog/it/10_developer/10_basic_concepts/30_build.md

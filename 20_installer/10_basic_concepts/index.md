
# Installazione

Per poter eseguire il software EjLog occorre soddisfare quanto richiesto dalle [Dipendenze di esecuzione](/ejlog/it/05_Introduction/20_dependencies).

Il software EjLog che viene customizzato per i vari impianti è un software che è sempre differente (ovvero non si ha lo stesso eseguibile da installare per l'impianto A o per l'impianto B).

Affinchè il programma venga installato e possa essere lanciato serve sicuramente reperire il pacchetto di file ottenuto per mezzo del task di deploy (**gradlew dist**).

Occorre poi tenere conto che il sistema EjLog è modulare e può avere differenti moduli installati su nodi di rete differenti.

In sostanza, un installer ommnicomprensivo non è (ancora) disponibile.

```{warning}
**Attenzione**: tutti i pacchetti software vanno installati sempre e unicamente in lingua **Inglese (US)** (esempio: SQL Server Management Studio deve essere installato in tale lingua)
```

Vediamo nel seguito come reperire ed installare i componenti base.

## Download pacchetti generali

Vedere [Choco](20_choco)

## Installazione di SQL Server

SQL Server è un pacchetto software molto articolato, con svariati parametri di installazione

Per informazioni generali consulare [Installazione SQL Server][1].

TBC: completare con parametri di default






  

  [1]: https://docs.microsoft.com/it-it/sql/database-engine/install-windows/install-sql-server

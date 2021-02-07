
# Stesura Documentazione

## Introduzione

Questa documentazione è realizzata tramite pagine in formato Markdown / ReStructuredText, compilate per mezzo del tool [Sphinx][1].

## Creazione di nuove pagine

Per creare nuove pagine è sufficiente

1. aggiungerle nella cartella relativa
2. aggiungere un link dalle altre pagine o in un file di indice
3. eseguire il commit dei file modificati

```{note}
Le fasi di compilazione e pubblicazione del sito sono eseguite in automatico da task Jenkins
```

Le pagine vanno normalmente create in formato [Markdown][3].

Il formato rst è più potente, ma più complesso. Entrambi i formati sono riconosciuti da Sphinx (con l'apposito plugin per Markdown).

## Editor da utilizzare

Ci sono tanti editor per creare file Markdown (dal blocco note in su).

* [Atom][4] non è fantastico, ma permette almeno di iniziare, ha la gestione della cartella del progetto (quindi lo switch tra un file e l'altro è comodo).
Sono disponobili dei plugin che semplificano la vita (offrendo anche una anteprima)
(TODO: indicare i plugin da utilizzare)
* [StackEdit][7] bello, ma sembra solo funzionare via Web, molto scomodo


```{warning}
Se l'editor utilizzato permette di visualizzare l'anteprima HTML potrà capitare che quanto mostrato non coincida con quanto  pubblicato sul sito (in "meglio" e in "peggio").

Queste differenze dipendono dai differenti parser dei file Markdown, dai differenti generatori Html, dai CSS, etc..)
```

## Convenzioni su creazione pagine

### Immagini

Tutte le immagini devono essere collocate in una sottocartella **img** della cartella dove si trova la pagina.

il caricamento dell'immagine è quindi qualcosa tipo:

```text
![](img/nome_file_immagine.png)
```

### Link

```{warning}
I collegamenti tra una pagina e l'altra devono essere sempre e solamente relativi.
```
Esempio di link relativo:

```text
[TestoLink](/ejlog/it/10_developer/10_basic_concepts/30_build.md)
```

## Compilazione del sito

Per compilare il sito occorre eseguire Sphinx, che è un software realizzato in [Python][1].

Per l'installazione seguire le indicazioni su internet.

1. Installare python (attenzione ad eseguire installazione per tutti gli utenti)
2. Installare Sphinx
3. Installare plugin (vedere sotto)
4. eseuire file batch in sottocartella docs (nomi file esplicativi)

TODO: completare procedura di installazione

### Sphinx plugin

Al momento dopo l'installazione di Python è sufficiente eseguire i seguenti comandi

```
pip install Sphinx
pip install Pygments
pip install myst-parser
pip install furo
pip install sphinx-copybutton
```

## Pubblicazione

La pubblicazione avviene lanciando una istanza di [Docker][5] che scarica una immagine del web server [NGINX][6].

```{warning}
Attenzione che il daemon Docker deve essere lanciato da un utente con privilegi di amministratore.
```



  [1]: https://www.sphinx-doc.org/en/master/
  [2]: https://www.python.org/
  [3]: https://www.markdownguide.org/basic-syntax/
  [4]: https://atom.io/
  [5]: https://www.docker.com/
  [6]: https://www.nginx.com/resources/wiki/
  [7]: https://stackedit.io/

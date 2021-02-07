# Web Service

## Introduzione

Per la comunicazione tramite web service si prevede la seguente configurazione:
Il sistema chiamante eseguirà chiamate inviando dati nel formato concordato e, in tempo reale, il sistema ricevente provvederà ad elaborare il set di dati restituendo il risultato al chiamante.

Dovendo implementare uno scambio dati tramite WebService è conveniente arrivare a generere delle classi Java che permettono di utilizzare i servizi in modo più semplice.

## Regole generali di operatività sui dati (WebService)

**Innesco dell’invio dati** Il Chiamante invia i dati in tempo reale ad ogni modifica al proprio dominio dati, nella sequenza cronologica degli eventi che hanno generato la necessità di una trasmissione dati.
**Raggruppamento ed identificazione delle transazioni** Prima di ogni invio dati, il Chiamante genera il codice identificativo della transazione (TransactionId) e uno identificativo della transazione di business (GroupId) e li associa al set dati che verrà inviato.
- **TransactionId** è l’identificativo della transazione dati e raggruppa tutti i record inviati nella medesima trasmissione.
- **GroupId** è l’identificativo della transazione di business e raggruppa tutti i record che appartengono ad un’unica transazione logica di business e che devono essere processati consecutivamente.
- Poiché le chiamate appartenenti allo stesso GroupId possono essere eseguite in tempi differenti, se una delle chiamate incorre in errore, le successive chiamate che contengono dati del medesimo GroupId non saranno eseguite, per evitare inconsistenza nei dati.
- **La ricezione dei record è transazionale** se si verifica un errore, l’intera struttura è rigettata e deve essere nuovamente inviata.
- **Strategia di aggiornamento dati** La strategia è di tipo “Update & Create”. Per ogni record, a parità di chiave, se esistente viene effettuata un’operazione di update altrimenti un’operazione di insert. La cancellazione è di tipo logico e gestita tramite apposito flag di disattivazione del record.
- **La gestione errori è in carico al Chiamante** il rigetto di una chiamata a causa del fallimento di una validazione (es formato data errato), o un problema di connessione di rete, sarà gestito dal chiamante (es. tramite log, interfaccia utente per la gestione e la rielaborazione dati in errore, notifiche, ecc.)
- **Errori di rete** Quando l'errore di transazione è dovuto a un problema di rete, la trasmissione viene ripetuta per 5 volte. In caso di persistenza errori, la trasmissione viene definitivamente contrassegnata come errore e nessun altro tentativo di trasmissione verrà intrapreso.

## REST panoramica architettura

Link: [REST][1]
Di seguito ora approfondiamo l'implementazione di un servizio REST con l'utilizzo del metodo http POST.

## REST Standard e Sicurezza

L’interoperabilità verrà implementata attraverso un servizio WebAPI di tipo REST, ovvero un’architettura che permette di creare delle Application Programming Interface “API” che utilizzano il protocollo http per la definizione dei metodi di comunicazione; è possibile pensare ai servizi REST basati su protocollo http come ad un insieme di diversi componenti:

**Risorsa** REST utilizza indirizzi URI per individuare la risorsa, ad esempio una richiesta rappresentata dall’URL https://www.example.com/ticket/get/1 consente di ottenere le informazioni relative alla risorsa “Ticket” con identificativo eguale ad 1.

**Verbs** sono i comandi inviati tramite protocollo http. Ad esempio GET restituisce una data risorsa, POST crea una data risorsa.

**Headers** informazioni relative alla richiesta presenti nella sezione Header della richiesta http.

**Request Body** il contenuto della richiesta. Ad esempio il body di una richiesta di tipo POST potrà contenere una struttura dati in formato Json.

**Response Body** il contenuto della risposta. Ad esempio body di una risposta potrà essere una struttura dati in formato Json o XML o in formato HTML.

**Response Status Code** Codici definiti dalle risposte http (rfc2616 e successive).

Ogni Metodo, come definito nei punti soprastanti, può essere protetto da una modalità di autenticazione.
La modalità di autenticazione che prendiamo in considerazione per l'esempio seguente è denominata “Basic Http Authentication”. Nel contesto di una transazione Http la “Basic Http Authentication” è una modalità definita per fornire credenziali (UserName e Password) di accesso tra Client e Server.

La **“Basic Http Authentication”** è una delle tecniche che consentono di applicare il controllo di accesso a risorse web; essa non richiede l’utilizzo di cookie o identificativi di sessione o form di login. La “Basic Http Authentication” utilizza Header http statici e standard ad esempio: “Authorization: Basic dGVzdDp0ZXN0”.
Nella modalità “Basic Http Authentication” le credenziali (UserName e Password) sono codificate in Base64 e inserite nella sezione Header della richiesta Http, la protezione delle credenziali, relative alla modalità “Basic Http Authentication”, è realizzata utilizzando il protocollo HTTPS per tutte le comunicazioni client-server.

## REST interfaccia dati API (Http verb: POST)

Il servizio REST esposto in questo esempio si chiama **"ResourceComponentInfo”**.

Integrazione mediante WebService (WebApi) - REST) con Request Payload in formato json.

```
URL: [WSURL]/api/v1/ResourceComponentInfo
```

Questo metodo web consente di interrogare il web service e di ricevere, a seguito dell’immissione di dati parametri (oggetto **Request Payload**), informazioni di dettaglio su sul contenuto di una Udc (oggetto **Response Object**).

I punti che verranno analizzati sono:

1. **Struttura Request Payload Web Service Rest**
2. **Struttura Response Object Web Service Rest**
3. **Creazione Server di testing locale**
4. **Definizione metodo per invio richieste al web service**

### Struttura Request Payload Web Service Rest

Esempio struttura json di Request Payload; il campo CodResource si riferisce nel nostro esempio al barcode letto dal barcode scanner:
```
{
  "LangCode": "it-IT",
  "PayLoadList": [
      {
        "CodResource": "HU0000000000000000001"
      },
      {
        "CodResource": "HU0000000000000000002"
      }
    ]
}
```
Le classi java correlate per il mapping del oggetto Request Payload sono:
```
package com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromFG;

import java.io.Serializable;
import java.util.List;

public class RequestPayload implements Serializable {

	private static final long serialVersionUID = -1909333640373082427L;

	private List<PayLoad> PayLoadList = null;
	private String LangCode = null;

	public List<PayLoad> getPayLoads() {
		return PayLoadList;
	}
	public void setPayLoads(List<PayLoad> payLoads) {
		this.PayLoadList = payLoads;
	}
	public String getLangCode() {
		return LangCode;
	}
	public void setLangCode(String langCode) {
		this.LangCode = langCode;
	}
}
```
```
package com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromFG;

import java.io.Serializable;

import com.promag.wms.base.system.session.SC;

public class PayLoad implements Serializable {

	private static final long serialVersionUID = 4123560145544047559L;

	private String CodResource = SC.EMPTY;

	public String getCodResource() {
		return CodResource;
	}

	public void setCodResource(String codResource) {
		this.CodResource = codResource;
	}
}
```
Nota: tutte le classi che devono essere serializzate/deserializzate in json devono implementare l'interfaccia Serializable.

### Struttura Response Object Web Service Rest

Esempio struttura json di Response Object:
```
{
   "LangCode":"it-IT",
   "ResultList":[
      {
         "ErrorCode":0,
         "Messages":[
            {
               "Message":"CodResource HU0000000000000000001 processato correttamente",
               "FieldName":"CodResource",
               "FieldCode":"HU0000000000000000001"
            }
         ],
         "ResourceCompInfo":{
            "CodResource":"HU0000000000000000001",
            "Description":"Descrizione risorsa",
            "Location":"M001",
            "StorageLocation":"DX003",
            "GoodReceiptDate":"2020-01-01",
            "ExpireDate":"2030-12-31",
            "IsAvailable":true,
            "Inactive":false,
            "CreateDate":"2019-12-31 00:00:00",
            "UpdateDate":null,
            "Ref_Ext":null,
            "Tag":"",
            "ResWidth":"0.00000",
            "ResLenght":"0.00000",
            "ResHeight":"0.00000",
            "ResDimensionUM":"ADUM",
            "ResWeight":"0.00000",
            "ResWeightUM":"ADUM",
            "Ref_Ext2":null,
            "ResVolume":"0.00000",
            "ResVolumeUM":"ADUM",
            "Type":"T1",
            "TypeDesc":"T1-Description",
            "Components":[
               {
                  "ComponentCod":"C0001",
                  "ComponentDescription":"C0001-Description",
                  "ComponentsQty":"22.000002",
                  "UnitMeasureCod":"ST"
               },
               {
                  "ComponentCod":"C0002",
                  "ComponentDescription":"C0002-Descrizione",
                  "ComponentsQty":"12.00000",
                  "UnitMeasureCod":"ST"
               }
            ],
            "Ancestors":[
               {
                  "CodResource":"HU0000000000000000000",
                  "RelType":"Parent"
               }
            ],
            "Descendants":[
               {
                  "CodResource":"HU0000000000000000011",
                  "RelType":"Child"
               },
               {
                  "CodResource":"HU0000000000000000012",
                  "RelType":"Child"
               }
            ]
         }
      },
      {
         "ErrorCode":32767,
         "Messages":[
            {
               "Message":"CodResource HU0000000000000000002 non presente nella base dati",
               "FieldName":"CodResource",
               "FieldCode":"HU0000000000000000002"
            }
         ],
         "ResourceCompInfo":null
      }
   ]
}
```
Le classi java che mappano il Response Object di risposta dal web service sono del tutto analoghe alle classi soprastanti, nel seguito riportiamo la classe di interesse per l'acquisizione dei dati dei prodotti presenti sul Udc (classe **Component**):
```
package com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta;

import java.io.Serializable;
import java.math.BigDecimal;

import com.fasterxml.jackson.annotation.JsonProperty;
import com.promag.wms.base.system.session.SC;
import com.promag.wms.base.utils.SF;
import com.promag.wms.custom.ws.ResultErrorEntrataDaProduzioneException;

public class Component implements Serializable {

	private static final long serialVersionUID = -3080777762310595768L;

	@JsonProperty(value="ComponentCod")
	private String ComponentCod = SC.EMPTY;

	@JsonProperty(value="ComponentDescription")
	private String ComponentDescription = SC.EMPTY;

	@JsonProperty(value="ComponentsQty")
	private BigDecimal ComponentsQty = BigDecimal.ZERO;

	@JsonProperty(value="UnitMeasureCod")
	private String UnitMeasureCod = SC.EMPTY;


	public Component() {
		super();
	}
	public Component(String componentCod, String componentDescription, BigDecimal componentsQty,
			String unitMeasureCod) {
		super();
		ComponentCod = componentCod;
		ComponentDescription = componentDescription;
		ComponentsQty = componentsQty;
		UnitMeasureCod = unitMeasureCod;
	}
	public String getComponentCod() {
		return ComponentCod;
	}
	public void setComponentCod(String componentCod) {
		ComponentCod = componentCod;
	}
	public String getComponentDescription() {
		return ComponentDescription;
	}
	public void setComponentDescription(String componentDescription) {
		ComponentDescription = componentDescription;
	}
	public BigDecimal getComponentsQty() {
		return ComponentsQty;
	}
	public void setComponentsQty(BigDecimal componentsQty) {
		ComponentsQty = componentsQty;
	}
	public String getUnitMeasureCod() {
		return UnitMeasureCod;
	}
	public void setUnitMeasureCod(String unitMeasureCod) {
		UnitMeasureCod = unitMeasureCod;
	}

	public boolean checkResult() {
		StringBuilder errorMessage = new StringBuilder();

		if(getComponentCod() == null || getComponentCod().isEmpty())
			errorMessage.append(SF.formatText("Codice prodotto {0} ricevuto dal WebService non valido", getComponentCod()));

		if(getComponentsQty() == null || getComponentsQty().compareTo(BigDecimal.ZERO) < 0)
			errorMessage.append(SF.formatText("Quantità {0} ricevuta dal WebService non valida", getComponentsQty()));

		if(getUnitMeasureCod() == null || getUnitMeasureCod().isEmpty())
			errorMessage.append(SF.formatText("Unita' di misura {0} ricevuta dal WebService non valida", getUnitMeasureCod()));

		if(errorMessage.length() > 0)
			throw new ResultErrorEntrataDaProduzioneException(errorMessage.toString());

		return true;
	}

	public boolean checkWSData(String componentCod, String qtyString, String unitMeasureCod) {
		StringBuilder errorMessage = new StringBuilder();

		if(componentCod == null || componentCod.isEmpty())
			errorMessage.append(SF.formatText("Codice prodotto {0} ricevuto dal WebService non valido", componentCod));

		if(qtyString == null)
			errorMessage.append(SF.formatText("Quantità {0} ricevuta dal WebService non valida", qtyString));
		else {
			try {
				BigDecimal qty = new BigDecimal(qtyString);
				if(qty.compareTo(BigDecimal.ZERO) < 0)
					errorMessage.append(SF.formatText("Quantità {0} ricevuta dal WebService non valida", qty));
			}
			catch(Exception ex) {
				errorMessage.append(SF.formatText("Quantità {0} ricevuta dal WebService non valida", qtyString));
			}

		}

		if(unitMeasureCod == null || unitMeasureCod.isEmpty())
			errorMessage.append(SF.formatText("Unita' di misura {0} ricevuta dal WebService non valida", unitMeasureCod));

		if(errorMessage.length() > 0)
			throw new ResultErrorEntrataDaProduzioneException(errorMessage.toString());

		return true;
	}

}
```
Analizziamo ora il contenuto di tale classe:
```
@JsonProperty(value="ComponentCod")
```
L'attibuto @JsonProperty indica il nome del campo con cui deve essere mappato l'oggetto presente con tale nome nella stringa json raffigurante tale oggetto.
Nel esempio: "ComponentCod":"C0001"-> C0001 viene mappato nel campo ComponentCod della classe Component.

Il metodo:
```
public boolean checkResult() {
...
}
```
Può essere utilizzato al termine della deserializzazione da json a oggetto java per verificare che l'oggetto java in questione possiede le necessarie specifiche implementative concordate.

### Creazione Server di testing locale

Al fine di testare localmente i servizi esposti dal web service Rest è necessario creare e configurare una classe che estende **HttpServlet** ([HttpServlet][2]), analoga alla seguente:

```
package com.promag.wms.custom.rest.servletTest;

import java.io.IOException;
import java.io.Writer;
import java.util.ArrayList;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.eclipse.jetty.server.Server;
import org.eclipse.jetty.servlet.ServletHandler;

import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.Ancestor;
import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.Descendant;
import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.Message;
import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.ResourceCompoInfo;
import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.ResponseObject;
import com.promag.wms.custom.rest.messages.ResourceComponentInfo.fromEpta.Result;
import com.thoughtworks.xstream.XStream;
import com.thoughtworks.xstream.io.HierarchicalStreamWriter;
import com.thoughtworks.xstream.io.json.JsonHierarchicalStreamDriver;
import com.thoughtworks.xstream.io.json.JsonWriter;

public class TestServerRest extends HttpServlet {

	private static final long serialVersionUID = -6462179732474354889L;

	String response = "{\r\n" +
			"\"LangCode\": \"it-IT\",\r\n" +
			"\"ResultList\": [\r\n" +
			"{\r\n" +
			"\"ErrorCode\": 0,\r\n" +
			"\"Messages\": [\r\n" +
			"{\r\n" +
			"\"Message\": \"CodResource HU0000000000000000001 processato correttamente\",\r\n" +
			"\"FieldName\": \"CodResource\",\r\n" +
			"\"FieldCode\": \"HU0000000000000000001\"\r\n" +
			"}\r\n" +
			"],\r\n" +
			"\"ResourceCompInfo\": {\r\n" +
			"\"CodResource\": \"HU0000000000000000001\",\r\n" +
			"\"Description\": \"Descrizione risorsa\",\r\n" +
			"\"Location\": \"M001\",\r\n" +
			"\"StorageLocation\": \"DX003\",\r\n" +
			"\"GoodReceiptDate\": \"2020-01-01\",\r\n" +
			"\"ExpireDate\": \"2030-12-31\",\r\n" +
			"\"IsAvailable\": true,\r\n" +
			"\"Inactive\": false,\r\n" +
			"\"CreateDate\": \"2019-12-31 00:00:00\",\r\n" +
			"\"UpdateDate\": null,\r\n" +
			"\"Ref_Ext\": null,\r\n" +
			"\"Tag\": \"\",\r\n" +
			"\"ResWidth\": \"0.00000\",\r\n" +
			"\"ResLenght\": \"0.00000\",\r\n" +
			"\"ResHeight\": \"0.00000\",\r\n" +
			"\"ResDimensionUM\": \"ADUM\",\r\n" +
			"\"ResWeight\": \"0.00000\",\r\n" +
			"\"ResWeightUM\": \"ADUM\",\r\n" +
			"\"Ref_Ext2\": null,\r\n" +
			"\"ResVolume\": \"0.00000\",\r\n" +
			"\"ResVolumeUM\": \"ADUM\",\r\n" +
			"\"Type\": \"T1\",\r\n" +
			"\"TypeDesc\": \"T1-Description\",\r\n" +
			"\"Components\": [\r\n" +
			"{\r\n" +
			"\"ComponentCod\": \"C0001\",\r\n" +
			"\"ComponentDescription\": \"C0001-Description\",\r\n" +
			"\"ComponentsQty\": \"22.000002\",\r\n" +
			"\"UnitMeasureCod\": \"ST\"\r\n" +
			"},\r\n" +
			"{\r\n" +
			"\"ComponentCod\": \"C0002\",\r\n" +
			"\"ComponentDescription\": \"C0002-Descrizione\",\r\n" +
			"\"ComponentsQty\": \"12.00000\",\r\n" +
			"\"UnitMeasureCod\": \"ST\"\r\n" +
			"}\r\n" +
			"],\r\n" +
			"\"Ancestors\": [\r\n" +
			"{\r\n" +
			"\"CodResource\": \"HU0000000000000000000\",\r\n" +
			"\"RelType\": \"Parent\"\r\n" +
			"}\r\n" +
			"],\r\n" +
			"\"Descendants\": [\r\n" +
			"{\r\n" +
			"\"CodResource\": \"HU0000000000000000011\",\r\n" +
			"\"RelType\": \"Child\"\r\n" +
			"},\r\n" +
			"{\r\n" +
			"\"CodResource\": \"HU0000000000000000012\",\r\n" +
			"\"RelType\": \"Child\"\r\n" +
			"}\r\n" +
			"]\r\n" +
			"}\r\n" +
			"},\r\n" +
			"{\r\n" +
			"\"ErrorCode\": 32767,\r\n" +
			"\"Messages\": [\r\n" +
			"{\r\n" +
			"\"Message\": \"CodResource HU0000000000000000002 non presente nella base dati\",\r\n" +
			"\"FieldName\": \"CodResource\",\r\n" +
			"\"FieldCode\": \"HU0000000000000000002\"\r\n" +
			"}\r\n" +
			"],\r\n" +
			"\"ResourceCompInfo\": null\r\n" +
			"}\r\n" +
			"]\r\n" +
			"}";

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		ResponseObject r = new ResponseObject();
		r.setLangCode("it-IT");
		ResourceCompoInfo resourceCompoInfo = new ResourceCompoInfo();
		List<Message> messages = new ArrayList<>();
		Message msg = new Message("aaaa", "filloField", "FilloCode");
		messages.add(msg);

		List<Ancestor> ancestors = new ArrayList<>();
		ancestors.add(new Ancestor("fizcod"));
//		ancestors.add(new Ancestor("fizcod2"));

		List<Descendant> descendants = new ArrayList<>();
		Descendant descendant = new Descendant("fiz");
		Descendant descendant2 = new Descendant("fiz2");
		descendants.add(descendant);
		descendants.add(descendant2);

		resourceCompoInfo.setAncestors(ancestors);
		resourceCompoInfo.setDescendants(descendants);

		List<Result> results = new ArrayList<>();
		Result result = new Result(0, messages, resourceCompoInfo);
		results.add(result);
		Result result2 = new Result(3201, messages, resourceCompoInfo);
		results.add(result2);

		r.setResultList(results);

		XStream xs = new XStream(new JsonHierarchicalStreamDriver() {
			//Questo per non mettere il livello padre (root appunto)
			@Override
			public HierarchicalStreamWriter createWriter(Writer writer) {
		        return new JsonWriter(writer, JsonWriter.DROP_ROOT_MODE);
			}
		});
		xs.setMode(XStream.NO_REFERENCES);
		xs.processAnnotations(ResponseObject.class);
		String xml = xs.toXML(r);

//		resp.getWriter().write(xml); se vogliamo rispondere con l'oggetto creato nel codice soprastante
		resp.getWriter().write(response); //se vogliamo rispondere con la stringa response creata
		resp.setStatus(HttpServletResponse.SC_OK);
		resp.setContentType("application/json"); //$NON-NLS-1$
		resp.setCharacterEncoding("UTF-8");
	}

	public static void main(String[] args) {

    //http://localhost:7008/api/v1/ResourceComponentInfo

		Server httpServer = new Server(7008);

		ServletHandler handler = new ServletHandler();

		handler.addServletWithMapping(TestServerRest.class, "/api/v1/ResourceComponentInfo"); //$NON-NLS-1$

		httpServer.setHandler(handler);

		try {
			httpServer.start();
		} catch (Exception ex) {
			ex.printStackTrace();
		}

	}
}
```

La stringa **response** rappresenta il json con cui il server Rest risponde alla richiesta ricevuta.

Il metodo che nel nostro esempio dobbiamo reimplementare, dato che il nostro web service utilizza il verbo POST, è il metodo doPost:

```
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
...
}
```
La risposta del web service è implementata da:
```
//		resp.getWriter().write(xml); se vogliamo rispondere con l'oggetto creato nel codice soprastante
		resp.getWriter().write(response); //se vogliamo rispondere con la stringa response creata
		resp.setStatus(HttpServletResponse.SC_OK); //OK response
		resp.setContentType("application/json"); //$NON-NLS-1$
		resp.setCharacterEncoding("UTF-8"); //encoding
```

Il metodo "main" si occupa invece di creare e lanciare il esecuzione, al indirizzo "http://localhost:7008/api/v1/ResourceComponentInfo" il web service Rest.

### **Definizione metodo per invio richieste al web service**

Dopo aver lanciato in esecuzione il web service localmente a questo punto per inviare richieste al web service da EjLog utilizziamo il seguente metodo:

```
public static ResponseObject sendIngressoPalletToWebServiceRestMapAll(RequestPayload info) throws IOException, JSONException, ResultErrorEntrataDaProduzioneException {

		XStream xs = new XStream(new JsonHierarchicalStreamDriver() {
			//Questo per non mettere il livello padre (root appunto)
			@Override
			public HierarchicalStreamWriter createWriter(Writer writer) {
		        return new JsonWriter(writer, JsonWriter.DROP_ROOT_MODE);
			}
		});
		xs.setMode(XStream.NO_REFERENCES); //every reference shall be completed serialized
		xs.processAnnotations(RequestPayload.class);
		String json = xs.toXML(info);

		String uri = env.getProperty("service.endpointREST"); //$NON-NLS-1$
		URL url = new URL(uri);
		HttpURLConnection conn = (HttpURLConnection) url.openConnection();

		conn.setRequestMethod("POST"); //$NON-NLS-1$
		conn.setDoOutput(true);

		//Header
		conn.setRequestProperty("Accept", "application/json");  //$NON-NLS-1$//$NON-NLS-2$
		conn.setRequestProperty("Content-Type", "application/json"); //$NON-NLS-1$ //$NON-NLS-2$

		//Authorization
		String username = env.getProperty("service.username"); //$NON-NLS-1$
		String password = env.getProperty("service.password"); //$NON-NLS-1$
		String encoded = Base64.getEncoder().encodeToString((username + ":" + password).getBytes(StandardCharsets.UTF_8)); //$NON-NLS-1$
		conn.setRequestProperty("Authorization", "Basic " + encoded); //$NON-NLS-1$//$NON-NLS-2$

		//Messaggio
		OutputStream outputStream = conn.getOutputStream();

		EventLogger.info(SF.formatString("WS: Inviato richiesta tramite chiamata al servizio %s. Messaggio inviato: %s", uri, json)); //$NON-NLS-1$

		byte[] b = json.getBytes("UTF-8"); //$NON-NLS-1$
		outputStream.write(b);
		outputStream.flush();
		outputStream.close();

		if (conn.getResponseCode() != HttpServletResponse.SC_OK) {
			throw new ResultErrorEntrataDaProduzioneException(SF.formatText("Validazione Request Payload non completata con successo. Payload di risposta diverso da {0}", HttpServletResponse.SC_OK));
		}

    //1* TECNICA -> DESERIALIZZAZIONE COMPLETA
		ObjectMapper objectMapper = new ObjectMapper();
		objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, true);
		objectMapper.enable(DeserializationFeature.ACCEPT_SINGLE_VALUE_AS_ARRAY);
		ResponseObject response = objectMapper.readValue(new InputStreamReader(conn.getInputStream()), ResponseObject.class);

		conn.disconnect();

		return response;
	}
```
Analizziamo ora il contenuto della classe:
```
String uri = env.getProperty("service.endpointREST"); //$NON-NLS-1$
```
La variabile env "punta" alla risorsa "classpath:/config/epta.service.properties".
Questo "puntamento" deve essere definito nella classe in cui si definisce il metodo per la richiesta al web service, tramite il seguente codice:
```
@Configuration
@PropertySource(value = "classpath:/config/epta.service.properties")
public class Epta {

	private static final AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(Epta.class);
	private static final ConfigurableEnvironment env = context.getEnvironment();

  ...

}
```
Nel esempio per segnalare una risposta di errore dal web service è stata definata la seguente classe:
```
package com.promag.wms.custom.ws;

public class ResultErrorEntrataDaProduzioneException extends RuntimeException {

	private static final long serialVersionUID = -9133976751245825917L;
	private String errorMessage;

	public ResultErrorEntrataDaProduzioneException(String errorMessage) {
		this.errorMessage = errorMessage;
	}
	public String getErrorMessage() {
		return errorMessage;
	}

}
```
Il mapping della risposta del web service nel oggetto java Response Object:
```
ObjectMapper objectMapper = new ObjectMapper();
		objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, true); //la deserializzazione fallisce se nel json sono presenti campi non mappati negli oggetti java corrispondenti
		objectMapper.enable(DeserializationFeature.ACCEPT_SINGLE_VALUE_AS_ARRAY);//se ci sono campi di tipo array
		ResponseObject response = objectMapper.readValue(new InputStreamReader(conn.getInputStream()), ResponseObject.class);
```
Ora è possibile ritornare l'oggetto java Response Object mappato al chiamante per elaborarlo.

Un'alternativa, invece di mappare tutti i campi presenti nella stringa json ricevuta, in oggetti java corrispettivi è quella di mappare unicamente gli oggetti java di interesse (nel nostro esempio la classe Component).
Per ottenere ciò è possibile utilizzare la seguente deserializzazione:
```
...
//2* TECNICA -> DESERIALIZZAZIONE PARZIALE (MAPPING SOLO DI COMPONENT)
JSONObject jsonObject = new JSONObject(new JSONTokener(new InputStreamReader((conn.getInputStream()))));
		JSONArray resultList = jsonObject.getJSONArray("ResultList"); //$NON-NLS-1$

        JSONObject resourceCompInfo = resultList.getJSONObject(0).getJSONObject("ResourceCompInfo"); //$NON-NLS-1$
        JSONArray comps = resourceCompInfo.getJSONArray("Components"); //$NON-NLS-1$
//
        List<Component> components = new ArrayList<>();

        for (int i = 0; i < comps.length(); i++) {

        	Component component = new Component();

        	String componentCod = comps.getJSONObject(i).getString("ComponentCod"); //$NON-NLS-1$
        	String componentDescription = comps.getJSONObject(i).getString("ComponentDescription"); //$NON-NLS-1$
        	String componentQtyString = comps.getJSONObject(i).getString("ComponentsQty"); //$NON-NLS-1$
        	String unitMeasureCod = comps.getJSONObject(i).getString("UnitMeasureCod"); //$NON-NLS-1$
        	component.checkWSData(componentCod, componentQtyString, unitMeasureCod);

        	component.setComponentCod(componentCod);
			component.setComponentDescription(componentDescription);
			component.setComponentsQty(new BigDecimal(componentQtyString));
			component.setUnitMeasureCod(unitMeasureCod);

            component.checkResult();
            components.add(component);
        }
...
```
Ed è quindi ancora possibile ritornare l'oggetto java mappato al chiamante per elaborarlo (in questo caso List<Component>).

[1]: https://it.wikipedia.org/wiki/Representational_State_Transfer
[2]: https://docs.oracle.com/javaee/7/api/javax/servlet/http/HttpServlet.html

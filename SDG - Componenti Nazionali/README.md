***Accordo quadro di servizi applicativi in ottica Cloud – Lotto 1***

***Progetto Single Digital Gateway (SDG)***

Specifica tecnica

Documento di integrazione

**Versione 1.0 – Stato del documento: Bozza per condivisione**

**Classificazione del documento: AgID Internal**

**08 aprile 2022**

Scopo e Ambito del Documento

## Scopo del documento

Il presente documento è utile alle PA Italiane che scelgono di
realizzare il proprio Procedure Portal integrato con la piattaforma
europea SDG. Le specifiche tecniche contenute permettono al team di
sviluppo del Procedure Portal di implementare le logiche di integrazione
nel rispetto degli standard dettati dalla Comunità Europea.

## Ambito di riferimento

Il sistema informatico oggetto di analisi nel presente documento è
l’integrazione con la piattaforma europea SDG (Single Digital Gateway).

In particolare, i componenti analizzati sono quindi:

1. **Evidence Broker.**

componente architetturale deputato alla consegna dell’elenco di
tipologie di prova (*Evidence Type List*) che consente ai portali delle
PA coinvolte di individuare la Tipologia di Prova (*Evidence Type*)
necessaria per soddisfare una richiesta.

1. **Data Service Directory.**

Componente architetturale deputato alla gestione e condivisione degli
Enti accreditati per l’erogazione (*Evidence Provider*) della Tipologia
di prova (*Evidence Type)*.

1. **Access Point.**

Componente architetturale di rete che rende trasparente agli Enti
Fruitori e agli Enti Erogatori le diverse scelte implementative e
garantisce i livelli di sicurezza necessari nello scambio di dati
cross-border. Interviene attivamente nello scambio di dati tra gli Stati
Membri.

## Glossario Definizioni ed Acronimi

| **ACRONIMO** | **DESCRIZIONE**                                                              |
| ------------ | ---------------------------------------------------------------------------- |
| PP           | Procedure Portal                                                             |
| PA           | Pubblica Amministrazione                                                     |
| SDG          | Single Digital Gateway                                                       |
| CS           | Catalogo dei Servizi                                                         |
| EB           | Evidence Broker                                                              |
| DSD          | Data Service Directory                                                       |
| PDND         | Piattaforma Digitale Nazionale Dati per l'interoperabilità di dati e servizi |
| BO           | Back office                                                                  |
| OOTS         | Once only technical system                                                   |

# Contesto del progetto

## Diagramma Logico di Architettura

<img src="media/DiagrammaLogicoDiArchitettura1.png" style="width:6.69375in;height:5.03403in" />

 

L’infrastruttura italiana dell’SDG espone, ad uso dei Procedure Portal,
le REST API dell'Evidence Broker e del Data Service Directory come da
diagramma riportato.

Nel confine logico indicato come “IT External Services” sono contenuti
schematicamente il Procedure Portal ovvero il portale della PA deputato
all’implementazione del Procedimento Amministrativo SDG, l'Evidence
Requestor ovvero il componente che implementa la richiesta di Evidence
necessaria ed il PDND ovvero l’infrastruttura nazionale Dati per
abilitare (authentication and authorization) l’interoperabilità tramite
API tra PA.

Il blocco “EU External Services” indicato in grigio è considerato OOO
(Out Of Scope) in questo contesto in quanto sono i servizi omologhi
degli altri Stati Membri riportati nel diagramma per completezza di
informazione.

L'utente transfrontaliero, nell’intento di recuperare la Prova
(*Evidence*) esegue l’accesso al Procedure Portal e in maniera indiretta
e del tutto trasparente interagisce con i servizi esposti dal SDG
riuscendo così a portare a termine l’operazione di recupero della prova
secondo il principio OOP tramite OOTS.

Per una corretta lettura dello schema le chiamate riportate con le
frecce di colore arancione dal Procedure Portal si avvalgono del
supporto del Evidence Requestor che, ottenuto il voucher dalla PDND, si
rivolge ai sistemi Evidence Broker o Data Service Directory del SDG.

Il voucher implementa il concetto di token autorizzativo per la gestione
della richiesta, pertanto, è un Access Token JWT utilizzato nelle
chiamate delle API REST.

Per permettere un accesso più sicuro ed agevole agli Swagger generati e
contenuti nel documento è stato creato un repository GitHub reperibile a
questo URL, a cui si può accedere con utenze opportunamente autorizzate
da AgID:

[https://github.com/AgID/sdg_it_architype](https://urldefense.proofpoint.com/v2/url?u=https-3A__github.com_AgID_sdg-5Fit-5Farchitype&d=DwMGaQ&c=eIGjsITfXP_y-DLLX0uEHXJvU8nOHrUK8IrwNKOtkVU&r=CcBA8Bcw3JtEnYhMMAMPCFmIB0_6RwzYnRcZYq4_vD4i39CHclsbDLC1-wbJR_OV&m=FPPtU_bnRexG_xIL1EVbVccauuEqXP74YaeRTfiX6qZKX1U33PQrs6yXj76EYgkY&s=o4eMYPDiSxpU4HGC4vOtQ-_IukRuBr3Ug-Rmx5vZxZs&e=)

# Gestione delle Chiavi per recupero delle Procedure

Sono analizzati i due possibili scenari di integrazione fra il Catalogo
dei Servizi ed il Procedure Portal.

## Scenario di accesso tramite Catalogo dei Servizi.

L’utente transfrontaliero può accedere al sistema SDG per tramite del
portale YourEurope che rappresenta il livello funzionale più alto e Hub
di tutti gli stati membri aderenti al SDG. Tramite YourEurope l’utente
comunitario potrà scegliere la lingua di fruizione del servizio, tra
quelle a disposizione, e di conseguenza lo Stato Membro verso cui
proseguire nella navigazione della richiesta verso il Catalogo dei
Servizi.

La navigazione nel portale del Catalogo dei Servizi avviene in modalità
anonima per selezionare la procedura di interesse.

Il processo termina con il Catalogo dei Servizi che produce una URL di
redirect verso il Procedure Portal (rif. §5.1) ed in particolare viene
generato un parametro passato in GET nella querystring dell’URL di
redirect noto come “*publicService*” che corrisponde all’identificativo
del Procedimento amministrativo, a livello Europeo, utile a filtrare i
Requirement nella successiva chiamata all’Evidence Broker da parte del
Procedure Portal.

È pertanto necessario che tale parametro, comunicato dal Procedure
Portal nella modalità sopra esposta, sia utilizzato nelle successive
chiamate verso il back-end del Catalogo dei Servizi proprio per filtrare
il *Requirement* ed evitare così di recuperare una lista di
*Requirement* troppo ampia rispetto ai desiderata.

## Scenario di accesso diretto al Procedure Portal

L’utente transfrontaliero però può altresì iniziare il processo di
richiesta delle Evidence, direttamente da un Procedure Portal saltando
di fatto il processo di ricerca e identificazione della procedura sul
Catalogo dei Servizi.

In tal caso il Procedure Portal non sarà invocato tramite la redirect
generata dal Catalogo dei Servizi e di conseguenza non avrà modo di
ricevere e mantenere il codice del *publicService* passato sulla URL di
redirect. In questo caso, dunque, il Procedure Portal nel momento in cui
ha necessità di recuperare i Requirements dall’Evidence Broker, deve
autonomamente provvedere al recupero di tale parametro attraverso
l’invocazione dell’API retrieveIdPublicServiceList (rif. §6.3) al fine
di poterlo passare nell’invocazione dell’API requirementList
dell’Evidence Broker (rif. §6.1).

# Gestione del meccanismo di autenticazione TRAMITE PDND

Al fine d’integrare i servizi esposti dal SDG è necessario registrare
sull’infrastruttura PDND sia l'applicazione SDG che svolge il ruolo di
Evidence Provider e sia i diversi Procedure Portal con il ruolo di Enti
Fruitori, in modo da favorire l’interoperabilità dei sistemi informativi
e delle basi di dati.

Come meccanismo di Security la PDND implementa il Voucher di
autorizzazione per l’utilizzo di un e-service per consentire la
comunicazione tra Evidence Provider e Evidence Requestor.

L’Access Token emesso dalla PDND consiste in un JWT conforme all’RFC7515
firmato dall’Infrastruttura PDND che svolge il ruolo di Voucher
utilizzato dai Procedure Portal per le chiamate ai servizi del SDG come
riportato di seguito nell’header del messaggio https nella chiamata alle
RestAPI necessarie all’integrazione.

Pertanto, è mandatorio che in tutte le chiamate verso i SDG sia
specificato il parametro “Authorization” nell’header https valorizzato
come specificato sopra.

Nel Sequence Diagram seguente è illustrata l’interazione fra il
Procedure Portal, la PDND e le API dell’Evidence Broker all’interno di
SGD in seguito di una richiesta da parte dell’utente transfrontaliero.
Il medesimo meccanismo è applicato anche per l'interazione tra il
Procedural Portal e Data Service Directory IT e tra il Procedural Portal
e Arch Common Service IT.

Nel diagramma seguente gli step 2, 3, 11, 12 sono oggetto di
approfondimento ed analisi per cui potrebbero subire un cambiamento
nella prossima versione del documento.

<img src="media/SequenceDiagram-EB-interazione-PP_SDG-1.png" style="width:6.69306in;height:5.91686in" />

Figura 1 - interazione PP con SDG

# Specifiche tecniche delle API relative alle chiamate ricevute dal Procedure Portal

In questo capitolo sono esposte le interfacce richiamabili dal Catalogo
dei Servizi e che i Procedure Portal dovranno esporre.

Nello specifico ad oggi deve essere messa a disposizione del SDG una
sola API necessaria alla selezione della procedura corretta all’interno
del Procedure Portal così come riportato ed esposto nel paragrafo
successivo.

## Interazione da Catalogo dei Servizi al Procedure Portal

Il catalogo dei servizi, a seguito dell’interazione con l’utente sarà in
grado di selezionare una URL contenente sia il dominio corretto del
Procedure Portal selezionato e sia il Public Service relativo alla
procedura voluta ed indicata dall’utente.

Pertanto, sul front end del catalogo dei servizi, è presente una
*redirect* verso una URL del Procedure Portal individuato alla quale è
aggiunto il parametro *PublicService* utilizzato dal Procedure Portal
per individuare e selezionare la singola procedura di interesse nel
Portale.

Per reindirizzare l’utente ad una pagina specifica di un dominio
selezionato dunque, è necessario aggiungere il valore del parametro del
*PublicService* selezionato inserendolo nella URL di destinazione della
GET come *querystring*.

A titolo di esempio è indicata di seguito la modalità di invio del
PublicService:

- [**Error! Hyperlink reference not valid.**]()\>

Dove *\<url_procedure_portal\>* e *\<codice_public_service\>* sono
definiti e selezionati dal back end del catalogo dei servizi in base
all’interazione con l’utente.

*NB: È in fase di studio di Sicurezza applicativa la necessità di
criptare il parametro proc-identifier in input alla successiva chiamata
alla API requirementList dell’Evidence Broker IT*

# Specifiche tecniche delle API relative alle chiamate effettuate dal Procedure Portal

In questo capitolo è esposto l’elenco di API a disposizione dei
Procedure Portal con l’indicazione dei parametri attesi in input, sia
essi mandatori che opzionali, ed i parametri di output.

Sono altresì riportati anche i file OpenAPI 3.0 in modo da semplificare
e standardizzare la documentazione delle API per i servizi RESTful.
Allegati al documento ci sono i file YAML dove sono descritte tutte le
funzionalità, ivi compresi tutti i parametri di input e output di dette
API.

I vantaggi di questa standardizzazione sono molti, come una migliore e
più condivisibile esplorazione delle funzionalità di un'applicazione,
oltre che alla possibilità di generare codice client/server sfruttando
direttamente i vincoli definiti nello schema. Infatti, i metadati
presenti nel file forniscono informazioni sufficienti sia per generare
il componente back-end, con le rotte https e la validazione degli input,
sia la parte client che in modo automatico può adattarsi all’evoluzione
delle API del back-end.

Alla ricezione del documento di specifiche tecniche aggiornato
(Technical Design Document) da parte della CE previsto per giugno 2022
sarà possibile integrare il paragrafo con un Sequence Diagram che
esponga l’interazione di dettaglio del Procedure Portal verso SDG.

Al momento è possibile far riferimento al Sequence Diagram mostrato nel
paragrafo *“4. GESTIONE DEL MECCANISMO DI AUTENTICAZIONE TRAMITE PDND”*

Per migliorare la comprensione del capitolo si descrive la struttura dei
paragrafi successivi.

Ogni paragrafo descrive l'API esposta da DSG italiano verso il PP; sono
menzionati i prerequisiti ed i requisiti affinché l'API possa essere
eseguita correttamente nel suo contesto.

I parametri di input all'API sono descritti nella sezione tabellare
INPUT mentre nella sezione OUTPUT è descritta la struttura dati attesa
al termine dell'esecuzione dell'API.

## Recupero RequirementList da Evidence Broker IT

<u>API:</u> /api/v1/requirement/list

<u>Prerequisito</u>: Per accedere al Procedure Portal l’utente dovrà
autenticarsi su di esso mediante nodo di tipo eIDAS, fornendo un primo
consenso ad accedere alle informazioni previste per mezzo del OOTS, con
un livello di autenticazione coerente con quanto previsto dal
procedimento di interesse; in tal senso dovrà essere lo stesso Procedure
Portal a verificare tale aspetto rispetto alle diverse situazioni che
potrebbero verificarsi (es.: accesso diretto, tentativo di accesso
successivo alla scadenza della sessione, riscrittura del URL, redirect
interno di un utente già autenticato, ecc.).

<u>Requisito</u>: Lista dei requisiti previsti per il procedimento
amministrativo d’interesse.

Parametri di input al metodo GET e payload di output

| **PROTOCOL**               | HTTPS                                                                |
| -------------------------- | -------------------------------------------------------------------- |
| **PATH (Public Exposure)** | https://\<url_service_catalog \>/\[APP_URL\]/api/v1/requirement/list |
| **METHOD**                 | GET                                                                  |
| **CONTENT TYPE**           | application / xml                                                    |

**Parameter description:**

<table style="width:100%;">
<colgroup>
<col style="width: 19%" />
<col style="width: 49%" />
<col style="width: 11%" />
<col style="width: 8%" />
<col style="width: 11%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="5"><strong>INPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="5"><strong>HEADER PARAM</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>Authorization</td>
<td>Bearer <<em>Voucher PDND</em>></td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="even">
<td colspan="5"><strong>BODY PARAM</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Max Length</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>queryId</td>
<td>Vincolo: deve sempre essere valorizzato con la seguente
stringa<br />
“urn:oots:eb:ebxml-regrep:queries:requirementsby-procedure-and-jurisdiction”</td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="odd">
<td>proc-identifier</td>
<td>Identificativo della Procedimento amministrativo al livello Europeo
al fine di filtrare i Requirement.</td>
<td>M</td>
<td></td>
<td>integer</td>
</tr>
<tr class="even">
<td>proc-jurisdiction</td>
<td><p>Giurisdizione della Procedure/Procedimento amministrativo al fine
di filtrare i Requirement utilizzati nello Stato Membro.</p>
<p>Vincolo: Codice ISO 3166-2</p></td>
<td>O</td>
<td></td>
<td>String</td>
</tr>
</tbody>
</table>

L’attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Success”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>requestId</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Deve corrispondere all'attributo id dell'oggetto QueryRequest che ha
generato queryResponse.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione corretta dell’API:</p>
<p>"urn:oasis:names:tc:ebxmlregrep: ResponseStatusType:Success"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

| ***Parameter***        | ***Description***                  | ***Mandatory / Optional*** | ***Type*** |
| ---------------------- | ---------------------------------- | -------------------------- | ---------- |
| rim:RegistryObjectList | Contiene le informazioni richieste | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:**
rim:RegistryObjectList*

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:RegistryObject</td>
<td>Contiene il dettaglio della singola richiesta</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>id</td>
<td><p>Attributo del nodo rim:RegistryObjectList</p>
<p>ID è l’identificativo univoco definito dall’Evidence
Provider</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** rim:RegistryObject

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Requirement</td>
<td><p>Contiene la lista degli oggetti requisiti.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="http://data.europa.eu/sdg#"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"</p>
<p>Contiene la condizione o prerequisito stabilito da un'autorità
competente nel contesto di un SDG online. Procedura, che un cittadino o
un'azienda deve soddisfare per completare la procedura</p></td>
<td>O</td>
<td>Object</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** Requirement

| ***Parameter***    | ***Description***                                                                                                                                                                                                       | ***Mandatory / Optional*** | ***Type*** |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Identifier         | Identificativo univoco del requisito                                                                                                                                                                                    | M                          | String     |
| Name               | Nome per identificare il requisito                                                                                                                                                                                      | O                          | String     |
| ReferenceFramework | Il Framework è la piattaforma o portale di riferimento da cui è stato creato il requirement, non rappresenta né identifica i requisiti. (rif. 5.OOTS - Technical Design Documents - Version December 2021.pdf pag. 200) | O                          | Object     |
| IssuedBy           | L'agente che ha emesso il requisito.                                                                                                                                                                                    | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:*** ReferenceFramework

| ***Parameter*** | ***Description***                                                                                               | ***Mandatory / Optional*** | ***Type*** |
| --------------- | --------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Identifier      | Codice univoco dell’identificato                                                                                | M                          | String     |
| Title           | Un nome per identificare il framework di riferimento.                                                           | O                          | String     |
| Description     | Descrizione del Framework                                                                                       | O                          | String     |
| RelatedTo       | L'identificatore della procedura SDGR a cui si riferisce questa procedura                                       | O                          | Objet      |
| Jurisdiction    | Livello amministrativo in cui si applica questo quadro di riferimento. Può essere applicato a più giurisdizioni | M                          | Object     |

***Nodo Padre della seguente lista di nodi figli:*** IssuedBy

<table style="width:100%;">
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 12%" />
<col style="width: 11%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Identifier</td>
<td>L'agente che ha emesso il requisito.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>Name</td>
<td><p>Identificazione univoca per l'agente.</p>
<p>Da utilizzare se e solo se non viene utilizzato un profilo agente più
preciso.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>Classification</td>
<td><p>Una breve etichetta per l'agente.</p>
<p>Da utilizzare dal fornitore di prove o dal richiedente di
prove</p></td>
<td>O</td>
<td>Integer</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** RelatedTo

| ***Parameter*** | ***Description***       | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------------- | -------------------------- | ---------- |
| Identifier      | Link alla norma europea | O                          | String     |

***Nodo Padre della seguente lista di nodi figli:** Jurisdiction*

| ***Parameter*** | ***Description*** | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------- | -------------------------- | ---------- |
| AdminUnitLevel1 | Stato Membro      | M                          | String     |

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP.

Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description**        |
| ------------- | ----------------------------- |
| 200           | Service executed successfully |

### Risposta e Gestione degli errori

E’ gestita la possiblità che l’API non risponda correttamente alle
richieste. Di seguito il payload di output, differente a quello in caso
di successo, contenente le indicazioni per gestire l’eccezione
riscontrata.

L’ attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Failure”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 58%" />
<col style="width: 11%" />
<col style="width: 10%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td><em>TBD</em></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>requestId</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Deve corrispondere all'attributo id dell'oggetto QueryRequest che ha
generato queryResponse.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione errata dell’API:
"urn:oasis:nammes:tc:ebxml-regrep:ResponseStatusType:Failure"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

<table style="width:100%;">
<colgroup>
<col style="width: 19%" />
<col style="width: 57%" />
<col style="width: 11%" />
<col style="width: 11%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rs:Exception</td>
<td>Contiene le informazioni relativa all’eccezione sollevata.</td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="even">
<td>xsi:type</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica a quale categoria appartiene l’errore riscontrato.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>severity</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica la gravità dell’eccezione riscontrata.</p>
<p>L’attributo dovrà essere valorizzato nel seguente modo:
"urn:oasis:names:tc:ebxml-regrep:ErrorSeverityType:Error"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>message</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Contiene il messaggio in lingua inglese come indicato nella tabella
degli scenari di errori.</p>
<p>Esempio:</p>
<p>"List of requirements requested is empty"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP.

Riguardano l'esito dell'eventuale elaborazione da parte del server.

| **HTTP Code** | **Result Description** |
| ------------- | ---------------------- |
| 400           | Bad Request            |

Ogni servizio dovrà gestire tutti gli scenari di errori elencati di
seguito.

<table>
<colgroup>
<col style="width: 15%" />
<col style="width: 24%" />
<col style="width: 29%" />
<col style="width: 29%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Code</strong></th>
<th><strong>Type</strong></th>
<th><strong>Message</strong></th>
<th><strong>Description</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>EB:ERR:0001</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>List of requirements requested is empty</td>
<td>La lista dei Requirement è vuota</td>
</tr>
<tr class="even">
<td>EB:ERR:0002</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>The requirement requested, represented by the requirement id, does
not exist</td>
<td>Requirement non trovato</td>
</tr>
<tr class="odd">
<td>EB:ERR:0003</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The jurisdiction level code query parameter is invalid or
unknown</td>
<td>Codice del livello di giurisdizione sconosciuto</td>
</tr>
<tr class="even">
<td>EB:ERR:0004</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedureid query parameter is invalid or
unknown</td>
<td>Procedimento amministrativo sconosciuto</td>
</tr>
<tr class="odd">
<td>EB:ERR:0005</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedure implementation country query parameter is
invalid or unknown</td>
<td>Codice del Paese sconosciuto</td>
</tr>
<tr class="even">
<td>EB:ERR:0006</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The requested Query does not exist</td>
<td>Query sconosciuta</td>
</tr>
</tbody>
</table>

Di seguito la lista dei codici di stato http per indicare il successo o
il fallimento di una request

**Codici di successo**

- **200 OK** - Request succeeded. Response included

- **201 Created** - Resource created. URL to new resource in Location
  header

- **204 No Content** - Request succeeded, but no response body

> **Codici di errore**

- **400 Bad Request** - Could not parse request

- **401 Unauthorized** - No authentication credentials provided or
  authentication failed

- **403 Forbidden** - Authenticated user does not have access (da non
  utilizzare)

- **404 Not Found** - Resource not found

- **415 Unsupported Media Type** - POST/PUT/PATCH request occurred
  without a **application/json content type**

- **422 Unprocessable Entry** - A request to modify or create a
  resource failed due to a validation error

- **429 Too Many Requests** - Request rejected due to rate limiting

- **500, 501, 502, 503, etc** - An internal server error occurred

La gestione impropria degli errori può introdurre una serie di problemi
di sicurezza per un sito web. Il problema più comune è quando vengono
visualizzati messaggi di errore degli ambienti interni dettagliati come
stack traces, database dumps e codici di errore all’utente finale
(Hacker).

Questi messaggi rivelano dettagli di implementazione che non dovrebbero
mai essere rivelati.

Tali dettagli o incongruenze (ad esempio “file not found” ed “access
denied”) possono fornire agli hacker indizi importanti su potenziali
difetti nel sito da sfruttare per potenziali attacchi mirati.

Pertanto, questi errori devono essere gestiti secondo uno schema ben
congegnato che fornirà un messaggio di errore significativo all'utente,
informazioni diagnostiche ai gestori del sito e nessuna informazione
utile a un utente malintenzionato.

Per tali questioni di sicurezza applicativa delle informazioni
condivise, sono utilizzati solamente i seguenti codici di stato: **200,
400, 500** customizzati in maniera tale che non vengano esportati dati
utili e di dettaglio dell’infrastruttura e della web app.

### Swagger

Di seguito la specifica Swagger per l’API retrieveRequirementList per la
response 200:

[retrieveRequirementList-200](openapi/retrieveRequirementListv03_20220407.yml)

Di seguito la specifica Swagger per l’API retrieveRequirementList per la
response 400:

[retrieveRequirementList-400](openapi/retrieveRequirementListv02_20220406_only-http400.yml)

## Recupero EvidenceTypeList da Evidence Broker IT

<u>API:</u> retrieveEvidenceTypeList

<u>Prerequisito</u>: Procedure portal chiede all’Evidence Broker IT
l’elenco delle Tipologie di prova relativi al Requisito selezionato
dall’utente.

<u>Requisito</u>: La lista conterrà le Tipologie di prova.

| **PROTOCOL**                | HTTPS                                                                  |
| --------------------------- | ---------------------------------------------------------------------- |
| **PATH (Private Exposure)** | https://\<url_service_catalog \>/\[APP_URL\]/api/v1/evidence-type/list |
| **METHOD**                  | GET                                                                    |
| **CONTENT TYPE**            | application / json                                                     |

**Parameter description:**

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 46%" />
<col style="width: 15%" />
<col style="width: 8%" />
<col style="width: 9%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="5"><strong>INPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="5"><strong>HEADER PARAM</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>Authorization</td>
<td>Bearer <<em>Voucher PDND</em>></td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="even">
<td colspan="5"><strong>BODY PARAM</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>queryId</td>
<td><p>Vincolo: deve sempre essere valorizzato con la seguente
stringa<br />
“urn:oots:eb:ebxml-regrep:queries:evidencetypes-</p>
<p>by-requirement-and-jurisdiction”.</p></td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="odd">
<td>requirement-id</td>
<td>Identificativo del Requirement.</td>
<td>M</td>
<td>n/a</td>
<td>integer</td>
</tr>
<tr class="even">
<td>country-code</td>
<td>Codice del Paese in cui devono essere ricercate le Evidence
Type.</td>
<td>O</td>
<td>n/a</td>
<td>string</td>
</tr>
<tr class="odd">
<td>jurisdiction-admin-l2</td>
<td><p>Identificativo dell’unità territoriale per la statistica (secondo
livello di identificazione amministrativa).</p>
<p>Vincolo: Codice NUTS, può essere valorizzato solo in combinazione con
il country-code.</p></td>
<td>O</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="even">
<td>jurisdiction-admin-l3</td>
<td><p>Identificativo dell’unità amministrativa locale.</p>
<p>Vincolo: Codice LAU, può essere valorizzato solo in combinazione con
il country-code.</p></td>
<td>O</td>
<td>n/a</td>
<td>String</td>
</tr>
</tbody>
</table>

L’ attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Success”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>requestId</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Deve corrispondere all'attributo id dell'oggetto QueryRequest che ha
generato queryResponse.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione corretta dell’API:</p>
<p>"urn:oasis:names:tc:ebxmlregrep: ResponseStatusType:Success"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

| ***Parameter***        | ***Description***                  | ***Mandatory / Optional*** | ***Type*** |
| ---------------------- | ---------------------------------- | -------------------------- | ---------- |
| rim:RegistryObjectList | Contiene le informazioni richieste | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:**
rim:RegistryObjectList*

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:RegistryObject</td>
<td>Contiene il dettaglio della singola richiesta</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>id</td>
<td><p>Attributo del nodo rim:RegistryObjectList</p>
<p>ID è l’identificativo univoco dell’EvidenceTypeList</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** rim:RegistryObject

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Requirement</td>
<td><p>Contiene la lista degli oggetti requisiti.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="http://data.europa.eu/sdg#"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"</p>
<p>Contiene la condizione o prerequisito stabilito da un'autorità
competente nel contesto di un SDG online</p>
<p>procedura, che un cittadino o un'azienda deve soddisfare per
completare la procedura</p></td>
<td>O</td>
<td>Object</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** Requirement

| ***Parameter***    | ***Description***                                                                                                                                                              | ***Mandatory / Optional*** | ***Type*** |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------- | ---------- |
| Identifier         | Identificativo univoco del requisito                                                                                                                                           | M                          | String     |
| Name               | Nome per identificare il requisito                                                                                                                                             | O                          | String     |
| ReferenceFramework | Contiene i requisiti identificati e derivati dall’EB limitatamente alle procedure implementate (rif. 5.OOTS - Technical Design Documents - Version December 2021.pdf pag. 200) | O                          | Object     |
| IssuedBy           | L'agente che ha emesso il requisito.                                                                                                                                           | O                          | Object     |
| EvidenceTypeList   | Un elenco di tipi di prove, per ciascuno dei quali deve essere fornita una prova corrispondente.                                                                               | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:*** ReferenceFramework

| ***Parameter*** | ***Description***                                                                                               | ***Mandatory / Optional*** | ***Type*** |
| --------------- | --------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Identifier      | Codice univoco dell’identificato                                                                                | M                          | String     |
| Title           | Un nome per identificare il framework di riferimento.                                                           | O                          | String     |
| Description     | Una breve spiegazione che aiuti a chiarire la comprensione del requisito di cui viene creata un'istanza.        | O                          | String     |
| RelatedTo       | L'identificatore della procedura SDGR a cui si riferisce questa procedura                                       | O                          | Object     |
| Jurisdiction    | Livello amministrativo in cui si applica questo quadro di riferimento. Può essere applicato a più giurisdizioni | M                          | Object     |

***Nodo Padre della seguente lista di nodi figli:*** IssuedBy

<table style="width:100%;">
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 12%" />
<col style="width: 11%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Identifier</td>
<td>L'agente che ha emesso il requisito.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>Name</td>
<td><p>Identificazione univoca per l'agente.</p>
<p>Da utilizzare se e solo se non viene utilizzato un profilo agente più
preciso.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>Classification</td>
<td><p>Una breve etichetta per l'agente.</p>
<p>Da utilizzare dal fornitore di prove o dal richiedente di
prove</p></td>
<td>O</td>
<td>Integer</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** RelatedTo

| ***Parameter*** | ***Description***        | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ------------------------ | -------------------------- | ---------- |
| Identifier      | Link alla norma europea. | O                          | String     |

***Nodo Padre della seguente lista di nodi figli:** Jurisdiction*

| ***Parameter*** | ***Description*** | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------- | -------------------------- | ---------- |
| AdminUnitLevel1 | Stato Membro      | M                          | String     |

***Nodo Padre della seguente lista di nodi figli:** EvidenceTypeList*

| ***Parameter*** | ***Description***                                                                                     | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Identifier      | Identificativo della lista Tipologie di prova                                                         | M                          | String     |
| Name            | Nome identificativo                                                                                   | O                          | String     |
| Description     | Breve descrizione su natura, attributi, usi o altra informazione aggiuntiva esplicativa del requisito | O                          | String     |
| EvidenceType    | Identificativo del tipo di prova.                                                                     | M                          | Object     |

***Nodo Padre della seguente lista di nodi figli:** EvidenceType*

| ***Parameter***            | ***Description***                                                                                                     | ***Mandatory / Optional*** | ***Type*** |
| -------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Identifier                 | Identificativo della Tipologie di prova                                                                               | O                          | String     |
| Description                | Una breve spiegazione sulla natura che aiuti a chiarire la comprensione del requisito di cui viene creata un'istanza. | O                          | String     |
| EvidenceTypeClassification | Un codice di classificazione per specificare il layout e il contenuto previsti per un tipo di prova                   | M                          | String     |
| Jurisdiction               | Contiene la lista delle Giurisdizioni a cui si applica questo tipo di prova.                                          | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:** Jurisdiction*

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>AdminUnitLevel1</td>
<td><p>Il livello 1 si riferisce all'unità amministrativa più alta per
l'indirizzo, quasi sempre un paese.</p>
<p>ISO code</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>dminUnitLevel2</td>
<td><p>Il livello 2 si riferisce alla regione dell'indirizzo, di solito
una contea, uno stato o un'altra area simile che in genere comprende
diverse località.</p>
<p>NUTS Code</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>AdminUnitLevel3</td>
<td><p>Il livello 3 si riferisce al comune.</p>
<p>LAU Code</p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP.

Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description**        |
| ------------- | ----------------------------- |
| 200           | Service executed successfully |

### Risposta e Gestione degli errori

E’ gestita la possiblità che l’API non risponda correttamente alle
richieste. Di seguito il payload di output, differente a quello in caso
di successo, contenente le indicazioni per gestire l’eccezione
riscontrata.

L’ attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Failure”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td><em>TBD</em></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione errata dell’API:
"urn:oasis:nammes:tc:ebxml-regrep:ResponseStatusType:Failure"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rs:Exception</td>
<td>Contiene le informazioni relativa all’eccezione sollevata.</td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="even">
<td>xsi:type</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica a quale categoria appartiene l’errore riscontrato.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>severity</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica la gravità dell’eccezione riscontrata.</p>
<p>L’attributo dovrà essere valorizzato nel seguente modo:</p>
<p>"urn:oasis:names:tc:ebxml-regrep:ErrorSeverityType:Error"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>message</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Contiene il messaggio in lingua inglese come indicato nella tabella
degli scenari di errori.</p>
<p>Esempio:</p>
<p>"List of requirements requested is empty"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP. Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description** |
| ------------- | ---------------------- |
| 400           | Bad Request            |

Ogni servizio dovrà gestire tutti gli scenari di errori elencati di
seguito.

<table>
<colgroup>
<col style="width: 16%" />
<col style="width: 19%" />
<col style="width: 32%" />
<col style="width: 32%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Code</strong></th>
<th><strong>Description</strong></th>
<th><strong>Type</strong></th>
<th><strong>Message</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>EB:ERR:0001</td>
<td>La lista dei Requirement è vuota</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>List of requirements requested is empty</td>
</tr>
<tr class="even">
<td>EB:ERR:0002</td>
<td>Requirement non trovato</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>The requirement requested, represented by the requirement id, does
not exist</td>
</tr>
<tr class="odd">
<td>EB:ERR:0003</td>
<td>Codice del livello di giurisdizione sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The jurisdiction level code query parameter is invalid or
unknown</td>
</tr>
<tr class="even">
<td>EB:ERR:0004</td>
<td>Procedimento amministrativo sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedureid query parameter is invalid or
unknown</td>
</tr>
<tr class="odd">
<td>EB:ERR:0005</td>
<td>Codice del Paese sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedure implementation country query parameter is
invalid or unknown</td>
</tr>
<tr class="even">
<td>EB:ERR:0006</td>
<td>Query sconosciuta</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The requested Query does not exist</td>
</tr>
</tbody>
</table>

Di seguito la lista dei codici di stato http per indicare il successo o
il fallimento di una request

**Codici di successo**

- **200 OK** - Request succeeded. Response included

- **201 Created** - Resource created. URL to new resource in Location
  header

- **204 No Content** - Request succeeded, but no response body

**Codici di errore**

- **400 Bad Request** - Could not parse request

- **401 Unauthorized** - No authentication credentials provided or
  authentication failed

- **403 Forbidden** - Authenticated user does not have access (da non
  utilizzare)

- **404 Not Found** - Resource not found

- **415 Unsupported Media Type** - POST/PUT/PATCH request occurred
  without a **application/json content type**

- **422 Unprocessable Entry** - A request to modify or create a
  resource failed due to a validation error

- **429 Too Many Requests** - Request rejected due to rate limiting

- **500, 501, 502, 503, etc** - An internal server error occurred

Per questioni di sicurezza applicativa delle informazioni condivise sono
utilizzati solamente i seguenti codici di stato: **200, 400, 500.**
[Fare riferimento a Capitolo 6.1.1](#risposta-e-gestione-degli-errori)

### Swagger

Di seguito la specifica Swagger per l’API retrieveEvidenceTypeList per
la response 200:

[retrieveEvidenceTypeList-200](openapi/retrieveEvidenceTypeListv03_20220407.yml)

Di seguito la specifica Swagger per l’API retrieveEvidenceTypeList per
la response 400:

[retrieveEvidenceTypeList-400](openapi/retrieveEvidenceTypeList02_20220406_only-http400.yml)

## Recupero IdPublicServiceList

<u>API:</u> /api/v1/public-service/list-id/

<u>Prerequisito</u>: Aver ottenuto il token JWT dalla PDND

<u>Requisito</u>: L’utente di backend invoca in GET il metodo
retrieveIdPublicServiceList passando in input il parametro
identificatore del tipo di PublicService da recuperare

| **PROTOCOL**                | HTTPS                                                                      |
| --------------------------- | -------------------------------------------------------------------------- |
| **PATH (Private Exposure)** | https://\<url_service_catalog\>/\[APP_URL\]/api/v1/public-service/list-id/ |
| **METHOD**                  | GET                                                                        |
| **CONTENT TYPE**            | application / json                                                         |

**Parameter description:**

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 46%" />
<col style="width: 15%" />
<col style="width: 8%" />
<col style="width: 9%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="5"><strong>INPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="5"><strong>HEADER PARAM</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>Authorization</td>
<td>Bearer <<em>Voucher PDND</em>></td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="even">
<td colspan="5"><strong>BODY PARAM</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>name</td>
<td>Nome del procedimento amministrativo</td>
<td>O</td>
<td></td>
<td>string</td>
</tr>
<tr class="odd">
<td>publicOrganizationId</td>
<td>Identificativo dell’ente del procedimento amministrativo</td>
<td>M</td>
<td></td>
<td>integer</td>
</tr>
</tbody>
</table>

| **OUTPUT**            |                   |                            |            |
| --------------------- | ----------------- | -------------------------- | ---------- |
| **HEADER**            |                   |                            |            |
| ***Parameter***       | ***Description*** | ***Mandatory / Optional*** | ***Type*** |
| TBD                   | *TBD*             |                            |            |
| **BODY**              |                   |                            |            |
| ***Parameter***       | ***Description*** | ***Mandatory / Optional*** | ***Type*** |
| publicServiceResponse | Oggetto Response  | M                          | Object     |

***Nodo Padre della seguente lista di nodi figli:***
publicServiceResponse

| ***Parameter*** | ***Description***                                                                                                                       | ***Mandatory / Optional*** | ***Type*** |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| code            | Rif [response code](https://ts.accenture.com/sites/CNCC-AGID/Shared%20Documents/General/ServiceCatalog_1.0_ST.docx#_Response_and_Error) | M                          | String     |
| description     | Eventuale descrizione dell’errore                                                                                                       | M                          | String     |
| message         | Eventuale messaggio di errore                                                                                                           | O                          | String     |
| publicService   | Array di oggetti JSON                                                                                                                   | M                          | Object     |

***Nodo Padre della seguente lista di nodi figli:*** publicService

| ***Parameter*** | ***Description***                                     | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------------------------------------------- | -------------------------- | ---------- |
| id              | Del procedimento amministrativo                       | O                          | integer    |
| name            | Nome del procedimento amministrativo                  | M                          | string     |
| description     | Descrizione del procedimento amministrativo           | M                          | string     |
| policyCode      | Identificativo univoco definito dall’Comunità Europea | M                          | object     |

***Nodo Padre della seguente lista di nodi figli:*** policyCode

| ***Parameter*** | ***Description***                                                | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ---------------------------------------------------------------- | -------------------------- | ---------- |
| id              | Identificatore della tabella type chiave esterna                 | M                          | integer    |
| keyId           | Attributo keyId della tabella Type con filtro code=”policy_code” | M                          | integer    |
| value           | Attributo value della tabella Type con filtro code=”policy_code” | M                          | String     |

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP. Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description**        |
| ------------- | ----------------------------- |
| 200           | Service executed successfully |

### Risposta e Gestione degli errori

E’ gestita la possiblità che l’API non risponda correttamente alle
richieste. Di seguito il payload di output, differente a quello in caso
di successo, contenente le indicazioni per gestire l’eccezione
riscontrata

| **OUTPUT**                |                                                                |                            |            |
| ------------------------- | -------------------------------------------------------------- | -------------------------- | ---------- |
| **HEADER**                |                                                                |                            |            |
| ***Parameter***           | ***Description***                                              | ***Mandatory / Optional*** | ***Type*** |
| TBD                       | *TBD*                                                          |                            |            |
| **BODY**                  |                                                                |                            |            |
| ***Parameter***           | ***Description***                                              | ***Mandatory / Optional*** | ***Type*** |
| publicServiceListResponse | oggetto Response                                               | M                          | Object     |
| code                      | Codice d’errore riscontrato                                    | M                          | String     |
| message                   | Messaggio di errore riscontrato                                | M                          | String     |
| description               | Descrizione di dettaglio dello specifico problema verificatosi | O                          | String     |

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP.

Riguardano l'esito dell'eventuale elaborazione da parte del server.

| **HTTP Code** | **Result Description** |
| ------------- | ---------------------- |
| 400           | Bad Request            |

Di seguito la lista dei codici di stato http per indicare il successo o
il fallimento di una request

**Codici di successo**

- **200 OK** - Request succeeded. Response included

- **201 Created** - Resource created. URL to new resource in Location
  header

- **204 No Content** - Request succeeded, but no response body

> **Codici di errore**

- **400 Bad Request** - Could not parse request

- **401 Unauthorized** - No authentication credentials provided or
  
  > authentication failed

- **402 No Rows Selected** - There is not any row extracted

- **403 Forbidden** - Authenticated user does not have access

- **404 Not Found** - Resource not found

- **429 Too Many Requests** - Request rejected due to rate limiting

- **500, 501, 502, 503, etc** - An internal server error occurred

Per questioni di sicurezza applicativa delle informazioni condivise sono
utilizzati solamente i seguenti codici di stato: **200, 400, 500.**
[Fare riferimento a Capitolo 6.1.1](#risposta-e-gestione-degli-errori)

### Swagger

Di seguito la specifica Swagger per l’API retrieveIdPublicServiceList
per la response 200:

[retrieveIdPublicServiceList-200](openapi/retrieveIdPublicServiceListv01_20220408.yml)

Di seguito la specifica Swagger per l’API retrieveIdPublicServiceList
per la response 400:

[retrieveIdPublicServiceList-400](openapi/retrieveIdPublicServiceListv03_20220428_only-http400.yml)

## Recupero DataServiceList da Data Service Directory IT

<u>API:</u> /api/v1/data-service/list/

<u>Prerequisito</u>: selezione della Tipologia di prova di interesse
dall'Evidence broker

<u>Requisito</u>: Recuperare dal Data Service Directory l’identificativo
e le informazioni descrittive dell’Ente Erogatore

| **PROTOCOL**               | HTTPS                                                                  |
| -------------------------- | ---------------------------------------------------------------------- |
| **PATH (Public Exposure)** | https://\<url_service_catalog \>/\[APP_URL\]/api/v1/data-service/list/ |
| **METHOD**                 | GET                                                                    |
| **CONTENT TYPE**           | application / xml                                                      |

**Parameter description:**

<table style="width:100%;">
<colgroup>
<col style="width: 19%" />
<col style="width: 49%" />
<col style="width: 11%" />
<col style="width: 8%" />
<col style="width: 11%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="5"><strong>INPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="5"><strong>HEADER PARAM</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><p><em><strong>Max</strong></em></p>
<p><em><strong>Length</strong></em></p></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>Authorization</td>
<td>Bearer <<em>Voucher PDND</em>></td>
<td>M</td>
<td>n/a</td>
<td>String</td>
</tr>
<tr class="even">
<td colspan="5"><strong>BODY PARAM</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Max Length</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>queryId</td>
<td><p>Vincolo: deve sempre essere valorizzato con la seguente
stringa</p>
<p>“urn:oots:dsd:ebxmlregrep:queries:dataservices-by-evidencetypeand-jurisdiction”</p></td>
<td>M</td>
<td></td>
<td>String</td>
</tr>
<tr class="odd">
<td>evidenceType</td>
<td><p>Identificativo del tipo di prova.</p>
<p><em>Il campo è EvidenceType, ricevuto dall’output
dell’EvidenceTypeList</em></p></td>
<td>M</td>
<td></td>
<td>String</td>
</tr>
<tr class="even">
<td>countryCode</td>
<td>2 lettere ISO 3166-1 alpha-2 del prefisso internazionale.</td>
<td>M</td>
<td></td>
<td>String</td>
</tr>
<tr class="odd">
<td>adminUnitL2</td>
<td>Codice NUTS da 3 a 6 lettere.</td>
<td>O</td>
<td></td>
<td>String</td>
</tr>
<tr class="even">
<td>adminUnitL3</td>
<td>Codice LAU</td>
<td>O</td>
<td></td>
<td>String</td>
</tr>
<tr class="odd">
<td>userIdentificationOrigin</td>
<td>Locazione utente dello schema eID usato per l’autenticazione.DEVE
essere un codice paese di due lettere di ISO 3166-1 alpha-2.</td>
<td>M</td>
<td></td>
<td>String</td>
</tr>
</tbody>
</table>

La response di questa API potrebbe restituire un elenco possibilmente
vuoto di tuple anche se la richiesta HTTP è stata completata
correttamente.

L’ attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Success”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>requestId</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Deve corrispondere all'attributo id dell'oggetto QueryRequest che ha
generato queryResponse.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione corretta dell’API:</p>
<p>"urn:oasis:names:tc:ebxmlregrep: ResponseStatusType:Success"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

| ***Parameter***        | ***Description***                  | ***Mandatory / Optional*** | ***Type*** |
| ---------------------- | ---------------------------------- | -------------------------- | ---------- |
| rim:RegistryObjectList | Contiene le informazioni richieste | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:**
rim:RegistryObjectList*

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:RegistryObject</td>
<td>Contiene il dettaglio della singola richiesta</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>id</td>
<td><p>Attributo del nodo rim:RegistryObjectList</p>
<p>ID è l’identificativo univoco definito dall’Evidence
Provider</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:** rim:*RegistryObject

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:slot</td>
<td>Sezione dedicata all’estrazione</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>name</td>
<td><p>Attributo del nodo rim:slot</p>
<p>Name è l’identificativo della query.</p>
<p>In questo caso “DataServiceEvidenceType”</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:** rim:*slot

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:SlotValue</td>
<td>Valori estratti dalla sezione definita</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>xsi:type</td>
<td><p>Tipologia d’estrazione dovrà essere valorizzata con</p>
<p>“rim:AnyValueType”</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:** rim:*SlotValue

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>DataServiceEvidenceType</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="http://data.europa.eu/sdg#"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"</p></td>
<td>O</td>
<td>Object</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:***
DataServiceEvidenceType

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Identifier</td>
<td><p>Tipologia di Prova (Evidence Type) Metadata</p>
<p>Identificatore univoco fornito dal Data Service per identificare in
modo univoco il tipo di prova ed i metadati correlati.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>EvidenceTypeClassification</td>
<td>Informazioni sulla classificazione. Usato per il collegamento con
l’Evidence Broker</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>Title</td>
<td>Titolo relativo alle informazioni sulla classificazione</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>DistributedAs</td>
<td>Lista delle multiple distribuzioni disponibili per Tipologia di
Prova del Servizio Dati</td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>AccessService</td>
<td><p>Servizio di accesso rappresenta il Servizio dati che notifica la
prova per conto di un Fornitore di prove.</p>
<p>E’ consentito avere Servizi ad accesso multiplo (Multiple Access
Services) al quale è associato un singolo Fornitore di prove (Evidence
Provider).</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="even">
<td>AuthenticationLevelOfAssurance</td>
<td>Livello di garanzia richiesto dell'autenticazione dell'utente.</td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>EvidenceSubjectLevelOfAssurance</td>
<td>Il livello minimo di proprietà del livello di garanzia deve avere
per essere preso in considerazione per l’account del record del soggetto
da parte di questo set di dati. Corrisponde alle capacità eIDAS.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>EvidenceSubjectIdentifierScheme</td>
<td>L'indicazione degli schemi di identificazione è supportata dalla
corrispondenza del record del soggetto.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>EvidenceProviderDeterminationContext</td>
<td>Le informazioni necessarie per selezionare il provider di prove da
utilizzare per questo tipo di prova associato</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>UserIdentityAttributeType</td>
<td><p>Mappare tutti gli attributi che riconducono alla corretta
identificazione.</p>
<p>Il nodo deve contenere il seguente attributo:</p>
<p>levelOfAssurance="http://eidas.europa.eu/LoA/High"</p>
<p>Possono essere aggiunti i seguenti attributi:</p>
<p>indica se l’attributo aggiuntivo è necessario o meno. In caso di
necessità indicherà l’univocità della richiesta.</p>
<p>mandatory="false"</p>
<p>Utilizzato in caso di fornire un meccanismo di filtraggio per il
richiedente di prove</p>
<p>userIdentificationOrigin="NL"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>EvidenceSubjectNature</td>
<td><p>Indica il tipo di persona da autenticare. Sono consentite 2
tipologie attualmente:</p>
<p>Una persona fisica, utilizzando l'URI predefinito eIDAS <a
href="http://eidas.europa.eu/attributes/naturalperson">http://eidas.europa.eu/attributes/naturalperson</a></p>
<p>Una persona giuridica, che utilizza l'URI predefinito eIDAS <a
href="http://eidas.europa.eu/attributes/legalperson">http://eidas.europa.eu/attributes/legalperson</a></p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** DistributedAs

| ***Parameter*** | ***Description***                                                                                                                                                                                                                                                                               | ***Mandatory / Optional*** | ***Type*** |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| Format          | formati disponibili per il tipo di prova in formato strutturato come XML, JSON o non strutturato come PDF.                                                                                                                                                                                      | M                          | String     |
| ConformsTo      | Solo per formati strutturati come XML, JSON, RDF il servizio dati può fornire una dichiarazione di conformità per indicare il profilo di conformità semantica e tecnica. L’URL che punta a una voce del repository semantico OOTS che contiene tutte le informazioni rilevanti di tale profilo. | O                          | String     |

***Nodo Padre della seguente lista di nodi figli:*** AccessService

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Identifier</td>
<td>Identificatore del servizio di accesso. E’utilizzato
dall'infrastruttura eDelivery per estrarre e utilizzare il PMode
preconfigurato appropriato per l'invio della richiesta di prove</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>schemeID</td>
<td><p>Attributo del nodo Identifier.</p>
<p>Per l’associazione del partyid-type far riferimento (CEF eDelivery
ebcore Party Identifier) link:
http://docs.oasis-open.org/ebcore/PartyIdType/v1.0/PartyIdType-1.0.pdf</p>
<p>Esempio:</p>
<p>schemeID="urn:oasis:names:tc:ebcore:partyid-type:iso6523:0060"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>AccessURL</td>
<td>URL del servizio d’accesso.</td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>ConformsTo</td>
<td><p>Versione e Profilo dell’Evidence Exchange Message Data Model.</p>
<p>Attualmente la sola valorizzazione consentita è:
“oots:edm-1.0”</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>Publisher</td>
<td><p>Informazioni sull’accesso del Fornitore di Prove (Evidence
Provider)</p>
<p>fornisce il nome, l'ubicazione e la giurisdizione del fornitore di
prove, utilizzato dal richiedente di prove (Evidence Requester) per
filtrare e selezionare il fornitore di prove corretto.</p></td>
<td>M</td>
<td>Object</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** Publisher

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>Identifier</td>
<td>Identificazione univoca per l’agente.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>schemeID</td>
<td><p>Attributo del nodo Identifier.</p>
<p>L’attributo è utilizzato per identificare il contesto in cui è
indicato l’Identifier.</p>
<p>Esempio:</p>
<p>schemeID="1204"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>Name</td>
<td>Etichetta associata all’agente.</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>Address</td>
<td><p>Una posizione del fornitore di prove sotto forma di
indirizzo.</p>
<p>Se la giurisdizione basata sul territorio fisico non può essere
fornita, l'indirizzo può essere fornito per facilitare il requisito
funzionale per determinare il fornitore di prove in base alle
informazioni sulla posizione.</p></td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>Jurisdiction</td>
<td>La giurisdizione spaziale del fornitore di prove. Il codice dovrebbe
riflettere la demarcazione geografica più precisa per la giurisdizione
applicabile nell'elenco dei codici utilizzati. Esempio: codice LAU o
NUTS per Vienna</td>
<td>M</td>
<td>Object</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** Address

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>FullAddress</td>
<td>Indirizzo completo del Fornitore delle Tipologie di Prova (Evidence
Provider).</td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>AdminUnitLevel1</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 1 si riferisce all'unità amministrativa più alta per
l'indirizzo, quasi sempre un paese.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>AdminUnitLevel2</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 2 si riferisce alla regione dell'indirizzo, di solito una
contea, uno stato o un'altra area simile che in genere comprende diverse
località.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>AdminUnitLevel3</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 3 si riferisce al comune.</p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** Jurisdiction

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>AdminUnitLevel1</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 1 si riferisce all'unità amministrativa più alta per
l'indirizzo, quasi sempre un paese.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>AdminUnitLevel2</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 2 si riferisce alla regione dell'indirizzo, di solito una
contea, uno stato o un'altra area simile che in genere comprende diverse
località.</p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>AdminUnitLevel3</td>
<td><p>Unità di amministrazione relativi all'esercizio dei diritti
giurisdizionali.</p>
<p>Il livello 3 si riferisce al comune.</p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP.

Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description**        |
| ------------- | ----------------------------- |
| 200           | Service executed successfully |

### Risposta e Gestione degli errori

E’ gestita la possiblità che l’API non risponda correttamente alle
richieste. Di seguito il payload di output, differente a quello in caso
di successo, contenente le indicazioni per gestire l’eccezione
riscontrata.

L’ attributo status dovrà indicare l’esito dell’elaborazione.

In questo caso lo status dovrà contenere “Failure”

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th colspan="4"><strong>OUTPUT</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td colspan="4"><strong>HEADER</strong></td>
</tr>
<tr class="even">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="odd">
<td>TBD</td>
<td><em>TBD</em></td>
<td></td>
<td></td>
</tr>
<tr class="even">
<td colspan="4"><strong>BODY</strong></td>
</tr>
<tr class="odd">
<td><em><strong>Parameter</strong></em></td>
<td><em><strong>Description</strong></em></td>
<td><em><strong>Mandatory / Optional</strong></em></td>
<td><em><strong>Type</strong></em></td>
</tr>
<tr class="even">
<td>query:QueryResponse</td>
<td><p>oggetto Response.</p>
<p>Il nodo deve contenere i seguenti attributi:</p>
<p>xmlns="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:lcm="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"
xmlns:query="urn:oasis:names:tc:ebxml-regrep:xsd:query:4.0"
xmlns:rim="urn:oasis:names:tc:ebxml-regrep:xsd:rim:4.0"
xmlns:rs="urn:oasis:names:tc:ebxml-regrep:xsd:rs:4.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="urn:oasis:names:tc:ebxml-regrep:xsd:lcm:4.0"</p></td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>status</td>
<td><p>Attributo del nodo query:QueryResponse</p>
<p>Dovrà essere valorizzato nel seguente modo per indicare
l’elaborazione errata dell’API:
"urn:oasis:nammes:tc:ebxml-regrep:ResponseStatusType:Failure"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** query:QueryResponse

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rs:Exception</td>
<td>Contiene le informazioni relativa all’eccezione sollevata.</td>
<td>M</td>
<td>Object</td>
</tr>
<tr class="even">
<td>xsi:type</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica a quale categoria appartiene l’errore riscontrato.</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="odd">
<td>severity</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Indica la gravità dell’eccezione riscontrata.</p>
<p>L’attributo dovrà essere valorizzato nel seguente modo:</p>
<p>"urn:oasis:names:tc:ebxml-regrep:ErrorSeverityType:Error"</p></td>
<td>M</td>
<td>String</td>
</tr>
<tr class="even">
<td>message</td>
<td><p>Attributo del nodo rs:Exception</p>
<p>Contiene il messaggio in lingua inglese come indicato nella tabella
degli scenari di errori.</p>
<p>Esempio:</p>
<p>"List of requirements requested is empty"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** rs:Exception

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:Slot</td>
<td>Sezione dedicata all’estrazione</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>name</td>
<td><p>Attributo del nodo rim:Slot</p>
<p>Opzionale:</p>
<p>name="JurisdictionDetermination"</p>
<p>name="UserRequestedClassificationConcepts"</p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** rim:Slot

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>rim:SlotValue</td>
<td>Sezione dedicata all’estrazione</td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="even">
<td>xsi:type</td>
<td><p>Attributo del nodo SlotValue</p>
<p>Valorizzazione possibile del parametro xsi:type:</p>
<p>xsi:type="rim:AnyValueType"</p></td>
<td>M</td>
<td>String</td>
</tr>
</tbody>
</table>

***Nodo Padre della seguente lista di nodi figli:*** rim:SlotValue

| ***Parameter***                               | ***Description***                                                                              | ***Mandatory / Optional*** | ***Type*** |
| --------------------------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| sdg:EvidenceProviderJurisdictionDetermination | Contestualizzazione dell’utente con una sua proprietà specifica ed il tipo della prova emessa. | O                          | Object     |
| sdg:EvidenceProviderClassification            | per supportare un meccanismo di filtraggio sul lato richiedente di prove.                      | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:***
sdg:EvidenceProviderJurisdictionDetermination

| ***Parameter***           | ***Description***                                                                                                     | ***Mandatory / Optional*** | ***Type*** |
| ------------------------- | --------------------------------------------------------------------------------------------------------------------- | -------------------------- | ---------- |
| sdg:JurisdictionContextId | response della Data Service Directory all’utente                                                                      | O                          | String     |
| sdg:JurisdictionContext   | Il contesto della giurisdizione stessa. Nella lingua da visualizzare nell’interfaccia utente del richiedente di prove | O                          | String     |
| sdg:JurisdictionLevel     | Il livello di giurisdizione richiesto, definendo la granularità richiesta della giurisdizione.                        | O                          | String     |

***Nodo Padre della seguente lista di nodi figli:***
sdg:EvidenceProviderClassification

| ***Parameter***           | ***Description***            | ***Mandatory / Optional*** | ***Type*** |
| ------------------------- | ---------------------------- | -------------------------- | ---------- |
| sdg:ClassificationConcept | Classificazione del concetto | O                          | Object     |

***Nodo Padre della seguente lista di nodi figli:***
sdg:ClassificationConcept

<table>
<colgroup>
<col style="width: 19%" />
<col style="width: 56%" />
<col style="width: 11%" />
<col style="width: 12%" />
</colgroup>
<thead>
<tr class="header">
<th><em><strong>Parameter</strong></em></th>
<th><em><strong>Description</strong></em></th>
<th><em><strong>Mandatory / Optional</strong></em></th>
<th><em><strong>Type</strong></em></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>sdg:Identifier</td>
<td><p>Identificatore del filtro</p>
<p>Esempio:</p>
<p><sdg:Identifier>TypeOfInsurance</sdg:Identifier></p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>sdg:Type</td>
<td><p>Tipo di dato</p>
<p>Esempio:</p>
<p><sdg:Type>String</sdg:Type></p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="odd">
<td>sdg:ValueExpression</td>
<td><p>Sintassi filtro attraverso Espressione Regolare</p>
<p>Esempio:</p>
<p><sdg:ValueExpression>^\d{5}$</sdg:ValueExpression></p></td>
<td>O</td>
<td>String</td>
</tr>
<tr class="even">
<td>sdg:Description</td>
<td><p>Descrizione del filtro</p>
<p>Esempio:</p>
<p><sdg:Description lang="en">Type of
insurance</sdg:Description></p></td>
<td>O</td>
<td>Object</td>
</tr>
<tr class="odd">
<td>lang</td>
<td><p>Attributo del nodo sdg:Description</p>
<p>Specifica il linguaggio</p>
<p>Esempio:</p>
<p>lang="en"</p></td>
<td>O</td>
<td>String</td>
</tr>
</tbody>
</table>

I codici di stato HTTP vengono consegnati al browser nell’intestazione
HTTP. Riguardano l'esito dell'eventuale elaborazione da pare del server.

| **HTTP Code** | **Result Description** |
| ------------- | ---------------------- |
| 400           | Bad Request            |

Ogni servizio dovrà gestire tutti gli scenari di errori elencati di
seguito.

<table>
<colgroup>
<col style="width: 16%" />
<col style="width: 19%" />
<col style="width: 32%" />
<col style="width: 32%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Code</strong></th>
<th><strong>Description</strong></th>
<th><strong>Type</strong></th>
<th><strong>Message</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>EB:ERR:0001</td>
<td>La lista dei Requirement è vuota</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>List of requirements requested is empty</td>
</tr>
<tr class="even">
<td>EB:ERR:0002</td>
<td>Requirement non trovato</td>
<td><p>rs:ObjectNotFoundEx</p>
<p>ceptionType</p></td>
<td>The requirement requested, represented by the requirement id, does
not exist</td>
</tr>
<tr class="odd">
<td>EB:ERR:0003</td>
<td>Codice del livello di giurisdizione sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The jurisdiction level code query parameter is invalid or
unknown</td>
</tr>
<tr class="even">
<td>EB:ERR:0004</td>
<td>Procedimento amministrativo sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedureid query parameter is invalid or
unknown</td>
</tr>
<tr class="odd">
<td>EB:ERR:0005</td>
<td>Codice del Paese sconosciuto</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The value of the procedure implementation country query parameter is
invalid or unknown</td>
</tr>
<tr class="even">
<td>EB:ERR:0006</td>
<td>Query sconosciuta</td>
<td><p>rs:InvalidRequestExc</p>
<p>eptionType</p></td>
<td>The requested Query does not exist</td>
</tr>
</tbody>
</table>

Di seguito la lista dei codici di stato http per indicare il successo o
il fallimento di una request

**Codici di successo**

- **200 OK** - Request succeeded. Response included

- **201 Created** - Resource created. URL to new resource in Location
  header

- **204 No Content** - Request succeeded, but no response body

**Codici di errore**

- **400 Bad Request** - Could not parse request

- **401 Unauthorized** - No authentication credentials provided or
  authentication failed

- **403 Forbidden** - Authenticated user does not have access (da non
  utilizzare)

- **404 Not Found** - Resource not found

- **415 Unsupported Media Type** - POST/PUT/PATCH request occurred
  without a **application/json content type**

- **422 Unprocessable Entry** - A request to modify or create a
  resource failed due to a validation error

- **429 Too Many Requests** - Request rejected due to rate limiting

- **500, 501, 502, 503, etc** - An internal server error occurred

Per questioni di sicurezza applicativa delle informazioni condivise sono
utilizzati solamente i seguenti codici di stato: **200, 400, 500.**
[Fare riferimento a Capitolo 6.1.1](#risposta-e-gestione-degli-errori)

### Swagger

Di seguito la specifica Swagger per l’API retrieveDataServiceList per la
response 200:

[retrieveDataServiceList-200](openapi/retrieveDataServiceListv06_20220408.yml)

Di seguito la specifica Swagger per l’API retrieveDataServiceList per la
response 400:

[retrieveDataServiceList-400](openapi/retrieveDataServiceListv03_20220427_only-http400.yml)

# Storico delle modifiche al documento (Changelog)

| **Vers.** | **Paragrafi modificati**                                      | **Tipo di modifica** | **Modifica apportata**                                                                                                                            |
| --------- | ------------------------------------------------------------- | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1.0       | Tutti                                                         | Creazione            | Creazione del documento e documentazione delle API dell’Evidence Broker                                                                           |
| 1.1       | RECUPERO DATASERVICELIST DA DATA SERVICE DIRECTORY IT         | Creazione            | Aggiunta del paragrafo                                                                                                                            |
|           | Tutti                                                         | Integrazione         | Modificato il paragrafo per allineare il parametro QueryResponse alla documentazione ufficiale europea ricevuta                                   |
|           | RECUPERO IDPUBLICSERVICELIST\Swagger                          | Correzione           | modificato lo swagger http 200                                                                                                                    |
|           | Tutti                                                         | Correzione           | Modifica Campi mandatory/optional in conformità alla documentazione ufficiale europea ricevuta. Ove necessario sono stati introdotti nuovi campi. |
|           | Tutti                                                         | Correzione           | Adeguamento swagger http 200                                                                                                                      |
|           | RECUPERO DATASERVICELIST DA DATA SERVICE DIRECTORY IT         | Correzione           | Modificati parametri di input                                                                                                                     |
|           | RECUPERO DATASERVICELIST DA DATA SERVICE DIRECTORY IT\swagger | Integrazione         | Aggiunto lo swagger http 400                                                                                                                      |
| 1.2       | RECUPERO IDPUBLICSERVICELIST\Swagger                          | Integrazione         | Aggiunto lo swagger http 400                                                                                                                      |
|           | RECUPERO DATASERVICELIST DA DATA SERVICE DIRECTORY IT\Swagger | Correzione           | Modificato lo swagger http 400                                                                                                                    |
|           | GESTIONE DELLE CHIAVI PER RECUPERO DELLE PROCEDURE            | Correzione           | Eliminata immagine                                                                                                                                |
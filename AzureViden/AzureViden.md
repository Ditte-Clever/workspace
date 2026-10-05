# Azure-viden

Dette dokument konkretiserer indholdet i [AzurePresentation.pptx](./AzurePresentation.pptx) til daglig reference for CRM-, integrations- og .NET-arbejde. Det er skrevet som en introduktion og et fælles sprog - ikke som en komplet Azure-standard eller en beskrivelse af et specifikt produktionsmiljø.

> **Kilde og afgrænsning:** Indholdet er baseret på præsentationens 77 slides. Navne på ressourcer, miljøer og integrationer i diagrammerne er eksempler fra præsentationen. De skal valideres mod den aktuelle Azure-konfiguration, før de bruges som implementeringsbeslutning.

## 1. Kort fortalt

- **Cloud** er en leverancemodel, hvor IT-ressourcer leveres over internettet i stedet for fra virksomhedens egne servere.
- **Azure** er Microsofts cloud-platform. Azure er ikke én service, men en platform af services, som kombineres til løsninger.
- En **resource** er en konkret Azure-service, for eksempel en App Service, Function App, Azure SQL Database eller Service Bus.
- **C#/.NET** er kode og runtime/platform. Azure leverer blandt andet hosting, netværk, identitet, messaging, datalagring og overvågning omkring koden.
- **Dynamics 365/Dataverse** kan være forretningssystem og source of truth, mens Azure håndterer integration, behandling, skalering og teknisk drift.
- Den vigtigste overgang fra on-premises er, at virksomheden ikke længere selv ejer og driver hele infrastrukturen. Ansvaret flyttes delvist til cloud-leverandøren, men applikation, data, konfiguration, identitet og korrekt sikkerhed er stadig vores ansvar.

## 2. Fra on-premises til cloud

### 2.1 On-premises-modellen

I et traditionelt setup står virksomheden selv for:

- fysiske servere, strøm, køling og netværk
- operativsystem, webserver og database
- firewall, backup og opdateringer
- hardwareindkøb, kapacitet og udskiftning
- deployment og adgangsstyring

Et typisk setup kan bestå af en firewall, en webserver, en applikation og en database på fysisk hardware. Når virksomheden vokser, skal den selv købe og opsætte flere servere, planlægge redundans og håndtere global tilgængelighed.

**Fordele ved on-premises** (slide 9):

- høj kontrol over hardware, operativsystem, netværk og fysisk adgang
- data kan opbevares i egne faciliteter
- hardware købes én gang, og omkostningerne kan være forudsigelige

**Udfordringer ved vækst** (slides 12-14):

- skalering kræver nye indkøb og installationer
- kapacitetsproblemer giver langsommere svartider
- høj tilgængelighed og backup kræver ekstra infrastruktur
- drift og vedligeholdelse kræver kompetencer og tid
- global drift kræver egne datacentre eller tilsvarende setup

### 2.2 Hvad cloud ændrer

Cloud ændrer primært, **hvordan infrastrukturen leveres og administreres**. Den ændrer ikke i sig selv den forretningslogik, som applikationen skal udføre.

| On-premises | Cloud |
|---|---|
| Virksomheden ejer serverne | Cloud-leverandøren ejer datacentret og hardwaren |
| Virksomheden vedligeholder infrastrukturen | Leverandøren håndterer en del af den underliggende drift |
| Hardware bestilles og installeres | Ressourcer kan oprettes på få minutter |
| Skalering planlægges manuelt | Skalering kan automatiseres, afhængigt af service og design |
| Kapacitet bindes til indkøbt hardware | Kapacitet lejes efter behov og konfiguration |

Cloud er ikke det samme som, at alt ansvar forsvinder. Ansvarsfordelingen afhænger af den valgte service. Vi har fortsat ansvar for blandt andet kode, data, konfiguration, identiteter, rettigheder og sikker anvendelse.

## 3. Azure som platform

Azure samler services i Microsofts datacentre og gør dem tilgængelige via Azure Portal, API'er, CLI, IaC og andre administrationsværktøjer. Den samme løsning kan typisk bygges af flere Azure-brikker:

| Område | Formål | Eksempler fra præsentationen |
|---|---|---|
| Compute | Køre kode | App Service, Virtual Machines, Azure Functions, AKS |
| Storage | Gemme filer og objekter | Blob Storage, Files, Managed Disks |
| Databaser | Gemme strukturerede eller distribuerede data | Azure SQL, Cosmos DB |
| Networking | Forbinde og beskytte services | Virtual Network, Application Gateway, Load Balancer, Front Door |
| Integration | Udveksle beskeder og events | Service Bus, Event Grid, Event Hubs, Storage Queues |
| Identity | Identificere brugere og services | Microsoft Entra ID, Managed Identity, RBAC |
| Monitoring | Observere drift og fejl | Application Insights, Azure Monitor, Log Analytics |

Azure er dermed bedst forstået som **lego-klodser**: Man vælger den service, der løser et konkret behov, og designer forbindelserne mellem dem.

## 4. Azure-hierarkiet

Ressourcer organiseres hierarkisk:

```text
Tenant
└── Subscription
    └── Resource group
        └── Resources
            ├── App Service
            ├── Function App
            ├── Azure SQL
            └── Key Vault
```

### Tenant

Organisationens identitetsgrænse i Microsoft Entra ID. Tenant’en indeholder brugere, grupper, applikationer og identitetsrelaterede politikker.

### Subscription

En administrativ og økonomisk grænse for Azure. Den bruges blandt andet til fakturering, kvoter, adgang og afgrænsning af ressourcer.

### Resource group

En logisk container for relaterede resources. Resource groups er ikke nødvendigvis en sikkerhedsgrænse i sig selv, men de bruges til organisering, adgang, livscyklus og drift.

### Resource

En konkret Azure-service, som kan konfigureres, overvåges og gives adgang til. Eksempler er en App Service, en Function App, en Service Bus eller en Key Vault.

Præsentationen illustrerer, at resources kan grupperes efter team og miljø, for eksempel separate grupper for produktion og non-production. De konkrete navne og grænser skal følge Clevers aktuelle Azure-governance.

## 5. De vigtigste byggeklodser

### 5.1 Compute: App Service og Azure Functions

**App Service** er managed hosting til webapps og API'er. En .NET API kan deployes til App Service uden, at teamet selv skal drive en fysisk Windows Server og IIS.

**Azure Functions** kører kode som reaktion på en trigger, for eksempel:

- en HTTP-request
- en timer
- en besked på Service Bus
- et event fra en anden service

Functions passer især til event-drevet og baggrundsbaseret behandling. Vælg ikke automatisk Functions til alt; hostingmodel, levetid, skalering, observability og fejlhåndtering skal passe til workloaden.

### 5.2 Storage: Blob Storage

Blob Storage er objekt- og fillagring. Det kan bruges til dokumenter, billeder, eksportfiler og andre objekter, hvor en relationel database ikke er det rigtige lagringssted.

Adgang, retention, kryptering, navngivning og placering skal aftales efter dataklassifikation og systemets behov. Persondata eller andre følsomme data må ikke placeres i storage uden godkendt design og passende adgangskontrol.

### 5.3 Networking

Networking forbinder services og styrer, hvordan trafik bevæger sig:

- **Virtual Network (VNet):** privat, logisk netværk i Azure
- **Application Gateway:** trafikstyring og routing til applikationer
- **Load Balancer:** fordeling af trafik
- **Front Door:** global indgang og routing, hvor det er relevant

Netværksdesign skal være mindst muligt eksponeret og følge Clevers sikkerhedsarkitektur. Brug ikke offentlige endpoints som standard, og dokumentér nødvendige forbindelser mellem CRM, Azure og eksterne systemer.

### 5.4 Integration: Service Bus og events

**Azure Service Bus** understøtter robust messaging mellem systemer. Med asynkron kommunikation kan afsenderen lægge en besked på en kø eller topic uden at vente på, at alle modtagere er færdige.

Typisk mønster:

1. Et system publicerer en besked.
2. En kø eller topic modtager beskeden.
3. En consumer, ofte en Azure Function, læser beskeden.
4. Consumeren validerer og behandler beskeden.
5. Consumeren kalder eventuelt et eksternt system eller publicerer næste event.
6. Fejl håndteres med retry og dead-letter, hvis beskeden ikke kan behandles.

**Service Bus**, **Event Grid**, **Event Hubs** og **Storage Queues** har forskellige formål. Valget skal baseres på leveringsgarantier, ordering, throughput, replay, consumer-model og fejlhåndtering - ikke kun på navnet "event".

### 5.5 Identity: Entra ID, Managed Identity og RBAC

- **Microsoft Entra ID** håndterer identitet og adgang.
- **Managed Identity (MI)** giver en Azure-resource en identitet, som andre services kan godkende uden hardcodede credentials.
- **Role-Based Access Control (RBAC)** bestemmer, hvem eller hvad der må udføre hvilke handlinger på hvilke resources.

Foretræk Managed Identity og mindst mulige rettigheder. Secrets skal ikke ligge i kildekode, almindelige konfigurationsfiler eller logs.

### 5.6 Monitoring og observability

**Application Insights**, **Azure Monitor** og **Log Analytics** bruges til at forstå, om løsningen virker:

- logs: hvad applikationen skrev
- metrics: målbare værdier som antal requests, fejl og svartider
- traces: sammenhængende forløb på tværs af services
- dashboards og alarmer: hvordan driftsteamet opdager og reagerer på problemer

En løsning er ikke driftsklar, blot fordi den kan deployes. Den skal også have korrelation, relevante logs, alarmer, retry-status og en dokumenteret procedure for fejlsøgning. Logs må ikke indeholde secrets, CPR-numre eller unødvendige persondata.

## 6. Dynamics 365 og Azure

Dynamics 365 står ikke alene. Platformen indeholder blandt andet:

- brugergrænseflade og standard CRM-funktionalitet
- Dataverse-data
- security roles
- business rules
- plugins
- workflows og Power Automate

Azure kan supplere Dynamics med services, der egner sig bedre til integration og teknisk behandling:

```text
Dynamics 365 / Dataverse
        │
        ├── API Management
        ├── Service Bus
        └── Function App / andre consumers
```

Et illustreret integrationsmønster fra præsentationen er:

1. Dynamics 365 gemmer en række i Dataverse.
2. En hændelse udsendes.
3. En besked lægges på et Service Bus topic, for eksempel et topic for bestillinger.
4. En Function App eller anden consumer lytter på topic’et.
5. Consumeren validerer, beriger eller videresender data til et eksternt system.

Dataverse er fortsat forretningssystemets datakilde, hvor det er besluttet. Integrationer må ikke uden en eksplicit forretningsbeslutning overskrive eksisterende CRM-værdier. Eventuelt payload-design, idempotens og deduplikering skal dokumenteres for den konkrete integration.

## 7. C#/.NET i Azure

### 7.1 C# og .NET

- **C#** er programmeringssproget, som bruges til at skrive kode og forretningslogik.
- **.NET** er platformen med runtime, standardbiblioteker og værktøjer til at bygge og køre applikationen.
- Et API kan eksponere endpoints som `GET`, `POST` og `DELETE`, der kommunikerer via HTTP.

En enkel mental model er:

```text
C#       = sproget, som beskriver løsningen
.NET     = platformen og værktøjskassen
API      = applikationens kontrollerede grænseflade
Azure    = hosting, netværk, data, messaging, identitet og drift omkring koden
```

### 7.2 Synkron og asynkron kommunikation

**Synkron** kommunikation betyder, at System A kalder System B og venter på svar, før A kan fortsætte. Det er enkelt at forstå, men kobler systemerne tæt sammen og kan give længere svartider eller kaskadefejl.

**Asynkron** kommunikation betyder, at System A sender en besked og fortsætter. System B og eventuelt System C behandler beskeden senere. Det giver løsere kobling og bedre mulighed for retry, men kræver eksplicit håndtering af:

- duplikater og idempotens
- rækkefølge
- dead-letter
- eventual consistency
- overvågning og genbehandling

### 7.3 Configuration, secrets og identity

En .NET-applikation skal kunne kende sin konfiguration uden at få secrets bygget ind i koden. Præsentationen viser kombinationen:

```text
.NET-applikation
      │
      ├── Managed Identity
      ├── Key Vault
      └── App Configuration
```

Brug Key Vault til secrets, App Configuration til ikke-hemmelig konfiguration, og Managed Identity til adgang, hvor servicen understøtter det. Konfiguration skal adskille Dev, Staging og Prod og må ikke indeholde miljøhemmeligheder i repositoryet.

## 8. Konkret business-scenarie: kunde opretter en ordre

Præsentationen bruger en ordre som gennemgående eksempel. Kravene er:

- validere data
- gemme ordren
- informere andre systemer
- udføre eventuel efterbehandling

### 8.1 Foreslået logisk forløb

```text
Kunde
  │
  ▼
Dynamics 365 / Dataverse
  │  Gemmer ordre og publicerer event
  ▼
API Management
  │
  ▼
Order API på App Service
  │
  ├── Azure SQL (data, hvis det er den valgte systemdel)
  └── Service Bus (asynkrone beskeder)
          │
          ▼
     Function App
          │
          ▼
     Eksternt system
```

Dette diagram er en konceptuel oversættelse af slides 62-68. Det er ikke en teknisk løsningsspecifikation.

### 8.2 Ansvar pr. trin

| Trin | Ansvar |
|---|---|
| Modtag ordre | CRM/API validerer input og afviser ugyldige requests tidligt |
| Gem ordre | Dataverse eller den aftalte datakilde gemmer den autoritative forretningsdata |
| Publicer event | Eventet indeholder kun nødvendige data og en stabil reference til ordren |
| Behandl besked | Consumer validerer, logger korrelation og er idempotent |
| Kald partner | Integration håndterer timeout, retry og fejl uden at miste beskeden |
| Afslut behandling | Status og fejl er synlige for drift og kundeservice |

### 8.3 Fejlhåndtering

Hvis et eksternt system fejler, kan consumeren forsøge igen efter en defineret retry-politik. Hvis beskeden fortsat ikke kan behandles, skal den ende i en dead-letter-kø eller tilsvarende kontrolleret fejlhåndtering.

Det skal være muligt at se:

- hvilken ordre og correlation id fejlen vedrører
- hvor mange gange beskeden er forsøgt
- hvilken service og version der behandlede den
- om fejlen kan genbehandles sikkert
- hvem eller hvad der må genbehandle den

En retry må ikke skabe dubletter eller overskrive korrekte CRM-data. Designet skal derfor beskrive idempotency key, deduplikering og statusovergange.

## 9. Praktisk tjekliste for nye Azure-integrationer

### Før udvikling

- [ ] Er systemgrænser og source of truth dokumenteret?
- [ ] Er Dev, Staging og Prod adskilt i konfiguration og adgang?
- [ ] Er den valgte Azure-service begrundet ud fra workloaden?
- [ ] Er data, persondata og eventuelle regulatoriske krav klassificeret?
- [ ] Er netværksadgang og eksponering beskrevet?
- [ ] Er identitet og RBAC designet med mindst mulige rettigheder?

### Før deployment

- [ ] Ligger ingen secrets, connection strings, Prod-GUID'er eller miljø-URL'er i kode eller dokumentation?
- [ ] Bruges Managed Identity, hvor det er muligt?
- [ ] Er Key Vault/App Configuration koblet korrekt til miljøet?
- [ ] Er inputvalidering, timeout, retry, dead-letter og idempotens defineret?
- [ ] Er logs, metrics, traces, correlation id og alarmer tilgængelige?
- [ ] Er deployment og konfiguration reproducerbar, gerne via godkendt IaC?

### Før Prod

- [ ] Er integrationen testet i Dev og Staging?
- [ ] Er både succes, timeout, retry, duplikat og dead-letter testet?
- [ ] Har mindst to testere valideret en ny proces før Prod?
- [ ] Findes en runbook for fejlsøgning og genbehandling?
- [ ] Er sikkerhed, GDPR og eventuelle NIS2/CER-forhold vurderet af de ansvarlige funktioner?

## 10. Azure-ordbog

| Begreb | Betydning |
|---|---|
| API | Grænseflade, som systemer bruger til at kommunikere |
| APIM | Azure API Management; eksponerer, beskytter og administrerer API'er |
| App Service | Managed hosting af webapps og .NET API'er |
| Azure Function | Kører kode på baggrund af events, HTTP, timers eller andre triggers |
| Azure SQL | Managed SQL-database |
| Blob Storage | Objekt- og fillagring |
| Application Insights | Telemetri, logs, traces og monitoring for applikationer |
| Entra ID | Microsofts identitets- og adgangstjeneste |
| Function App | Container for en eller flere Azure Functions |
| IaC | Infrastructure as Code; infrastruktur defineret som kode, for eksempel Bicep |
| Managed Identity | Identitet, som en Azure-resource kan bruge uden gemte credentials |
| RBAC | Role-Based Access Control; bestemmer hvem der må hvad |
| Resource | En konkret Azure-service |
| Resource group | Logisk container for Azure-resources |
| Service Bus | Messaging mellem systemer, typisk asynkront |
| Subscription | Azure-enhed for fakturering, adgang og kvoter |
| Tenant | Organisationens identitetsgrænse i Entra ID |
| VNet | Virtual Network; privat, logisk netværk i Azure |

## 11. Opsummering

1. Cloud er en måde at levere IT-infrastruktur på.
2. Azure er Microsofts platform for cloud-services.
3. Azure-resources er byggeklodser, som sammensættes til en løsning.
4. App Service og Functions kører kode; Azure SQL og Blob Storage gemmer data; Service Bus forbinder systemer; Entra ID og RBAC styrer adgang; Application Insights og Azure Monitor viser, om løsningen virker.
5. Dynamics 365 kan integrere med Azure, så CRM håndterer forretningen, mens Azure håndterer skalerbar integration og teknisk behandling.
6. En god integration er ikke kun en successti. Den skal også være sikker, observerbar, idempotent og håndterbar, når et eksternt system fejler.

> **KAN KRÆVE JURIDISK GENNEMGANG:** Dataplacering, persondata, retention, adgangsstyring og krav under GDPR, NIS2 eller CER skal vurderes af Clevers ansvarlige for IT-sikkerhed, GDPR og Jura. Denne side er teknisk introduktionsmateriale og erstatter ikke en sådan vurdering.

---

copyright:
  years: 2017
lastupdated: "2018-11-10"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}


# Introduzione a IBM Virtual Router Appliance
{: #getting-started-with-ibm-virtual-router-appliance}

Per iniziare a utilizzare IBM© Virtual Router Appliance (VRA), passa alla pagina degli ordini nel portale del cliente: 

1. Dal tuo browser, apri [Customer Portal ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://control.softlayer.com/){: new_window} e accedi al tuo account.
2. Nella navigazione del portale del cliente, seleziona **Network > Gateway Appliances**.
3. Dalla pagina di elenco **Gateway Appliances**, fai clic su **Order Gateway**.
4. Dalla pagina **Order**, seleziona il tuo data center desiderato dal menu a discesa, poi scegli il tipo desiderato di hardware server.

    **NOTA:** i requisisti server minimi di VRA sono 8 GB di RAM e un core CPU per ogni 10 Gbps di capacità di rete. Ad esempio, un sistema con uplink privato e pubblico 10 Gbps duale richiede almeno quattro core. Riserva più spazio rispetto alle impostazioni predefinite del disco se pensi di eseguire le diagnostiche di rete che generano i log dettagliati. Infine, se la tua intenzione è di configurare i servizi VPN con la codifica, potresti voler aggiungere ulteriori core. L'aggiunta di ulteriori core ai servizi VPN garantirà che la VRA non si bloccherà a causa di un carico pesante quando instradi e contemporaneamente codifichi/decodifichi i dati.

5. Nella pagina Order, seleziona, se lo desideri, l'opzione **High Availability Pair**, seleziona la dimensione della memoria, seleziona la versione appropriata del sistema operativo VRA e seleziona quindi la velocità di uplink di rete.

6. Controlla le tue selezioni e fai clic su **Add to Order**, l'ordine sarà verificato automaticamente.
7. Nella pagina **CHECKOUT**, se già gestisci le tue VLAN nel data center selezionato, seleziona le VLAN di backend che devono essere protette. Fornisci un nome host e dominio alla tua VRA. Seleziona tutte le caselle dei termini del servizio IBM Cloud e del contratto di servizio di terze parti. Quindi fai clic su **Submit Order**.

Dopo l'approvazione del tuo ordine, il provisioning della tua VRA viene avviato automaticamente. Quando il processo di provisioning è completo, la nuova VRA sarà visualizzata nella pagina di elenco **Gateway Appliances**. Fai clic sul nome del gateway per aprire la pagina **Gateway Details**, quindi fai clic su ogni membro del gateway per aprire la pagina **Device Details**. Troverai gli indirizzi IP, la password e il nome di accesso del dispositivo.  

**NOTA:** è importante ricordare che, dopo aver ordinato e configurato la tua VRA dal Portale del cliente di IBM Cloud, devi configurare anche il dispositivo stesso con le stesse impostazioni.

## Ruolo dell'applicazione gateway e VLAN
Una VLAN (LAN virtuale) è un meccanismo che suddivide une rete fisica in molti segmenti virtuali. Per comodità, il traffico da più VLAN selezionate può essere consegnato tramite un solo cavo di rete, un processo normalmente chiamato "trunking."

VRA viene consegnata in due parti: i server VLAN e l'apparecchiatura Applicazione gateway. L'applicazione gateway ti fornisce un'interfaccia (GUI e API) per selezionare le VLAN che desideri associare alla tua VRA. L'associazione di una VLAN a un'applicazione gateway reinstrada (o "trunks") tale VLAN e tutte le sue sottoreti alla tua VRA, fornendoti il controllo sul filtro, l'inoltro e la protezione. I server in una VLAN associata possono essere raggiunti solo da altre VLAN passando attraverso la tua VRA; non è possibile aggirare la VRA a meno che non escludi o annulli l'associazione alla VLAN.

Per impostazione predefinita, una nuova applicazione gateway viene associata a due VLAN "transit" non rimovibili, una per ogni VLAN pubblica e privata. Queste vengono normalmente utilizzate per la gestione e possono essere protette separatamente dai comandi VRA.

Le VLAN di transito sono per i dispositivi di rete quali i firewall o i programmi di bilanciamento del carico in modo che possano instradare il traffico mantenendo al tempo stesso gli altri dispositivi, quali i server o i contenitori, isolati da internet.

Le VLAN "gateway", invece, sono dove sono ospitati i dispositivi, quali i server e i contenitori.

La VRA può solo gestire le VLAN associate a esso tramite l'applicazione gateway.

Per informazioni su come gestire le VLAN dalla schermata dei dettagli delle applicazioni gateway, fai riferimento a [Gestione delle VLAN](/docs/infrastructure/virtual-router-appliance?topic=virtual-router-appliance-managing-your-vlans).

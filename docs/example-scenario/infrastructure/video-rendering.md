---
title: Rendering di video 3D in Azure
description: Eseguire carichi di lavoro HPC nativi in Azure con il servizio Azure Batch.
author: adamboeglin
ms.date: 07/13/2018
ms.openlocfilehash: 1206fa7d931fca635118929d433abe232ec5ca9a
ms.sourcegitcommit: b2a4eb132857afa70201e28d662f18458865a48e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2018
ms.locfileid: "48818630"
---
# <a name="3d-video-rendering-on-azure"></a>Rendering di video 3D in Azure

Il rendering video 3D è un processo di lunga durata il cui completamento richiede un'elevata quantità di tempo CPU. In un singolo computer, il processo di generazione di un file video da asset statici può richiedere ore o addirittura giorni, a seconda della lunghezza e della complessità del video che si intende produrre. Per eseguire queste attività, molte aziende acquistano costosi computer desktop di fascia alta oppure investono in farm di rendering di grandi dimensioni a cui possono inviare i processi. Sfruttando Azure Batch, tuttavia, la stessa potenza è disponibile quando necessario e si arresta in caso contrario, senza investimenti di capitale.

Batch offre un'esperienza di gestione e di pianificazione dei processi coerente indipendentemente dal fatto che si selezionino nodi di calcolo Windows Server o Linux. Con Batch, è possibile usare le applicazioni Windows o Linux esistenti, come AutoDesk Maya e Blender, per eseguire processi di rendering su larga scala in Azure.

## <a name="relevant-use-cases"></a>Casi d'uso pertinenti

Prendere in considerazione questo scenario per questi casi d'uso simili:

* Modellazione 3D
* Rendering VFX (Visual FX)
* Transcodifica di video
* Elaborazione, correzione dei colori e ridimensionamento di immagini

## <a name="architecture"></a>Architettura

![Panoramica dell'architettura dei componenti coinvolti in una soluzione HPC nativa cloud con Azure Batch][architecture]

Questo scenario illustra un flusso di lavoro che usa Azure Batch. Il flusso di dati è il seguente:

1. Caricare i file di input e le applicazioni per elaborarli nell'account di archiviazione di Azure.
2. Creare un pool Batch di nodi di calcolo nell'account Batch, un processo per eseguire il carico di lavoro nel pool e le attività nel processo
3. Scaricare i file di input e le applicazioni in Batch.
4. Monitorare l'esecuzione delle attività.
5. Caricare l'output delle attività.
6. Scaricare i file di output.

Per semplificare questo processo, è anche possibile usare i [plug-in di Batch per Maya e 3DS Max][batch-plugins]

### <a name="components"></a>Componenti

Azure Batch si basa sulle tecnologie Azure seguenti:

* Le [reti virtuali](/azure/virtual-network/virtual-networks-overview) vengono usate sia per il nodo head che per le risorse di calcolo.
* Gli [account di archiviazione di Azure](/azure/storage/common/storage-introduction) vengono usati per la sincronizzazione e la conservazione dei dati.
* I [set di scalabilità di macchine virtuali][vmss] vengono usati da CycleCloud per le risorse di calcolo.

## <a name="considerations"></a>Considerazioni

### <a name="machine-sizes-available-for-azure-batch"></a>Dimensioni di macchine virtuali disponibili per Azure Batch

Nonostante la maggior parte dei clienti di soluzioni di rendering scelga risorse con un'elevata potenza di CPU, la scelta delle macchine virtuali per altri carichi di lavoro che usano set di scalabilità di macchine virtuali potrebbe essere effettuata in modo diverso e dipendere dai fattori seguenti:

* L'esecuzione dell'applicazione è associata alla memoria?
* L'applicazione deve usare GPU? 
* I tipi di processi presentano elevati livelli di parallelismo o richiedono connettività InfiniBand per processi strettamente associati?
* È necessaria un'elevata velocità di I/O per accedere alle risorse di archiviazione nei nodi di calcolo.

Azure offre numerose dimensioni di macchine virtuali che possono soddisfare ognuno dei requisiti delle applicazioni indicati sopra. Alcune sono specifiche per HPC, ma anche le dimensioni più ridotte possono essere usate per ottenere un'efficace implementazione a griglia:

* [Dimensioni di macchine virtuali HPC][compute-hpc]: dato che il rendering è associato alla CPU, è consigliabile usare macchine virtuali di Azure serie H. Queste macchine virtuali sono specificamente realizzate per esigenze di calcolo di fascia alta, sono disponibili in dimensioni da 8 a 16 vCPU core e sono dotate di memoria DDR4, archiviazione temporanea in unità SSD e tecnologia Haswell E5 Intel.
* [Dimensioni di VM GPU][compute-gpu]: le dimensioni di VM ottimizzate per la GPU sono macchine virtuali specializzate disponibili con una o più GPU NVIDIA. Queste dimensioni sono progettate per carichi di lavoro di visualizzazione oppure a elevato utilizzo di calcolo o di grafica.
* Le dimensioni NC, NCv2, NCv3 e ND sono ottimizzate per applicazioni e algoritmi a elevato utilizzo di calcolo e reti, come applicazioni e simulazioni basate su OpenCL e CUDA, intelligenza artificiale e Deep Learning. Le dimensioni NV sono ottimizzate e progettate per scenari di visualizzazione remota, streaming, giochi, codifica e VDI che utilizzano framework come OpenGL e DirectX.
* [Dimensioni di macchine virtuali ottimizzate per la memoria][compute-memory]: quando è necessaria una maggiore quantità di memoria, queste dimensioni di macchine virtuali offrono un rapporto memoria-CPU superiore.
* [Dimensioni di macchine virtuali per utilizzo generico][compute-general]: sono disponibili anche dimensioni di macchine virtuali per utilizzo generico che offrono un rapporto CPU-memoria equilibrato.

### <a name="alternatives"></a>Alternative

Se si necessita di maggiore controllo sull'ambiente di rendering in Azure o di un'implementazione ibrida, l'elaborazione con CycleCloud consente di orchestrare una griglia IaaS nel cloud e, usando le stesse tecnologie Azure sottostanti di Azure Batch, rende efficiente il processo di creazione e gestione di una griglia IaaS. Per altre informazioni sui principi di progettazione, usare il collegamento seguente:

Per una panoramica completa di tutte le soluzioni HPC disponibili in Azure, vedere l'articolo [Soluzioni HPC, batch e Big Compute con macchine virtuali di Azure][hpc-alt-solutions]

### <a name="availability"></a>Disponibilità

Funzionalità di monitoraggio dei componenti di Azure Batch sono disponibili tramite diversi servizi, strumenti e API. Il monitoraggio è illustrato in maggiore dettaglio nell'articolo [Monitorare le soluzioni Batch][batch-monitor].

### <a name="scalability"></a>Scalabilità

I pool in un account Azure Batch possono essere ridimensionati manualmente oppure automaticamente con una formula basata sulle metriche di Azure Batch. Per altre informazioni in proposito, vedere l'articolo [Creare una formula di scalabilità automatica per la scalabilità dei nodi di calcolo in un pool Batch][batch-scaling].

### <a name="security"></a>Sicurezza

Per indicazioni generali sulla progettazione di soluzioni sicure, vedere la [documentazione sulla sicurezza di Azure][security].

### <a name="resiliency"></a>Resilienza

Attualmente non sono presenti funzionalità di failover in Azure Batch, ma è consigliabile usare questa procedura per garantire la disponibilità in caso di interruzione non pianificata:

* Creare un account Azure Batch in un percorso di Azure alternativo con un account di archiviazione alternativo
* Creare gli stessi pool di nodi con lo stesso nome, con 0 nodi allocati
* Assicurarsi che le applicazioni vengano create e aggiornate nell'account di archiviazione alternativo
* Caricare i file di input e inviare i processi all'account Azure Batch alternativo

## <a name="deploy-this-scenario"></a>Distribuire lo scenario

### <a name="creating-an-azure-batch-account-and-pools-manually"></a>Creazione manuale di un account e dei pool Azure Batch

Questo scenario illustra il funzionamento di Azure Batch e presenta Azure Batch Labs come soluzione SaaS di esempio che è possibile sviluppare per i clienti:

[Azure Batch Masterclass][batch-labs-masterclass]

### <a name="deploying-the-example-scenario-using-an-azure-resource-manager-template"></a>Distribuzione dello scenario di esempio con un modello di Azure Resource Manager

Il modello distribuirà quanto segue:

* Un nuovo account Azure Batch
* Un account di archiviazione
* Un pool di nodi associato all'account Batch
* Il pool di nodi verrà configurato per l'uso di VM A2 v2 con immagini di Canonical Ubuntu
* Il pool di nodi conterrà inizialmente 0 macchine virtuali e richiederà il ridimensionamento manuale per l'aggiunta di macchine virtuali

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fhpc%2Fbatchcreatewithpools.json" target="_blank">
    <img src="https://azuredeploy.net/deploybutton.png"/>
</a>

[Altre informazioni sui modelli di Resource Manager][azure-arm-templates]

## <a name="pricing"></a>Prezzi

Il costo correlato all'uso di Azure Batch dipenderà dalle dimensioni delle macchine virtuali usate per i pool e dalla durata dell'allocazione e dell'esecuzione. Alla creazione di un account Azure Batch non è associato alcun costo. È necessario tenere in considerazione le risorse di archiviazione e i dati in uscita, perché comportano l'applicazione di costi aggiuntivi.

Di seguito sono riportati esempi dei costi che potrebbero essere addebitati per un processo completato in 8 ore con un diverso numero di server.

* 100 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-high]

  100 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita

* 50 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-med]

  50 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita

* 10 macchine virtuali con CPU ad alte prestazioni: [stima dei costi][hpc-est-low]

  10 x H16m (16 core, 225 GB di RAM, 512 GB di archiviazione Premium), 2 TB di archiviazione BLOB, 1 TB in uscita

### <a name="pricing-for-low-priority-vms"></a>Prezzi per le macchine virtuali con priorità bassa

Azure Batch supporta anche l'uso di macchine virtuali con priorità bassa nei pool di nodi e questo può offrire un significativo risparmio sui costi. Per altre informazioni, incluso un confronto dei prezzi tra macchine virtuali standard e macchine virtuali con priorità bassa, vedere [Prezzi di Azure Batch][batch-pricing].

> [!NOTE] 
> Le macchine virtuali con priorità bassa sono adatte solo per alcune applicazioni e carichi di lavoro.

## <a name="related-resources"></a>Risorse correlate

[Panoramica di Azure Batch][batch-overview]

[Documentazione di Azure Batch][batch-doc]

[Uso di contenitori in Azure Batch][batch-containers]

<!-- links -->
[architecture]: ./media/architecture-video-rendering.png
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[resiliency]: /azure/architecture/resiliency/
[scalability]: /azure/architecture/checklist/scalability
[vmss]: /azure/virtual-machine-scale-sets/overview
[storage]: https://azure.microsoft.com/services/storage/
[batch]: https://azure.microsoft.com/services/batch/
[batch-arch]: https://azure.microsoft.com/solutions/architecture/big-compute-with-azure-batch/
[compute-hpc]: /azure/virtual-machines/windows/sizes-hpc
[compute-gpu]: /azure/virtual-machines/windows/sizes-gpu
[compute-compute]: /azure/virtual-machines/windows/sizes-compute
[compute-memory]: /azure/virtual-machines/windows/sizes-memory
[compute-general]: /azure/virtual-machines/windows/sizes-general
[compute-storage]: /azure/virtual-machines/windows/sizes-storage
[compute-acu]: /azure/virtual-machines/windows/acu
[compute=benchmark]: /azure/virtual-machines/windows/compute-benchmark-scores
[hpc-est-high]: https://azure.com/e/9ac25baf44ef49c3a6b156935ee9544c
[hpc-est-med]: https://azure.com/e/0286f1d6f6784310af4dcda5aec8c893
[hpc-est-low]: https://azure.com/e/e39afab4e71949f9bbabed99b428ba4a
[batch-labs-masterclass]: https://github.com/azurebigcompute/BigComputeLabs/tree/master/Azure%20Batch%20Masterclass%20Labs
[batch-scaling]: /azure/batch/batch-automatic-scaling
[hpc-alt-solutions]: /azure/virtual-machines/linux/high-performance-computing?toc=%2fazure%2fbatch%2ftoc.json
[batch-monitor]: /azure/batch/monitoring-overview
[batch-pricing]: https://azure.microsoft.com/pricing/details/batch/
[batch-doc]: /azure/batch/
[batch-overview]: https://azure.microsoft.com/services/batch/
[batch-containers]: https://github.com/Azure/batch-shipyard
[azure-arm-templates]: /azure/azure-resource-manager/resource-group-overview#template-deployment
[batch-plugins]: /azure/batch/batch-rendering-service#options-for-submitting-a-render-job
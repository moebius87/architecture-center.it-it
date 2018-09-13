---
title: "Adozione del cloud nell'organizzazione: definizione di governance delle risorse cloud"
description: Spiegazione del concetto di governance dell'accesso alle risorse in Azure
author: petertaylor9999
ms.date: 09/10/2018
ms.openlocfilehash: 14c26cbccdbea524a7c7220b3bb98ed2a118c30d
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389214"
---
# <a name="enterprise-cloud-adoption-what-is-cloud-resource-governance"></a><span data-ttu-id="b6170-103">Adozione del cloud nell'organizzazione: definizione di governance delle risorse cloud</span><span class="sxs-lookup"><span data-stu-id="b6170-103">Enterprise Cloud Adoption: What is cloud resource governance?</span></span>

<span data-ttu-id="b6170-104">Nell'articolo [Funzionamento di Azure](what-is-azure.md) si è appreso che Azure è un insieme di server e componenti hardware di rete che eseguono software e hardware virtualizzati per conto degli utenti.</span><span class="sxs-lookup"><span data-stu-id="b6170-104">In [how does Azure work?](what-is-azure.md), you learned that Azure is a collection of servers and networking hardware running virtualized hardware and software on behalf of users.</span></span> <span data-ttu-id="b6170-105">Azure consente ai reparti che si occupano di IT e sviluppo dell'organizzazione di lavorare in modo flessibile, creando, leggendo, aggiornando ed eliminando facilmente le risorse in base alle esigenze.</span><span class="sxs-lookup"><span data-stu-id="b6170-105">Azure enables your organization's development and IT departments to be agile by making it easy to create, read, update, and delete resources as needed.</span></span>

<span data-ttu-id="b6170-106">Tuttavia, sebbene offrire agli sviluppatori un accesso illimitato alle risorse garantisca loro ampia flessibilità, ciò può anche comportare costi imprevisti.</span><span class="sxs-lookup"><span data-stu-id="b6170-106">However, while giving unrestricted resource access to developers can make them very agile, it can also lead to unintended cost consequences.</span></span> <span data-ttu-id="b6170-107">Un team di sviluppo, ad esempio, potrebbe avere l'approvazione per distribuire un set di risorse per i test, ma potrebbe dimenticarsi di eliminare tali risorse al termine della fase di test.</span><span class="sxs-lookup"><span data-stu-id="b6170-107">For example, a development team might be approved to deploy a set of resources for testing but forget to delete them when testing is complete.</span></span> <span data-ttu-id="b6170-108">Queste risorse continueranno ad accumulare costi anche se il loro uso non è più approvato o necessario.</span><span class="sxs-lookup"><span data-stu-id="b6170-108">These resources will continue to accrue costs even though their use is no longer approved or necessary.</span></span> 

<span data-ttu-id="b6170-109">La soluzione a questo problema è la **governance** dell'accesso alle risorse.</span><span class="sxs-lookup"><span data-stu-id="b6170-109">The solution to this problem is resource access **governance**.</span></span> <span data-ttu-id="b6170-110">Per governance si intende il processo continuativo di gestione, monitoraggio e controllo dell'uso delle risorse di Azure per soddisfare gli obiettivi e i requisiti dell'organizzazione.</span><span class="sxs-lookup"><span data-stu-id="b6170-110">Governance refers to the ongoing process of managing, monitoring, and auditing the use of Azure resources to meet the goals and requirements of your organization.</span></span> 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2ii94] 

<span data-ttu-id="b6170-111">Questi obiettivi e requisiti sono univoci per ogni organizzazione, quindi non è possibile definire un approccio alla governance appropriato per tutti.</span><span class="sxs-lookup"><span data-stu-id="b6170-111">These goals and requirements are unique to each organization so it's not possible to have a one-size-fits-all approach to governance.</span></span> <span data-ttu-id="b6170-112">Azure implementa due strumenti di governance principali, il **controllo degli accessi in base al ruolo** e i **criteri delle risorse**, che ogni organizzazione può usare per progettare un modello di governance personalizzato.</span><span class="sxs-lookup"><span data-stu-id="b6170-112">Rather, Azure implements two primary governance tools, **resource based access control (RBAC)**, and **resource policy**, and it's up to each organization to design their governance model using them.</span></span>

<span data-ttu-id="b6170-113">Il controllo degli accessi in base al ruolo definisce i ruoli e i ruoli definiscono le funzionalità per un utente a cui viene assegnato il ruolo.</span><span class="sxs-lookup"><span data-stu-id="b6170-113">RBAC defines roles, and roles define the capabilities for a user that is assigned the role.</span></span> <span data-ttu-id="b6170-114">Il ruolo **proprietario**, ad esempio, consente tutte le funzionalità (creazione, lettura, aggiornamento ed eliminazione) per una risorsa, mentre il ruolo **lettore** consente solo la capacità di lettura.</span><span class="sxs-lookup"><span data-stu-id="b6170-114">For example, the **owner** role enables all capabilites (create, read, update, and delete) for a resource, while the  **reader** roles enables only the read capability.</span></span> <span data-ttu-id="b6170-115">I ruoli possono essere definiti con un ambito ampio che si applica a molti tipi di risorse oppure un ambito ristretto che si applica a pochi tipi.</span><span class="sxs-lookup"><span data-stu-id="b6170-115">Roles can be defined with a broad scope that applies to many resources types, or a narrow scope that applies to a few.</span></span> 

<span data-ttu-id="b6170-116">I criteri delle risorse definiscono le regole per la creazione delle risorse.</span><span class="sxs-lookup"><span data-stu-id="b6170-116">Resource policies define rules for resource creation.</span></span> <span data-ttu-id="b6170-117">Un criterio delle risorse può ad esempio limitare lo SKU di una macchina virtuale a una dimensione specifica pre-approvata.</span><span class="sxs-lookup"><span data-stu-id="b6170-117">For example, a resource policy can limit the SKU of a VM to a particular pre-appproved size.</span></span> <span data-ttu-id="b6170-118">Un altro criterio delle risorse può imporre l'aggiunta di un tag con un centro di costo quando viene effettuata la richiesta di creazione della risorsa.</span><span class="sxs-lookup"><span data-stu-id="b6170-118">Or, a resource policy can enforce the addition of a tag with a cost center when the request is made to create the resource.</span></span> 

<span data-ttu-id="b6170-119">Quando si configurano questi strumenti, è importante trovare il giusto equilibrio tra governance e flessibilità organizzativa.</span><span class="sxs-lookup"><span data-stu-id="b6170-119">When configuring these tools, an important consideration is balancing governance versus organizational agility.</span></span> <span data-ttu-id="b6170-120">Più sono restrittivi i criteri di governance, meno flessibilità avranno gli sviluppatori e i professionisti IT.</span><span class="sxs-lookup"><span data-stu-id="b6170-120">That is, the more restrictive your governance policy, the less agile your developers and IT workers become.</span></span> <span data-ttu-id="b6170-121">Criteri di governance restrittivi potrebbero infatti richiedere un maggior numero di passaggi manuali, ad esempio la compilazione di un modulo o l'invio di un messaggio di posta elettronica da uno sviluppatore a un membro del team di governance per la creazione manuale di una risorsa.</span><span class="sxs-lookup"><span data-stu-id="b6170-121">This is because a restrictive goverance policy may require more manual steps, such as requiring a developer to fill out a form or send an email to a person on the governance team to manually create a resource.</span></span> <span data-ttu-id="b6170-122">Il team di governance ha capacità limitate e potrebbe accumulare lavoro arretrato, con una conseguente diminuzione della produttività dei team di sviluppo che devono attendere la creazione delle risorse e con il rischio di accumulare costi per risorse non necessarie in attesa di essere eliminate.</span><span class="sxs-lookup"><span data-stu-id="b6170-122">The goverance team has finite capabilities and may become backlogged, resulting in unproductive development teams waiting for their resources to be created and unneeded resources accruing costs while they wait to be deleted.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b6170-123">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="b6170-123">Next steps</span></span>

<span data-ttu-id="b6170-124">Ora che si è appreso il concetto di governance delle risorse cloud, si approfondirà [come viene gestito l'accesso alle risorse](azure-resource-access.md) in Azure, quindi come progettare un modello di governance per un [solo team](../governance/governance-single-team.md) o per [più team](../governance/governance-multiple-teams.md).</span><span class="sxs-lookup"><span data-stu-id="b6170-124">Now that you understand the concept of cloud resource goverance, move on to learn more about [how resource access is managed](azure-resource-access.md) in Azure in preparation for learning how to design a governance model for a [single team](../governance/governance-single-team.md) or [multiple teams](../governance/governance-multiple-teams.md).</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="b6170-125">Informazioni sull'accesso alle risorse in Azure</span><span class="sxs-lookup"><span data-stu-id="b6170-125">Learn about resource access in Azure</span></span>](azure-resource-access.md)
---
title: "Adozione del cloud nell'organizzazione: progettazione della governance per un carico di lavoro semplice"
description: Guida alla configurazione dei controlli della governance di Azure per consentire all'utente di distribuire un carico di lavoro semplice
author: petertaylor9999
ms.date: 09/10/2018
ms.openlocfilehash: 13c7c4b41df14151d28b9c685f01019af3ec63f2
ms.sourcegitcommit: c49aeef818d7dfe271bc4128b230cfc676f05230
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2018
ms.locfileid: "44389248"
---
# <a name="enterprise-cloud-adoption-governance-design-for-a-simple-workload"></a><span data-ttu-id="2d764-103">Adozione del cloud nell'organizzazione: progettazione della governance per un carico di lavoro semplice</span><span class="sxs-lookup"><span data-stu-id="2d764-103">Enterprise Cloud Adoption: Governance design for a simple workload</span></span>

<span data-ttu-id="2d764-104">L'obiettivo di questa guida è illustrare il processo di progettazione di un modello di governance delle risorse in Azure per supportare un solo team e un carico di lavoro semplice.</span><span class="sxs-lookup"><span data-stu-id="2d764-104">The goal of this guidance is to help you learn the process for designing a resource governance model in Azure to support a single team and a simple workload.</span></span>  <span data-ttu-id="2d764-105">Verranno esaminati alcuni ipotetici requisiti di governance, quindi verranno illustrate diverse implementazioni di esempio che soddisfano tali requisiti.</span><span class="sxs-lookup"><span data-stu-id="2d764-105">We'll look at a set of hypothetical governance requirements, then go through several example implementations that satisfy those requirements.</span></span> 

<span data-ttu-id="2d764-106">Nella fase di adozione iniziale, l'obiettivo è distribuire un carico di lavoro semplice in Azure.</span><span class="sxs-lookup"><span data-stu-id="2d764-106">In the foundational adoption stage, our goal is to deploy a simple workload to Azure.</span></span> <span data-ttu-id="2d764-107">Ne derivano i requisiti seguenti:</span><span class="sxs-lookup"><span data-stu-id="2d764-107">This results in the following requirements:</span></span>
* <span data-ttu-id="2d764-108">Gestione delle identità per un singolo **proprietario del carico di lavoro** responsabile della distribuzione e della gestione del carico di lavoro semplice.</span><span class="sxs-lookup"><span data-stu-id="2d764-108">Identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="2d764-109">Il proprietario del carico di lavoro necessita di autorizzazioni per creare, leggere, aggiornare ed eliminare risorse, nonché dell'autorizzazione a delegare questi diritti ad altri utenti nel sistema di gestione delle identità.</span><span class="sxs-lookup"><span data-stu-id="2d764-109">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>
* <span data-ttu-id="2d764-110">Gestire tutte le risorse per il carico di lavoro semplice come un'unica unità di gestione.</span><span class="sxs-lookup"><span data-stu-id="2d764-110">Manage all resources for the simple workload as a single management unit.</span></span>

## <a name="licensing-azure"></a><span data-ttu-id="2d764-111">Licenze di Azure</span><span class="sxs-lookup"><span data-stu-id="2d764-111">Licensing Azure</span></span>

<span data-ttu-id="2d764-112">Prima di iniziare a progettare il modello di governance, è importante capire come avviene la concessione in licenza di Azure,</span><span class="sxs-lookup"><span data-stu-id="2d764-112">Before we begin designing our governance model, it's important to understand how Azure is licensed.</span></span> <span data-ttu-id="2d764-113">perché gli account amministrativi associati alla licenza di Azure hanno il massimo livello di accesso a tutte le risorse di Azure.</span><span class="sxs-lookup"><span data-stu-id="2d764-113">This is because the administrative accounts associated with your Azure license have the highest level of access to all of your Azure resources.</span></span> <span data-ttu-id="2d764-114">Questi account amministrativi costituiscono la base del modello di governance.</span><span class="sxs-lookup"><span data-stu-id="2d764-114">These administrative accounts form the basis of your governance model.</span></span>  

> [!NOTE]
> <span data-ttu-id="2d764-115">Se l'organizzazione ha già un [Contratto Enterprise Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) che non include Azure, è possibile aggiungere Azure assumendo un impegno monetario iniziale.</span><span class="sxs-lookup"><span data-stu-id="2d764-115">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="2d764-116">Per altre informazioni, vedere [Licenze di Azure per l'azienda](https://azure.microsoft.com/pricing/enterprise-agreement/).</span><span class="sxs-lookup"><span data-stu-id="2d764-116">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span> 

<span data-ttu-id="2d764-117">Quando Azure è stato aggiunto al Contratto Enterprise dell'organizzazione, a questa è stato chiesto di creare un **account Azure**.</span><span class="sxs-lookup"><span data-stu-id="2d764-117">When Azure was added to your organization's Enterprise Agreement, your organization was prompted to create an **Azure account**.</span></span> <span data-ttu-id="2d764-118">Durante il processo di creazione dell'account, sono stati creati un **proprietario dell'account Azure** e un tenant di Azure Active Directory (Azure AD) con un account di **amministratore globale**.</span><span class="sxs-lookup"><span data-stu-id="2d764-118">During the account creation process, an **Azure account owner** was created, as well as an Azure Active Directory (Azure AD) tenant with a **global administrator** account.</span></span> <span data-ttu-id="2d764-119">Un tenant di Azure AD è un costrutto logico che rappresenta un'istanza sicura e dedicata di Azure AD.</span><span class="sxs-lookup"><span data-stu-id="2d764-119">An Azure AD tenant is a logical construct that represents a secure, dedicated instance of Azure AD.</span></span>

<span data-ttu-id="2d764-120">![Account Azure con proprietario dell'account Azure e amministratore globale di Azure AD](../_images/governance-3-0.png)
*Figura 1. Un account Azure con un proprietario dell'account e un amministratore globale di Azure AD.*</span><span class="sxs-lookup"><span data-stu-id="2d764-120">![Azure account with Azure Account Manager and Azure AD global administrator](../_images/governance-3-0.png)
*Figure 1. An Azure account with an Account Manager and Azure AD Global Administrator.*</span></span>

## <a name="identity-management"></a><span data-ttu-id="2d764-121">Gestione delle identità</span><span class="sxs-lookup"><span data-stu-id="2d764-121">Identity management</span></span>

<span data-ttu-id="2d764-122">Azure considera attendibile solo [Azure AD](/azure/active-directory) per autenticare gli utenti e autorizzare l'accesso degli utenti alle risorse, quindi Azure AD è il sistema di gestione delle identità.</span><span class="sxs-lookup"><span data-stu-id="2d764-122">Azure only trusts [Azure AD](/azure/active-directory) to authenticate users and authorize user access to resources, so Azure AD is our identity management system.</span></span> <span data-ttu-id="2d764-123">L'amministratore globale di Azure AD ha il livello massimo di autorizzazioni e può eseguire tutte le azioni relative all'identità, incluse la creazione di utenti e l'assegnazione di autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2d764-123">The Azure AD global administrator has the highest level of permissions and can perform all actions related to identity, including creating users and assigning permissions.</span></span> 

<span data-ttu-id="2d764-124">Il requisito è la gestione delle identità per un singolo **proprietario del carico di lavoro** responsabile della distribuzione e della gestione del carico di lavoro semplice.</span><span class="sxs-lookup"><span data-stu-id="2d764-124">Our requirement is identity management for a single **workload owner** who is responsible for deploying and maintaining the simple workload.</span></span> <span data-ttu-id="2d764-125">Il proprietario del carico di lavoro necessita di autorizzazioni per creare, leggere, aggiornare ed eliminare risorse, nonché dell'autorizzazione a delegare questi diritti ad altri utenti nel sistema di gestione delle identità.</span><span class="sxs-lookup"><span data-stu-id="2d764-125">The workload owner requires permission to create, read, update, and delete resources as well as permission to delegate these rights to other users in the identity management system.</span></span>

<span data-ttu-id="2d764-126">L'amministratore globale di Azure AD creerà l'account del **proprietario del carico di lavoro** per il **proprietario del carico di lavoro**:</span><span class="sxs-lookup"><span data-stu-id="2d764-126">Our Azure AD global administrator will create the **workload owner** account for the **workload owner**:</span></span>

<span data-ttu-id="2d764-127">![L'amministratore globale di Azure AD crea l'account del proprietario del carico di lavoro](../_images/governance-1-2.png)
*Figura 2. L'amministratore globale di Azure AD crea l'account utente del proprietario del carico di lavoro.*</span><span class="sxs-lookup"><span data-stu-id="2d764-127">![The Azure AD global administrator creates the workload owner account](../_images/governance-1-2.png)
*Figure 2. The Azure AD global administrator creates the workload owner user account.*</span></span>

<span data-ttu-id="2d764-128">Non è possibile assegnare autorizzazioni di accesso alle risorse fino a quando questo utente non viene aggiunto a una **sottoscrizione**, quindi questa operazione verrà eseguita nelle prossime due sezioni.</span><span class="sxs-lookup"><span data-stu-id="2d764-128">We aren't able to assign resource access permission until this user is added to a **subscription**, so we'll do that in the next two sections.</span></span> 

## <a name="resource-management-scope"></a><span data-ttu-id="2d764-129">Ambito di gestione delle risorse</span><span class="sxs-lookup"><span data-stu-id="2d764-129">Resource management scope</span></span>

<span data-ttu-id="2d764-130">Con l'aumento del numero di risorse distribuite dall'organizzazione, cresce anche la complessità della governance di tali risorse.</span><span class="sxs-lookup"><span data-stu-id="2d764-130">As the number of resources deployed by your organization grows, the complexity of governing those resources grows as well.</span></span> <span data-ttu-id="2d764-131">Azure implementa una gerarchia di contenitori logica per consentire all'organizzazione di gestire le risorse in gruppi a vari livelli di granularità, noti anche come **ambito**.</span><span class="sxs-lookup"><span data-stu-id="2d764-131">Azure implements a logical container hierarchy to enable your organization to manage your resources in groups at various levels of granularity, also known as **scope**.</span></span> 

<span data-ttu-id="2d764-132">Il livello massimo dell'ambito di gestione delle risorse è il livello **sottoscrizione**.</span><span class="sxs-lookup"><span data-stu-id="2d764-132">The top level of resource management scope is the **subscription** level.</span></span> <span data-ttu-id="2d764-133">La sottoscrizione viene creata dal **proprietario dell'account** Azure, che stabilisce l'impegno finanziario ed è responsabile del pagamento di tutte le risorse di Azure associate alla sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="2d764-133">A subscription is created by the Azure **account owner**, who establishes the financial commitment and is responsible for paying for all Azure resources associated with the subscription:</span></span>

<span data-ttu-id="2d764-134">![Il proprietario di un account Azure crea una sottoscrizione](../_images/governance-1-3.png)
*Figura 3. Il proprietario dell'account Azure crea una sottoscrizione.*</span><span class="sxs-lookup"><span data-stu-id="2d764-134">![The Azure account owner creates a subscription](../_images/governance-1-3.png)
*Figure 3. The Azure account owner creates a subscription.*</span></span>

<span data-ttu-id="2d764-135">Quando viene creata la sottoscrizione, il **proprietario dell'account Azure** associa alla sottoscrizione un tenant di Azure AD che viene usato per l'autenticazione e l'autorizzazione degli utenti:</span><span class="sxs-lookup"><span data-stu-id="2d764-135">When the subscription is created, the Azure **account owner** associates an Azure AD tenant with the subscription, and this Azure AD tenant is used for authenticating and authorizing users:</span></span>

<span data-ttu-id="2d764-136">![Il proprietario dell'account Azure associa il tenant di Azure AD alla sottoscrizione](../_images/governance-1-4.png)
*Figura 4. Il proprietario dell'account Azure associa il tenant di Azure AD alla sottoscrizione.*</span><span class="sxs-lookup"><span data-stu-id="2d764-136">![The Azure account owner associates the Azure AD tenant with the subscription](../_images/governance-1-4.png)
*Figure 4. The Azure account owner associates the Azure AD tenant with the subscription.*</span></span>

<span data-ttu-id="2d764-137">Si sarà notato che attualmente non c'è alcun utente associato alla sottoscrizione; ciò significa che nessuno è autorizzato a gestire le risorse.</span><span class="sxs-lookup"><span data-stu-id="2d764-137">You may have noticed that there is currently no user associated with the subscription, which means that no one has permission to manage resources.</span></span> <span data-ttu-id="2d764-138">In realtà, il **proprietario dell'account** è il proprietario della sottoscrizione ed è autorizzato a eseguire qualsiasi azione su una risorsa della sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2d764-138">In reality, the **account owner** is the owner of the subscription and has permission to take any action on a resource in the subscription.</span></span> <span data-ttu-id="2d764-139">In termini pratici, il **proprietario dell'account** sarà tuttavia molto probabilmente un responsabile della divisione finanziaria dell'organizzazione e non si occuperà delle operazioni di creazione, lettura, aggiornamento ed eliminazione delle risorse; tali attività saranno svolte dal **proprietario del carico di lavoro**.</span><span class="sxs-lookup"><span data-stu-id="2d764-139">However, in practical terms the **account owner** is more than likely a finance person in your organization and is not responsible for creating, reading, updating, and deleting resources - those tasks will be performed by the **workload owner**.</span></span> <span data-ttu-id="2d764-140">È quindi necessario aggiungere il **proprietario del carico di lavoro** alla sottoscrizione e assegnare le autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2d764-140">Therefore, we need to add the **workload owner** to the subscription and assign permissions.</span></span>

<span data-ttu-id="2d764-141">Essendo l'unico utente attualmente autorizzato ad aggiungere il **proprietario del carico di lavoro** alla sottoscrizione, il **proprietario dell'account** aggiungerà il **proprietario del carico di lavoro** alla sottoscrizione:</span><span class="sxs-lookup"><span data-stu-id="2d764-141">Since the **account owner** is currently the only user with permission to add the **workload owner** to the subscription, they add the **workload owner** to the subscription:</span></span>

<span data-ttu-id="2d764-142">![Il proprietario dell'account Azure aggiunge il **proprietario del carico di lavoro** alla sottoscrizione](../_images/governance-1-5.png)
*Figura 5. Il proprietario dell'account Azure aggiunge il proprietario del carico di lavoro alla sottoscrizione.*</span><span class="sxs-lookup"><span data-stu-id="2d764-142">![The Azure account owner adds the **workload owner** to the subscription](../_images/governance-1-5.png)
*Figure 5. The Azure account owner adds the workload owner to the subscription.*</span></span>

<span data-ttu-id="2d764-143">Il **proprietario dell'account** Azure concede le autorizzazioni al **proprietario del carico di lavoro** assegnando un [ruolo di controllo degli accessi in base al ruolo](/azure/role-based-access-control/).</span><span class="sxs-lookup"><span data-stu-id="2d764-143">The Azure **account owner** grants permissions to the **workload owner** by assigning a [role-based access control (RBAC)](/azure/role-based-access-control/) role.</span></span> <span data-ttu-id="2d764-144">Il ruolo di controllo degli accessi in base al ruolo specifica un insieme di autorizzazioni assegnate al **proprietario del carico di lavoro** per un singolo tipo di risorsa o un insieme di tipi di risorsa.</span><span class="sxs-lookup"><span data-stu-id="2d764-144">The RBAC role specifies a set of permissions that the **workload owner** has for an individual resource type or a set of resource types.</span></span>

<span data-ttu-id="2d764-145">Si noti che in questo esempio il **proprietario dell'account** ha assegnato il ruolo di [proprietario **predefinito**](/azure/role-based-access-control/built-in-roles#owner):</span><span class="sxs-lookup"><span data-stu-id="2d764-145">Notice that in this example, the **account owner** has assigned the [built-in **owner** role](/azure/role-based-access-control/built-in-roles#owner):</span></span> 

<span data-ttu-id="2d764-146">![Al **proprietario del carico di lavoro** è stato assegnato il ruolo di proprietario predefinito](../_images/governance-1-6.png)
*Figura 6. Al proprietario del carico di lavoro è stato assegnato il ruolo di proprietario predefinito.*</span><span class="sxs-lookup"><span data-stu-id="2d764-146">![The **workload owner** was assigned the built-in owner role](../_images/governance-1-6.png)
*Figure 6. The workload owner was assigned the built-in owner role.*</span></span>

<span data-ttu-id="2d764-147">Il ruolo di **proprietario** predefinito concede tutte le autorizzazioni al **proprietario del carico di lavoro** nell'ambito della sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2d764-147">The built-in **owner** role grants all permissions to the **workload owner** at the subscription scope.</span></span> 

> [!IMPORTANT]
> <span data-ttu-id="2d764-148">Il **proprietario dell'account** Azure è responsabile dell'impegno finanziario associato alla sottoscrizione, ma il **proprietario del carico di lavoro** ha le stesse autorizzazioni.</span><span class="sxs-lookup"><span data-stu-id="2d764-148">The Azure **acount owner** is responsible for the financial committment associated with the subscription, but the **workload owner** has the same permissions.</span></span> <span data-ttu-id="2d764-149">Il **proprietario dell'account** deve considerare il **proprietario del carico di lavoro** affidabile per la distribuzione delle risorse che rientrano nel budget della sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2d764-149">The **account owner** must trust the **workload owner** to deploy resources that are within the subscription budget.</span></span>

<span data-ttu-id="2d764-150">Il livello successivo dell'ambito di gestione è il livello **risorsa di gruppo**.</span><span class="sxs-lookup"><span data-stu-id="2d764-150">The next level of management scope is the **resource group** level.</span></span> <span data-ttu-id="2d764-151">Un gruppo di risorse è un contenitore logico per le risorse.</span><span class="sxs-lookup"><span data-stu-id="2d764-151">A resource group is a logical container for resources.</span></span> <span data-ttu-id="2d764-152">Le operazioni a livello di gruppo di risorse si applicano a tutte le risorse di un gruppo.</span><span class="sxs-lookup"><span data-stu-id="2d764-152">Operations applied at the resource group level apply to all resources in a group.</span></span> <span data-ttu-id="2d764-153">È anche importante notare che le autorizzazioni per ogni utente vengono ereditate dal livello superiore successivo, a meno che non vengano esplicitamente modificate in quell'ambito.</span><span class="sxs-lookup"><span data-stu-id="2d764-153">Also, it's important to note that permissions for each user are inherited from the next level up unless they are explicitly changed at that scope.</span></span> 

<span data-ttu-id="2d764-154">Per spiegare questo concetto, di seguito è illustrato cosa accade quando il **proprietario del carico di lavoro** crea un gruppo di risorse:</span><span class="sxs-lookup"><span data-stu-id="2d764-154">To illustrate this, let's look at what happens when the **workload owner** creates a resource group:</span></span>

<span data-ttu-id="2d764-155">![Il **proprietario del carico di lavoro** crea un gruppo di risorse](../_images/governance-1-7.png)
*Figura 7. Il proprietario del carico di lavoro crea un gruppo di risorse ed eredita il ruolo di proprietario predefinito nell'ambito del gruppo di risorse.*</span><span class="sxs-lookup"><span data-stu-id="2d764-155">![The **workload owner** creates a resource group](../_images/governance-1-7.png)
*Figure 7. The workload owner creates a resource group and inherits the built-in owner role at the resource group scope.*</span></span>

<span data-ttu-id="2d764-156">Il ruolo di **proprietario** predefinito concede tutte le autorizzazioni al **proprietario del carico di lavoro** nell'ambito del gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="2d764-156">Again, the built-in **owner** role grants all permissions to the **workload owner** at the resource group scope.</span></span> <span data-ttu-id="2d764-157">Come accennato in precedenza, questo ruolo viene ereditato dal livello di sottoscrizione.</span><span class="sxs-lookup"><span data-stu-id="2d764-157">As we discussed earlier, this role is inherited from the subscription level.</span></span> <span data-ttu-id="2d764-158">Se a questo utente è assegnato un ruolo diverso in questo ambito, questo ruolo sarà applicabile solo a questo ambito.</span><span class="sxs-lookup"><span data-stu-id="2d764-158">If a different role is assigned to this user at this scope, it applies to this scope only.</span></span>

<span data-ttu-id="2d764-159">Il livello più basso dell'ambito di gestione è il livello **risorsa**.</span><span class="sxs-lookup"><span data-stu-id="2d764-159">The lowest level of management scope is at the **resource** level.</span></span> <span data-ttu-id="2d764-160">Le operazioni a livello di risorsa si applicano solo alla risorsa stessa.</span><span class="sxs-lookup"><span data-stu-id="2d764-160">Operations applied at the resource level apply only to the resource itself.</span></span> <span data-ttu-id="2d764-161">Le autorizzazioni a livello di risorsa vengono ereditate dall'ambito del gruppo di risorse.</span><span class="sxs-lookup"><span data-stu-id="2d764-161">And once again, permissions at the resource level are inherited from resource group scope.</span></span> <span data-ttu-id="2d764-162">Se ad esempio il **proprietario del carico di lavoro** distribuisce una [rete virtuale](/azure/virtual-network/virtual-networks-overview) nel gruppo di risorse:</span><span class="sxs-lookup"><span data-stu-id="2d764-162">For example, let's look at what happens if the **workload owner** deploys a [virtual network](/azure/virtual-network/virtual-networks-overview) into the resource group:</span></span>

<span data-ttu-id="2d764-163">![Il **proprietario del carico di lavoro** crea una risorsa](../_images/governance-1-8.png)
*Figura 8. Il proprietario del carico di lavoro crea una risorsa ed eredita il ruolo di proprietario predefinito nell'ambito della risorsa.*</span><span class="sxs-lookup"><span data-stu-id="2d764-163">![The **workload owner** creates a resource](../_images/governance-1-8.png)
*Figure 8. The workload owner creates a resource and inherits the built-in owner role at the resource scope.*</span></span>

<span data-ttu-id="2d764-164">Il **proprietario del carico di lavoro** eredita il ruolo di proprietario nell'ambito della risorsa, ovvero ha tutte le autorizzazioni per la rete virtuale.</span><span class="sxs-lookup"><span data-stu-id="2d764-164">The **workload owner** inherits the owner role at the resource scope, which means the workload owner has all permissions for the virtual network.</span></span>

## <a name="implementing-the-basic-resource-access-management-model"></a><span data-ttu-id="2d764-165">Implementazione di un modello di base per la gestione dell'accesso alle risorse</span><span class="sxs-lookup"><span data-stu-id="2d764-165">Implementing the basic resource access management model</span></span>

<span data-ttu-id="2d764-166">A questo punto viene illustrato come implementare il modello di governance progettato in precedenza.</span><span class="sxs-lookup"><span data-stu-id="2d764-166">Let's move on to learn how to implement the governance model designed earlier.</span></span> 

<span data-ttu-id="2d764-167">Per iniziare, l'organizzazione richiede un account Azure.</span><span class="sxs-lookup"><span data-stu-id="2d764-167">To begin, your organization requires an Azure account.</span></span> <span data-ttu-id="2d764-168">Se l'organizzazione ha già un [Contratto Enterprise Microsoft](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) che non include Azure, è possibile aggiungere Azure assumendo un impegno monetario iniziale.</span><span class="sxs-lookup"><span data-stu-id="2d764-168">If your organization has an existing [Microsoft Enterprise Agreement](https://www.microsoft.com/licensing/licensing-programs/enterprise.aspx) that does not include Azure, Azure can be added by making an upfront monetary commitment.</span></span> <span data-ttu-id="2d764-169">Per altre informazioni, vedere [Licenze di Azure per l'azienda](https://azure.microsoft.com/pricing/enterprise-agreement/).</span><span class="sxs-lookup"><span data-stu-id="2d764-169">See [licensing Azure for the enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) for more information.</span></span> 

<span data-ttu-id="2d764-170">Quando viene creato l'account Azure, è necessario specificare una persona dell'organizzazione che sarà il **proprietario dell'account Azure**.</span><span class="sxs-lookup"><span data-stu-id="2d764-170">When your Azure account is created, you specify a person in your organization to be the Azure **account owner**.</span></span> <span data-ttu-id="2d764-171">Per impostazione predefinita, viene quindi creato un tenant Azure Active Directory (Azure AD).</span><span class="sxs-lookup"><span data-stu-id="2d764-171">An Azure Active Directory (Azure AD) tenant is then created by default.</span></span> <span data-ttu-id="2d764-172">Il **proprietario dell'account** Azure deve creare l'[account utente](/azure/active-directory/add-users-azure-active-directory) per la persona dell'organizzazione che sarà il **proprietario del carico di lavoro**.</span><span class="sxs-lookup"><span data-stu-id="2d764-172">Your Azure **account owner** must [create the user account](/azure/active-directory/add-users-azure-active-directory) for the person in your organization who is the **workload owner**.</span></span> 

<span data-ttu-id="2d764-173">Il **proprietario dell'account** Azure deve quindi [creare una sottoscrizione](https://docs.microsoft.com/partner-center/create-a-new-subscription) alla quale dovrà essere [associato il tenant Azure AD](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory).</span><span class="sxs-lookup"><span data-stu-id="2d764-173">Next, your Azure **account owner** must [create a subscription](https://docs.microsoft.com/partner-center/create-a-new-subscription) and [associate the Azure AD tenant](/azure/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory) with it.</span></span>

<span data-ttu-id="2d764-174">Dopo aver creato la sottoscrizione e aver associato il tenant Azure AD, è possibile [aggiungere il **proprietario del carico di lavoro** alla sottoscrizione con il ruolo di **proprietario** predefinito](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).</span><span class="sxs-lookup"><span data-stu-id="2d764-174">Finally, now that the subscription is created and your Azure AD tenant is associated with it, you can [add the **workload owner** to the subscription with the built-in **owner** role](/azure/billing/billing-add-change-azure-subscription-administrator#add-an-rbac-owner-for-a-subscription-in-azure-portal).</span></span>

## <a name="next-steps"></a><span data-ttu-id="2d764-175">Passaggi successivi</span><span class="sxs-lookup"><span data-stu-id="2d764-175">Next steps</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="2d764-176">Distribuire un carico di lavoro di base in Azure</span><span class="sxs-lookup"><span data-stu-id="2d764-176">Deploy a basic workload to Azure</span></span>](../infrastructure/basic-workload.md)
> [!div class="nextstepaction"]
> [<span data-ttu-id="2d764-177">Informazioni sull'accesso alle risorse per più team</span><span class="sxs-lookup"><span data-stu-id="2d764-177">Learn about resource access for multiple teams</span></span>](governance-multiple-teams.md)
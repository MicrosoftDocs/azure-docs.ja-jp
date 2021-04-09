---
title: Azure Stack Edge Pro GPU デバイス上の Kubernetes で Azure Arc を有効にする | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイス上の既存の Kubernetes クラスターで Azure Arc を有効にする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 4d75986880075f6eb07aa31b9322bdae15535802
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437606"
---
# <a name="enable-azure-arc-on-kubernetes-cluster-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上の Kubernetes クラスターで Azure Arc を有効にする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro デバイス上の既存の Kubernetes クラスターで Azure Arc を有効にする方法について説明します。 

この手順は、[Azure Stack Edge Pro デバイス上の Kubernetes ワークロード](azure-stack-edge-gpu-kubernetes-workload-management.md)に関する記事を確認し、[Azure Arc 対応 Kubernetes (プレビュー)](../azure-arc/kubernetes/overview.md) の概念を理解しているユーザーを対象としています。


## <a name="prerequisites"></a>前提条件

Kubernetes クラスターで Azure Arc を有効にする前に、Azure Stack Edge Pro デバイスと、デバイスへのアクセスに使用するクライアントで次の前提条件が満たされていることを確認してください。

### <a name="for-device"></a>デバイスでは

1. 1 ノードの Azure Stack Edge Pro デバイスに対するサインイン資格情報がある。
    1. デバイスがアクティブ化されている。 [デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。
    1. デバイスに、Azure portal を使用して構成されたコンピューティング ロールがあり、Kubernetes クラスターがある。 [コンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関する記事を参照してください。

1. サブスクリプションに対して所有者アクセス権がある。 サービス プリンシパルのロールの割り当て手順でこのアクセス権が必要になります。
 

### <a name="for-client-accessing-the-device"></a>デバイスにアクセスするクライアントでは

1. Azure Stack Edge Pro デバイスへのアクセスに使用される Windows クライアント システムがある。
  
    - クライアントでは、Windows PowerShell 5.0 以降が実行されている。 Windows PowerShell の最新バージョンをダウンロードするには、「[Windows PowerShell のインストール](/powershell/scripting/install/installing-powershell-core-on-windows)」を参照してください。
    
    - [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)が搭載されている他のクライアントを使用することもできます。 この記事では、Windows クライアントを使用する場合の手順について説明します。 
    
1. [Azure Stack Edge Pro デバイス上の Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関する記事で説明されている手順を完了している。 完了した内容:
    
    - クライアントに `kubectl` がインストールされている。    
    - `kubectl` クライアントのバージョンと、Azure Stack Edge Pro デバイスで実行されている Kubernetes マスターのバージョンの差が 1 未満であることを確認する。 
      - クライアントで実行されている kubectl のバージョンを確認するには、`kubectl version` を使用します。 完全なバージョン番号をメモしておきます。
      - Azure Stack Edge Pro デバイスのローカル UI で、 **[ソフトウェア更新プログラム]** に移動し、Kubernetes サーバーのバージョン番号をメモします。 
    
        ![Kubernetes サーバーのバージョン番号を確認する](media/azure-stack-edge-gpu-connect-powershell-interface/verify-kubernetes-version-1.png)      
      
      - これら 2 つのバージョンに互換性があることを確認します。 


## <a name="register-kubernetes-resource-providers"></a>Kubernetes リソース プロバイダーを登録する

Kubernetes クラスターで Azure Arc を有効にする前に、サブスクリプションに対して `Microsoft.Kubernetes` と `Microsoft.KubernetesConfiguration` を有効にして登録する必要があります。 

1. リソース プロバイダーを有効にするには、Azure portal で、デプロイに使用する予定のサブスクリプションに移動します。 **[リソース プロバイダー]** に移動します。 
1. 右側のペインで、追加するプロバイダーを検索します。 この例では、`Microsoft.Kubernetes` と `Microsoft.KubernetesConfiguration` です。

    ![Kubernetes リソース プロバイダーを登録する](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-1.png)

1. リソース プロバイダーを選択し、コマンド バーの上部から **[登録]** を選択します。 登録には数分かかります。 

    ![Kubernetes リソース プロバイダーを登録する 2](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-2.png)

1. リソース プロバイダーが登録されていることがわかるまで、UI を最新の情報に更新します。 両方のリソース プロバイダーに対してこのプロセスを繰り返します。
    
    ![Kubernetes リソース プロバイダーを登録する 3](media/azure-stack-edge-gpu-connect-powershell-interface/register-k8-resource-providers-4.png)

`az cli` を使用してリソース プロバイダーを登録することもできます。 詳細については、「[Azure Arc 対応 Kubernetes 用の 2 つのプロバイダーを登録する](../azure-arc/kubernetes/connect-cluster.md#register-the-two-providers-for-azure-arc-enabled-kubernetes)」を参照してください

## <a name="create-service-principal-assign-role"></a>サービス プリンシパルの作成、ロールの割り当て

1. Azure Stack Edge サービスのリソースのデプロイに使用した `Subscription ID` とリソース グループの名前があることを確認します。 サブスクリプション ID を取得するには、Azure portal で Azure Stack Edge リソースに移動します。 **[概要] > [基本]** に移動します。

    ![サブスクリプション ID の取得](media/azure-stack-edge-gpu-connect-powershell-interface/get-subscription-id-1.png)

    リソース グループ名を取得するには、 **[プロパティ]** に移動します。

    ![リソース グループ名の取得](media/azure-stack-edge-gpu-connect-powershell-interface/get-resource-group-name-1.png)

1. サービス プリンシパルを作成するには、`az cli` で次のコマンドを使用します。

    `az ad sp create-for-rbac --skip-assignment --name "<Informative name for service principal>"`  

    `az cli` にログインする方法については、[Azure portal で Cloud Shell を開始する](../cloud-shell/quickstart-powershell.md#start-cloud-shell)方法に関する記事を参照してください。

    次に例を示します。 
    
    ```azurecli
    PS /home/user> az ad sp create-for-rbac --skip-assignment --name "https://azure-arc-for-ase-k8s"
    {
      "appId": "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b",
      "displayName": "azure-arc-for-ase-k8s",
      "name": "https://azure-arc-for-ase-k8s",
      "password": "<password>",
      "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
    }
    PS /home/user>
    ```

1. 次のコマンドの入力として使用するため、`appID`、`name`、`password`、および `tenantID` をメモします。

1. 新しいサービス プリンシパルを作成したら、新しく作成したプリンシパルに `Kubernetes Cluster - Azure Arc Onboarding` ロールを割り当てます。 これは、アクセス許可が制限された組み込みの Azure ロールです (コマンドでロール ID を使用します)。 次のコマンドを使用します。

    `az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee <appId-from-service-principal> --scope /subscriptions/<SubscriptionID>/resourceGroups/<Resource-group-name>`

    次に例を示します。
    
    ```azurecli
    PS /home/user> az role assignment create --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 --assignee aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b --scope /subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1
    {
      "canDelegate": null,
      "id": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1/providers/Microsoft.Authorization/roleAssignments/59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "name": "59272f92-e5ce-4aeb-9c0c-62532d8caf25",
      "principalId": "b045b3fe-8745-4097-9674-91cb0afaad91",
      "principalType": "ServicePrincipal",
      "resourceGroup": "myaserg1",
      "roleDefinitionId": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
      "scope": "/subscriptions/062c67a6-019b-40af-a775-c4dc1abe56ed/resourceGroups/myaserg1",
      "type": "Microsoft.Authorization/roleAssignments"
    }
    PS /home/user>
    ```
    サービス プリンシパルを作成してロールの割り当てを実行する方法の詳細については、「[Azure Arc 対応オンボード サービス プリンシパルの作成](../azure-arc/kubernetes/create-onboarding-service-principal.md)」の手順を参照してください。


## <a name="enable-arc-on-kubernetes-cluster"></a>Kubernetes クラスターで Arc を有効にする

Azure Arc 管理用に Kubernetes クラスターを構成するには、次の手順を実行します。

1. デバイスの [PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。

1. 型:

    `Set-HcsKubernetesAzureArcAgent -SubscriptionId "<Your Azure Subscription Id>" -ResourceGroupName "<Resource Group Name>" -ResourceName "<Azure Arc resource name (shouldn't exist already)>" -Location "<Region associated with resource group>" -TenantId "<Tenant Id of service principal>" -ClientId "<App id of service principal>" -ClientSecret "<Password of service principal>"`


    > [!NOTE]
    > - お使いのデバイスに Azure Arc を展開するには、[Azure Arc でサポートされているリージョン](../azure-arc/kubernetes/overview.md#supported-regions)を使用していることを確認してください。 
    > - `az account list-locations` コマンドを使用して、`Set-HcsKubernetesAzureArcAgent` コマンドレットで渡す正確な場所の名前を確認します。 場所の名前は通常、スペースなしの形式が使用されます。
    
    たとえば次のようになります。
   
    ```powershell
    [10.128.44.240]: PS>Set-HcsKubernetesAzureArcAgent -SubscriptionId "062c67a6-019b-40af-a775-c4dc1abe56ed" -ResourceGroupName "myaserg1" -ResourceName "myasetestresarc" -Location "westeurope" -TenantId "72f988bf-86f1-41af-91ab-2d7cd011db47" -ClientId "aa8a082e-0fa1-4a82-b51c-e8b2a9fdaa8b" -ClientSecret "<password>"
        [10.128.44.240]: PS>
    ```
    
    Azure portal で、前のコマンドで指定した名前でリソースを作成する必要があります。

    ![Azure Arc リソースに移動する](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Azure Arc が正常に有効になったことを確認するには、PowerShell インターフェイスから次のコマンドを実行します。

    `kubectl get deployments -n azure-arc`

    このコマンドを使用すると、Azure Arc に対応する `azure-arc` 名前空間に展開されているアプリケーションがすべて検索されます。

    これは、`azure-arc` 名前空間の Kubernetes クラスターに展開された Azure Arc エージェントを示すサンプル出力です。 


    ```powershell
    [10.128.44.240]: PS>kubectl get deployments -n azure-arc
    NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
    cluster-metadata-operator   1/1     1            1           45m
    clusteridentityoperator     1/1     1            1           45m
    config-agent                1/1     1            1           45m
    connect-agent               1/1     1            1           45m
    controller-manager          1/1     1            1           45m
    flux-logs-agent             1/1     1            1           45m
    metrics-agent               1/1     1            1           45m
    resource-sync-agent         1/1     1            1           45m
    [10.128.44.240]: PS>
    ```

    `azure-arc` 名前空間の Kubernetes クラスターで実行されているポッドの一覧を取得することもできます。 ポッドは、Kubernetes クラスターで実行されるアプリケーション コンテナー (つまり、プロセス) です。 

    次のコマンドを使用します。
    
    `kubectl get pods -n azure-arc`
    
    出力例を次に示します。
    
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n azure-arc
    NAME                                         READY   STATUS    RESTARTS   AGE
    cluster-metadata-operator-64cbdf95b4-s2q52   2/2     Running   0          16m
    clusteridentityoperator-6f6dbccf7-nwnxg      3/3     Running   0          16m
    config-agent-7df5bf497b-mjm8k                3/3     Running   0          16m
    connect-agent-5d4c766764-m7h46               1/1     Running   0          16m
    controller-manager-777555fb57-t7tdp          3/3     Running   0          16m
    flux-logs-agent-845476c899-zcmtj             2/2     Running   0          16m
    metrics-agent-84d6fc8f4d-g9jkm               2/2     Running   0          16m
    resource-sync-agent-8f88dbf96-zgxjj          3/3     Running   0          16m
    [10.128.44.240]: PS>
    ```


前述の出力が示すように、Azure Arc 対応の Kubernetes は、`azure-arc` 名前空間にデプロイされたクラスター内で実行されるいくつかのエージェント (オペレーター) で構成されています。

- `config-agent`: クラスターに適用されるソース管理構成リソースの接続されたクラスターを監視し、コンプライアンス状態を更新します
- `controller-manager`: オペレーターのオペレーターであり、Azure Arc コンポーネント間の相互作用を統制します
- `metrics-agent`: 他の Arc エージェントのメトリックを収集し、これらのエージェントのパフォーマンスが最適であることを確認します
- `cluster-metadata-operator`: クラスターのメタデータを収集します (クラスターのバージョン、ノード数、Azure Arc エージェントのバージョン)
- `resource-sync-agent`: 前述のクラスター メタデータを Azure に同期します
- `clusteridentityoperator`:Azure Arc 対応 Kubernetes では、現在、システムによって割り当てられた ID がサポートされています。 clusteridentityoperator では、他のエージェントが Azure との通信に使用する管理サービス ID (MSI) 証明書が保持されます。
- `flux-logs-agent`: ソース管理構成の一部としてデプロイされる Flux オペレーターからログを収集します。
- `connect-agent`: Azure Arc リソースと対話します。

### <a name="remove-arc-from-the-kubernetes-cluster"></a>Kubernetes クラスターから Arc を削除する

Azure Arc 管理を削除するには、次の手順を実行します。

1. 1. デバイスの [PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。
2. 型:

    `Remove-HcsKubernetesAzureArcAgent` 


> [!NOTE]
> Git リポジトリからリソース `yamls` を削除した場合、既定で Kubernetes クラスターから対応するリソースは削除されません。 Git リポジトリから削除したときにリソースが削除されるようにするには、Arc OperatorParams に `--sync-garbage-collection` を設定する必要があります。 詳細については、「[構成を削除する](../azure-arc/kubernetes/use-gitops-connected-cluster.md#additional-parameters)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Arc デプロイを実行する方法については、[GitOps を介し、Redis を使用してステートレス PHP `Guestbook` アプリケーションを Azure Stack Edge Pro デバイスにデプロイする](azure-stack-edge-gpu-deploy-stateless-application-git-ops-guestbook.md)方法に関するページを参照してください。

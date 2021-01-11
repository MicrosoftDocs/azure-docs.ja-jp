---
title: Azure Key Vault と Kubernetes の統合
description: このチュートリアルでは、シークレット ストア コンテナー ストレージ インターフェイス (CSI) ドライバーを使用して Azure キー コンテナーにアクセスしてシークレットを取得し、Kubernetes ポッドにマウントします。
author: ShaneBala-keyvault
ms.author: sudbalas
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/25/2020
ms.openlocfilehash: bfcaf9d4b1d03457f2e4cddd2e0eaf9d9d58eee2
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88869186"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>チュートリアル:Kubernetes 上のシークレット ストア CSI ドライバー向けに Azure Key Vault プロバイダーを構成して実行する

> [!IMPORTANT]
> CSI ドライバーは、Azure テクニカル サポートではサポートされていないオープン ソース プロジェクトです。 CSI ドライバー Key Vault 統合に関連するすべてのフィードバックと問題を、このページの下部にある github リンクで報告してください。 このツールはユーザーがクラスターに自己インストールし、コミュニティからフィードバックを収集するために提供されています。

このチュートリアルでは、シークレット ストア コンテナー ストレージ インターフェイス (CSI) ドライバーを使用して Azure キー コンテナーにアクセスしてシークレットを取得し、そのシークレットを Kubernetes ポッドにマウントします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * サービス プリンシパルを作成するか、マネージド ID を使用する。
> * Azure CLI を使用して Azure Kubernetes Service (AKS) クラスターをデプロイする。
> * Helm とシークレット ストア CSI ドライバーをインストールする。
> * Azure キー コンテナーを作成してシークレットを設定する。
> * 独自の SecretProviderClass オブジェクトを作成する。
> * サービス プリンシパルを割り当てるか、マネージド ID を使用する。
> * キー コンテナーからマウントされたシークレットを使用してポッドをデプロイする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* このチュートリアルを開始する前に、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) をインストールします。

## <a name="create-a-service-principal-or-use-managed-identities"></a>サービス プリンシパルを作成するかマネージド ID を使用する

マネージド ID の使用を予定している場合は、次のセクションに進むことができます。

Azure キー コンテナーからアクセスできるリソースを制御するには、サービス プリンシパルを作成します。 このサービス プリンシパルのアクセスは、それに割り当てられたロールによって制限されます。 この機能を使用すると、サービス プリンシパルでシークレットを管理する方法を制御できます。 次の例では、サービス プリンシパルの名前は *contosoServicePrincipal* です。

```azurecli
az ad sp create-for-rbac --name contosoServicePrincipal --skip-assignment
```
この操作では、一連のキーと値のペアが返されます。

![contosoServicePrincipal のアプリ ID とパスワードを示すスクリーンショット](../media/kubernetes-key-vault-1.png)

後で使用するために、**appId** と **password** の資格情報をコピーします。

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure CLI を使用して Azure Kubernetes Service (AKS) クラスターをデプロイする

Azure Cloud Shell を使用する必要はありません。 Azure CLI がインストールされているコマンド プロンプト (ターミナル) で十分です。 

[Azure CLI を使用した Azure Kubernetes Service クラスターのデプロイ](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough)に関するページの「リソース グループを作成する」、「AKS クラスターの作成」、および「クラスターに接続する」セクションを完了します。 

> [!NOTE] 
> サービス プリンシパルの代わりにポッド ID を使用する予定の場合は、次のコマンドに示すように、Kubernetes クラスターを作成する際にそれを必ず有効にしてください。
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [PATH 環境変数](https://www.java.com/en/download/help/path.xml)を、ダウンロードした *kubectl.exe* ファイルに設定します。
1. 次のコマンドを使用して、Kubernetes バージョンを確認します。これにより、クライアントとサーバーのバージョンが出力されます。 クライアントのバージョンは、インストールした *kubectl.exe* ファイルであり、サーバーのバージョンは、クラスターが実行されている Azure Kubernetes Service (AKS) です。
    ```azurecli
    kubectl version
    ```
1. Kubernetes のバージョンが 1.16.0 以降であることを確認します。 次のコマンドを実行すると、Kubernetes クラスターとノード プールの両方がアップグレードされます。 このコマンドの実行には数分かかる場合があります。 この例では、リソース グループは *contosoResourceGroup*、Kubernetes クラスターは *contosoAKSCluster* です。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
1. 作成した AKS クラスターのメタデータを表示するには、次のコマンドを使用します。 後で使用するために、**principalId**、**clientId**、**subscriptionId**、**nodeResourceGroup** をコピーします。 マネージド ID を有効にして ASK クラスターを作成しなかった場合、**principalId** と **clientId** は null になります。 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    この出力では、両方のパラメーターが強調表示されています。
    
    ![principalId と clientId の値が強調表示されている Azure CLI のスクリーンショット](../media/kubernetes-key-vault-2.png) ![subscriptionId と nodeResourceGroup の値が強調表示されている Azure CLI のスクリーンショット](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Helm とシークレット ストア CSI ドライバーをインストールする

シークレット ストア CSI ドライバーをインストールするには、最初に [Helm](https://helm.sh/docs/intro/install/) をインストールする必要があります。

[シークレット ストア CSI](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/charts/csi-secrets-store-provider-azure/README.md) ドライバー インターフェイスを使用すると、自分の Azure キー コンテナー インスタンスに格納されているシークレットを取得し、シークレット コンテンツを Kubernetes ポッドにマウントすることができます。

1. Helm のバージョンが v3 以降であることを確認します。
    ```azurecli
    helm version
    ```
1. シークレット ストア CSI ドライバーとそのドライバー用の Azure Key Vault プロバイダーをインストールします。
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure キー コンテナーを作成してシークレットを設定する

独自のキー コンテナーを作成してシークレットを設定するには、[Azure CLI を使用した Azure キー コンテナーへのシークレットの設定および取得](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)に関するページの手順に従います。

> [!NOTE] 
> Azure Cloud Shell を使用したり、新しいリソース グループを作成したりする必要はありません。 以前 Kubernetes クラスター用に作成したリソース グループを使用できます。

## <a name="create-your-own-secretproviderclass-object"></a>独自の SecretProviderClass オブジェクトを作成する

シークレット ストア CSI ドライバーのプロバイダー固有のパラメーターを使用して独自のカスタム SecretProviderClass オブジェクトを作成するには、[このテンプレートを使用](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/test/bats/tests/azure_v1alpha1_secretproviderclass.yaml)します。 このオブジェクトにより、自分のキー コンテナーへのアクセス権が ID に提供されます。

サンプルの SecretProviderClass YAML ファイルで、不足しているパラメーターを入力します。 次のパラメーターが必要です。

* **userAssignedIdentityID**: サービス プリンシパルのクライアント ID
* **keyvaultName**: キー コンテナーの名前
* **objects**: マウントするすべてのシークレット コンテンツ用のコンテナー
    * **objectName**: シークレット コンテンツの名前
    * **objectType**: オブジェクトの種類 (シークレット、キー、証明書)
* **resourceGroup**: リソース グループの名前
* **subscriptionId**: お使いのキー コンテナーのサブスクリプション ID
* **tenantID**: お使いのキー コンテナーのテナント ID またはディレクトリ ID

更新されたテンプレートを次のコードに示します。 これを YAML ファイルとしてダウンロードし、必須フィールドに入力します。 この例のキー コンテナーは **contosoKeyVault5** です。 これには、**secret1** と **secret2** という 2 つのシークレットが含まれています。

> [!NOTE] 
> マネージド ID を使用している場合は、**usePodIdentity** 値を *true* として設定し、**userAssignedIdentityID** 値を引用符のペア ( **""** ) として設定します。 

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                   # [REQUIRED] Set to "true" if using managed identities
    useVMManagedIdentity: "false"             # [OPTIONAL] if not provided, will default to "false"
    userAssignedIdentityID: "servicePrincipalClientID"       # [REQUIRED] If you're using a service principal, use the client id to specify which user-assigned managed identity to use. If you're using a user-assigned identity as the VM's managed identity, specify the identity's client id. If the value is empty, it defaults to use the system-assigned identity on the VM
                                                             #     az ad sp show --id http://contosoServicePrincipal --query appId -o tsv
                                                             #     the preceding command will return the client ID of your service principal
    keyvaultName: "contosoKeyVault5"          # [REQUIRED] the name of the key vault
                                              #     az keyvault show --name contosoKeyVault5
                                              #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                 # [REQUIRED] object name
                                              #     az keyvault secret list --vault-name "contosoKeyVault5"
                                              #     the above command will display a list of secret names from your key vault
          objectType: secret                  # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                   # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    resourceGroup: "contosoResourceGroup"     # [REQUIRED] the resource group name of the key vault
    subscriptionId: "subscriptionID"          # [REQUIRED] the subscription ID of the key vault
    tenantId: "tenantID"                      # [REQUIRED] the tenant ID of the key vault
```
次の画像は、**az keyvault show --name contosoKeyVault5** のコンソール出力を示しています。関連するメタデータが強調表示されています。

!["az keyvault show --name contosoKeyVault5" のコンソール出力を示すスクリーンショット](../media/kubernetes-key-vault-4.png)

## <a name="assign-your-service-principal-or-use-managed-identities"></a>サービス プリンシパルを割り当てるかマネージド ID を使用する

### <a name="assign-a-service-principal"></a>サービス プリンシパルの割り当て

サービス プリンシパルを使用している場合は、対象のキー コンテナーにアクセスしてシークレットを取得するためのアクセス許可をサービス プリンシパルに付与します。 次のコマンドを実行して、"*閲覧者*" ロールを割り当て、対象のキー コンテナーからシークレットを "*取得する*" ためのアクセス許可をサービス プリンシパルに付与します。

1. サービス プリンシパルを既存の対象のキー コンテナーに割り当てます。 **$AZURE_CLIENT_ID** パラメーターは、サービス プリンシパルを作成した後にコピーした **appId** です。
    ```azurecli
    az role assignment create --role Reader --assignee $AZURE_CLIENT_ID --scope /subscriptions/$SUBID/resourcegroups/$KEYVAULT_RESOURCE_GROUP/providers/Microsoft.KeyVault/vaults/$KEYVAULT_NAME
    ```

    コマンドの出力を次の画像に示します。 

    ![principalId 値を示すスクリーンショット](../media/kubernetes-key-vault-5.png)

1. シークレットを取得するためのアクセス許可をサービス プリンシパルに付与します。
    ```azurecli
    az keyvault set-policy -n $KEYVAULT_NAME --secret-permissions get --spn $AZURE_CLIENT_ID
    ```

1. これで、対象のキー コンテナーからシークレットを読み取るためのアクセス許可が付与されたサービス プリンシパルが構成されました。 **$AZURE_CLIENT_SECRET** は、対象のサービス プリンシパルのパスワードです。 シークレット ストア CSI ドライバーからアクセスできる Kubernetes シークレットとして、対象のサービス プリンシパルの資格情報を追加します。
    ```azurecli
    kubectl create secret generic secrets-store-creds --from-literal clientid=$AZURE_CLIENT_ID --from-literal clientsecret=$AZURE_CLIENT_SECRET
    ```

> [!NOTE] 
> Kubernetes ポッドのデプロイ中に、無効なクライアント シークレット ID に関するエラーが発生した場合は、有効期限が切れたかリセットされた古いクライアント シークレット ID を使用している可能性があります。 この問題を解決するには、*secrets-store-creds* シークレットを削除し、現在のクライアント シークレット ID を使用して新しいものを作成してください。 *secrets-store-creds* を削除するには、次のコマンドを実行します。
>
> ```azurecli
> kubectl delete secrets secrets-store-creds
> ```

対象のサービス プリンシパルのクライアント シークレット ID を忘れた場合は、次のコマンドを使用してリセットすることができます。

```azurecli
az ad sp credential reset --name contosoServicePrincipal --credential-description "APClientSecret" --query password -o tsv
```

### <a name="use-managed-identities"></a>マネージド ID の使用

マネージド ID を使用する場合は、作成した AKS クラスターに特定のロールを割り当てます。 

1. ユーザー割り当てのマネージド ID の作成、一覧表示、または読み取りを行うには、AKS クラスターに[マネージド ID オペレーター](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-identity-operator) ロールを割り当てる必要があります。 **$clientId** が Kubernetes クラスターの clientId であることを確認します。 スコープについては、お使いの Azure サブスクリプション サービス、特に AKS クラスターの作成時に作成されたノード リソース グループの下に配置されます。 このスコープによって、そのグループ内のリソースのみが、以下に割り当てられたロールの影響を受けるようになります。 

    ```azurecli
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/$SUBID/resourcegroups/$NODE_RESOURCE_GROUP
    ```

1. AKS に Azure Active Directory (Azure AD) ID をインストールします。
    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

1. Azure AD ID を作成します。 後で使用するために、出力の **clientId** と **principalId** をコピーします。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

1. 対象のキー コンテナー用に前の手順で作成した Azure AD ID に "*閲覧者*" ロールを割り当てた後、キー コンテナーからシークレットを取得するためのアクセス許可をその ID に付与します。 Azure AD ID の **clientId** と **principalId** を使用します。
    ```azurecli
    az role assignment create --role "Reader" --assignee $principalId --scope /subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/contosoResourceGroup/providers/Microsoft.KeyVault/vaults/contosoKeyVault5

    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>キー コンテナーからマウントされたシークレットを使用してポッドをデプロイする

SecretProviderClass オブジェクトを構成するには、次のコマンドを実行します。
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-a-service-principal"></a>サービス プリンシパルを使用する

サービス プリンシパルを使用している場合は、次のコマンドを使用して、SecretProviderClass と以前に構成した secrets-store-creds を使用して Kubernetes ポッドをデプロイします。 デプロイ テンプレートは次のとおりです。
* [Linux](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/nginx-pod-inline-volume-service-principal.yaml) の場合
* [Windows](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/windows-pod-secrets-store-inline-volume-secret-providerclass.yaml) の場合

```azurecli
kubectl apply -f updateDeployment.yaml
```

### <a name="use-managed-identities"></a>マネージド ID の使用

マネージド ID を使用している場合は、前に作成した ID を参照する *AzureIdentity* をクラスター内に作成します。 次に、作成した AzureIdentity を参照する *AzureIdentityBinding* を作成します。 次のテンプレートにパラメーターを入力した後、*podIdentityAndBinding.yaml* として保存します。  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: "azureIdentityName"               # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed service identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "managedIdentityClientId"     # The clientId of the Azure AD identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
次のコマンドを実行して、バインドを実行します。

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

次に、ポッドをデプロイします。 次のコードは、前の手順のポッド ID のバインドを使用するデプロイ YAML ファイルです。 このファイルを *podBindingDeployment.yaml* として保存します。

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

次のコマンドを実行してポッドをデプロイします。

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>ポッドの状態とシークレット コンテンツを確認する 

デプロイしたポッドを表示するには、次のコマンドを実行します。
```azurecli
kubectl get pods
```

ポッドの状態を確認するには、次のコマンドを実行します。
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Azure CLI 出力のスクリーンショット。ポッドの状態が [Running]\(実行中\)、すべてのイベントが [Normal]\(標準\) と表示されている ](../media/kubernetes-key-vault-6.png)

出力ウィンドウで、デプロイされたポッドは *[Running]\(実行中\)* 状態になります。 下部の **[Events]\(イベント\)** セクションで、すべてのイベントの種類が *[Normal]\(標準\)* として表示されます。

ポッドが実行中であることを確認したら、対象のキー コンテナーのシークレットがポッドに含まれていることを確認できます。

ポッドに含まれるすべてのシークレットを表示するには、次のコマンドを実行します。
```azurecli
kubectl exec -it nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

特定のシークレットの内容を表示するには、次のコマンドを実行します。
```azurecli
kubectl exec -it nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

シークレットの内容が表示されていることを確認します。

## <a name="next-steps"></a>次のステップ

キー コンテナーが回復可能であることを確認するには、以下を参照してください。
> [!div class="nextstepaction"]
> [論理的な削除を有効にする](https://docs.microsoft.com/azure/key-vault/general/soft-delete-cli)

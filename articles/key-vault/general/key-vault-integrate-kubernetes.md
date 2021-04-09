---
title: Azure Key Vault と Kubernetes の統合
description: このチュートリアルでは、シークレット ストア コンテナー ストレージ インターフェイス (CSI) ドライバーを使用して Azure キー コンテナーにアクセスしてシークレットを取得し、Kubernetes ポッドにマウントします。
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: b130fd3f85b676f0a394ad95730181ff499dac96
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216498"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>チュートリアル:Kubernetes 上のシークレット ストア CSI ドライバー向けに Azure Key Vault プロバイダーを構成して実行する

> [!IMPORTANT]
> シークレット ストア CSI ドライバーは、Azure テクニカル サポートではサポートされていないオープンソース プロジェクトです。 CSI ドライバー Key Vault 統合に関連するすべてのフィードバックと問題を、このページの下部にある github リンクで報告してください。 このツールはユーザーがクラスターに自己インストールし、コミュニティからフィードバックを収集するために提供されています。

このチュートリアルでは、シークレット ストア コンテナー ストレージ インターフェイス (CSI) ドライバーを使用して Azure キー コンテナーにアクセスしてシークレットを取得し、そのシークレットを Kubernetes ポッドにマウントします。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マネージド ID を使用する。
> * Azure CLI を使用して Azure Kubernetes Service (AKS) クラスターをデプロイする。
> * Helm、シークレット ストア CSI ドライバーおよびその CSI ドライバー用の Azure Key Vault プロバイダーをインストールする。
> * Azure キー コンテナーを作成してシークレットを設定する。
> * 独自の SecretProviderClass オブジェクトを作成する。
> * キー コンテナーからマウントされたシークレットを使用してポッドをデプロイする。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

* このチュートリアルを開始する前に、[Azure CLI](/cli/azure/install-azure-cli-windows) をインストールします。

このチュートリアルでは、Azure Kubernetes Service を Linux ノード上で実行することを前提としています。

## <a name="use-managed-identities"></a>マネージド ID の使用

この図は、マネージド ID の AKS と Key Vault の統合のフローを示しています。

![マネージド ID の AKS と Key Vault の統合のフローを示す図](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure CLI を使用して Azure Kubernetes Service (AKS) クラスターをデプロイする

Azure Cloud Shell を使用する必要はありません。 Azure CLI がインストールされているコマンド プロンプト (ターミナル) で十分です。 

[Azure CLI を使用した Azure Kubernetes Service クラスターのデプロイ](../../aks/kubernetes-walkthrough.md)に関するページの「リソース グループを作成する」、「AKS クラスターの作成」、および「クラスターに接続する」セクションを完了します。 

> [!NOTE] 
> ポッド ID の使用を予定している場合、推奨されるネットワーク プラグインは `azure` です。 詳細については、[ドキュメント](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/)を参照してください。 次のコマンドに示すように、Kubernetes クラスターを作成します。
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [PATH 環境変数](https://www.java.com/en/download/help/path.xml)を、ダウンロードした *kubectl.exe* ファイルに設定します。
2. 次のコマンドを使用して、Kubernetes バージョンを確認します。これにより、クライアントとサーバーのバージョンが出力されます。 クライアントのバージョンは、インストールした *kubectl.exe* ファイルであり、サーバーのバージョンは、クラスターが実行されている Azure Kubernetes Service (AKS) です。
    ```azurecli
    kubectl version
    ```
3. Kubernetes のバージョンが 1.16.0 以降であることを確認します。 Windows クラスターの場合、Kubernetes のバージョンが 1.18.0 以降であることを確認します。 次のコマンドを実行すると、Kubernetes クラスターとノード プールの両方がアップグレードされます。 このコマンドの実行には数分かかる場合があります。 この例では、リソース グループは *contosoResourceGroup*、Kubernetes クラスターは *contosoAKSCluster* です。
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. 作成した AKS クラスターのメタデータを表示するには、次のコマンドを使用します。 後で使用するために、**principalId**、**clientId**、**subscriptionId**、**nodeResourceGroup** をコピーします。 マネージド ID を有効にして AKS クラスターを作成しなかった場合、**principalId** と **clientId** は null になります。 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    この出力では、両方のパラメーターが強調表示されています。
    
    ![principalId と clientId の値が強調表示されている Azure CLI のスクリーンショット](../media/kubernetes-key-vault-2.png) ![subscriptionId と nodeResourceGroup の値が強調表示されている Azure CLI のスクリーンショット](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>Helm とシークレット ストア CSI ドライバーをインストールする
> [!NOTE]
> 以下のインストールは、Linux 上の AKS でのみ機能します。 シークレット ストア CSI ドライバーのインストールの詳細については、「[シークレット ストア CSI ドライバーの Azure Key Vault プロバイダー](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/)」をご覧ください。 

シークレット ストア CSI ドライバーと Azure Key Vault プロバイダーをインストールするには、最初に [Helm](https://helm.sh/docs/intro/install/) をインストールする必要があります。

[シークレット ストア CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/) ドライバー インターフェイスを使用すると、自分の Azure Key Vault インスタンスに格納されているシークレットを取得してから、ドライバー インターフェイスを使用してシークレット コンテンツを Kubernetes ポッドにマウントすることができます。

1. Helm のバージョンが v3 以降であることを確認します。
    ```azurecli
    helm version
    ```
1. シークレット ストア CSI ドライバーとそのドライバー用の Azure Key Vault プロバイダーをインストールします。
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Windows ノードでシークレット ストア CSI ドライバーと Azure Key Vault プロバイダーを使用する予定の場合は、[Helm 構成値](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration)を使用して Windows ノードでドライバーとプロバイダーを有効にしてください

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure キー コンテナーを作成してシークレットを設定する

独自のキー コンテナーを作成してシークレットを設定するには、[Azure CLI を使用した Azure キー コンテナーへのシークレットの設定および取得](../secrets/quick-create-cli.md)に関するページの手順に従います。

> [!NOTE] 
> Azure Cloud Shell を使用したり、新しいリソース グループを作成したりする必要はありません。 以前 Kubernetes クラスター用に作成したリソース グループを使用できます。

## <a name="create-your-own-secretproviderclass-object"></a>独自の SecretProviderClass オブジェクトを作成する

シークレット ストア CSI ドライバーのプロバイダー固有のパラメーターを使用して独自のカスタム SecretProviderClass オブジェクトを作成するには、[このテンプレートを使用](https://github.com/Azure/secrets-store-csi-driver-provider-azure/blob/master/examples/service-principal/v1alpha1_secretproviderclass_service_principal.yaml)します。 このオブジェクトにより、自分のキー コンテナーへのアクセス権が ID に提供されます。

サンプルの SecretProviderClass YAML ファイルで、不足しているパラメーターを入力します。 次のパラメーターが必要です。

* **keyvaultName**: キー コンテナーの名前
* **objects**: マウントするすべてのシークレット コンテンツの一覧
    * **objectName**: シークレット コンテンツの名前
    * **objectType**: オブジェクトの種類 (シークレット、キー、証明書)
* **tenantID**: お使いのキー コンテナーのテナント ID またはディレクトリ ID

すべての必須フィールドおよびサポートされている構成に関するドキュメントについては、こちらを参照してください: [リンク](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

更新されたテンプレートを次のコードに示します。 これを YAML ファイルとしてダウンロードし、必須フィールドに入力します。 この例のキー コンテナーは **contosoKeyVault5** です。 これには、**secret1** と **secret2** という 2 つのシークレットが含まれています。

> [!NOTE] 
> ポッド ID を使用している場合は、**usePodIdentity** 値を *true* として設定し、**userAssignedIdentityID** 値を引用符のペア ( **""** ) として設定します。 マネージド ID を使用している場合は、**useVMManagedIdentity** 値を *true* として設定し、**userAssignedIdentityID** 値をユーザー割り当て ID の clientID として設定します。

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
次の画像は、**az keyvault show --name contosoKeyVault5** のコンソール出力を示しています。関連するメタデータが強調表示されています。

!["az keyvault show --name contosoKeyVault5" のコンソール出力を示すスクリーンショット](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Azure Active Directory (Azure AD) ポッド ID をインストールする

1. 作成した AKS クラスターに特定のロールを割り当てます。 

    すべての必須のロールの割り当てと Azure Active Directory (Azure AD) ポッド ID に関するドキュメントについては、こちらを参照してください: [リンク](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. AKS に Azure Active Directory (Azure AD) ID をインストールします。

    > [!NOTE] 
    > kubenet ネットワーク プラグインと共に AKS クラスターを使用している場合は、クラスターにポッド ID をデプロイする方法について、こちらの[ドキュメント](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/)を確認してください。

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. ユーザー割り当てマネージド ID を作成します。 後で使用するために、出力の **clientId** をコピーします。
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. その ID に対して、キー コンテナーからシークレットを取得するためのアクセス許可を付与します。 ユーザー割り当てマネージド ID から **clientId** を使用します。
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>キー コンテナーからマウントされたシークレットを使用してポッドをデプロイする

SecretProviderClass オブジェクトを構成するには、次のコマンドを実行します。
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>マネージド ID の使用

ポッド ID を使用している場合は、前に作成した ID を参照する *AzureIdentity* をクラスター内に作成します。 次に、作成した AzureIdentity を参照する *AzureIdentityBinding* を作成します。 次のテンプレートにパラメーターを入力した後、*podIdentityAndBinding.yaml* として保存します。  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
次のコマンドを実行して、バインドを構成します。

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
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
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
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

特定のシークレットの内容を表示するには、次のコマンドを実行します。
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

シークレットの内容が表示されていることを確認します。

## <a name="next-steps"></a>次のステップ

キー コンテナーが回復可能であることを確認するには、以下を参照してください。
> [!div class="nextstepaction"]
> [論理的な削除を有効にする](./key-vault-recovery.md)

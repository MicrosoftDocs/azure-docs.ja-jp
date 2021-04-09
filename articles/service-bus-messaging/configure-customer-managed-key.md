---
title: Azure Service Bus 保存データの暗号化用に独自のキーを構成する
description: この記事では、Azure Service Bus の保存データを暗号化するための独自のキーを構成する方法について説明します。
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 5d14c8953819575d1c2688520838135efc7121e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378317"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Azure portal を使用して Azure Service Bus 保存データの暗号化用のカスタマー マネージド キーを構成する
Azure Service Bus Premium では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Service Bus Premium では、データを格納するために Azure Storage が使用されます。 Azure Storage に格納されているすべてのデータは、Microsoft マネージド キーを使用して暗号化されます。 独自のキー (Bring Your Own Key (BYOK) またはカスタマーマネージド キーとも呼ばれます) を使用する場合、データは引き続き Microsoft マネージド キーを使用して暗号化されますが、さらに Microsoft マネージド キーはカスタマー マネージド キーを使用して暗号化されます。 この機能を使用して、Microsoft マネージド キーの暗号化に使用されるカスタマー マネージド キーへの作成、ローテーション、無効化、およびアクセスの取り消しを実行できます。 BYOK 機能の有効化は、名前空間での 1 回限りのセットアップ プロセスです。

カスタマー マネージド キーには、サービス側の暗号化に関するいくつかの注意事項があります。 
- この機能は [Azure Service Bus Premium](service-bus-premium-messaging.md) レベルでサポートされます。 これは、Standard レベルの Service Bus 名前空間に対して有効にすることはできません。
- 暗号化は、新規または空の名前空間に対してのみ有効にすることができます。 名前空間にキューまたはトピックが含まれている場合、暗号化操作は失敗します。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使用できます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/general/overview.md)

この記事では、Azure portal を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault を作成する](../key-vault/general/quick-create-portal.md)」を参照します。

> [!IMPORTANT]
> Azure Service Bus でカスタマー マネージド キーを使うには、キー コンテナーに 2 つの必須プロパティが構成されている必要があります。 これらは次のとおりです。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする
Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. お使いの Service Bus Premium 名前空間に移動します。
2. Service Bus 名前空間の **[設定]** ページで、 **[暗号化]** を選択します。
3. 次の図に示すように、**カスタマー マネージド キーによる保存中の暗号化** を選択します。

    ![カスタマー マネージド キーの有効化](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>キーを使用したキー コンテナーの設定

カスタマー マネージド キーを有効にした後、カスタマー マネージド キーを Azure Service Bus 名前空間に関連付ける必要があります。 Service Bus では Azure Key Vault のみがサポートされます。 前のセクションで **カスタマー マネージド キーによる暗号化** のオプションを有効にした場合は、キーが Azure Key Vault にインポートされている必要があります。 また、キーに対して **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** が構成されている必要があります。 これらの設定は、[PowerShell](../key-vault/general/key-vault-recovery.md) または [CLI](../key-vault/general/key-vault-recovery.md) を使用して構成できます。

1. 新しいキー コンテナーを作成するには、Azure Key Vault の[クイック スタート](../key-vault/general/overview.md)に従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/general/about-keys-secrets-certificates.md)」を参照してください。
1. コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) コマンドを使用します。

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault#az-keyvault-update) コマンドを使用します。

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 次の手順に従ってキーを作成します。
    1. 新しいキーを作成するには、 **[設定]** の下にある **[キー]** メニューから **[生成/インポート]** を選択します。
        
        ![[Generate/Import]\(生成/インポート\) ボタンの選択](./media/configure-customer-managed-key/select-generate-import.png)

    1. **[オプション]** を **[生成]** に設定し、キーの名前を付けます。

        ![キーの作成](./media/configure-customer-managed-key/create-key.png) 

    1. 次に、ドロップダウン リストからこのキーを選択して、暗号化用の Service Bus 名前空間に関連付けることができます。 

        ![Key Vault からのキーの選択](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > 冗長性を確保するために、最大 3 つのキーを追加できます。 いずれかのキーの有効期限が切れた場合、またはアクセスできない場合は、他のキーが暗号化に使用されます。
        
    1. キーの詳細を入力し、 **[選択]** をクリックします。 これにより、Microsoft マネージド キーを自分のキー (カスタマー マネージド キー) で暗号化できます。 


    > [!IMPORTANT]
    > カスタマー マネージド キー を geo ディザスター リカバリーと共に使用する場合は、このセクションを確認してください。 
    >
    > カスタマー マネージド キーを使用した Microsoft マネージド キーの暗号化を有効にするために、指定した Azure KeyVault の Service Bus のマネージド ID に対して[アクセス ポリシー](../key-vault/general/secure-your-key-vault.md)が設定されます。 これにより、Azure Service Bus 名前空間から Azure Key Vault へのアクセスが制御されます。
    >
    > これにより:
    > 
    >   * Service Bus 名前空間に対して [geo ディザスター リカバリー](service-bus-geo-dr.md)が既に有効になっているときに、カスタマー マネージド キーを有効化する場合は、以下を行います。 
    >     * ペアリングを解除します。
    >     * キー コンテナーに対して、プライマリとセカンダリの名前空間の両方のマネージド ID を対象とする[アクセス ポリシー を設定](../key-vault/general/assign-access-policy-portal.md)します。
    >     * プライマリ名前空間に暗号化を設定します。
    >     * プライマリとセカンダリの名前空間を再びペアリングします。
    > 
    >   * カスタマー マネージド キーが既に設定されている Service Bus 名前空間で Geo DR を有効にする場合は、以下を実行します。
    >     * キー コンテナーに対して、セカンダリ名前空間のマネージド ID を対象とする[アクセス ポリシー を設定](../key-vault/general/assign-access-policy-portal.md)します。
    >     * プライマリとセカンダリの名前空間をペアリングします。


## <a name="rotate-your-encryption-keys"></a>暗号化キーのローテーション

Azure Key Vault のローテーション メカニズムを使用して、キー コンテナー内のキーをローテーションできます。 アクティブ化と有効期限の日付を設定してキー ローテーションを自動化することもできます。 Service Bus サービスによって新しいキーのバージョンが検出され、それらの使用が自動的に開始されます。

## <a name="revoke-access-to-keys"></a>キーへのアクセスの取り消し

暗号化キーへのアクセスを取り消しても、Service Bus からデータが消去されることはありません。 ただし、Service Bus 名前空間からデータにアクセスすることはできません。 暗号化キーは、アクセス ポリシーを使用して、またはキーを削除することで取り消すことができます。 アクセス ポリシーと、キー コンテナーのセキュリティ保護の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)」を参照してください。

暗号化キーを取り消すと、暗号化されている名前空間で Service Bus サービスが機能しなくなります。 キーへのアクセスを有効にするか、削除済みのキーを復元すると、Service Bus サービスによってキーが選択され、暗号化された Service Bus 名前空間からデータにアクセスできるようになります。

## <a name="caching-of-keys"></a>キーのキャッシュ
Service Bus インスタンスでは、そのリストされた暗号化キーが 5 分ごとにポーリングされます。 それらは、次のポーリング (5 分後) までキャッシュされて使用されます。 少なくとも 1 つのキーが使用できる限り、キューとトピックにアクセスできます。 リストされたすべてのキーがポーリング時にアクセスできない場合は、すべてのキューとトピックが使用できなくなります。 

詳細を次に示します。 

- Service Bus サービスは、5 分ごとに名前空間のレコードに記載されているすべてのカスタマー マネージド キーをポーリングします。
    - キーがローテーションされている場合、このレコードは新しいキーで更新されます。
    - キーが取り消されている場合、そのキーはレコードから削除されます。
    - すべてのキーが取り消されている場合は、名前空間の暗号化状態が **[取り消し済み]** に設定されます。 その Service Bus 名前空間からはデータにアクセスできません。 
    

## <a name="use-resource-manager-template-to-enable-encryption"></a>Resource Manager テンプレートを使用して暗号化を有効にする
このセクションでは、**Azure Resource Manager テンプレート** を使用して次のタスクを実行する方法を示します。 

1. **マネージド サービス ID** がある **Premium** Service Bus 名前空間を作成する。
2. **キー コンテナー** を作成し、サービス ID にキー コンテナーへのアクセス権を付与する。 
3. キー コンテナー情報 (キー/値) を使用して、Service Bus 名前空間を更新する。 


### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>マネージド サービス ID がある Premium Service Bus 名前空間を作成する
このセクションでは、Azure Resource Manager テンプレートと PowerShell を使用して、マネージド サービス ID がある Azure Service Bus 名前空間を作成する方法について説明します。 

1. マネージド サービス ID がある Service Bus Premium レベルの名前空間を作成するための Azure Resource Manager テンプレートを作成します。 ファイルに次の名前を付けます。**CreateServiceBusPremiumNamespace.json** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 次の名前のテンプレート パラメーター ファイルを作成します。**CreateServiceBusPremiumNamespaceParams.json** 

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<ServiceBusNamespaceName>` - お使いの Service Bus 名前空間の名前
    > - `<Location>` - お使いの Service Bus 名前空間の場所

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. 次の PowerShell コマンドを実行して、Premium Service Bus 名前空間を作成するテンプレートをデプロイします。 次に、後で使用するために Service Bus 名前空間の ID を取得します。 コマンドを実行する前に、`{MyRG}` をリソース グループの名前に置き換えます。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Service Bus 名前空間の ID にキー コンテナーへのアクセス権を付与する

1. 次のコマンドを実行して、**消去保護** と **論理的な削除** が有効なキー コンテナーを作成します。 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    (または)
    
    次のコマンドを実行して、**既存のキー コンテナー** を更新します。 コマンドを実行する前に、リソース グループとキー コンテナー名の値を指定します。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. キー コンテナーのアクセス ポリシーを設定して、Service Bus 名前空間のマネージド ID で、キー コンテナー内のキー値にアクセスできるようにします。 前のセクションの Service Bus 名前空間の ID を使用します。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Service Bus 名前空間のデータをキー コンテナーのユーザーが管理するキーを使用して暗号化する
ここまで、次の手順を実行しました。 

1. マネージド ID がある Premium 名前空間を作成しました。
2. キー コンテナーを作成し、マネージド ID にキー コンテナーへのアクセス権を付与しました。 

この手順では、キー コンテナー情報を使用して、Service Bus 名前空間を更新します。 

1. 次の内容の **UpdateServiceBusNamespaceWithEncryption.json** という名前の JSON ファイルを作成します。 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. 次のテンプレート パラメーター ファイルを作成します。**UpdateServiceBusNamespaceWithEncryptionParams.json**

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<ServiceBusNamespaceName>` - お使いの Service Bus 名前空間の名前
    > - `<Location>` - お使いの Service Bus 名前空間の場所
    > - `<KeyVaultName>` - お使いのキー コンテナーの名前
    > - `<KeyName>` - キー コンテナー内のキーの名前  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. 次の PowerShell コマンドを実行して、Resource Manager テンプレートをデプロイします。 コマンドを実行する前に、`{MyRG}` をお使いのリソース グループの名前に置き換えます。 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```
    

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。
- [Service Bus の概要](service-bus-messaging-overview.md)
- [Key Vault の概要](../key-vault/general/overview.md)
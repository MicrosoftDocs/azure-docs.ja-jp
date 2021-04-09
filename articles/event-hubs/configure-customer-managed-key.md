---
title: Azure Event Hubs 保存データの暗号化用に独自のキーを構成する
description: この記事では、Azure Event Hubs の保存データを暗号化するために独自のキーを構成する方法について説明します。
ms.topic: conceptual
ms.date: 02/01/2021
ms.openlocfilehash: c9d1ac1c3a3387600fed80939598baafe658054b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595983"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure portal を使用して Azure Event Hubs 保存データの暗号化用にカスタマー マネージド キーを構成する
Azure Event Hubs では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Event Hubs サービスには、データを格納するために Azure Storage が使用されます。 Azure Storage に格納されているすべてのデータは、Microsoft マネージド キーを使用して暗号化されます。 独自のキー (Bring Your Own Key (BYOK) またはカスタマーマネージド キーとも呼ばれます) を使用する場合、データは引き続き Microsoft マネージド キーを使用して暗号化されますが、さらに Microsoft マネージド キーはカスタマー マネージド キーを使用して暗号化されます。 この機能を使用して、Microsoft マネージド キーの暗号化に使用されるカスタマー マネージド キーへの作成、ローテーション、無効化、およびアクセスの取り消しを実行できます。 BYOK 機能の有効化は、名前空間での 1 回限りのセットアップ プロセスです。

> [!NOTE]
> - BYOK 機能は [Event Hubs Dedicated のシングルテナント](event-hubs-dedicated-overview.md) クラスターでサポートされています。 Standard Event Hubs 名前空間用に有効にすることはできません。
> - 暗号化は、新規または空の名前空間に対してのみ有効にすることができます。 名前空間にイベント ハブが含まれている場合、暗号化操作は失敗します。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使用できます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/general/overview.md)

この記事では、Azure portal を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault を作成する](../key-vault/general/quick-create-portal.md)」を参照します。

> [!IMPORTANT]
> Azure Event Hubs でカスタマー マネージド キーを使うには、キー コンテナーに 2 つの必須プロパティが構成されている必要があります。 これらは次のとおりです。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする
Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. Event Hubs Dedicated クラスターに移動します。
1. BYOK を有効にする名前空間を選択します。
1. Event Hubs 名前空間の **[設定]** ページで、 **[暗号化]** を選択します。 
1. 次の図に示すように、**カスタマー マネージド キーによる保存中の暗号化** を選択します。 

    ![カスタマー マネージド キーの有効化](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>キーを使用したキー コンテナーの設定
カスタマー マネージド キーを有効にした後、カスタマー マネージド キーを Azure Event Hubs 名前空間に関連付ける必要があります。 Event Hubs では Azure Key Vault のみがサポートされています。 前のセクションで **カスタマー マネージド キーによる暗号化** のオプションを有効にした場合は、キーが Azure Key Vault にインポートされている必要があります。 また、キーに対して **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** が構成されている必要があります。 これらの設定は、[PowerShell](../key-vault/general/key-vault-recovery.md) または [CLI](../key-vault/general/key-vault-recovery.md) を使用して構成できます。

1. 新しいキー コンテナーを作成するには、Azure Key Vault の[クイック スタート](../key-vault/general/overview.md)に従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/general/about-keys-secrets-certificates.md)」を参照してください。
1. コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) コマンドを使用します。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault#az-keyvault-update) コマンドを使用します。

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. 次の手順に従ってキーを作成します。
    1. 新しいキーを作成するには、 **[設定]** の下にある **[キー]** メニューから **[生成/インポート]** を選択します。
        
        ![[Generate/Import]\(生成/インポート\) ボタンの選択](./media/configure-customer-managed-key/select-generate-import.png)
    1. **[オプション]** を **[生成]** に設定し、キーの名前を付けます。

        ![キーの作成](./media/configure-customer-managed-key/create-key.png) 
    1. 次に、ドロップダウン リストからこのキーを選択して、暗号化用の Event Hubs 名前空間に関連付けることができます。 

        ![Key Vault からのキーの選択](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. キーの詳細を入力し、 **[選択]** をクリックします。 これにより、Microsoft マネージド キーを自分のキー (カスタマー マネージド キー) で暗号化できるようになります。 


## <a name="rotate-your-encryption-keys"></a>暗号化キーのローテーション
Azure Key Vault のローテーション メカニズムを使用して、キー コンテナー内のキーをローテーションできます。 アクティブ化と有効期限の日付を設定してキー ローテーションを自動化することもできます。 Event Hubs サービスによって新しいキーのバージョンが検出され、自動的に使用が開始されます。

## <a name="revoke-access-to-keys"></a>キーへのアクセスの取り消し
暗号化キーへのアクセスを取り消しても、Event Hubs からデータが消去されることはありません。 ただし、Event Hubs 名前空間からデータへのアクセスはできません。 暗号化キーは、アクセス ポリシーを使用して、またはキーを削除することで取り消すことができます。 アクセス ポリシーと、キー コンテナーのセキュリティ保護の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/secure-your-key-vault.md)」を参照してください。

暗号化キーを取り消すと、暗号化した名前空間で Event Hubs サービスが機能しなくなります。 キーへのアクセスを有効にするか、削除キーを復元すると、Event Hubs サービスによってキーが選択され、暗号化した Event Hubs 名前空間からデータにアクセスできるようになります。

## <a name="set-up-diagnostic-logs"></a>診断ログの設定 
BYOK が有効な名前空間の診断ログを設定すると、操作に関する必要な情報が得られます。 これらのログを有効にして、後からイベント ハブにストリーミングしたり、ログ分析を使用して分析したり、ストレージにストリーミングしてカスタマイズした分析を実行したりできます。 診断ログの詳細については、「[Azure 診断ログの概要](../azure-monitor/essentials/platform-logs-overview.md)」を参照してください。

## <a name="enable-user-logs"></a>ユーザー ログの有効化
カスタマー マネージド キーのログを有効にするには、次の手順に従います。

1. Azure portal 上で、BYOK が有効になっている名前空間に移動します。
1. **[監視]** の **[診断設定]** を選択します。

    ![[診断設定] の選択](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **[+ 診断設定の追加]** を選択します。 

    ![[診断設定の追加] の選択](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. **名前** を指定し、ログのストリーミング先となる場所を選択します。
1. **[CustomerManagedKeyUserLogs]** を選択し、 **[保存]** を選択します。 この操作により、名前空間で BYOK のログが有効になります。

    ![カスタマー マネージド キーのユーザー ログ オプションの選択](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>ログのスキーマ 
すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下の表で説明している形式を使用する文字列フィールドがあります。 

| 名前 | 説明 |
| ---- | ----------- | 
| TaskName | 失敗したタスクの説明。 |
| ActivityId | 追跡のために使用される内部 ID。 |
| category | タスクの分類を定義します。 たとえば、キー コンテナーのキーが無効になっている場合は、情報カテゴリになります。または、キーをラップ解除できない場合は、エラーが発生する可能性があります。 |
| resourceId | Azure Resource Manager リソース ID |
| keyVault | キー コンテナーの完全名。 |
| key | Event Hubs 名前空間の暗号化に使用されるキー名。 |
| version | 使用されているキーのバージョン。 |
| operation | キー コンテナー内のキーに対して実行される操作。 たとえば、キーの無効化/有効化、ラップ、またはラップ解除 |
| code | 操作に関連付けられたコード。 例:エラー コード 404 は、キーが見つからなかったことを示します。 |
| message | 操作に関連付けられたエラー メッセージ |

カスタマー マネージド キーのログの例を次に示します。

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="use-resource-manager-template-to-enable-encryption"></a>Resource Manager テンプレートを使用して暗号化を有効にする
このセクションでは、**Azure Resource Manager テンプレート** を使用して次のタスクを実行する方法を示します。 

1. マネージド サービス ID がある **Event Hubs 名前空間** を作成する。
2. **キー コンテナー** を作成し、サービス ID にキー コンテナーへのアクセス権を付与する。 
3. キー コンテナー情報 (キー/値) を使用して、Event Hubs 名前空間を更新する。 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>マネージド サービス ID がある Event Hubs クラスターと名前空間を作成する。
このセクションでは、Azure Resource Manager テンプレートと PowerShell を使用して、マネージド サービス ID がある Azure Event Hubs 名前空間を作成する方法について説明します。 

1. マネージド サービス ID がある Azure Event Hubs 名前空間を作成するための Azure Resource Manager テンプレートを作成します。 ファイルに次の名前を付けます。**CreateEventHubClusterAndNamespace.json** 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. 次の名前のテンプレート パラメーター ファイルを作成します。**CreateEventHubClusterAndNamespaceParams.json** 

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<EventHubsClusterName>` - お使いの Event Hubs クラスターの名前    
    > - `<EventHubsNamespaceName>` - お使いの Event Hubs 名前空間の名前
    > - `<Location>` - お使いの Event Hubs 名前空間の場所

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. 次の PowerShell コマンドを実行して、Event Hubs 名前空間を作成するテンプレートをデプロイします。 次に、後で使用するために Event Hubs 名前空間の ID を取得します。 コマンドを実行する前に、`{MyRG}` をリソース グループの名前に置き換えます。  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Event Hubs 名前空間の ID にキー コンテナーへのアクセス権を付与する

1. 次のコマンドを実行して、**消去保護** と **論理的な削除** が有効なキー コンテナーを作成します。 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    (または)    
    
    次のコマンドを実行して、**既存のキー コンテナー** を更新します。 コマンドを実行する前に、リソース グループとキー コンテナー名の値を指定します。 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. キー コンテナーのアクセス ポリシーを設定して、Event Hubs 名前空間のマネージド ID で、キー コンテナー内のキー値にアクセスできるようにします。 前のセクションの Event Hubs 名前空間の ID を使用します。 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Event Hubs 名前空間のデータをキー コンテナーのユーザーが管理するキーを使用して暗号化する
ここまで、次の手順を実行しました。 

1. マネージド ID がある Premium 名前空間を作成しました。
2. キー コンテナーを作成し、マネージド ID にキー コンテナーへのアクセス権を付与しました。 

この手順では、キー コンテナー情報を使用して、Event Hubs 名前空間を更新します。 

1. 次の内容の **CreateEventHubClusterAndNamespace.json** という名前の JSON ファイルを作成します。 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
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
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
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

2. 次のテンプレート パラメーター ファイルを作成します。**UpdateEventHubClusterAndNamespaceParams.json** 

    > [!NOTE]
    > 次の値を置き換えます。 
    > - `<EventHubsClusterName>` - お使いの Event Hubs クラスターの名前        
    > - `<EventHubsNamespaceName>` - お使いの Event Hubs 名前空間の名前
    > - `<Location>` - お使いの Event Hubs 名前空間の場所
    > - `<KeyVaultName>` - お使いのキー コンテナーの名前
    > - `<KeyName>` - キー コンテナー内のキーの名前

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="troubleshoot"></a>トラブルシューティング
ベスト プラクティスとして、前のセクションで示したように、ログは常に有効にしてください。 BYOK 暗号化が有効になっている場合にアクティビティを追跡するのに役立ちます。 また、問題の範囲を絞り込むのにも役立ちます。

BYOK 暗号化が有効になっている場合に調べる一般的なエラー コードを次に示します。

| アクション | エラー コード | 結果のデータの状態 |
| ------ | ---------- | ----------------------- | 
| キー コンテナーからラップ/ラップ解除のアクセス許可を削除する | 403 |    Inaccessible |
| ラップ/ラップ解除のアクセス許可を付与された AAD プリンシパルから AAD ロールのメンバーシップを削除する | 403 |  Inaccessible |
| キー コンテナーから暗号化キーを削除する | 404 | Inaccessible |
| キー コンテナーを削除する | 404 | アクセス不可 (必須の設定である、論理的な削除が有効になっていることが前提) |
| 暗号化キーの有効期限を既に経過した期限に変更する | 403 |   Inaccessible  |
| キー暗号化キーがアクティブでなくなるように NBF (期間の開始時刻) を変更する | 403 | Inaccessible  |
| キー コンテナー ファイアウォールに対して **[Allow MSFT Services]\(MSFT サービスを許可する\)** オプションを選択するか、暗号化キーを使用しているキー コンテナーへのネットワーク アクセスをブロックする | 403 | Inaccessible |
| キーコンテナーを別のテナントに移動する | 404 | Inaccessible |  
| 断続的なネットワークの問題または DNS/AAD/MSI の停止 |  | キャッシュされたデータ暗号化キーを使用してアクセス可能 |

> [!IMPORTANT]
> BYOK 暗号化を使用している名前空間で Geo-DR を有効にするには、ペアリング対象のセカンダリ名前空間が専用クラスターに存在し、システムによって割り当てられたマネージド ID がそのクラスター上で有効になっている必要があります。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。
- [Event Hubs の概要](event-hubs-about.md)
- [Key Vault の概要](../key-vault/general/overview.md)
---
title: マネージド ID Azure Stream Analytics による BLOB 出力の認証
description: この記事では、マネージド ID を使用して、Azure Blob Storage 出力に対して Azure Stream Analytics ジョブを認証する方法について説明します。
author: kim-ale
ms.author: kimal
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 369348133f7395f5db5b5923bd438cec8e4ad733
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954380"
---
# <a name="use-managed-identity-preview-to-authenticate-your-azure-stream-analytics-job-to-azure-blob-storage"></a>マネージド ID (プレビュー) を使用して、Azure Blob Storage に対して Azure Stream Analytics ジョブを認証する

Azure Blob Storage への出力に対して[マネージド ID 認証](../active-directory/managed-identities-azure-resources/overview.md) (プレビュー) を使用すると、Stream Analytics ジョブで、接続文字列を使用せずに、ストレージ アカウントに直接アクセスできます。 この機能により、セキュリティが向上し、さらに Azure 内の仮想ネットワーク (VNET) のストレージ アカウントにデータを書き込むことができます。

この記事では、Azure portal を通じて、および Azure Resource Manager デプロイを通じて、Stream Analytics ジョブの Blob 出力に対してマネージド ID を有効にする方法を示します。

## <a name="create-the-stream-analytics-job-using-the-azure-portal"></a>Azure portal を使用して Stream Analytics ジョブを作成する

1. Azure portal で、新しい Stream Analytics ジョブを作成するか、既存のジョブを開きます。 画面の左側にあるメニュー バーで、 **[構成]** の下にある **[マネージド ID]** を選択します。 [システム割り当てマネージド ID を使用] が選択されていることを確認し、画面下部にある **[保存]** ボタンをクリックします。

   ![Stream Analytics のマネージド ID の構成](./media/common/stream-analytics-enable-managed-identity.png)

2. Azure Blob Storage 出力シンクの [出力プロパティ] ウィンドウで、[認証モード] ドロップダウンを選択し、 **[マネージド ID]** を選択します。 その他の出力プロパティについて詳しくは、「[Azure Stream Analytics からの出力を理解する](./stream-analytics-define-outputs.md)」を参照してください。 操作が終了したら、 **[OK]** をクリックします。

   ![Azure Blob Storage 出力を構成する](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-blob-output-blade.png)

3. ジョブが作成されたので、この記事の「[Stream Analytics ジョブにストレージ アカウントへのアクセス権を付与する](#give-the-stream-analytics-job-access-to-your-storage-account)」セクションを参照してください。

## <a name="azure-resource-manager-deployment"></a>Azure Resource Manager デプロイ

Azure Resource Manager を使用すると、Stream Analytics ジョブのデプロイを完全に自動化できます。 Azure PowerShell または [Azure CLI](/cli/azure/) を使用して、Resource Manager テンプレートをデプロイできます。 次の例では、Azure CLI を使用しています。


1. Resource Manager テンプレートのリソース セクションに次のプロパティを含めることで、マネージド ID を持つ **Microsoft.StreamAnalytics/streamingjobs** リソースを作成できます。

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   このプロパティにより、Stream Analytics ジョブの ID を作成し、管理するように Azure Resource Manager に通知されます。 次に示すのは、マネージド ID が有効になった Stream Analytics ジョブと、マネージド ID を使用する Blob 出力シンクをデプロイする Resource Manager テンプレートの例です。

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "MyStreamingJob",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "serialization": {
                                    "type": "JSON",
                                    "properties": {
                                        "encoding": "UTF8"
                                    }
                                },
                                "datasource":{
                                    "type":"Microsoft.Storage/Blob",
                                    "properties":{
                                        "storageAccounts": [
                                            { "accountName": "MyStorageAccount" }
                                        ],
                                        "container": "test",
                                        "pathPattern": "segment1/{date}/segment2/{time}",
                                        "dateFormat": "yyyy/MM/dd",
                                        "timeFormat": "HH",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    次の Azure CLI コマンドを使用して、上記のジョブをリソース グループ **ExampleGroup** にデプロイできます。

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. ジョブが作成された後、Azure Resource Manager を使用して、そのジョブの完全な定義を取得できます。

    ```azurecli
    az resource show --ids /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/StreamingJobs/{RESOURCE_NAME}
    ```

    上記のコマンドでは、次のような応答が返されます。

    ```json
    {
        "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.StreamAnalytics/streamingjobs/{RESOURCE_NAME}",
        "identity": {
            "principalId": "{PRINCIPAL_ID}",
            "tenantId": "{TENANT_ID}",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "{RESOURCE_NAME}",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "{JOB_ID}",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "{RESOURCE_GROUP}",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

   ジョブの定義から **principalId** を書き留めておきます。これは、Azure Active Directory 内でのジョブのマネージド ID を示し、Stream Analytics ジョブにストレージ アカウントへのアクセス権を付与する次の手順で使用します。

3. ジョブが作成されたので、この記事の「[Stream Analytics ジョブにストレージ アカウントへのアクセス権を付与する](#give-the-stream-analytics-job-access-to-your-storage-account)」セクションを参照してください。


## <a name="give-the-stream-analytics-job-access-to-your-storage-account"></a>Stream Analytics ジョブにストレージ アカウントへのアクセス権を付与する

Stream Analytics ジョブには、次の 2 つのレベルのアクセス権のいずれかを付与できます。

1. **コンテナー レベルのアクセス権:** このオプションでは、既存の特定コンテナーへのアクセス権がジョブに付与されます。
2. **アカウント レベルのアクセス権:** このオプションでは、ストレージ アカウントへの一般的なアクセス権がジョブに付与され、新しいコンテナーの作成など行うことができます。

自分のためにコンテナーを作成するためのジョブが必要でない限り、**コンテナー レベルのアクセス権** を選択してください。これは、このオプションでは、必要な最小レベルのアクセス権がジョブに付与されるためです。 Azure portal とコマンド ラインでの両方のオプションについて、以下で説明します。

### <a name="grant-access-via-the-azure-portal"></a>Azure portal を使用してアクセス権を付与する

#### <a name="container-level-access"></a>コンテナー レベルのアクセス権

1. ストレージ アカウント内のコンテナーの構成ウィンドウに移動します。

2. 左側で **[アクセス制御 (IAM)]** を選択します。

3. [ロールの割り当てを追加する] セクションで、 **[追加]** をクリックします。

4. [ロールの割り当て] ウィンドウで、次のようにします。

    1. **[ロール]** を [ストレージ BLOB データ共同作成者] に設定します。
    2. **[アクセスの割り当て先]** ドロップダウンが [Azure AD のユーザー、グループ、サービス プリンシパル] に設定されていることを確認します。
    3. 検索フィールドに、ご使用の Stream Analytics ジョブの名前を入力します。
    4. ご使用の Stream Analytics ジョブを選択し、 **[保存]** をクリックします。

   ![コンテナー アクセス権を付与する](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-container-access-portal.png)

#### <a name="account-level-access"></a>アカウント レベルのアクセス権

1. ストレージ アカウントに移動します。

2. 左側で **[アクセス制御 (IAM)]** を選択します。

3. [ロールの割り当てを追加する] セクションで、 **[追加]** をクリックします。

4. [ロールの割り当て] ウィンドウで、次のようにします。

    1. **[ロール]** を [ストレージ BLOB データ共同作成者] に設定します。
    2. **[アクセスの割り当て先]** ドロップダウンが [Azure AD のユーザー、グループ、サービス プリンシパル] に設定されていることを確認します。
    3. 検索フィールドに、ご使用の Stream Analytics ジョブの名前を入力します。
    4. ご使用の Stream Analytics ジョブを選択し、 **[保存]** をクリックします。

   ![アカウント アクセス権を付与する](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-account-access-portal.png)

### <a name="grant-access-via-the-command-line"></a>コマンド ラインを使用してアクセス権を付与する

#### <a name="container-level-access"></a>コンテナー レベルのアクセス権

特定のコンテナーへのアクセス権を付与するには、Azure CLI を使用して次のコマンドを実行します。

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
   ```

#### <a name="account-level-access"></a>アカウント レベルのアクセス権

アカウント全体へのアクセス権を付与するには、Azure CLI を使用して次のコマンドを実行します。

   ```azurecli
   az role assignment create --role "Storage Blob Data Contributor" --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
   ```

## <a name="enable-vnet-access"></a>VNET アクセスを有効にする

ストレージ アカウントの **[ファイアウォールと仮想ネットワーク]** を構成する場合、必要に応じて、他の信頼された Microsoft サービスからのネットワーク トラフィックを許可できます。 Stream Analytics は、マネージド ID を使用して認証を行う場合、要求が信頼されたサービスから発信されていることを証明します。 この VNET アクセスの例外を有効にする手順を次に示します。

1.    ストレージ アカウントの構成ペイン内の [ファイアウォールと仮想ネットワーク] ペインに移動します。
2.    [信頼された Microsoft サービスによるこのストレージ アカウントに対するアクセスを許可します] オプションが有効になっていることを確認します。
3.    これを有効にした場合、 **[保存]** をクリックします。

   ![VNET アクセスを有効にする](./media/stream-analytics-managed-identities-blob-output-preview/stream-analytics-vnet-exception.png)

## <a name="remove-managed-identity"></a>マネージド ID を削除する

Stream Analytics ジョブに対して作成されたマネージド ID は、ジョブが削除されたときにのみ削除されます。 ジョブを削除せずにマネージド ID を削除することはできません。 マネージド ID を使用する必要がなくなった場合は、出力の認証方法を変更できます。 マネージド ID は、ジョブが削除されるまで存在し続け、マネージド ID の認証を再度使用する場合に使用されます。

## <a name="limitations"></a>制限事項
この機能の現在の制限は次のとおりです。

1. 従来の Azure Storage アカウント。

2. Azure Active Directory なしの Azure アカウント。

3. マルチテナント アクセスは、サポートされていません。 特定の Stream Analytics ジョブに対して作成されたサービス プリンシパルは、ジョブが作成された同じ Azure Active Directory テナントに配置する必要があり、別の Azure Active Directory テナントに配置されたリソースでは使用できません。

4. [ユーザー割り当て ID](../active-directory/managed-identities-azure-resources/overview.md) はサポートされていません。 つまりユーザーは、Stream Analytics ジョブで使用される、独自のサービス プリンシパルを入力することはできません。 サービス プリンシパルは、Azure Stream Analytics で生成する必要があります。

## <a name="next-steps"></a>次のステップ

* [Azure Stream Analytics からの出力を理解する](./stream-analytics-define-outputs.md)
* [Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割](./stream-analytics-custom-path-patterns-blob-storage-output.md)

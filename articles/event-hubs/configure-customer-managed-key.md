---
title: Azure Event Hubs 保存データの暗号化用に独自のキーを構成する
description: この記事では、Azure Event Hubs の保存データを暗号化するために独自のキーを構成する方法について説明します。
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 37ca2b655d30ffd330d5430da20d07d9548a7c84
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260871"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Azure portal を使用して Azure Event Hubs 保存データの暗号化用にカスタマー マネージド キーを構成する
Azure Event Hubs では、Azure Storage Service Encryption (Azure SSE) による保存データの暗号化が提供されます。 Event Hubs では、データを格納するために Azure Storage が使用されます。既定では、Azure Storage を使用して格納されるすべてのデータは、Microsoft のマネージド キーを使用して暗号化されます。 

## <a name="overview"></a>概要
Azure Event Hubs で、Microsoft のマネージド キーまたはカスタマー マネージド キー (Bring Your Own Key – BYOK) を使用した保存データ暗号化のオプションがサポートされるようになりました。 この機能を使用すると、Azure Event Hubs の保存データ暗号化に使用されるカスタマー マネージド キーへのアクセスの作成、ローテーション、無効化、取り消しができます。

BYOK 機能の有効化は、名前空間での 1 回限りのセットアップ プロセスです。

> [!NOTE]
> BYOK 機能は [Event Hubs Dedicated のシングルテナント](event-hubs-dedicated-overview.md) クラスターでサポートされています。 Standard Event Hubs 名前空間用に有効にすることはできません。

キーの管理およびキーの使用状況の監査には、Azure Key Vault を使用できます。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/key-vault-overview.md)

この記事では、Azure portal を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う」(../key-vault/quick-create-portal.md) を参照してください。

> [!IMPORTANT]
> Azure Event Hubs でカスタマー マネージド キーを使うには、キー コンテナーに 2 つの必須プロパティが構成されている必要があります。 次に例を示します。 **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** です。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする
Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. Event Hubs Dedicated クラスターに移動します。
1. BYOK を有効にする名前空間を選択します。
1. Event Hubs 名前空間の **[設定]** ページで、 **[暗号化 (プレビュー)]** を選択します。 
1. 次の図に示すように、**カスタマー マネージド キーによる保存中の暗号化**を選択します。 

    ![カスタマー マネージド キーの有効化](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>キーを使用したキー コンテナーの設定
カスタマー マネージド キーを有効にした後、カスタマー マネージド キーを Azure Event Hubs 名前空間に関連付ける必要があります。 Event Hubs では Azure Key Vault のみがサポートされています。 前のセクションで**カスタマー マネージド キーによる暗号化**のオプションを有効にした場合は、キーが Azure Key Vault にインポートされている必要があります。 また、キーに対して **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** が構成されている必要があります。 これらの設定は、[PowerShell](../key-vault/key-vault-soft-delete-powershell.md) または [CLI](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection) を使用して構成できます。

1. 新しいキー コンテナーを作成するには、Azure Key Vault の[クイック スタート](../key-vault/key-vault-overview.md)に従ってください。 既存のキーをインポートする方法については、「[キー、シークレット、証明書について](../key-vault/about-keys-secrets-and-certificates.md)」を参照してください。
1. コンテナーの作成時、論理的な削除と消去保護の両方をオンにするには、[az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) コマンドを使用します。

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. 既存のコンテナー (論理的な削除が既に有効になっている) に消去保護を追加するには、[az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) コマンドを使用します。

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
    1. キーの詳細を入力し、 **[選択]** をクリックします。 これにより、カスタマー マネージド キーを使用して、名前空間の保存データを暗号化できるようになります。 

        > [!NOTE]
        > プレビューでは、キーを 1 つだけ選択できます。 

## <a name="rotate-your-encryption-keys"></a>暗号化キーのローテーション
Azure Key Vault のローテーション メカニズムを使用して、キー コンテナー内のキーをローテーションできます。 詳細については、[キー ローテーションと監査を設定する](../key-vault/key-vault-key-rotation-log-monitoring.md)方法に関するページを参照してください。 アクティブ化と有効期限の日付を設定してキー ローテーションを自動化することもできます。 Event Hubs サービスによって新しいキーのバージョンが検出され、自動的に使用が開始されます。

## <a name="revoke-access-to-keys"></a>キーへのアクセスの取り消し
暗号化キーへのアクセスを取り消しても、Event Hubs からデータが消去されることはありません。 ただし、Event Hubs 名前空間からデータへのアクセスはできません。 暗号化キーは、アクセス ポリシーを使用して、またはキーを削除することで取り消すことができます。 アクセス ポリシーと、キー コンテナーのセキュリティ保護の詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/key-vault-secure-your-key-vault.md)」を参照してください。

暗号化キーを取り消すと、暗号化した名前空間で Event Hubs サービスが機能しなくなります。 キーへのアクセスを有効にするか、削除キーを復元すると、Event Hubs サービスによってキーが選択され、暗号化した Event Hubs 名前空間からデータにアクセスできるようになります。

> [!NOTE]
> 既存の暗号化キーをご自身のキー コンテナーから削除し、Event Hubs 名前空間の新しいキーに置き換えた場合、削除キーは (キャッシュされているため) 最大 1 時間は引き続き有効です。そのため、新しいキーを使用してのみアクセスできる新しいデータと共に、古いデータ (古いキーで暗号化したデータ) に引き続きアクセスできる可能性があります。 この動作は、プレビュー バージョンの機能の設計によるものです。 

## <a name="set-up-diagnostic-logs"></a>診断ログの設定 
BYOK が有効になっている名前空間に対して診断ログを設定すると、カスタマー マネージド キーを使用して名前空間を暗号化した場合に、操作に関する必要な情報を得ることができます。 これらのログを有効にして、後からイベント ハブにストリーミングしたり、ログ分析を使用して分析したり、ストレージにストリーミングしてカスタマイズした分析を実行したりできます。 診断ログの詳細については、「[Azure 診断ログの概要](../azure-monitor/platform/resource-logs-overview.md)」を参照してください。

## <a name="enable-user-logs"></a>ユーザー ログの有効化
カスタマー マネージド キーのログを有効にするには、次の手順に従います。

1. Azure portal 上で、BYOK が有効になっている名前空間に移動します。
1. **[監視]** の **[診断設定]** を選択します。

    ![[診断設定] の選択](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. **[+ 診断設定の追加]** を選択します。 

    ![[診断設定の追加] の選択](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. **名前**を指定し、ログのストリーミング先となる場所を選択します。
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

## <a name="troubleshoot"></a>トラブルシューティング
ベスト プラクティスとして、前のセクションで示したように、ログは常に有効にしてください。 BYOK 暗号化が有効になっている場合にアクティビティを追跡するのに役立ちます。 また、問題の範囲を絞り込むのにも役立ちます。

BYOK 暗号化が有効になっている場合に調べる一般的なエラー コードを次に示します。

| Action | エラー コード | 結果のデータの状態 |
| ------ | ---------- | ----------------------- | 
| キー コンテナーからラップ/ラップ解除のアクセス許可を削除する | 403 |    アクセス不可 |
| ラップ/ラップ解除のアクセス許可を付与された AAD プリンシパルから AAD ロールのメンバーシップを削除する | 403 |  アクセス不可 |
| キー コンテナーから暗号化キーを削除する | 404 | アクセス不可 |
| キー コンテナーを削除する | 404 | アクセス不可 (必須の設定である、論理的な削除が有効になっていることが前提) |
| 暗号化キーの有効期限を既に経過した期限に変更する | 403 |   アクセス不可  |
| キー暗号化キーがアクティブでなくなるように NBF (期間の開始時刻) を変更する | 403 | アクセス不可  |
| キー コンテナー ファイアウォールに対して **[Allow MSFT Services]\(MSFT サービスを許可する\)** オプションを選択するか、暗号化キーを使用しているキー コンテナーへのネットワーク アクセスをブロックする | 403 | アクセス不可 |
| キーコンテナーを別のテナントに移動する | 404 | アクセス不可 |  
| 断続的なネットワークの問題または DNS/AAD/MSI の停止 |  | キャッシュされたデータ暗号化キーを使用してアクセス可能 |

> [!IMPORTANT]
> BYOK 暗号化を使用している名前空間で Geo-DR を有効にするには、ペアリング対象のセカンダリ名前空間が専用クラスターに存在し、システムによって割り当てられたマネージド ID がそのクラスター上で有効になっている必要があります。 詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

> [!NOTE]
> Event Hubs 名前空間の Azure Key Vault に仮想ネットワーク (VNet) サービスのエンドポイントが構成されている場合、BYOK はサポートされません。 


## <a name="next-steps"></a>次の手順
次の記事を参照してください。
- [Event Hubs の概要](event-hubs-about.md)
- [Key Vault の概要](../key-vault/key-vault-overview.md)





---
title: 受信要求に必要な最小バージョンのトランスポート層セキュリティ (TLS) を適用する
titleSuffix: Azure Storage
description: クライアントで Azure Storage に対して要求を行う場合に最小バージョンのトランスポート層セキュリティ (TLS) を要求するように、ストレージ アカウントを構成します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/11/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 257cd8dce2a080203f116a6f0d5b7c7ebd6d13f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593178"
---
# <a name="enforce-a-minimum-required-version-of-transport-layer-security-tls-for-requests-to-a-storage-account"></a>ストレージ アカウントへの要求に必要な最小バージョンのトランスポート層セキュリティ (TLS) を適用する

クライアント アプリケーションと Azure Storage アカウントの間の通信は、トランスポート層セキュリティ (TLS) を使用して暗号化されます。 TLS は、インターネットを介してクライアントとサービスの間のプライバシーおよびデータ整合性を確保する標準の暗号化プロトコルです。 TLS の詳細については、「[Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)」を参照してください。

現在、Azure Storage では、次の 3 つのバージョンの TLS プロトコルがサポートされています: 1.0、1.1、および 1.2。 Azure Storage では、パブリック HTTPS エンドポイントに対して TLS 1.2 が使用されますが、下位互換性のために TLS 1.0 と TLS 1.1 もまだサポートされています。

既定では、Azure Storage アカウントで、クライアントによる最も古いバージョンの TLS、TLS 1.0、およびそれ以降でのデータの送受信が許可されます。 より厳密なセキュリティ対策を実施するために、クライアントで新しいバージョンの TLS を使用してデータを送受信することを要求するように、ストレージ アカウントを構成することができます。 ストレージ アカウントで最小バージョンの TLS が要求されている場合、それより古いバージョンで行われた要求はすべて失敗します。

この記事では、DRAG (検出-修復-監査-ガバナンス) フレームワークを使用して、ストレージ アカウントに対するセキュリティで保護された TLS を継続的に管理する方法について説明します。

クライアント アプリケーションから要求を送信するときに特定のバージョンの TLS を指定する方法については、「[クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)」を参照してください。

## <a name="detect-the-tls-version-used-by-client-applications"></a>クライアント アプリケーションによって使用される TLS のバージョンを検出する

ストレージ アカウントに対して TLS の最小バージョンを適用すると、それより古いバージョンの TLS を使用してデータを送信するクライアントからの要求が拒否される危険があります。 TLS の最小バージョンの構成がクライアント アプリケーションにどのように影響する可能性があるかを理解するために、Microsoft では、Azure Storage アカウントのログ記録を有効にし、一定期間後にログを分析して、クライアント アプリケーションによって使用される TLS のバージョンを検出することをお勧めします。

Azure Storage アカウントに対する要求をログに記録し、クライアントによって使用される TLS のバージョンを特定するために、Azure Monitor の Azure Storage ログ記録 (プレビュー) を使用することができます。 詳細については、「[Azure Storage を監視する](../blobs/monitor-blob-storage.md)」を参照してください。

Azure Monitor の Azure Storage ログ記録では、ログ クエリを使用したログ データの分析がサポートされています。 ログに対してクエリを実行するために、Azure Log Analytics ワークスペースを使用できます。 ログ クエリの詳細については、「[チュートリアル: Log Analytics クエリの使用方法](../../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

Azure Monitor で Azure Storage のデータをログに記録し、Azure Log Analytics で分析するには、まず、データをログに記録する要求の種類とストレージ サービスを示す診断設定を作成する必要があります。 Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブルに対してログが有効になります。 Azure portal で診断設定を作成するには、これらの手順に従います。

1. ご利用の Azure Storage アカウントが含まれるサブスクリプションに、新しい Log Analytics ワークスペースを作成します。 ストレージ アカウントのログ記録を構成した後、Log Analytics ワークスペースでログを使用できるようになります。 詳細については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/logs/quick-create-workspace.md)」を参照してください。
1. Azure Portal のストレージ アカウントに移動します。
1. [監視] セクションで、 **[診断設定 (プレビュー)]** を選択します。
1. 要求をログに記録する Azure Storage サービスを選択します。 たとえば、Blob Storage に対する要求をログに記録するには、 **[Blob]** を選択します。
1. **[診断設定の追加]** を選択します。
1. 診断設定の名前を指定します。
1. **[カテゴリの詳細]** の **[ログ]** セクションで、ログを記録する要求の種類を選択します。 読み取り、書き込み、および削除要求をログに記録できます。 たとえば、**StorageRead** と **StorageWrite** を選択すると、選択されたサービスへの読み取りおよび書き込み要求がログに記録されます。
1. **[宛先の詳細]** で、 **[Log Analytics への送信]** を選択します。 以下の図に示すように、ご利用のサブスクリプションと、先ほど作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/create-diagnostic-setting-logs.png" alt-text="要求のログを記録するための診断設定の作成方法を示すスクリーンショット":::

診断設定を作成した後、ストレージ アカウントに対する要求が、その設定に従ってログに記録されるようになります。 詳細については、[Azure でリソース ログとメトリックを収集するための診断設定の作成](../../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。

Azure Monitor の Azure Storage ログで使用できるフィールドのリファレンスについては、「[リソース ログ (プレビュー)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview)」を参照してください。

### <a name="query-logged-requests-by-tls-version"></a>TLS バージョンでログに記録された要求に対してクエリを実行する

Azure Monitor の Azure Storage ログには、ストレージ アカウントに要求を送信するために使用される TLS のバージョンが含まれます。 **TlsVersion** プロパティを使用して、ログに記録された要求の TLS のバージョンを確認します。

過去 7 日間に、異なるバージョンの TLS で Blob Storage に対して行われた要求の数を特定するには、Log Analytics ワークスペースを開きます。 次に、以下のクエリを新しいログ クエリに貼り付けて実行します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>"
| summarize count() by TlsVersion
```

結果には、各バージョンの TLS で行われた要求の数が表示されます。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/log-analytics-query-version.png" alt-text="TLS のバージョンを返す Log Analytics クエリの結果を示すスクリーンショット":::

### <a name="query-logged-requests-by-caller-ip-address-and-user-agent-header"></a>呼び出し元の IP アドレスとユーザー エージェント ヘッダーでログに記録された要求に対してクエリを実行する

Azure Monitor の Azure Storage ログには、ストレージ アカウントにアクセスしたクライアント アプリケーションを評価するのに役立つ、呼び出し元の IP アドレスとユーザー エージェント ヘッダーも含まれます。 これらの値を分析して、新しいバージョンの TLS を使用するようにクライアント アプリケーションを更新する必要があるかどうか、または、TLS の最小バージョンで送信されない場合にクライアントの要求を失敗させることを受け入れられるかどうかを判断できます。

過去 7 日間に、TLS 1.2 より古いバージョンの TLS で要求を行ったクライアントを特定するには、新しいログ クエリに次のクエリを貼り付けて実行します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AccountName == "<account-name>" and TlsVersion != "TLS 1.2"
| project TlsVersion, CallerIpAddress, UserAgentHeader
```

## <a name="remediate-security-risks-with-a-minimum-version-of-tls"></a>最小バージョンの TLS を使用してセキュリティ リスクを修復する

古いバージョンの TLS を使用しているクライアントからのトラフィックが最小限であること、または古いバージョンの TLS で行われた要求を失敗させても構わないことが確実である場合は、ストレージ アカウントで最小 TLS バージョンの適用を開始できます。 最小バージョンの TLS を使用してストレージ アカウントに対する要求を行うようクライアントに要求することは、データに対するセキュリティ リスクを最小限に抑えるための戦略の一部です。

> [!IMPORTANT]
> Azure Storage に接続するサービスを使用している場合は、ストレージ アカウントに必要な最低バージョンを設定する前に、そのサービスが適切なバージョンの TLS を使用して Azure Storage に要求を送信していることを確認してください。

### <a name="configure-the-minimum-tls-version-for-a-storage-account"></a>ストレージ アカウントの最小 TLS バージョンを構成する

ストレージ アカウントの TLS の最小バージョンを構成するには、アカウントに対して **MinimumTlsVersion** バージョンを設定します。 このプロパティは、Azure Resource Manager デプロイ モデルで作成されたすべてのストレージ アカウントで使用できます。 Azure Resource Manager デプロイ モデルの詳細については、「[ストレージ アカウントの概要](storage-account-overview.md)」を参照してください。

**MinimumTlsVersion** プロパティは既定では設定されず、明示的に設定するまで値は返されません。  プロパティ値が **null** の場合は、ストレージ アカウントで TLS バージョン 1.0 以降で送信された要求が許可されます。

# <a name="portal"></a>[ポータル](#tab/portal)

Azure portal でストレージ アカウントを作成する場合、TLS の最小バージョンは既定で 1.2 に設定されます。

Azure portal を使用して既存のストレージ アカウントの TLS の最小バージョンを構成するには、これらの手順に従います。

1. Azure Portal のストレージ アカウントに移動します。
1. **[構成]** 設定を選択します。
1. 次の図に示すように、 **[TLS の最小バージョン]** で、ドロップダウンを使用して、このストレージ アカウントのデータにアクセスするために必要な TLS の最小バージョンを選択します。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/configure-minimum-version-portal.png" alt-text="Azure portal で TLS の最小バージョンを構成する方法を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してストレージ アカウントの最小 TLS バージョンを構成するには、[Azure PowerShell バージョン 4.4.0](https://www.powershellgallery.com/packages/Az/4.4.0) 以降をインストールします。 次に、新規または既存のストレージ アカウントに **MinimumTLSVersion** プロパティを構成します。 **MinimumTlsVersion** の有効な値は、`TLS1_0`、`TLS1_1`、`TLS1_2` です。

次の例では、ストレージ アカウントを作成し、**MinimumTLSVersion** を TLS 1.1 に設定した後、アカウントを更新して、**MinimumTLSVersion** を TLS 1.2 に設定します。 この例では、各ケースのプロパティ値も取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with MinimumTlsVersion set to TLS 1.1.
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS `
    -MinimumTlsVersion TLS1_1

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion

# Update the MinimumTlsVersion version for the storage account to TLS 1.2.
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -MinimumTlsVersion TLS1_2

# Read the MinimumTlsVersion property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).MinimumTlsVersion
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してストレージ アカウントの最小 TLS バージョンを構成するには、Azure CLI バージョン 2.9.0 以降をインストールします。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 次に、新規または既存のストレージ アカウントに **minimumTlsVersion** プロパティを構成します。 **minimumTlsVersion** の有効な値は、`TLS1_0`、`TLS1_1`、`TLS1_2` です。

次の例では、ストレージ アカウントを作成し、**minimumTLSVersion** を TLS 1.1 に設定します。 次に、アカウントを更新して、**minimumTLSVersion** プロパティを TLS 1.2 に設定します。 この例では、各ケースのプロパティ値も取得します。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --min-tls-version TLS1_1

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --min-tls-version TLS1_2

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query minimumTlsVersion \
    --output tsv
```

# <a name="template"></a>[テンプレート](#tab/template)

テンプレートを使用してストレージ アカウントの最小 TLS バージョンを構成するには、**MinimumTLSVersion** プロパティを `TLS1_0`、`TLS1_1`、または `TLS1_2` に設定してテンプレートを作成します。 次の手順は、Azure portal でテンプレートを作成する方法について説明しています。

1. Azure portal で、 **[リソースの作成]** を選択します。
1. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。
1. **[Template deployment (deploy using custom templates) (preview)]\(テンプレートのデプロイ (カスタム テンプレートを使用してデプロイ)\)** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。
1. テンプレート エディターで、次の JSON を貼り付けて新しいアカウントを作成し、最小 TLS バージョンを TLS 1.2 に設定します。 山かっこ内のプレースホルダーは、実際の値に置き換えてください。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'tls')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "minimumTlsVersion": "TLS1_2"
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. テンプレートを保存します。
1. リソース グループ パラメーターを指定し、 **[確認と作成]** ボタンを選択してテンプレートをデプロイし、**MinimumTLSVersion** プロパティが構成されたストレージ アカウントを作成します。

---

> [!NOTE]
> ストレージ アカウントの TLS の最小バージョンを更新した後、変更が完全に反映されるまでに最大で 30 秒かかることがあります。

最小 TLS バージョンを構成するには、バージョン 2019-04-01 以降の Azure Storage リソース プロバイダーが必要です。 詳細については、[Azure Storage リソース プロバイダー REST API](/rest/api/storagerp/)」 に関するページを参照してください。

### <a name="check-the-minimum-required-tls-version-for-multiple-accounts"></a>複数のアカウントに必要な TLS の最小バージョンを調べる

最適なパフォーマンスの一連のストレージ アカウント全体で必要な TLS の最小バージョンを確認するために、Azure portal の Azure Resource Graph エクスプローラーを使用できます。 Resource Graph エクスプローラーの使用の詳細については、「[クイック スタート:Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../../governance/resource-graph/first-query-portal.md)」を参照してください。

Resource Graph エクスプローラーで次のクエリを実行すると、ストレージ アカウントの一覧が返され、各アカウントの TLS の最小バージョンが表示されます。

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend minimumTlsVersion = parse_json(properties).minimumTlsVersion
| project subscriptionId, resourceGroup, name, minimumTlsVersion
```

### <a name="test-the-minimum-tls-version-from-a-client"></a>クライアントから TLS の最小バージョンをテストする

ストレージ アカウントに必要な TLS の最小バージョンにより、古いバージョンで行われた呼び出しが禁止されることをテストするには、古いバージョンの TLS を使用するようにクライアントを構成します。 特定のバージョンの TLS を使用するようにクライアントを構成する方法の詳細については、「[クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)」を参照してください。

アカウントに構成されている TLS の最小バージョンを満たしていない TLS バージョンを使用して、クライアントによってストレージ アカウントにアクセスされた場合、Azure Storage からエラー コード 400 エラー (無効な要求) と、使用された TLS バージョンにはこのストレージ アカウントに対する要求を行うことが許可されていないことを示すメッセージが返されます。

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy を使用してコンプライアンスを監査する

多数のストレージ アカウントがある場合、監査を実行して、すべてのアカウントが組織で必要な最小バージョンの TLS を使用するように構成されていることを、確認したい場合があります。 一連のストレージ アカウントのコンプライアンスを監査するには、Azure Policy を使用します。 Azure Policy は、Azure リソースにルールを適用するポリシーの作成、割り当て、管理に使用できるサービスです。 Azure Policy を使用すると、それらのリソースが会社の標準やサービス レベル アグリーメントに準拠した状態を維持するのに役立ちます。 詳細については、[Azure Policy の概要](../../governance/policy/overview.md)に関するページを参照してください。

### <a name="create-a-policy-with-an-audit-effect"></a>Audit 効果を持つポリシーを作成する

Azure Policy では、ポリシー規則がリソースに対して評価されたときに実行される動作を決定する効果がサポートされています。 Audit 効果を使用すると、リソースが準拠していない場合に警告が生成されますが、要求は停止されません。 効果の詳細については、「[Azure Policy の効果について](../../governance/policy/concepts/effects.md)」を参照してください。

Azure portal を使用して最小 TLS バージョンに対して Audit 効果を持つポリシーを作成するには、次の手順のようにします。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[作成]** セクションで **[定義]** を選択します。
1. **[ポリシー定義の追加]** を選択して、新しいポリシー定義を作成します。
1. **[定義の場所]** フィールドで、 **[More]\(詳細\)** ボタンを選択して、監査ポリシーのリソースがある場所を指定します。
1. ポリシーの名前を指定します。 必要に応じて説明およびカテゴリを指定することもできます。
1. **[ポリシー規則]** で、次のポリシー定義を **policyRule** セクションに追加します。

    ```json
    {
      "policyRule": {
        "if": {
          "allOf": [
            {
              "field": "type",
              "equals": "Microsoft.Storage/storageAccounts"
            },
            {
              "not": {
                "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
                "equals": "TLS1_2"
              }
            }
          ]
        },
        "then": {
          "effect": "audit"
        }
      }
    }
    ```

1. ポリシーを保存します。

### <a name="assign-the-policy"></a>ポリシーを割り当てる

次に、ポリシーをリソースに割り当てます。 ポリシーのスコープは、そのリソースとその下にあるすべてのリソースに対応します。 ポリシー割り当ての詳細については、「[Azure Policy の割り当ての構造](../../governance/policy/concepts/assignment-structure.md)」を参照してください。

Azure portal でポリシーを割り当てるには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[作成]** セクションで **[割り当て]** を選択します。
1. 新しいポリシー割り当てを作成するために、 **[ポリシーの割り当て]** を選択します。
1. **[スコープ]** フィールドで、ポリシー割り当てのスコープを選択します。
1. **[ポリシー定義]** フィールドで、 **[More]\(詳細\)** ボタンを選択して、前のセクションで定義したポリシーを一覧から選択します。
1. ポリシー割り当て用の名前 を入力します。 説明は省略できます。
1. **[ポリシーの適用]** を "*有効*" のままに設定しておきます。 この設定は、監査ポリシーには影響しません。
1. **[確認および作成]** を選択して割り当てを作成します。

### <a name="view-compliance-report"></a>コンプライアンス レポートを表示する

ポリシーを割り当てたら、コンプライアンス レポートを表示できます。 監査ポリシーのコンプライアンス レポートには、ポリシーに準拠していないストレージ アカウントに関する情報が表示されます。 詳細については、[ポリシーのコンプライアンス データを取得する](../../governance/policy/how-to/get-compliance-data.md)ことに関する記事を参照してください。

ポリシー割り当てが作成された後、コンプライアンス レポートが使用可能になるまで数分かかる場合があります。

Azure portal でコンプライアンス レポートを表示するには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[コンプライアンス]** を選択します。
1. 前の手順で作成したポリシー割り当ての名前の結果をフィルター処理します。 このレポートには、ポリシーに準拠していないリソースの数が表示されます。
1. レポートをドリルダウンして、準拠していないストレージ アカウントの一覧などの詳細を表示できます。

    :::image type="content" source="media/transport-layer-security-configure-minimum-version/compliance-report-policy-portal.png" alt-text="最小 TLS バージョンについての監査ポリシーのコンプライアンス レポートを示すスクリーンショット":::

## <a name="use-azure-policy-to-enforce-the-minimum-tls-version"></a>Azure Policy を使用して最小 TLS バージョンを適用する

Azure Policy では、Azure リソースが要件と標準に準拠していることを確認することで、クラウド ガバナンスがサポートされます。 組織内のストレージ アカウントに対して最小 TLS バージョン要件を適用するには、ポリシーで指定されているものより古いバージョンの TLS に最小 TLS 要件が設定されている新しいストレージ アカウントの作成を禁止するポリシーを作成します。 また、このポリシーでは、既存のアカウントに対する最小 TLS バージョンの設定がポリシーに準拠していない場合に、そのアカウントに対するすべての構成変更が禁止されます。

適用ポリシーでは、Deny 効果を使用して、最小 TLS バージョンが組織の標準に準拠しなくなるようなストレージ アカウントの作成要求または変更要求が禁止されます。 効果の詳細については、「[Azure Policy の効果について](../../governance/policy/concepts/effects.md)」を参照してください。

TLS 1.2 より小さい最小 TLS バージョンに対して Deny 効果を持つポリシーを作成するには、「[Azure Policy を使用してコンプライアンスを監査する](#use-azure-policy-to-audit-for-compliance)」で説明されている手順に従いますが、ポリシー定義の **policyRule** セクションに次の JSON を指定します。

```json
{
  "policyRule": {
    "if": {
      "allOf": [
        {
          "field": "type",
          "equals": "Microsoft.Storage/storageAccounts"
        },
        {
          "not": {
            "field": "Microsoft.Storage/storageAccounts/minimumTlsVersion",
            "equals": "TLS1_2"
          }
        }
      ]
    },
    "then": {
      "effect": "deny"
    }
  }
}
```

Deny 効果を持つポリシーを作成し、これをスコープに割り当てると、ユーザーは 1.2 より古い最小 TLS バージョンでストレージ アカウントを作成できなくなります。 また、ユーザーは、現在 1.2 より古い最小 TLS バージョンを要求している既存のストレージ アカウントに対して構成変更を行うこともできません。 この操作を行おうとすると、エラーが発生します。 アカウントの作成または構成を続行するには、ストレージ アカウントに必要な最小 TLS バージョンを、1.2 に設定する必要があります。

次の図では、Deny 効果を持つポリシーで、最小 TLS バージョンを TLS 1.2 に設定することが要求されているときに、最小 TLS バージョンを TLS 1.0 に設定して (新しいアカウントの既定) ストレージ アカウントを作成しようとした場合に発生するエラーが示されています。

:::image type="content" source="media/transport-layer-security-configure-minimum-version/deny-policy-error.png" alt-text="ポリシーに違反するストレージ アカウントを作成したときに発生したエラーを示すスクリーンショット":::

## <a name="permissions-necessary-to-require-a-minimum-version-of-tls"></a>最低バージョンの TLS を要求するために必要なアクセス許可

ストレージ アカウントの **MinimumTlsVersion** プロパティを設定するには、ストレージ アカウントを作成および管理するためのアクセス許可が必要です。 これらのアクセス許可を提供する Azure ロールベースのアクセス制御 (Azure RBAC) ロールには、**Microsoft.Storage/storageAccounts/write** または **Microsoft.Storage/storageAccounts/\*** アクションが含まれます。 このアクションの組み込みロールには、次のようなロールがあります。

- Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール
- [Storage Account の共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)ロール

これらのロールでは、Azure Active Directory (Azure AD) を使用してストレージ アカウントのデータにアクセスすることはできません。 ただし、アカウント アクセス キーへのアクセスを許可する **Microsoft.Storage/storageAccounts/listkeys/action** が含まれています。 このアクセス許可では、ユーザーがアカウント アクセス キーを使用して、ストレージ アカウント内のすべてのデータにアクセスできます。

ユーザーがストレージ アカウントに対する最低バージョンの TLS を要求できるようにするには、ロール割り当てのスコープをストレージ アカウント以上のレベルにする必要があります。 ロール スコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」をご覧ください。

これらのロールを割り当てる際には、ストレージ アカウントを作成したり、そのプロパティを更新したりする機能を必要とするユーザーにのみ割り当てるように、注意してください。 最小限の特権の原則を使用して、ユーザーに、それぞれのタスクを実行するのに必要な最小限のアクセス許可を割り当てるようにします。 Azure RBAC でアクセスを管理する方法の詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者** ロールにはすべてのアクションが含まれているため、これらの管理者ロールのいずれかを持つユーザーも、ストレージ アカウントを作成および管理できます。 詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)に関する記事を参照してください。

## <a name="network-considerations"></a>ネットワークに関する考慮事項

クライアントがストレージ アカウントに要求を送信すると、クライアントは、最初にストレージ アカウントのパブリック エンドポイントとの接続を確立してから、要求を処理します。 TLS の最小バージョンの設定は、接続が確立された後にチェックされます。 設定で指定されたものよりも前のバージョンの TLS が要求で使用されている場合、接続は引き続き成功しますが、要求は最終的に失敗します。 Azure Storage のパブリック エンドポイントの詳細については、「[リソースの URI の構文](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#resource-uri-syntax)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [クライアント アプリケーションのトランスポート層セキュリティ (TLS) を構成する](transport-layer-security-configure-client-version.md)
- [BLOB ストレージのセキュリティに関する推奨事項](../blobs/security-recommendations.md)

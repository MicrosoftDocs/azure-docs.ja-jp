---
title: コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ
titleSuffix: Azure Storage
description: ストレージ アカウントに対する匿名の要求を分析する方法と、ストレージ アカウント全体または個々のコンテナーに対する匿名アクセスを防ぐ方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/09/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 53f29c2b8f7a17ac2a23cc081660e8dcb4b9f387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701860"
---
# <a name="prevent-anonymous-public-read-access-to-containers-and-blobs"></a>コンテナーと BLOB への匿名パブリック読み取りアクセスを防ぐ

Azure Storage 内のコンテナーと BLOB への匿名パブリック読み取りアクセスは、データを共有するための便利な方法ですが、セキュリティ上のリスクが生じる場合もあります。 匿名アクセスを管理するときは慎重を期すこと、また、データへの匿名アクセスを評価する方法について理解することが重要です。 運用の複雑さ、人為的なエラー、パブリックにアクセス可能なデータに対する悪意のある攻撃によって、データが侵害され、損失を被る可能性があります。 匿名アクセスは、アプリケーションのシナリオで必要な場合にのみ有効にすることをお勧めします。

既定では、BLOB データへのパブリック アクセスは常に禁止されています。 ただし、ストレージ アカウントの既定の構成では、適切なアクセス許可を持つユーザーが、ストレージ アカウントのコンテナーおよび BLOB へのパブリック アクセスを構成することができます。 セキュリティを強化するために、個々のコンテナーのパブリック アクセス設定に関係なく、ストレージ アカウントへのすべてのパブリック アクセスを禁止することができます。 ストレージ アカウントへのパブリック アクセスを禁止すると、ユーザーはアカウント内のコンテナーに対してパブリック アクセスを有効にできなくなります。 お客様のシナリオで必要とされない限り、ストレージ アカウントへのパブリック アクセスを禁止することをお勧めします。 パブリック アクセスを禁止することで、望ましくない匿名アクセスによって発生するデータ侵害を防ぐことができます。

ストレージ アカウントに対してパブリック BLOB アクセスを禁止すると、Azure Storage はそのアカウントに対するすべての匿名要求を拒否します。 アカウントのパブリック アクセスが許可されなくなったら、そのアカウントのコンテナーは後でパブリック アクセス用に構成できません。 パブリック アクセス用に既に構成されているコンテナーは、匿名要求を受け付けなくなります。 詳細については、[コンテナーと BLOB の匿名パブリック読み取りアクセスの構成](anonymous-read-access-configure.md)に関するページを参照してください。

この記事では、DRAG (検出-修復-監査-ガバナンス) フレームワークを使用して、ストレージ アカウントに対するパブリック アクセスを継続的に管理する方法について説明します。

## <a name="detect-anonymous-requests-from-client-applications"></a>クライアント アプリケーションからの匿名要求の検出

ストレージ アカウントでパブリック読み取りアクセスを禁止すると、現在パブリック アクセスが構成されているコンテナーと BLOB への要求が拒否されるリスクがあります。 ストレージ アカウントでパブリック アクセスを禁止すると、そのストレージ アカウント内の個々のコンテナーのパブリック アクセス設定がオーバーライドされます。 ストレージ アカウントでパブリック アクセスを禁止すると、以後、そのアカウントに対する匿名要求は失敗します。

パブリック アクセスを禁止するとクライアント アプリケーションにどのような影響があるかを理解するために、そのアカウントでログ記録とメトリックを有効にし、一定の期間内の匿名要求のパターンを分析することをお勧めします。 ストレージ アカウントに対する匿名要求の数をメトリックを使って調べ、匿名でアクセスされているコンテナーをログから特定します。

### <a name="monitor-anonymous-requests-with-metrics-explorer"></a>メトリックス エクスプローラーを使用した匿名要求の監視

ストレージ アカウントに対する匿名要求を追跡するには、Azure portal で Azure メトリックス エクスプローラーを使用します。 メトリックス エクスプローラーの詳細については、「[Azure メトリックス エクスプローラーの概要](../../azure-monitor/essentials/metrics-getting-started.md)」を参照してください。

匿名要求を追跡するメトリックを作成するには、次の手順に従います。

1. Azure Portal のストレージ アカウントに移動します。 **[監視]** セクションで、 **[メトリック]** を選択します。
1. **[メトリックの追加]** を選択します。 **[メトリック]** ダイアログで、次の値を指定します。
    1. [スコープ] フィールドは、ストレージ アカウントの名前に設定したままにします。
    1. **[Metric Namespace]\(メトリックの名前空間\)** を *[Blob]\(BLOB\)* に設定します。 このメトリックでは、BLOB ストレージに対する要求のみが報告されます。
    1. **[メトリック]** フィールドを *[トランザクション]* に設定します。
    1. **[集計]** フィールドを *[合計]* に設定します。

    新しいメトリックには、指定された期間中の BLOB ストレージに対するトランザクション数の合計が表示されます。 結果のメトリックは、次の図のように表示されます。

    :::image type="content" source="media/anonymous-read-access-prevent/configure-metric-blob-transactions.png" alt-text="BLOB トランザクションを合計するようにメトリックを構成する方法を示すスクリーンショット":::

1. 次に、 **[フィルターの追加]** ボタンをクリックして、匿名要求のメトリックにフィルターを作成します。
1. **[フィルター]** ダイアログで、次の値を指定します。
    1. **[プロパティ]** の値を *[認証]* に設定します。
    1. **[演算子]** フィールドを等号 (=) に設定します。
    1. **[値]** フィールドを *[匿名]* に設定します。
1. 右上隅で、メトリックを表示する期間を選択します。 1 分から 1 か月の範囲で間隔を指定して、要求の集計のきめ細かさを指示することもできます。

メトリックを構成すると、匿名要求がグラフに表示されるようになります。 次の図は、過去 30 分間に集計された匿名要求を示しています。

:::image type="content" source="media/anonymous-read-access-prevent/metric-anonymous-blob-requests.png" alt-text="BLOB ストレージに対する匿名要求の集計を示すスクリーンショット":::

ストレージ アカウントに対して一定数の匿名要求が行われたら通知する警告ルールも構成できます。 詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/alerts/alerts-metric.md)」を参照してください。

### <a name="analyze-logs-to-identify-containers-receiving-anonymous-requests"></a>ログを分析して匿名要求を受信しているコンテナーを識別する

Azure Storage のログには、要求の承認方法など、ストレージ アカウントに対して行われた要求の詳細が記録されます。 ログを分析して、どのコンテナーが匿名要求を受信しているかを特定できます。

Azure Monitor の Azure Storage ログ記録 (プレビュー) を使用すると、Azure Storage アカウントに対する要求をログに記録して、匿名要求を評価することができます。 詳細については、「[Azure Storage を監視する](./monitor-blob-storage.md)」を参照してください。

Azure Monitor の Azure Storage ログ記録では、ログ クエリを使用したログ データの分析がサポートされています。 ログに対してクエリを実行するために、Azure Log Analytics ワークスペースを使用できます。 ログ クエリの詳細については、「[チュートリアル: Log Analytics クエリの使用方法](../../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

> [!NOTE]
> Azure Monitor での Azure Storage のログ記録のプレビューは、Azure パブリック クラウドでのみサポートされています。 Government クラウドでは、Azure Monitor を使用した Azure Storage のログ記録はサポートされていません。

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Azure portal での診断設定の作成

Azure Monitor で Azure Storage のデータをログに記録し、Azure Log Analytics で分析するには、まず、データをログに記録する要求の種類とストレージ サービスを指示する診断設定を作成する必要があります。 Azure portal で診断設定を作成するには、これらの手順に従います。

1. ご利用の Azure Storage アカウントが含まれるサブスクリプションに、新しい Log Analytics ワークスペースを作成します。 ストレージ アカウントのログ記録を構成した後、Log Analytics ワークスペースでログを使用できるようになります。 詳細については、「[Azure ポータルで Log Analytics ワークスペースを作成する](../../azure-monitor/logs/quick-create-workspace.md)」を参照してください。
1. Azure Portal のストレージ アカウントに移動します。
1. [監視] セクションで、 **[Diagnostic settings (preview)]\(診断設定 (プレビュー)\)** を選択します。
1. BLOB ストレージに対して行われた要求をログに記録するには、 **[Blob]\(BLOB\)** を選択します。
1. **[診断設定の追加]** を選択します。
1. 診断設定の名前を指定します。
1. **[カテゴリの詳細]** の **[ログ]** セクションで、ログを記録する要求の種類を選択します。 すべての匿名要求は読み取り要求になるため、匿名要求をキャプチャするには **StorageRead** を選択します。
1. **[Destination details]\(宛先の詳細\)** で、 **[Send to Log Analytics]\(Log Analytics に送信\)** を選択します。 以下の図に示すように、ご利用のサブスクリプションと、先ほど作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="media/anonymous-read-access-prevent/create-diagnostic-setting-logs.png" alt-text="要求のログを記録するための診断設定の作成方法を示すスクリーンショット":::

診断設定を作成した後、ストレージ アカウントに対する要求が、その設定に従ってログに記録されるようになります。 詳細については、[Azure でリソース ログとメトリックを収集するための診断設定の作成](../../azure-monitor/essentials/diagnostic-settings.md)に関するページを参照してください。

Azure Monitor の Azure Storage ログで使用できるフィールドのリファレンスについては、「[リソース ログ (プレビュー)](./monitor-blob-storage-reference.md#resource-logs-preview)」を参照してください。

#### <a name="query-logs-for-anonymous-requests"></a>匿名要求のログのクエリ

Azure Monitor の Azure Storage ログには、ストレージ アカウントに要求を行うために使用された承認の種類が記録されます。 匿名要求を表示するには、ログ クエリで **AuthenticationType** プロパティをフィルターにします。

BLOB ストレージに対する匿名要求の過去 7 日間のログを取得するには、Log Analytics ワークスペースを開きます。 次に、以下のクエリを新しいログ クエリに貼り付けて実行します。

```kusto
StorageBlobLogs
| where TimeGenerated > ago(7d) and AuthenticationType == "Anonymous"
| project TimeGenerated, AccountName, AuthenticationType, Uri
```

このクエリに基づいて警告ルールを構成し、匿名要求の通知を受けることもできます。 詳細については、「[Azure Monitor を使用してログ アラートを作成、表示、管理する](../../azure-monitor/alerts/alerts-log.md)」を参照してください。

## <a name="remediate-anonymous-public-access"></a>匿名パブリック アクセスの修正

ストレージ アカウント内のコンテナーと BLOB に対する匿名要求を評価した後は、パブリック アクセスを制限または禁止するためのアクションを実行できます。 ストレージ アカウント内の一部のコンテナーをパブリック アクセス可能にする必要がある場合、ストレージ アカウント内のコンテナーごとにパブリック アクセス設定を構成できます。 このオプションを使用すると、最もきめ細かくパブリック アクセスを制御できます。 詳細については、[コンテナーのパブリック アクセス レベルの設定](anonymous-read-access-configure.md#set-the-public-access-level-for-a-container)に関するページを参照してください。

セキュリティを強化するために、ストレージ アカウント全体でパブリック アクセスを禁止することができます。 ストレージ アカウントに対するパブリック アクセス設定は、そのアカウント内のコンテナーに対する個別設定をオーバーライドします。 ストレージ アカウントに対してパブリック アクセスを禁止すると、パブリック アクセスを許可するように構成されているコンテナーに匿名でアクセスできなくなります。 詳細については、[ストレージ アカウントのパブリック読み取りアクセスの許可または禁止](anonymous-read-access-configure.md#allow-or-disallow-public-read-access-for-a-storage-account)に関するページを参照してください。

特定のコンテナーをパブリック アクセスで使用できるようにする必要があるシナリオでは、当該のコンテナーとその BLOB を、パブリック アクセス用に予約されているストレージ アカウントに移動することをお勧めします。 その後、他のストレージ アカウントのパブリック アクセスを禁止できます。

### <a name="verify-that-public-access-to-a-blob-is-not-permitted"></a>BLOB へのパブリック アクセスが許可されていないことの確認

特定の BLOB へのパブリック アクセスが禁止されていることを確認するために、その URL から BLOB のダウンロードを試みることができます。 ダウンロードが成功した場合、BLOB はパブリックにアクセス可能な状態のままです。 ストレージ アカウントでパブリック アクセスが禁止されているため BLOB にパブリックにアクセスできない場合、このストレージ アカウントではパブリック アクセスが許可されていないというエラー メッセージが表示されます。

次の例は、PowerShell を使用してその URL から BLOB のダウンロードを試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$url = "<absolute-url-to-blob>"
$downloadTo = "<file-path-for-download>"
Invoke-WebRequest -Uri $url -OutFile $downloadTo -ErrorAction Stop
```

### <a name="verify-that-modifying-the-containers-public-access-setting-is-not-permitted"></a>コンテナーのパブリック アクセス設定の変更が許可されていないことの確認

ストレージ アカウントでパブリック アクセスを禁止した後、コンテナーのパブリック アクセス設定を変更できないことを確認するために、設定の変更を試みることができます。 ストレージ アカウントでパブリック アクセスが禁止されている場合、コンテナーのパブリック アクセス設定の変更は失敗します。

次の例は、PowerShell を使用してコンテナーのパブリック アクセス設定の変更を試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Set-AzStorageContainerAcl -Context $ctx -Container $containerName -Permission Blob
```

### <a name="verify-that-creating-a-container-with-public-access-enabled-is-not-permitted"></a>パブリック アクセスを有効にしたコンテナーの作成が許可されていないことの確認

ストレージ アカウントでパブリック アクセスが禁止されている場合、パブリック アクセスを有効にして新しいコンテナーを作成することはできません。 確認するために、パブリック アクセスを有効にしてコンテナーの作成を試みることができます。

次の例は、PowerShell を使用して、パブリック アクセスが有効なコンテナーの作成を試みる方法を示しています。 かっこ内のプレースホルダー値を独自の値に置き換えることを忘れないでください。

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container-name>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Permission Blob -Context $ctx
```

### <a name="check-the-public-access-setting-for-multiple-accounts"></a>複数のアカウントのパブリック アクセス設定を確認する

Azure portal の Azure Resource Graph エクスプローラーを使用すると、複数のストレージ アカウントにまたがって最も高速にパブリック アクセス設定を確認できます。 Resource Graph エクスプローラーの使用方法については、「[クイックスタート: Azure Resource Graph エクスプローラーを使用して初めての Resource Graph クエリを実行する](../../governance/resource-graph/first-query-portal.md)」を参照してください。

既定では、ストレージ アカウントに **AllowBlobPublicAccess** プロパティは設定されておらず、明示的に設定されるまで値を返しません。 プロパティ値が **null** か **true** の場合、ストレージ アカウントによってパブリック アクセスが許可されます。

Resource Graph エクスプローラーで次のクエリを実行すると、ストレージ アカウントの一覧が返され、各アカウントのパブリック アクセス設定が表示されます。

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
```

次の図は、サブスクリプション全体におけるクエリの結果を示しています。 **AllowBlobPublicAccess** プロパティが明示的に設定されているストレージ アカウントの場合、結果には **true** または **false** として表示されます。 ストレージ アカウントに **AllowBlobPublicAccess** プロパティが設定されていない場合、クエリ結果には空白 (または null) として表示されます。

:::image type="content" source="media/anonymous-read-access-prevent/check-public-access-setting-accounts.png" alt-text="ストレージ アカウント全体のパブリック アクセス設定のクエリ結果を示すスクリーンショット":::

## <a name="use-azure-policy-to-audit-for-compliance"></a>Azure Policy を使用してコンプライアンスを監査する

多数のストレージ アカウントがある場合、監査を実行して、それらのアカウントがパブリック アクセスを防止するように構成されていることを確認したい場合があります。 一連のストレージ アカウントのコンプライアンスを監査するには、Azure Policy を使用します。 Azure Policy は、Azure リソースにルールを適用するポリシーの作成、割り当て、管理に使用できるサービスです。 Azure Policy を使用すると、それらのリソースが会社の標準やサービス レベル アグリーメントに準拠した状態を維持するのに役立ちます。 詳細については、[Azure Policy の概要](../../governance/policy/overview.md)に関するページを参照してください。

### <a name="create-a-policy-with-an-audit-effect"></a>Audit 効果を持つポリシーを作成する

Azure Policy では、ポリシー規則がリソースに対して評価されたときに実行される動作を決定する効果がサポートされています。 Audit 効果を使用すると、リソースが準拠していない場合に警告が生成されますが、要求は停止されません。 効果の詳細については、「[Azure Policy 効果について](../../governance/policy/concepts/effects.md)」を参照してください。

Azure portal でストレージ アカウントのパブリック アクセス設定のために Audit 効果を持つポリシーを作成するには、次の手順を実行します。

1. Azure portal で、Azure Policy サービスに移動します。
1. **[作成]** セクションで **[定義]** を選択します。
1. **[ポリシー定義の追加]** を選択して、新しいポリシー定義を作成します。
1. **[定義の場所]** フィールドで、 **[More]\(詳細\)** ボタンを選択して、監査ポリシーのリソースがある場所を指定します。
1. ポリシーの名前を指定します。 必要に応じて説明およびカテゴリを指定することもできます。
1. **[ポリシー規則]** で、次のポリシー定義を **policyRule** セクションに追加します。

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
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

    :::image type="content" source="media/anonymous-read-access-prevent/compliance-report-policy-portal.png" alt-text="BLOB パブリック アクセスについての監査ポリシーのコンプライアンス レポートを示すスクリーンショット":::

## <a name="use-azure-policy-to-enforce-authorized-access"></a>Azure Policy を使用して承認されたアクセスを適用する

Azure Policy は、Azure リソースが要件と標準に準拠していることを保証することによって、クラウド ガバナンスをサポートします。 組織内のストレージ アカウントが承認された要求のみを許可するようにするために、匿名要求を許可するパブリック アクセス設定で新しいストレージ アカウントを作成できないようにするポリシーを作成します。 また、このポリシーは、そのアカウントのパブリック アクセス設定がポリシーに準拠していない場合に、既存のアカウントに対するすべての構成変更を防止します。

強制ポリシーでは、パブリック アクセスを許可するためにストレージ アカウントを作成または変更する要求を防ぐための Deny 効果が使用されます。 効果の詳細については、「[Azure Policy 効果について](../../governance/policy/concepts/effects.md)」を参照してください。

匿名要求を許可するパブリック アクセス設定の Deny 効果を持つポリシーを作成するには、「[Azure Policy を使用してコンプライアンスを監査する](#use-azure-policy-to-audit-for-compliance)」で説明されている手順に従いますが、ポリシー定義の **policyRule** セクションに次の JSON を指定します。

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowBlobPublicAccess",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Deny 効果を持つポリシーを作成し、これをスコープに割り当てると、ユーザーはパブリック アクセスを許可するストレージ アカウントを作成できなくなります。 また、ユーザーは、現在パブリック アクセスを許可している既存のストレージ アカウントに対して構成変更を行うこともできません。 この操作を行おうとすると、エラーが発生します。 アカウントの作成または構成を続行するには、ストレージ アカウントのパブリック アクセス設定を **false** に設定する必要があります。

次の図は、Deny 効果を持つポリシーで、パブリック アクセスが許可されないことが要求されているとき、パブリック アクセスを許可するストレージ アカウント (新しいアカウントの既定) を作成しようとした場合に発生するエラーを示しています。

:::image type="content" source="media/anonymous-read-access-prevent/deny-policy-error.png" alt-text="ポリシーに違反するストレージ アカウントを作成したときに発生したエラーを示すスクリーンショット":::

## <a name="permissions-for-allowing-or-disallowing-public-access"></a>パブリック アクセスを許可または禁止するためのアクセス許可

ストレージ アカウントの **AllowBlobPublicAccess** プロパティを設定するには、ストレージ アカウントを作成および管理するためのアクセス許可が必要です。 これらのアクセス許可を提供する Azure ロールベースのアクセス制御 (Azure RBAC) ロールには、**Microsoft.Storage/storageAccounts/write** または **Microsoft.Storage/storageAccounts/\*** アクションが含まれます。 このアクションの組み込みロールには、次のようなロールがあります。

- Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロール
- Azure Resource Manager の[共同作成者](../../role-based-access-control/built-in-roles.md#contributor)ロール
- [Storage Account の共同作成者](../../role-based-access-control/built-in-roles.md#storage-account-contributor)ロール

これらのロールでは、Azure Active Directory (Azure AD) を使用してストレージ アカウントのデータにアクセスすることはできません。 ただし、アカウント アクセス キーへのアクセスを許可する **Microsoft.Storage/storageAccounts/listkeys/action** が含まれています。 このアクセス許可では、ユーザーがアカウント アクセス キーを使用して、ストレージ アカウント内のすべてのデータにアクセスできます。

ユーザーがストレージ アカウントに対するパブリック アクセスを許可または禁止できるようにするには、ロール割り当てのスコープをストレージ アカウント以上のレベルにする必要があります。 ロール スコープの詳細については、「[Azure RBAC のスコープについて](../../role-based-access-control/scope-overview.md)」を参照してください。

これらのロールを割り当てる際には、ストレージ アカウントを作成したり、そのプロパティを更新したりする機能を必要とするユーザーにのみ割り当てるように、注意してください。 最小限の特権の原則を使用して、ユーザーに、それぞれのタスクを実行するのに必要な最小限のアクセス許可を割り当てるようにします。 Azure RBAC でアクセスを管理する方法の詳細については、「[Azure RBAC のベスト プラクティス](../../role-based-access-control/best-practices.md)」を参照してください。

> [!NOTE]
> 従来のサブスクリプション管理者ロールであるサービス管理者と共同管理者には、Azure Resource Manager の[所有者](../../role-based-access-control/built-in-roles.md#owner)ロールと同等のものが含まれています。 **所有者** ロールにはすべてのアクションが含まれているため、これらの管理者ロールのいずれかを持つユーザーも、ストレージ アカウントを作成および管理できます。 詳細については、[従来のサブスクリプション管理者ロール、Azure ロール、および Azure AD 管理者ロール](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

- [コンテナーと BLOB の匿名パブリック読み取りアクセスを構成する](anonymous-read-access-configure.md)
- [.NET を使用してパブリックのコンテナーと BLOB に匿名でアクセスする](anonymous-read-access-client.md)
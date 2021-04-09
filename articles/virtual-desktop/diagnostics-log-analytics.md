---
title: Windows Virtual Desktop の診断ログ分析 - Azure
description: Windows Virtual Desktop の診断機能でログ分析を使用する方法について説明します。
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 37990cc4322717f090c7a35c62512ba0e1a04293
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100576139"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>診断機能に Log Analytics を使用する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (classic) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md)を参照してください。

Windows Virtual Desktop では、他の多くの Azure サービスと同様に、監視やアラートに [Azure Monitor](../azure-monitor/overview.md) が使用されます。 これにより、管理者は 1 つのインターフェイス経由で問題を特定できます。 サービスでは、ユーザーと管理者の両方のアクションに対してアクティビティ ログが作成されます。 各アクティビティ ログは、次のカテゴリに分類されます。

- 管理アクティビティ:
    - API または PowerShell を使用して Windows Virtual Desktop のオブジェクトを変更しようとした場合に、成功するかどうかを追跡する。 たとえば、ユーザーは PowerShell を使用してホスト プールを正常に作成できるか。
- フィード:
    - ユーザーが、正常にワークスペースにサブスクライブできるか。
    - ユーザーには、リモート デスクトップ クライアント上でパブリッシュされているすべてのリソースが表示されるか。
- 接続:
    - ユーザーがサービスへの接続を開始および完了する時点。
- ホスト登録:
    - 接続時にセッション ホストが正常にサービスに登録されたか。
- エラー:
    - 特定のアクティビティでユーザーに問題が発生しているか。 この機能では、情報がアクティビティと結合されている場合に、アクティビティ データを自動で追跡するテーブルを生成できます。
- チェックポイント:
    - アクティビティの有効期間内に到達した特定のステップ。 たとえば、セッション中に、ユーザーが特定のホストに負荷分散された後、接続中にそのユーザーへのサインオンが行われた、など。

診断ロール サービス自体が Windows Virtual Desktop の一部であるため、Windows Virtual Desktop に到達しない接続は診断結果に表示されません。 Windows Virtual Desktop 接続の問題は、ユーザーにネットワーク接続の問題が発生しているときに発生する可能性があります。

Azure Monitor を使用すると、Windows Virtual Desktop のデータを分析して、仮想マシン (VM) のパフォーマンス カウンターを確認できます。すべて、同じツール内で行われます。 この記事では、ご自身の Windows 仮想デスクトップ環境で診断を有効にする方法について説明します。

>[!NOTE]
>Azure で VM を監視する方法については、「[Azure Monitor を使用して Azure 仮想マシンを監視する](../azure-monitor/vm/monitor-vm-azure.md)」を参照してください。 また、セッション ホストでのユーザー エクスペリエンスをより深く理解するために、必ず[パフォーマンス カウンターのしきい値を確認](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds)してください。

## <a name="before-you-get-started"></a>開始する前に

Log Analytics を使用するには、まず、ワークスペースを作成する必要があります。 これを行うには、次の 2 つの記事のいずれかの手順に従います。

- Azure portal を使用する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください。
- PowerShell を使用する場合は、「[PowerShell を使用して Log Analytics ワークスペースを作成する](../azure-monitor/logs/powershell-workspace-configuration.md)」を参照してください。

ワークスペースを作成したら、「[Windows コンピューターを Azure Monitor に接続する](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)」の指示に従って、次の情報を取得します。

- ワークスペース ID
- ワークスペースの主キー

この情報は後のセットアップ プロセスで必要になります。

お使いの Windows Virtual Desktop 環境を監視およびメンテナンスする担当者がデータにアクセスできるように、確実に Azure Monitor のアクセス許可を管理してください。 詳細については、[「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](../azure-monitor/roles-permissions-security.md)を参照してください。

## <a name="push-diagnostics-data-to-your-workspace"></a>診断データをワークスペースにプッシュする

お使いの Windows Virtual Desktop オブジェクトからワークスペースの Log Analytics に診断データをプッシュできます。 この機能は、最初にオブジェクトを作成するときに、すぐに設定できます。

新しいオブジェクトに Log Analytics を設定するには:

1. Azure portal にサインインして、 **[Windows Virtual Desktop]** に移動します。

2. ログとイベントをキャプチャするオブジェクト (ホスト プール、アプリ グループ、ワークスペースなど) に移動します。

3. 画面の左側にあるメニューで、 **[診断設定]** を選択します。

4. 画面の右側に表示されるメニューで、 **[診断設定を追加する]** を選択します。

    [診断設定] ページに表示されるオプションは、編集しているオブジェクトの種類に応じて異なります。

    たとえば、アプリ グループの診断を有効にすると、チェックポイント、エラー、および管理を構成するためのオプションが表示されます。 ワークスペースでは、これらのカテゴリによって、ユーザーがアプリの一覧にサブスクライブした時点を追跡するためのフィードが構成されます。 診断設定の詳細については、「[Azure でリソース ログとメトリックを収集するための診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md)」を参照してください。

     >[!IMPORTANT]
     >監視する Azure Resource Manager オブジェクトごとに、診断を有効にすることを忘れないでください。 診断が有効になると、データはアクティビティで利用可能になります。 最初のセットアップ後は、数時間かかる場合があります。

5. 設定構成の名前を入力して、 **[Log Analytics への送信]** を選択します。 使用する名前には、スペースを含めず、[Azure の名前付け規則](../azure-resource-manager/management/resource-name-rules.md)に準拠する必要があります。 ログの一部として、チェックポイント、エラー、管理など、ログ分析に追加したいすべてオプションを選択できます。

6. **[保存]** を選択します。

>[!NOTE]
>Log Analytics には、データを [Event Hubs](../event-hubs/event-hubs-about.md) にストリーミングしたり、ストレージ アカウント内にアーカイブしたりするオプションが用意されています。 この機能の詳細については、「[イベント ハブへの Azure 監視データのストリーム配信](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)」および「[Azure リソース ログをストレージ アカウントにアーカイブする](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)」を参照してください。

## <a name="how-to-access-log-analytics"></a>Log Analytics にアクセスする方法

Azure portal または Azure Monitor 上で Log Analytics ワークスペースにアクセスできます。

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics ワークスペース上で Log Analytics にアクセスする

1. Azure portal にサインインします。

2. **[Log Analytics ワークスペース]** を探します。

3. [サービス] 下で、 **[Log Analytics ワークスペース]** を選択します。

4. 一覧から、お使いの Windows 仮想デスクトップ オブジェクト用に構成したワークスペースを選択します。

5. お使いのワークスペースを表示し、 **[ログ]** を選択します。 **[検索]** 機能を使用して、メニュー リストをフィルター処理できます。

### <a name="access-log-analytics-on-azure-monitor"></a>Azure Monitor 上で Log Analytics にアクセスする

1. Azure portal にサインインする

2. **[モニター]** を検索して選択します。

3. **[ログ]** を選択します。

4. ログ記録のページ上にある手順に従って、クエリのスコープを設定します。

5. クエリ診断の準備が整いました。 すべての診断テーブルに、"WVD" プレフィックスがあります。

>[!NOTE]
>Azure Monitor ログ内に格納されているテーブルの詳細については、「[Azure Monitor のデータ リファレンス](/azure/azure-monitor/reference/)」を参照してください。 Windows Virtual Desktop に関連するすべてのテーブルに "WVD" というラベルが付いています。

## <a name="cadence-for-sending-diagnostic-events"></a>診断イベントの送信頻度

診断イベントは、完了時に Log Analytics に送信されます。

接続アクティビティに対しては、Log Analytics では以下の中間状態でのみ報告を行います。

- 開始済み: ユーザーがリモート デスクトップ クライアントでアプリまたはデスクトップを選択して接続したとき。
- 接続済み: アプリまたはデスクトップがホストされている VM にユーザーが正常に接続したとき。
- 完了: ユーザーまたはサーバーが、アクティビティが行われたセッションを切断したとき。

## <a name="example-queries"></a>クエリの例

Azure Monitor Log Analytics UI を使用したサンプル クエリへのアクセス:
1. Log Analytics ワークスペースにアクセスし、 **[ログ]** を選択します。 クエリ UI の例が自動的に表示されます。
1. フィルターを **[カテゴリ]** に変更します。
1. **[Windows Virtual Desktop]** を選択して、使用可能なクエリを確認します。
1. **[実行]** を選択して、選択したクエリを実行します。

サンプル クエリ インターフェイスの詳細については、「[Azure Monitor Log Analytics の保存済みクエリ](../azure-monitor/logs/example-queries.md)」を参照してください。

次のクエリ一覧では、1 人のユーザーの接続情報または問題を確認できます。 これらのクエリは、[Log Analytics クエリ エディター](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query)で実行できます。 クエリごとに、`userupn` を検索するユーザーの UPN に置き換えます。


1 人のユーザーのすべての接続を検索するには:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


ユーザーの 1 日ごとの接続回数を確認するには:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

ユーザーによるセッション継続時間を確認するには:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

特定のユーザーのエラーを確認するには:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

他のユーザーに対して特定のエラーが発生したかどうかを確認するには:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- ユーザーが完全なデスクトップを開いた場合、セッション内でのユーザーによるアプリの使用は WVDCheckpoints テーブルのチェックポイントとして追跡されません。
>- WVDConnections テーブル内の ResourcesAlias 列は、ユーザーが完全なデスクトップと公開済みアプリのどちらに接続しているかを示します。 この列には、接続中に開いた最初のアプリのみが表示されます。 ユーザーが開いた公開済みアプリは、WVDCheckpoints 内で追跡されます。
>- WVDErrors テーブルには、管理エラー、ホスト登録の問題、およびユーザーがアプリまたはデスクトップの一覧をサブスクライブしている間に発生したその他の問題が表示されます。
>- WVDErrors は、管理タスクによって解決できる問題を特定するのに役立ちます。 ServiceError の値は、これらの種類の問題の場合、必ず "false" と表示されます。 ServiceError = "true" の場合は、問題を Microsoft にエスカレートする必要があります。 エスカレートするエラーの CorrelationID をお伝えください。

## <a name="next-steps"></a>次のステップ

診断機能で特定できる一般的なエラー シナリオについては、「[問題の特定と診断](diagnostics-role-service.md#common-error-scenarios)」をご確認ください。
---
title: Azure Backup で保護されたワークロードの監視
description: この記事では、Azure portal を使用した Azure Backup ワークロードの監視機能と通知機能について説明します。
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2273b66be88cb22a15d0779ed2918ba3d94da1ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713373"
---
# <a name="monitoring-azure-backup-workloads"></a>Azure Backup ワークロードの監視

Azure Backup では、バックアップ要件とインフラストラクチャ トポロジ (オンプレミスと Azure) に基づく複数のバックアップ ソリューションを提供します。 すべてのバックアップ ユーザーまたは管理者は、すべてのソリューション全体で何が起こっているかを確認し、重要なシナリオで通知を受け取ることができます。 この記事では、Azure Backup サービスによって提供される監視と通知の機能について説明します。

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Recovery Services コンテナーでのバックアップ項目

Recovery Services コンテナーを使用して、すべてのバックアップ項目を監視できます。 コンテナーの **[バックアップ項目]** セクションに移動すると、コンテナーに関連付けられている各ワークロードの種類のバックアップ項目の数を示すビューが開きます。 任意の行をクリックすると、指定したワークロードの種類のすべてのバックアップ項目が一覧表示され、各項目の最後のバックアップの状態、利用可能な最新の復元ポイントなどの情報が表示されます。

![RS コンテナーのバックアップ項目](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> DPM を使用して Azure にバックアップされた項目については、DPM サーバーを使用して保護されているすべてのデータソース (ディスクとオンラインの両方) が一覧に表示されます。 バックアップ データが保持されているデータソースの保護が停止した場合、データソースはポータルに表示されたままになります。 データ ソースの詳細にアクセスして、回復ポイントがディスク、オンライン、またはその両方に存在するかどうかを確認できます。 また、オンラインのデータ保護が停止されていてデータが保持されたデータソースの場合、データが完全に削除されるまで、オンライン回復ポイントの課金が継続されます。
>
> バックアップ項目を Recovery Services コンテナー ポータルに表示するには、DPM のバージョンが DPM 1807 (5.1.378.0) または DPM 2019 (バージョン 10.19.58.0 以降) である必要があります。

## <a name="backup-jobs-in-recovery-services-vault"></a>Recovery Services コンテナーでのバックアップ ジョブ

Azure Backup では、Azure Backup で保護されているワークロード用の組み込みの監視とアラートの機能を提供します。 Recovery Services コンテナーの設定の **[監視]** セクションに、組み込みのジョブおよびアラートが提供されています。

![RS コンテナーの組み込みの監視](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

ジョブは、バックアップの構成、バックアップ、復元、バックアップの削除などの操作が実行されるときに生成されます。

ここには、次の Azure Backup ソリューションからのジョブが表示されます。

- Azure VM バックアップ
- Azure ファイルのバックアップ
- SQL や SAP HANA などの Azure ワークロードのバックアップ
- Microsoft Azure Recovery Services (MARS) エージェント

System Center Data Protection Manager (SC-DPM)、Microsoft Azure Backup Server (MABS) からのジョブは表示されません。

> [!NOTE]
> Azure VM 内の SQL や SAP HANA のバックアップなどの Azure ワークロードには、膨大な数のバックアップ ジョブがあります。 たとえば、ログ バックアップは 15 分ごとに実行できます。 そのため、このようなデータベース ワークロードについては、ユーザーがトリガーした操作のみが表示されます。 スケジュールされたバックアップ操作は表示されません。

## <a name="backup-alerts-in-recovery-services-vault"></a>Recovery Services コンテナーでのバックアップ アラート

> [!NOTE]
> 現在、コンテナーを跨いでアラートを表示することは、Backup Center ではサポートされていません。 特定のコンテナーのアラートを表示するには、個々のコンテナーに移動する必要があります。

アラートは、主に、ユーザーが通知を受け取るシナリオであり、これによりユーザーは関連アクションを行うことができます。 **[バックアップ アラート]** セクションに、Azure Backup サービスによって生成されたアラートが表示されます。 これらのアラートはサービスによって定義され、ユーザーがカスタム アラートを作成することはできません。

### <a name="alert-scenarios"></a>アラート シナリオ

次のシナリオは、サービスによって警告可能シナリオとして定義されています。

- バックアップ/復元エラー
- バックアップの成功 (Microsoft Azure Recovery Services (MARS) エージェントに対する警告を含む)
- 保護の停止 (データの保持を含む)/保護の停止 (データの削除を含む)

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>ここには、次の Azure Backup ソリューションからのアラートが表示されます。

- Azure VM バックアップ
- Azure ファイルのバックアップ
- SQL、SAP HANA などの Azure ワークロードのバックアップ
- Microsoft Azure Recovery Services (MARS) エージェント

> [!NOTE]
> System Center Data Protection Manager (SC-DPM)、Microsoft Azure Backup Server (MABS) からのアラートは表示されません。

### <a name="consolidated-alerts"></a>統合されたアラート

SQL や SAP HANA などの Azure ワークロード バックアップ ソリューションでは、ログ バックアップを非常に頻繁に (ポリシーに従って 15 分ごとに) 生成できます。 そのため、ログ バックアップ エラーも頻繁に発生する可能性があります (最大 15 分間隔)。 このシナリオで、エラーが発生するたびにアラートが発生した場合、エンド ユーザーの気が滅入ってしまいます。 そのため、最初の発生時にはアラートが送信され、それ以降のエラーが同じ根本原因である場合、それ以降のアラートは生成されません。 最初のアラートは、エラー数で更新されます。 しかし、アラートがユーザーによって非アクティブにされている場合は、次の発生時に別の電子メールがトリガーされ、これがその発生に対する最初のアラートとして扱われます。 このようにして Azure Backup は、SQL と SAP HANA のバックアップに対してアラートの統合を実行します。

### <a name="exceptions-when-an-alert-is-not-raised"></a>アラートが生成されない例外

失敗時にアラートが生成されない次のいくつかの例外があります。 これらは次のとおりです。

- ユーザーが実行中のジョブを明示的に取り消しました
- 別のバックアップ ジョブが処理中であるため、ジョブが失敗しました (前のジョブが完了するまで待つしかないため、行うことができる操作はありません)
- バックアップの Azure VM が存在しなくなっているため、VM バックアップ ジョブが失敗します
- [統合されたアラート](#consolidated-alerts)

上記の例外は、これらの操作 (主にユーザーがトリガーするもの) の結果がポータル/PS/CLI クライアントで即時に表示されるという理解に基づいて設計されています。 そのため、ユーザーはすぐにこれに気付くことから、通知は必要ありません。

### <a name="alert-types"></a>アラートの種類

アラートは、その重大度に基づいて次の 3 つの種類に定義できます。

- **[高]** :原則として、バックアップまたは回復が失敗すると (スケジュールされたかユーザーがトリガーしたかを問わず)、アラートが生成されて重大アラートとして表示され、バックアップの削除といった破壊的な操作の原因となります。
- **警告**:バックアップ操作が成功したもののいくつかの警告を伴う場合、これらは警告アラートとして表示されます。 警告アラートは現在、Azure Backup エージェントのバックアップにのみ使用できます。
- **情報**:現時点では、Azure Backup サービスで情報アラートは生成されません。

## <a name="notification-for-backup-alerts"></a>バックアップ アラートの通知

> [!NOTE]
> 通知の構成は、Azure portal を介してのみ実行できます。 PS/CLI/REST API/Azure Resource Manager テンプレートはサポートされていません。

アラートが生成されると、ユーザーは通知を受け取ります。 Azure Backup では、メール経由での組み込みの通知メカニズムを提供しています。 アラートの生成時に通知を受け取る個別のメール アドレスや配布リストを指定できます。 また、それぞれのアラートについて個別に通知を受け取るか、アラートをまとめて 1 時間ごとのダイジェストとして通知を受け取るかを選択することもできます。

![RS コンテナーの組み込みのメール通知](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

通知が構成されている場合、ウェルカム メールまたは導入時のメールを受け取ります。 これは、アラートの生成時に、Azure Backup がこれらのアドレスにメールを送信できるかどうかを確認するものです。<br>

頻度が 1 時間ごとのダイジェストに設定されており、アラートが生成されて 1 時間以内にこれが解決された場合は、次の 1 時間ごとのダイジェストにこのアラートは含まれません。

> [!NOTE]
>
> - 破壊的な操作 (**データを削除して保護を停止** など) が実行されると、アラートが生成され、Recovery Services コンテナー用に通知が構成されていない場合でも、サブスクリプションの所有者、管理者、共同管理者にメールが送信されます。
> - 成功したジョブの通知を構成するには、[Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace) を使用します。

## <a name="inactivating-alerts"></a>アラートの非アクティブ化

アクティブなアラートを無効化または解決するには、非アクティブ化するアラートに対応するリスト アイテムを選択できます。 これにより、アラートに関する詳細情報を表示する画面が開き、上部に **[非アクティブ化]** ボタンが表示されます。 このボタンを選択すると、アラートの状態が **非アクティブ** に変わります。 また、アラートに対応するリスト アイテムを右クリックし、 **[非アクティブ化]** を選択して、アラートを非アクティブにすることもできます。

![RS コンテナーのアラートの非アクティブ化](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Backup の Azure Monitor アラート (プレビュー)

Azure Backup は、Azure Monitor を介してアラートを提供し、ユーザーがバックアップなどのさまざまな Azure サービス間で一貫したアラート管理エクスペリエンスを得られるようにします。 Azure Monitor アラートを使用すると、Azure Backup でサポートされている通知チャネル (電子メール、ITSM、Webhook、ロジック アプリなど) にアラートをルーティングできます。

現時点では、この機能は、Azure Database for PostgreSQL サーバー、Azure Blob、Azure Managed Disks で使用できます。 アラートは次のシナリオに対して生成され、Backup コンテナーに移動して **[アラート]** メニュー項目をクリックすることでアクセスできます。

- バックアップ データを削除する
- バックアップ エラー (バックアップ エラーに関するアラートを取得するには、プレビュー ポータルで **EnableAzureBackupJobFailureAlertsToAzureMonitor** という名前の AFEC フラグを登録する必要があります)
- 復元エラー (復元エラーに関するアラートを取得するには、プレビュー ポータルで **EnableAzureBackupJobFailureAlertsToAzureMonitor** という名前の AFEC フラグを登録する必要があります)

Azure Monitor アラートについて詳しくは、「[Azure のアラートの概要](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-overview)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure Monitor を使用した Azure Backup ワークロードの監視](backup-azure-monitoring-use-azuremonitor.md)

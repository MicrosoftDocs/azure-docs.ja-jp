---
title: SSIS 統合ランタイムでのパッケージ実行のトラブルシューティング | Microsoft Docs
description: この記事では、SSIS 統合ランタイムでの SSIS パッケージ実行に関するトラブルシューティングのガイダンスを提供します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621850"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>SSIS 統合ランタイムでのパッケージ実行のトラブルシューティング

この記事では、SSIS 統合ランタイムで SQL Server Integration Services (SSIS) パッケージを実行しているときに見つかる可能性がある最も一般的なエラーを取り扱います。 ここでは、考えられる原因とそのエラーを解決するためのアクションについて説明します。

## <a name="where-to-find-logs-for-troubleshooting"></a>トラブルシューティングのためのログがある場所

Azure Data Factory ポータルを使用して、SSIS パッケージの実行アクティビティの出力を確認します。 この出力には、実行結果、エラー メッセージ、および操作 ID が含まれています。 詳細については、「[パイプラインの監視](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)」を参照してください。

実行の詳細なログを確認するには、SSIS カタログ (SSISDB) を使用します。 詳細については、「[実行中のパッケージとその他の操作の監視](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)」を参照してください。

## <a name="common-errors-causes-and-solutions"></a>一般的なエラー、原因、解決策

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>エラー メッセージ:"接続がタイムアウトしました" または "要求の処理中にサービスでエラーが発生しました。 再試行してください。"

考えられる原因と推奨されるアクションを次に示します。
* データ ソースまたは宛先が過負荷になっています。 データ ソースまたは宛先の負荷を調べて、十分な容量があるかどうかを確認します。 たとえば、Azure SQL Database を使用した場合、そのデータベースがタイムアウトする可能性があればスケールアップを検討します。
* SSIS 統合ランタイムとデータ ソースまたは宛先との間のネットワークが不安定です (特に複数のリージョンにまたがる接続やオンプレミスと Azure の間の接続の場合)。 次の手順に従って、SSIS パッケージ内で再試行パターンを適用します。
  * エラー時に SSIS パッケージを副作用 (たとえば、データの損失またはデータの重複) なしに再実行できることを確認します。
  * **[全般]** タブで、 **[SSIS パッケージの実行]** アクティビティの **[再試行]** および **[再試行の間隔]** を構成します。![[全般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * ADO.NET および OLE DB のソースまたは宛先コンポーネントでは、SSIS パッケージまたは SSIS アクティビティの接続マネージャーで **ConnectRetryCount** および **ConnectRetryInterval** を設定します。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>エラー メッセージ:"ADO NET 変換元では、接続 '...' を取得できませんでした。" と "SQL Server への接続を確立しているときにネットワーク関連またはインスタンス固有のエラーが発生しました。 サーバーが見つからなかったか、サーバーにアクセスできませんでした。"

この問題は、通常、SSIS 統合ランタイムからデータ ソースまたは宛先にアクセスできないことを意味します。 その理由はさまざまです。 次のアクションを試してください。
* データ ソースまたは宛先の名前または IP を正しく渡していることを確認します。
* ファイアウォールが正しく設定されていることを確認します。
* データ ソースまたは宛先がオンプレミスの場合は、仮想ネットワークが正しく構成されていることを確認します。
  * 同じ仮想ネットワーク内に Azure VM をプロビジョニングすることで、この問題が仮想ネットワークの構成によるものかどうかを確認できます。 その後、Azure VM からデータ ソースまたは宛先にアクセスできるかどうかを確認します。
  * SSIS 統合ランタイムで仮想ネットワークを使用する方法の詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>エラー メッセージ:"ADO NET 変換元では、接続 '...' を取得できませんでした。" と "マネージド接続マネージャーを作成できませんでした。"

考えられる原因は、パッケージで使用されている ADO.NET プロバイダーが SSIS 統合ランタイムにインストールされていないことです。 このプロバイダーは、カスタム セットアップを使用してインストールできます。 カスタム セットアップの詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)」を参照してください。

### <a name="error-message-the-connection--is-not-found"></a>エラー メッセージ:"接続 '...' が見つかりません"

以前のバージョンの SQL Server Management Studio (SSMS) における既知の問題によってこのエラーが発生する可能性があります。 このパッケージに、デプロイの実行に SSMS を使用するマシンにインストールされていないカスタム コンポーネント (SSIS Azure Feature Pack やパートナー コンポーネントなど) が含まれている場合、SSMS ではそのコンポーネントが削除され、エラーが発生します。 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) を、この問題が解決されている最新バージョンにアップグレードしてください。

### <a name="error-messagessis-executor-exit-code--1073741819"></a>エラー メッセージ: "SSIS 実行プログラムの実行コード: -1073741819"

* 考えられる原因と推奨される操作:
  * このエラーは、複数の Excel ソースまたは宛先がマルチスレッドで並列で実行されているときに、Excel のソースと宛先の制限によって発生する場合があります。 この制限を回避するには、順番に実行されるように Excel コンポーネントを変更するか、別々のパッケージに分割して、ExecuteOutOfProcess プロパティを True に設定して "パッケージ実行タスク" を通じてトリガーします。

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>エラー メッセージ:"There is not enough space on the disk (ディスクに十分な領域がありません)"

このエラーは、ローカル ディスクが SSIS 統合ランタイム ノードで使い果たされていることを意味します。 お使いのパッケージまたはカスタム セットアップによってディスク領域が大量に消費されているかどうかを確認します。
* ディスクがお使いのパッケージによって消費されている場合は、パッケージの実行が終了した後に解放されます。
* ディスクがお使いのカスタム セットアップによって消費されている場合は、SSIS 統合ランタイムを停止し、スクリプトを変更してから、もう一度統合ランタイムを開始する必要があります。 カスタム セットアップ用に指定した Azure BLOB コンテナー全体が SSIS 統合ランタイム ノードにコピーされるため、そのコンテナーの下に不要なコンテンツがないかどうかを確認してください。

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>エラー メッセージ:マスターからリソースを取得できませんでした。 Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException:Code:300004。 Description:Load ファイル "***" が失敗しました"

* 考えられる原因と推奨される操作:
  * SSIS アクティビティがファイル システム (パッケージ ファイルまたはプロジェクト ファイル) からパッケージを実行している場合、プロジェクト、パッケージ、または構成ファイルに SSIS アクティビティで指定したパッケージ アクセス資格情報でにアクセスできない場合にこのエラーが発生します。
    * Azure File を使用する場合:
      * ファイル パスは、\\\\\<ストレージ アカウント名\>.file.core.windows.net\\\<ファイル共有パス\> で開始する必要があります。
      * ドメインは "Azure" にします
      * ユーザー名は \<ストレージ アカウント名\> である必要があります
      * パスワードは \<ストレージ アクセス キー\> である必要があります
    * オンプレミスのファイルを使用する場合は、Azure-SSIS 統合ランタイムがオンプレミスのファイル共有にアクセスできるように、VNet、パッケージ アクセス資格情報、およびアクセス許可が適切に構成されていることを確認してください。

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>エラー メッセージ:"接続に指定されたファイル名 '...' は無効です"

* 考えられる原因と推奨される操作:
  * 無効なファイル名が指定されている
  * 接続マネージャー内で、短い時間ではなく FQDN (完全修飾ドメイン名) を使用していることを確認します。

### <a name="error-message-cannot-open-file-"></a>エラー メッセージ:"Cannot open file '...' (ファイル '...' を開けません)"

このエラーは、SSIS 統合ランタイムでパッケージの実行時にファイルがローカル ディスク内に見つからない場合に発生します。 次のアクションを試してください。
* SSIS 統合ランタイムで実行されているパッケージでは絶対パスを使用しないでください。 代わりに、現在実行中の作業ディレクトリ (.) または一時フォルダー (%TEMP%) を使用してください。
* SSIS 統合ランタイム ノード上にファイルを保持する必要がある場合は、[セットアップのカスタマイズ](how-to-configure-azure-ssis-ir-custom-setup.md)に関するページの説明に従ってファイルを準備します。 作業ディレクトリ内のすべてのファイルは、実行が完了した後にクリーンアップされます。
* SSIS 統合ランタイム ノードにファイルを格納する代わりに、Azure Files を使用します。 詳細については、「[Azure Files 共有を使用する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)」を参照してください。

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>エラー メッセージ:"データベース 'SSISDB' のサイズ クォータに達しました"

考えられる原因は、Azure SQL データベースに作成された SSISDB データベース、または SSIS 統合ランタイム作成時のマネージド インスタンスがクォータに達したことです。 次のアクションを試してください。
* データベースの DTU を増やすことを検討します。 詳細については、「[Azure SQL Database サーバーの SQL Database リソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)」で確認できます。
* お使いのパッケージによって大量のログが生成されているかどうかを確認します。 その場合は、これらのログをクリーンアップするようエラスティック ジョブを構成できます。 詳細については、「[Azure Elastic Database ジョブで SSISDB のログをクリーンアップする](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)」を参照してください。

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>エラー メッセージ:"データベースの要求制限 ... に達しました。"

SSIS 統合ランタイムで多数のパッケージが並列に実行されている場合、SSISDB がその要求制限に達したことが原因でこのエラーが発生する可能性があります。 この問題を解決するには、SSISDB の DTC を増やすことを検討してください。 詳細については、「[Azure SQL Database サーバーの SQL Database リソース制限](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)」で確認できます。

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>エラー メッセージ:"SSIS Operation failed with unexpected operation status: ... (SSIS 操作が予期しない操作状態で失敗しました: ...)"

ほとんどの場合、このエラーは一時的な問題が原因で発生するため、パッケージの実行を再試行します。 次の手順に従って、SSIS パッケージ内で再試行パターンを適用します。

* エラー時に SSIS パッケージを副作用 (たとえば、データの損失またはデータの重複) なしに再実行できることを確認します。
* **[全般]** タブで、 **[SSIS パッケージの実行]** アクティビティの **[再試行]** および **[再試行の間隔]** を構成します。![[全般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* ADO.NET および OLE DB のソースまたは宛先コンポーネントでは、SSIS パッケージまたは SSIS アクティビティの接続マネージャーで **ConnectRetryCount** および **ConnectRetryInterval** を設定します。

### <a name="error-message-there-is-no-active-worker"></a>エラー メッセージ:"There is no active worker. (アクティブなワーカーがありません。)"

通常、このエラーは、SSIS 統合ランタイムが異常な状態にあることを意味します。 Azure portal で状態と詳細なエラーを確認します。 詳細については、「[Azure-SSIS 統合ランタイム](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)」を参照してください。

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>エラー メッセージ:"Your integration runtime cannot be upgraded and will eventually stop working, since we cannot access the Azure Blob container you provided for custom setup. (カスタム セットアップ用に指定された Azure BLOB コンテナーにアクセスできないため、統合ランタイムはアップグレードできず、最終的に機能しなくなります。)"

このエラーは、カスタム セットアップ用に構成されたストレージに SSIS 統合ランタイムからアクセスできない場合に発生します。 指定した Shared Access Signature (SAS) URI が有効で、期限切れになっていないかどうかを確認します。

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>エラー メッセージ:"Microsoft OLE DB Provider for Analysis Services。 'Hresult: 0x80004005 説明:' COM エラー: COM エラー: mscorlib; 呼び出しのターゲットが例外をスローしました"

考えられる原因の 1 つは、Azure Analysis Services の認証用に、Azure Multi-Factor Authentication が有効になっているユーザー名またはパスワードが構成されていることです。 この認証は、SSIS 統合ランタイムではサポートされていません。 Azure Analysis Services の認証には、サービス プリンシパルを使用してみてください。
1. 「[サービス プリンシパルによる自動化](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)」の説明に従って、サービス プリンシパルを準備します。
2. 接続マネージャーで、 **[特定のユーザー名とパスワードを使用する]** を構成します。ユーザー名として **AppID**、パスワードとして **clientSecret** を設定します。

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>エラー メッセージ:"ADO NET 変換元では、接続 {GUID} を取得できませんでした。エラー メッセージ: ユーザー 'NT AUTHORITY\ANONYMOUS LOGON' はログインできませんでした。'" (マネージド ID の使用時)

*ConnectUsingManagedIdentity* パラメーターが **True** のときは、接続マネージャーの認証方法を **[Active Directory パスワード認証]** として構成していないことを確認してください。 代わりに **[SQL 認証]** として構成できます。これは、*ConnectUsingManagedIdentity* が設定されている場合は無視されます。

### <a name="package-execution-takes-too-long"></a>パッケージの実行に時間がかかりすぎる

考えられる原因と推奨されるアクションを次に示します。
* SSIS 統合ランタイムでスケジュール設定されているパッケージの実行が多すぎます。 これらのすべての実行は、キュー内で順番を待つことになります。
  * 次の数式を使用して、最大値を確認します。 
    
    IR あたりの最大並列実行数 = ノード数 * ノードあたりの最大並列実行数
  * ノード数とノードあたりの最大並列実行数を設定する方法については、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](create-azure-ssis-integration-runtime.md)」を参照してください。
* SSIS 統合ランタイムが停止しているか、異常な状態にあります。 SSIS 統合ランタイムの状態とエラーを確認する方法については、「[Azure-SSIS 統合ランタイム](monitor-integration-runtime.md#azure-ssis-integration-runtime)」を参照してください。

さらに、 **[全般]** タブでタイムアウトを設定することもお勧めします。![[全般] タブでプロパティを設定する](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)。

### <a name="poor-performance-in-package-execution"></a>パッケージの実行のパフォーマンスが低い

次のアクションを試してください。

* SSIS 統合ランタイムがデータ ソースや宛先と同じリージョンにあることを確認します。

* パッケージの実行のログ レベルを **[パフォーマンス]** に設定して、実行内の各コンポーネントの実行時間の情報を収集します。 詳細については、「[Integration Services (SSIS) のログ記録](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)」を参照してください。

* Azure portal で IR ノードのパフォーマンスを確認します。
  * SSIS 統合ランタイムを監視する方法については、「[Azure-SSIS 統合ランタイム](monitor-integration-runtime.md#azure-ssis-integration-runtime)」を参照してください。
  * Azure portal でデータ ファクトリのメトリックを表示することで、SSIS 統合ランタイムの CPU またはメモリの履歴を確認できます。
    ![SSIS 統合ランタイムのメトリックを監視する](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)

---
title: 診断と Message Analyzer を使用した Azure Storage のトラブルシューティング | Microsoft Docs
description: Azure Storage Analytics、AzCopy、Microsoft Message Analyzer を使用したエンド ツー エンド トラブルシューティングについて説明するチュートリアル
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: f88a560d4fa819a055534530ddc0862e4aa330fe
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351883"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Azure Storage のメトリックおよびログ、AzCopy、Message Analyzer を使用したエンド ツー エンド トラブルシューティング
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

診断とトラブルシューティングは、Microsoft Azure Storage を使用してクライアント アプリケーションを構築し、サポートするうえでの重要なスキルです。 Azure アプリケーションの分散型の性質により、エラーやパフォーマンスの問題に対する診断とトラブルシューティングは、従来の環境で実施するよりも複雑になる場合があります。

このチュートリアルでは、クライアント アプリケーションの最適化を目的に、パフォーマンスに影響する可能性のある特定のエラーを特定すると共に、Microsoft と Azure Storage が提供するツールを使用して、これらのエラーについてエンド ツー エンドのトラブルシューティングを実施する方法を説明します。

このチュートリアルでは、エンド ツー エンド トラブルシューティングのシナリオを実践的に学ぶことができます。 Azure Storage アプリケーションのトラブルシューティングに関する詳細な概念ガイドについては、「 [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)」を参照してください。

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Azure Storage アプリケーションのトラブルシューティングに使用するツール
Microsoft Azure Storage を使用してクライアント アプリケーションのトラブルシューティングを実施する場合、問題がいつ発生したのか、原因は何なのかを見極めるために複数のツールを組み合わせて使用することができます。 これには以下のツールが含まれます。

* **Azure Storage Analytics**。 [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) では、Azure Storage 向けのメトリックとログが利用できます。

  * **ストレージ メトリック** は、ストレージ アカウントのトランザクション メトリックと容量メトリックを追跡するためのものです。 メトリックを使用すると、アプリケーションがどのように機能しているかを、さまざまな基準に従って判断できます。 Storage Analytics が追跡するメトリックの種類の詳細については、「 [Storage Analytics Metrics のテーブル スキーマ](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) 」を参照してください。
  * **ストレージ ログ** は、Azure Storage サービスへの各要求をサーバー側のログに記録します。 このログは、実行された操作、操作のステータス、遅延情報などを含む各要求の詳細データを追跡します。 Storage Analytics がログに書き込む要求および応答データの詳細については、「 [Storage Analytics のログの形式](/rest/api/storageservices/Storage-Analytics-Log-Format) 」を参照してください。

* **Azure Portal** [Azure Portal](https://portal.azure.com) では、ストレージ アカウントのメトリックとログを構成することができます。 アプリケーションの経時的な動作を表すチャートやグラフを表示できるほか、指定したメトリックに関してアプリケーションが予期しない動作をした場合に通知するよう、アラートを構成することもできます。

    Azure Portal での監視の構成については、「[Azure Portal でのストレージ アカウントの監視](storage-monitor-storage-account.md)」をご覧ください。
* **AzCopy**。 Azure Storage のサーバー ログは BLOB として格納されているので、AzCopy を使用してログ BLOB をローカル ディレクトリにコピーし、Microsoft Message Analyzer による分析に使用することができます。 AzCopy の詳細については、「[AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」をご覧ください。
* **Microsoft Message Analyzer**。 Message Analyzer はログ ファイルを使用してログ データを画像形式で表示するツールです。これにより、ログ データのフィルター処理や検索のほか、エラーやパフォーマンス問題の分析に使用できる有用なデータ セットへのグループ化が容易になります。 Message Analyzer の詳細については、[Microsoft Message Analyzer の操作ガイド](https://technet.microsoft.com/library/jj649776.aspx)を参照してください。

## <a name="about-the-sample-scenario"></a>サンプル シナリオについて
このチュートリアルでは、Azure Storage を呼び出すアプリケーションの成功率に関して、Azure Storage メトリックが低い割合を示すシナリオについて調べます。 低い成功率メトリック (**PercentSuccess** として [Azure Portal](https://portal.azure.com) とメトリック テーブル内に表示される) では、成功した操作を追跡しますが、299 を超える数字の HTTP 状態コードを返します。 サーバー側のストレージ ログ ファイルには、これらの操作が **ClientOtherErrors**のトランザクション状態で記録されます。 低い成功率メトリックの詳細については、「 [メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success)」を参照してください。

Azure Storage の操作では、その通常の機能の一部として 299 を超える数字の HTTP 状態コードを返す場合があります。 しかしこれらのエラーは、クライアント アプリケーションのパフォーマンスを最適化できる可能性があることを示している場合もあります。

このシナリオでは、100% 未満の値についてはすべて低い成功率であると判断します。 ただし、必要に応じて異なるメトリック レベルを選択することができます。 アプリケーションのテスト時には、主なパフォーマンス メトリックについて基準となる許容値を設定することをお勧めします。 たとえば、アプリケーションの成功率は 90% または 85% を維持する必要があることを、テストに基づいて決定することができます。 アプリケーションがその数値から外れていることをメトリック データが示している場合、その増加を引き起こしているのが何なのかを調査することができます。

サンプル シナリオでは、成功率メトリックの割合を 100% 未満に設定した後、ログを調べてそのメトリックと相関関係があるエラーを見つけ、それを使用して成功率が低くなる原因を解明します。 ここでは、特に 400 番台のエラーに着目します。 その後、404 (Not Found) エラーに的を絞って調査します。

### <a name="some-causes-of-400-range-errors"></a>400 番台のエラーの原因
以下の例では、Azure Blob Storage への要求に関するいくつかの 400 番台のエラーと、その考えられる原因を示します。 これらのエラーは、300 および 500 番台のエラーと同様に、いずれも低い成功率の一因となる可能性があります。

以下の一覧は不完全であるという点に注意してください。 通常の Azure Storage エラーおよび各ストレージ サービスに特有のエラーの詳細については、MSDN の「 [状態コードとエラー コード](https://msdn.microsoft.com/library/azure/dd179382.aspx) 」を参照してください。

**ステータス コード 404 (Not Found) の例**

BLOB またはコンテナーが見つからないことが原因で、それらに対する読み取り操作が失敗した場合に発生します。

* この要求の前に別のクライアントによってコンテナーまたは BLOB が削除された場合に発生します。
* コンテナーまたは BLOB を作成する前に、それらが存在するかどうかを確認する API 呼び出しを使用する場合に発生します。 CreateIfNotExists API は最初に HEAD 呼び出しを実行し、コンテナーまたは BLOB が存在するかどうかを確認します。存在しない場合は 404 エラーが返され、その後、2 番目の PUT 呼び出しを実行してコンテナーまたは BLOB を書き込みます。

**状態コード 409 (Conflict) の例**

* Create API を使用して最初に存在を確認することなくコンテナーまたは BLOB を作成する際、同じ名前のコンテナーまたは BLOB が既に存在する場合に発生します。
* コンテナーが削除される際、その削除操作が完了する前に同じ名前の新しいコンテナーを作成しようとした場合に発生します。
* コンテナーまたは BLOB にリースを指定する際、既にリースが存在している場合に発生します。

**状態コード 412 (Precondition Failed) の例**

* 条件ヘッダーにより指定された条件が満たされていない場合に発生します。
* 指定されたリース ID がコンテナーまたは BLOB のリース ID と一致しない場合に発生します。

## <a name="generate-log-files-for-analysis"></a>分析用にログ ファイルを生成する
このチュートリアルでは、Message Analyzer を使用して 3 種類のログ ファイルを操作しますが、実際にはどれか 1 つを選択して操作できます。

* **サーバー ログ**は、Azure Storage ログを有効にすると作成されます。 サーバー ログには、Azure Storage サービスの 1 つ、つまり BLOB、キュー、テーブル、ファイルに対して呼び出された各操作についてのデータが含まれます。 サーバー ログは、どの操作が呼び出され、どの状態コードが返されたかに加え、要求と応答に関するその他の詳細を示します。
* **.NET クライアント ログ**は、.NET アプリケーション内部からクライアント側のログを有効にした場合に作成されます。 このクライアント ログには、クライアントが要求を準備し、応答を受信して処理する方法についての詳細情報が含まれます。
* **HTTP ネットワーク トレース ログ**は、HTTP/HTTPS の要求および応答データを収集しますが、これには Azure Storage に対する操作についてのデータも含まれます。 このチュートリアルでは、Message Analyzer を使用してネットワーク トレースを生成します。

### <a name="configure-server-side-logging-and-metrics"></a>サーバー側のログとメトリックを構成する
まず、サービス側から分析用データを取得できるよう、Azure Storage のログとメトリックを構成する必要があります。 [Azure Portal](https://portal.azure.com) や PowerShell を使用する方法、プログラミングによって構成する方法など、さまざまな方法でログとメトリックを構成することができます。 ログとメトリックの構成の詳細については、[メトリックの有効化](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal)に関するページと[ログの有効化](storage-analytics-logging.md#enable-storage-logging)に関するページをご覧ください。

### <a name="configure-net-client-side-logging"></a>.NET のクライアント側のログを構成する
.NET アプリケーションのクライアント側のログを構成するには、アプリケーションの構成ファイル (web.config または app.config) 内で .NET 診断を有効にします。 詳しくは、MSDN の「[.NET ストレージ クライアント ライブラリと、クライアント側のログ記録](https://msdn.microsoft.com/library/azure/dn782839.aspx)」および「[Microsoft Azure Storage SDK for Java によるクライアント側のログ](https://msdn.microsoft.com/library/azure/dn782844.aspx)」をご覧ください。

クライアント側のログには、クライアントが要求を準備し、応答を受信して処理する方法についての詳細情報が含まれます。

ストレージ クライアント ライブラリは、クライアント側のログ データをアプリケーションの構成ファイル (web.config または app.config) 内で指定した場所に格納します。

### <a name="collect-a-network-trace"></a>ネットワーク トレースを収集する
Message Analyzer を使用して、クライアント アプリケーションの動作時の HTTP/HTTPS ネットワーク トレースを収集することができます。 Message Analyzer は、バックエンドで [Fiddler](https://www.telerik.com/fiddler) を使用します。 ネットワーク トレースを収集する前に、暗号化されていない HTTPS トラフィックを記録するよう、Fiddler を構成することをお勧めします。

1. [Fiddler](https://www.telerik.com/download/fiddler)をインストールします。
2. Fiddler を起動します。
3. **[Tools]、[Fiddler Options]** の順に選択します。
4. Options ダイアログで、以下に示すように **[Capture HTTPS CONNECTs]** と **[Decrypt HTTPS Traffic]** が両方とも選択されている状態にします。

![Fiddler オプションの構成](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

このチュートリアルでは、まず Message Analyzer でネットワーク トレースを収集して保存し、次に分析セッションを作成してトレースとログを分析します。 Message Analyzer でネットワーク トレースを収集するには:

1. Message Analyzer で、**[File]、[Quick Trace]、[Unencrypted HTTPS]** の順に選択します。
2. すぐにトレースが開始します。 **[Stop]** を選択してトレースを停止します。これで、ストレージ トラフィックのみをトレースするように構成することができます。
3. **[Edit]** を選択してトレース セッションを編集します。
4. **Microsoft-Pef-WebProxy** ETW プロバイダーの右側にある **[Configure]** リンクを選択します。
5. **[Advanced Settings]** ダイアログで、**[Provider]** タブをクリックします。
6. **Hostname Filter** フィールドに、スペースで区切られたストレージ エンドポイントを指定します。 たとえば、次のようにエンドポイントを指定することができます。`storagesample` をストレージ アカウントの名前に変更してください。

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. ダイアログを終了し、**[Restart]** をクリックして、ホスト名フィルターを配置した状態でトレースの収集を開始します。これで、Azure Storage ネットワーク トラフィックのみがトレースに含まれるようになります。

> [!NOTE]
> ネットワーク トレースの収集が完了したら、Fiddler で変更した HTTPS トラフィックを復号化する設定を元に戻すことを強くお勧めします。 [Fiddler Options] ダイアログで、**[Capture HTTPS CONNECTs]** および **[Decrypt HTTPS Traffic]** チェックボックスをオフにします。
>
>

詳細については、Technet の [ネットワーク トレース機能の使用](https://technet.microsoft.com/library/jj674819.aspx) に関するページを参照してください。

## <a name="review-metrics-data-in-the-azure-portal"></a>Azure Portal でメトリック データを確認する
アプリケーションが動作を開始してから一定の時間が経過すると、[Azure Portal](https://portal.azure.com) に表示されるメトリック チャートを確認し、サービスがどのように動作しているかを観察することができます。

まず、Azure Portal のストレージ アカウントに移動します。 既定では、**成功のパーセンテージ** メトリック付きの監視グラフが [アカウント] ブレードに表示されます。 別のメトリックを表示するようにグラフを変更している場合は、**成功のパーセンテージ** メトリックを追加します。

これで、監視グラフに **成功のパーセンテージ**が、追加した他のメトリックと共に表示されます。 次に調査するシナリオでは Message Analyzer でログを分析しますが、成功率が 100% をいくらか下回っています。

メトリック グラフへの追加とカスタマイズの詳細については、「[メトリック グラフのカスタマイズ](storage-monitor-storage-account.md#customize-metrics-charts)」を参照してください。

> [!NOTE]
> ストレージ メトリックを有効にした後、Azure Portal にメトリック データが表示されるまでには少し時間がかかる場合があります。 これは、1 つ前の時間単位メトリックが、現在の単位時間が経過するまでは Azure Portal に表示されないためです。 また、分単位メトリックについては現在のところ Azure Portal には表示されません。 つまり、メトリックを有効にしたタイミングによっては、メトリック データが表示されるまでに最大で 2 時間を要する可能性があります。
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>AzCopy を使用してサーバー ログをローカル ディレクトリにコピーする
Azure Storage は、サーバー ログ データを BLOB に書き込みますが、メトリックについてはテーブルに書き込みます。 ログ BLOB は、ストレージ アカウントの `$logs` コンテナー内にあります。 ログ BLOB は、年、月、日、時間により階層的に名前が付けられるので、調査する時間範囲が簡単に見つかります。 たとえば、`storagesample` アカウントでの、2015 年 1 月 2 日の午前 8 時から 9 時に記録されたログ BLOB のコンテナーは、`https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` です。 このコンテナー内の個々の BLOB は、 `000000.log`から始まる連番で名前が付けられます。

AzCopy コマンドライン ツールを使用して、これらのサーバー側のログ ファイルをローカル コンピューター上の好きな場所にダウンロードすることができます。 たとえば、次のコマンドを使用して、2015 年 1 月 2 日に記録された BLOB 操作のログ ファイルを、`C:\Temp\Logs\Server` フォルダーにダウンロードすることができます。`<storageaccountname>` はストレージ アカウント名に置き換え、`<storageaccountkey>` はアカウントのアクセス キーに置き換えます。

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy は、 [Azure ダウンロード](https://azure.microsoft.com/downloads/) のページからダウンロードできます。 AzCopy の使用方法の詳細については、「 [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)」をご覧ください。

サーバー側のログのダウンロードの詳細については、「 [ストレージ ログのログ データのダウンロード](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata)」をご覧ください。

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Microsoft Message Analyzer を使用してログ データを分析する
Microsoft Message Analyzer は、トラブルシューティングや診断のシナリオにおいて、プロトコル メッセージング トラフィック、イベント、およびその他のシステムやアプリケーションに対してキャプチャ、表示、分析を実施するためのツールです。 また、Message Analyzer では、ログや保存したトレース ファイルからのデータの読み込み、集計、分析ができます。 Message Analyzer の詳細については、 [Microsoft Message Analyzer の操作ガイド](https://technet.microsoft.com/library/jj649776.aspx)に関するページを参照してください。

Message Analyzer には、サーバー、クライアント、およびネットワーク ログの分析に役立つ、Azure Storage 向けのアセットが含まれています。 このセクションでは、それらのツールを使用して、ストレージ ログにおける低い成功率の問題に対処する方法を説明します。

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Message Analyzer と Azure Storage アセットをダウンロードしてインストールする
1. [Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) を Microsoft ダウンロード センターからダウンロードし、インストーラーを実行します。
2. Message Analyzer を起動します。
3. **[ツール]** メニューの **[資産マネージャー]** を選択します。 **[資産マネージャー]** ダイアログで、**[ダウンロード]** を選択し、**Azure Storage** をフィルター処理します。 以下の画像のように、Azure Storage アセットが表示されます。
4. **[Sync All Displayed Items]** をクリックして、Azure Storage アセットをインストールします。 以下に利用できるアセットを挙げます。
   * **Azure Storage の色ルール:** Azure Storage の色ルールにより、色、テキスト、およびフォント スタイルを使用する特別なフィルターを定義し、トレース内の特定の情報を含むメッセージを強調表示することができます。
   * **Azure Storage チャート:** Azure Storage チャートは、サーバー ログ データをグラフ化する定義済みのチャートです。 現時点で Azure Storage チャートを使用する場合、Analysis Grid に読み込むことができるのはサーバー ログのみである点に注意してください。
   * **Azure Storage パーサー:** Azure Storage パーサーは、Azure Storage クライアント、サーバー、および HTTP のログを解析して Analysis Grid に表示します。
   * **Azure Storage フィルター:** Azure Storage フィルターは、Analysis Grid でデータのクエリに使用できる、定義済みの条件です。
   * **Azure Storage ビュー レイアウト:** Azure Storage ビュー レイアウトは、列の定義済みレイアウトであり、Analysis Grid 内のグループです。
5. アセットのインストール後に、Message Analyzer を再起動します。

![Message Analyzer の資産マネージャー](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> このチュートリアルのために、表示された Azure Storage アセットはすべてインストールしてください。
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>ログ ファイルを Message Analyzer にインポートする
すべての保存済みログ ファイル (サーバー側、クライアント側、ネットワーク) を、分析のために Microsoft Message Analyzer 内の単一のセッションにインポートできます。

1. Microsoft Message Analyzer の **[File]** メニューで、**[New Session]**、**[Blank Session]** の順にクリックします。 **[New Session]** ダイアログで、分析セッションの名前を入力します。 **[Session Details]** パネルで、**[Files]** をクリックします。
2. Message Analyzer によって生成されるネットワーク トレース データを読み込むには、**[Add Files]** をクリックし、Web トレース セッションから .matp ファイルを保存した場所に移動して .matp ファイルを選択し、**[Open]** をクリックします。
3. サーバー側のログ データを読み込むには、**[Add Files]** をクリックし、サーバー側のログをダウンロードした場所に移動します。そのうえで、分析対象の時間範囲のログ ファイルを選択し、**[Open]** をクリックします。 次に、**[Session Details]** パネルで、各サーバー側ログ ファイルの **[Text Log Configuration]** ボックスを **[AzureStorageLog]** に設定し、Microsoft Message Analyzer がログ ファイルを正しく解析できるようにします。
4. クライアント側のログ データを読み込むには、**[Add Files]** をクリックし、クライアント側のログを保存した場所に移動します。そのうえで、分析対象のログ ファイルを選択し、**[Open]** をクリックします。 次に、**[Session Details]** パネルで、各クライアント側ログ ファイルの **[Text Log Configuration]** ボックスを **[AzureStorageClientDotNetV4]** に設定し、Microsoft Message Analyzer がログ ファイルを正しく解析できるようにします。
5. **[New Session]** ダイアログで **[Start]** をクリックし、ログ データの読み込みと解析を実施します。 ログ データは Message Analyzer Analysis Grid に表示されます。

以下の画像では、サーバー、クライアント、およびネットワーク トレースのログ ファイルで構成したセッションの例が表示されています。

![Message Analyzer のセッションの構成](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Message Analyzer はログ ファイルをメモリに読み込むという点に注意してください。 大きなサイズのログ データ セットを持っている場合は、Message Analyzer のパフォーマンスを最大限に引き出すため、そのファイルにフィルターを適用することをお勧めします。

最初に、確認したいタイム フレームを決めますが、このタイム フレームはできる限り短い期間にします。 多くの場合、確認するタイム フレームは数分から、最大でも数時間に制限する必要があります。 必要条件を満たすログ セットの中から、最小サイズのものをインポートします。

まだサイズの大きいログ データが残っている場合は、セッション フィルターを指定して、読み込む前にログ データをフィルター処理する必要があります。 **[Session Filter]** ボックスで、**[Library]** をクリックして定義済みのフィルターを選択します。たとえば、Azure Storage フィルターから **[Global Time Filter I]** を選択して、時間間隔によるフィルターを適用します。 その後、フィルター条件を編集し、確認したい時間間隔について開始および終了時のタイムスタンプを指定することができます。 特定の状態コードによってフィルターを適用することもできます。たとえば、状態コードが 404 のログ エントリのみを読み込むように選択できます。

Microsoft Message Analyzer に対するログ データのインポートの詳細については、TechNet の [メッセージ データの取得](https://technet.microsoft.com/library/dn772437.aspx) に関するページを参照してください。

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>クライアント要求 ID を使用してログ ファイル データを関連付ける
Azure ストレージ クライアント ライブラリは、すべての要求に対して自動的に一意のクライアント要求 ID を生成します。 この値はクライアント ログ、サーバー ログ、ネットワーク トレースに書き込まれるため、Message Analyzer 内の 3 種類のログすべてにわたって、この値をデータの関連付けに使用することができます。 クライアント要求 ID の詳細については、「 [クライアント要求 ID](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) 」を参照してください。

以下のセクションでは、定義済みのカスタム レイアウト ビューを使用して、クライアント要求 ID に基づきデータの関連付けとグループ化を行う方法について説明します。

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>ビュー レイアウトを選択して Analysis Grid に表示する
Message Analyzer 向けの Storage アセットには Azure Storage View Layouts という定義済みのビューが含まれていますが、これを使用すると、さまざまなシナリオに役立つグループや列によってデータを表示することができます。 カスタム ビュー レイアウトを作成し、それを保存して再利用することもできます。

以下の画像では、ツール バー リボンから **[View Layout]** を選択するとことでアクセスできる、**[View Layout]** メニューが表示されています。 Azure Storage のビュー レイアウトは、このメニューの **[Azure Storage]** ノードの下にグループ化されています。 検索ボックスで `Azure Storage` を検索すると、Azure Storage ビュー レイアウトのみでフィルター処理できます。 ビュー レイアウトの横にある星を選択してお気に入りに設定し、メニューの最上部に表示することもできます。

![[View Layout] メニュー](./media/storage-e2e-troubleshooting/view-layout-menu.png)

まずは、 **[Grouped by ClientRequestID and Module]** を選択します。 このビュー レイアウトは、3 種類すべてのログからのログ データを、最初はクライアント要求 ID、次にソース ログ ファイル (または Message Analyzer 内の **Module** ) によってグループ化しています。 このビューを使用すると、特定のクライアント要求 ID へとドリル ダウンし、そのクライアント要求 ID について、3 種類すべてのログ ファイルのデータを見ることができます。

以下の画像では、このレイアウト ビューがサンプル ログ データに適用され、列のサブセットが表示されています。 特定のクライアント要求 ID について、Analysis Grid がクライアント ログ、サーバー ログ、ネットワーク トレースのデータを表示していることがわかります。

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> ログ ファイルの種類によって含まれる列は異なるため、Analysis Grid に複数のログ ファイルのデータが表示された場合、いくつかの列は任意の行に何のデータも含まれていない場合があります。 たとえば前の図では、**[Timestamp]**、**[TimeElapsed]**、**[Source]**、**[Destination]** の各列について、クライアント ログの行には何のデータも表示されていませんが、この原因は、クライアント ログにはこれらの列が存在せず、ネットワーク トレースには存在するためです。 同様に、**[Timestamp]** 列にはサーバー ログのタイムスタンプ データが表示されていますが、サーバー ログには含まれていない **[TimeElapsed]**、**[Source]**、**[Destination]** の各列には何も表示されていません。
>
>

Azure Storage ビュー レイアウトを使用するだけでなく、独自のビュー レイアウトを定義して保存することもできます。 データのグループ化に必要なその他のフィールドを選択し、そのグループをカスタム レイアウトの一部として保存することができます。

### <a name="apply-color-rules-to-the-analysis-grid"></a>Analysis Grid にカラー ルールを適用する
Storage アセットにはカラー ルールも含まれており、これによって Analysis Grid で異なる種類のエラーを視覚的に識別できます。 定義済みのカラー ルールは HTTP エラーに適用されるため、サーバー ログとネットワーク トレースに対してだけ影響があります。

カラー ルールを適用するには、ツール バー リボンから **[Color Rules]** を選択します。 Azure Storage カラー ルールがメニューに表示されます。 このチュートリアルでは、以下の画像のように **[Client Errors (StatusCode between 400 and 499)]** を選択します。

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Azure Storage カラー ルールを使用するだけでなく、独自のカラー ルールを定義して保存することもできます。

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>ログ データをグループ化してフィルターを適用し、400 番台のエラーを見つける
次に、ログ データをグループ化してフィルターを適用し、400 番台のすべてのエラーを見つけます。

1. Analysis Grid で **[StatusCode]** 列を見つけて列見出しを右クリックし、**[Group]** を選択します。
2. 次に、 **[ClientRequestId]** 列によってグループ化します。 これで、Analysis Grid 内のデータが状態コードとクライアント要求 ID によって整理されたことがわかります。
3. View Filter ツール ウィンドウがまだ表示されていない場合、これを表示します。 ツール バー リボンで、**[Tool Windows]**、**[View Filter]** の順に選択します。
4. ログ データにフィルターを適用して 400 番台のエラーのみを表示するには、次のフィルター条件を **[View Filter]** ウィンドウに追加し、**[Apply]** をクリックします。

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

以下の画像では、このグループ化およびフィルター適用の結果が表示されています。 たとえば、状態コード 409 のグループ内にある **ClientRequestID** フィールドを展開し、その状態コードを引き起こした操作を表示します。

![Azure Storage View Layout](./media/storage-e2e-troubleshooting/400-range-errors1.png)

クライアント ログに **StatusCode** 列が含まれていないため、このフィルターを適用した後は、クライアント ログの行が除外されていることがわかります。 まず、サーバーおよびネットワーク トレース ログを確認して 404 エラーを見つけてから、クライアント ログに戻ってそのエラーを引き起こしたクライアント操作を調べます。

> [!NOTE]
> ステータス コードが null であるログ エントリを含むフィルターに式を追加すると、 **[StatusCode]** 列によるフィルターを適用しつつ、クライアント ログを含む 3 つのログすべてのデータを表示することができます。 このフィルター式を作成するには、以下を使用します:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> このフィルターは、クライアント ログのすべての行を返し、サーバー ログと HTTP ログからは状態コードが 400 を超える行のみを返します。 クライアント要求 ID とモジュールによりグループ化したビュー レイアウトにこれを適用した場合、ログ エントリを検索するか下へスクロールすれば、3 つのログがすべて表示されている場所を見つけることができます。   
>
>

### <a name="filter-log-data-to-find-404-errors"></a>ログ データにフィルターを適用して 404 エラーを見つける
Storage アセットには定義済みのフィルターが含まれており、これを使用してログ データを絞り込み、探しているエラーや傾向を見つけることができます。 次は、2 つの定義済みフィルターを適用します。1 つは 404 エラーについてサーバーおよびネットワーク トレース ログに適用するフィルターで、もう 1 つは指定した時間範囲についてそれらのデータに適用するフィルターです。

1. View Filter ツール ウィンドウがまだ表示されていない場合、これを表示します。 ツール バー リボンで、**[Tool Windows]**、**[View Filter]** の順に選択します。
2. View Filter ウィンドウで、**[Library]** を選択し、`Azure Storage` を検索して Azure Storage フィルターを見つけます。 **[404 (Not Found) messages in all logs]** のフィルターを選択します。
3. もう一度 **[Library]** メニューを表示し、**[Global Time Filter]** を見つけて選択します。
4. フィルター内に表示されるタイムスタンプを、確認したい範囲に編集します。 これにより、分析するデータの範囲を絞り込むことができます。
5. フィルターは以下の例のように表示されます。 **[Apply]** をクリックして Analysis Grid にフィルターを適用します。

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Azure Storage View Layout](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>ログ データを分析する
データをグループ化してフィルターを適用したので、404 エラーを生成した個々の要求について詳細を調べることができます。 現在のビュー レイアウトでは、データはまずクライアント要求 ID、次にログ ソースによってグループ化されています。 StatusCode フィールドに 404 が含まれる要求によってフィルター処理しているため、サーバーおよびネットワーク トレースのデータのみが表示され、クライアント ログのデータは表示されません。

以下の画像の要求を見ると、BLOB が存在しないために、Get Blob 操作によって 404 が発生しています。 関連データを表示するために、標準ビューから削除されている列があることに注意してください。

![フィルター選択されたサーバーとネットワークのトレース ログ](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

次に、このクライアント要求 ID とクライアント ログ データを関連付けて、エラーが発生した際にクライアントがどのように対処していたかを確認します。 このセッションのために新しい Analysis Grid ビューを表示してクライアント ログ データを確認することができますが、このビューは 2 番目のタブに開きます。

1. まず、 **ClientRequestId** フィールドの値をクリップボードにコピーします。 コピーするには、**ClientRequestId** フィールドにあるどちらかの行を選択し、データ値を右クリックして **[Copy 'ClientRequestId']** を選択します。
2. ツール バー リボンで、**[New Viewer]**、**[Analysis Grid]** の順に選択して新しいタブを開きます。新しいタブは、ログ ファイル内のすべてのデータを、グループ化やフィルター、カラー ルールを適用せずに表示します。
3. ツール バー リボンで、**[View Layout]** を選択し、**[Azure Storage]** セクションの **[All .NET Client Columns]** を選択します。 このビュー レイアウトでは、サーバーおよびネットワーク トレース ログだけでなく、クライアント ログのデータも表示されます。 このデータは、既定では **[MessageNumber]** 列によって並べ替えが行われています。
4. 次に、クライアント ログでクライアント要求 ID を検索します。 ツール バー リボンで **[Find Messages]** を選択し、**Find** フィールドでクライアント要求 ID についてのカスタム フィルターを指定します。 フィルターには、この構文を使用して独自のクライアント要求 ID を指定します。

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer は、検索条件がクライアント要求 ID に一致する最初のログ エントリを見つけて選択します。 クライアント ログには、各クライアント要求 ID に対して複数のエントリがあるため、それらをまとめて確認しやすいように、 **ClientRequestId** フィールドでグループ化する必要があります。 以下の画像では、指定したクライアント要求 ID のクライアント ログにあるすべてのメッセージが表示されています。

![404 エラーを示すクライアント ログ](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

これら 2 つのタブのビュー レイアウトに表示されているデータを使用することで、要求データを分析して何がエラーを引き起こしたのかを判断することができます。 また、これに先行する要求を確認して、以前のイベントが 404 エラーを引き起こした可能性があるかどうかを調べることもできます。 たとえば、このクライアント要求 ID に先行するクライアント ログを確認して、BLOB が削除されていないか、あるいはクライアント アプリケーションがコンテナーまたは BLOB に対して CreateIfNotExists API を呼び出したことがエラーの発生原因ではないかを判断できます。 クライアント ログでは、**Description** フィールドに BLOB のアドレスが表示されます。サーバーおよびネットワーク トレース ログの場合、この情報は **Summary** フィールドに表示されます。

404 エラーを引き起こした BLOB のアドレスが判明したら、さらに調査を進めることができます。 同じ BLOB に対する操作に関連付けられた他のメッセージのログ エントリを検索した場合、クライアントが事前にそのエンティティを削除していたかどうかが確認できます。

## <a name="analyze-other-types-of-storage-errors"></a>他の種類のストレージ エラーを分析する
Message Analyzer を使用したログ データの分析に慣れてきたところで、ビュー レイアウト、カラー ルール、検索、フィルター処理を使用して他の種類のログ データを分析することもできます。 以下の表は、発生する可能性のある問題と、それらの特定に使用できるフィルター条件の一覧です。 フィルターの作成方法および Message Analyzer フィルタリング言語の詳細については、 [メッセージ データのフィルター処理](https://technet.microsoft.com/library/jj819365.aspx)に関するページを参照してください。

| 調査目的… | 使用するフィルター式… | 式を適用するログ (クライアント、サーバー、ネットワーク、すべて) |
| --- | --- | --- |
| キューのメッセージ配信での予期しない遅延 |AzureStorageClientDotNetV4.Description に "失敗した操作の再試行" が含まれている。 |クライアント |
| HTTP の PercentThrottlingError の増加 |HTTP.Response.StatusCode  == 500 &#124;&#124; HTTP.Response.StatusCode == 503 |ネットワーク |
| PercentTimeoutError の増加 |HTTP.Response.StatusCode   == 500 |ネットワーク |
| PercentTimeoutError の増加 (すべて) |*StatusCode   == 500 |All |
| PercentNetworkError の増加 |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |クライアント |
| HTTP 403 (Forbidden) メッセージ |HTTP.Response.StatusCode   == 403 |ネットワーク |
| HTTP 404 (Not found) メッセージ |HTTP.Response.StatusCode   == 404 |ネットワーク |
| 404 (すべて) |*StatusCode   == 404 |All |
| Shared Access Signature (SAS) 認証の問題 |AzureStorageLog.RequestStatus ==  "SASAuthorizationError" |ネットワーク |
| HTTP 409 (Conflict) メッセージ |HTTP.Response.StatusCode   == 409 |ネットワーク |
| 409 (すべて) |*StatusCode   == 409 |All |
| PercentSuccess が低いか、分析ログ エントリにトランザクションの状態が ClientOtherErrors の操作がある |AzureStorageLog.RequestStatus ==   "ClientOtherError" |サーバー |
| Nagle   警告 |((AzureStorageLog.EndToEndLatencyMS   - AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS *   1.5)) and (AzureStorageLog.RequestPacketSize <1460) and (AzureStorageLog.EndToEndLatencyMS -   AzureStorageLog.ServerLatencyMS >= 200) |サーバー |
| サーバーおよびネットワーク ログの時間範囲 |#Timestamp   >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39 |サーバー、ネットワーク |
| サーバー ログの時間範囲 |AzureStorageLog.Timestamp   >= 2014-10-20T16:36:38 and AzureStorageLog.Timestamp <=   2014-10-20T16:36:39 |サーバー |

## <a name="next-steps"></a>次の手順
Azure Storage におけるエンド ツー エンド シナリオのトラブルシューティングの詳細については、次のリソースを参照してください。

* [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Azure Portal でのストレージ アカウントの監視](storage-monitor-storage-account.md)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)
* [Microsoft Message Analyzer の操作ガイド](https://technet.microsoft.com/library/jj649776.aspx)

---
title: Azure Stream Analytics からの出力を理解する
description: この記事では、分析結果への Power BI を含む、Azure Stream Analytics で使用可能なデータ出力オプションについて説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/25/2019
ms.custom: seodec18
ms.openlocfilehash: f2318d3026578aef1e0e5c08d4a816b8f95a366f
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448706"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics からの出力を理解する
この記事では、Azure Stream Analytics ジョブで使用できるさまざまな種類の出力について説明します。 出力を使用すると、Stream Analytics ジョブの結果を格納したり保存したりできます。 出力データを使用して、データのビジネス分析をさらに進めたり、データ ウェアハウスを使用したりできます。

Stream Analytics のクエリを作成するときは、[INTO 句](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics)を使用して出力の名前を参照します。 ジョブごとに 1 つの出力を使用できます。または、クエリで複数の INTO 句を指定することによって、必要に応じてストリーミング ジョブごとに複数の出力を使用できます。

Stream Analytics ジョブの出力を作成、編集、テストするには、[Azure portal](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、[REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)、および [Visual Studio](stream-analytics-quick-create-vs.md) を使用できます。

一部の出力の種類は[パーティション分割](#partitioning)に対応します。また、スループットを最適化するために[出力のバッチ サイズ](#output-batch-size)が異なります。


## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics は [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)をサポートしています。 Azure Data Lake Store は、ビッグ データの分析ワークロードに対応するエンタープライズ規模のハイパースケール リポジトリです。 Data Lake Store を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、取り込み速度のデータを格納できます。 Data Lake Store にアクセスするには、Stream Analytics を承認する必要があります。

Stream Analytics からの Azure Data Lake Store 出力は、現在、Azure 中国 (21Vianet) および Azure ドイツ (T-Systems International) リージョンでは利用できません。

### <a name="authorize-an-azure-data-lake-store-account"></a>Azure Data Lake Store アカウントの承認

1. Data Lake Storage を Azure Portal で出力として選択すると、既存の Data Lake Store への接続を承認するように求められます。

   ![Data Lake Store への接続を承認する](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)

2. Data Lake Store へのアクセス権を既に持っている場合、**[今すぐ承認]** を選択すると、"**承認にリダイレクトしています**" というページが表示されます。 承認が成功すると、Data Lake Store の出力を構成できるページが表示されます。

3. Data Lake Store アカウントが認証されたら、Data Lake Store 出力のプロパティを構成できます。 次の表は、Data Lake Store 出力を構成するためのプロパティ名とその説明の一覧です。

   ![Stream Analytics の出力として Data Lake Store を定義する](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | クエリの出力をこの Data Lake Store に出力するためにクエリで使用されるわかりやすい名前です。 |
| アカウント名 | 出力を送信する Data Lake Storage アカウントの名前。 サブスクリプションで利用できる Data Lake Store アカウントのドロップダウン リストが表示されます。 |
| パスのプレフィックス パターン | 指定した Data Lake Store アカウント内のファイルを書き込むために使用するファイル パス。 {date} および {time} 変数のインスタンスを 1 つ以上指定できます。<br /><ul><li>例 1: folder1/logs/{date}/{time}</li><li>例 2: folder1/logs/{date}</li></ul><br />作成されたフォルダー構造のタイムスタンプでは、現地時刻ではなく、UTC に従います。<br /><br />ファイル パス パターンの末尾に "/" が含まれていない場合、ファイル パスの最後のパターンがファイル名のプレフィックスとして扱われます。 <br /><br />次の状況では新しいファイルが作成されます。<ul><li>出力スキーマの変更</li><li>ジョブの外部または内部再起動</li></ul> |
| 日付の形式 | 省略可能。 日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例:YYYY/MM/DD |
|時刻の形式 | 省略可能。 時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。|
| エンコード | CSV または JSON 形式を使用している場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。|
| 区切り記号 | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。|
| 形式 | JSON のシリアル化のみに適用されます。 [改行区切り] を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 [配列] を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。|

### <a name="renew-data-lake-store-authorization"></a>Data Lake Store の承認を更新する
ジョブが作成されてから、または前回の認証以降に Data Lake Store アカウントのパスワードが変わった場合、そのアカウントを再認証する必要があります。 再認証しないと、ジョブから出力結果が生成されず、操作ログに再認証の必要性を示すエラーが表示されます。 現在、Data Lake Store 出力のあるすべてのジョブについて、90 日おきに認証トークンを手動で更新する必要があるという制限事項があります。 ただし、[マネージド ID (プレビュー) を使用して認証](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-managed-identities-adls)することで、この制限を回避できます。

承認を更新するには、ジョブを **[停止]** して、Data Lake Store の出力に移動し、**[承認の更新]** リンクをクリックします。"**承認にリダイレクトしています**" というページが短時間表示されます。このページは自動的に閉じられ、処理が成功すると "**承認が正常に更新されました**" と表示されます。 ページの下部にある **[保存]** をクリックする必要があります。**[最後に停止した時刻]** からジョブを再開して継続することで、データの損失を防ぐことができます。

![出力での Data Lake Store の承認を更新する](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、本質的にリレーショナルであるデータや、リレーショナル データベースにホストされているコンテンツに依存するアプリケーションの出力として使用できます。 Stream Analytics ジョブは、Azure SQL Database の既存のテーブルに書き込みます。 テーブル スキーマは、ジョブから出力されるフィールドとその型にぴったり一致する必要があります。 また、SQL Database 出力オプションを使用して、[Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) を出力として指定することもできます。 書き込みのスループットを向上させる方法の詳細については、[出力としての Azure SQL DB を使用した Stream Analytics](stream-analytics-sql-output-perf.md)に関する記事を参照してください。 次の表に、SQL Database の出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
| Database | 出力を送信するデータベースの名前です。 |
| サーバー名 | SQL Database サーバー名です。 |
| ユーザー名 | データベースに書き込むためのアクセス権を持つユーザー名です。 Stream Analytics では、SQL 認証のみがサポートされます。 |
| パスワード | データベースに接続するためのパスワード。 |
| テーブル | 出力の書き込み先のテーブル名です。 テーブル名は大文字小文字が区別されます。このテーブルのスキーマは、ジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。 |
|パーティション構成を継承する| これによって、以前のクエリ ステップのパーティション構成を継承でき、テーブルへの複数のライターによる完全な並列トポロジを有効にすることができます。 詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。|
|最大バッチ カウント| 各一括挿入トランザクションで送信されるレコードの推奨上限数。|

> [!NOTE]
> 現在、Azure SQL Database は Stream Analytics の出力ジョブでサポートされています。 ただし、データベースに接続された SQL Server を実行する Azure 仮想マシンはサポートされていません。 これは、今後のリリースで変更されることがあります。
>

## <a name="blob-storage"></a>BLOB ストレージ
BLOB ストレージを使用すると、大量の非構造化データをクラウドに保存する場合に、コスト効率の高いスケーラブルなソリューションを実現できます。 Azure BLOB ストレージとその使用法の説明については、「 [How to use Blobs (BLOB の使用方法)](../storage/blobs/storage-dotnet-how-to-use-blobs.md)」をご覧ください。

次の表に、BLOB 出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名       | 説明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 出力のエイリアス        | クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント     | 出力を送信するストレージ アカウントの名前。               |
| ストレージ アカウント キー | ストレージ アカウントに関連付けられている秘密キー。                              |
| ストレージ コンテナー   | コンテナーにより、Microsoft Azure Blob service に格納される BLOB が論理的にグループ化されます。 BLOB を Blob service にアップロードするとき、その BLOB のコンテナーを指定する必要があります。 |
| パスのパターン | 省略可能。 指定したコンテナー内の BLOB を書き込むために使用されるファイル パス パターン。 <br /><br /> パス パターン内では、BLOB が書き込まれる頻度を指定するために、日付と時刻の変数のインスタンスを 1 つまたは複数使用できます。 <br /> {date}、{time} <br /><br />カスタム BLOB パーティション分割を使用して、イベント データの 1 つのカスタム {field} 名を指定することで、BLOB をパーティション分割できます。 このフィールド名は英数字であり、スペース、ハイフン、およびアンダースコアを含めることができます。 カスタム フィールドには次の制限事項が含まれます。 <ul><li>大文字小文字を区別しない (列 "ID" と列 "id" を区別できません)</li><li>入れ子になったフィールドを使用できない (代わりに、ジョブ クエリで別名を使用して、フィールドを "フラット化" します)</li><li>式はフィールド名として使用できません。</li></ul> <br /><br /> この機能により、パスでカスタム日付/時刻書式指定子の構成を使用できるようになります。 カスタム日時書式は、一度に 1 つを {datetime:\<specifier>} キーワードで囲んで指定する必要があります。 使用可能な入力の \<specifier> は、yyyy、MM、M、dd、d、HH、H、mm、m、ss、または s です。 {datetime:\<specifier>} キーワードは、カスタム日付/時刻の構成を形成するために、パスで複数回使用できます。 <br /><br />次に例を示します。 <ul><li>例 1: cluster1/logs/{date}/{time}</li><li>例 2: cluster1/logs/{date}</li><li>例 3: cluster1/{client_id}/{date}/{time}</li><li>例 4: cluster1/{datetime:ss}/{myField}。この場合、クエリは次のようになります。SELECT data.myField AS myField FROM Input;</li><li>例 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br /><br />作成されたフォルダー構造のタイムスタンプでは、現地時刻ではなく、UTC に従います。<br /><br />ファイルの名前付けは、次の規則に従います。 <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />出力ファイル例:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> この機能の詳細については、[Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割](stream-analytics-custom-path-patterns-blob-storage-output.md)に関するページを参照してください。 |
| 日付の形式 | 省略可能。 日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例:YYYY/MM/DD |
| 時刻の形式 | 省略可能。 時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。 |
| エンコード    | CSV または JSON 形式を使用している場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。 |
| 区切り記号   | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式      | JSON のシリアル化のみに適用されます。 [改行区切り] を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 [配列] を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。 |

出力として BLOB ストレージを使用しているときに、BLOB に新しいファイルが作成されるのは、次の場合です。

* ファイルが許可されるブロックの最大数 (現在は 50,000) を超えている場合。 BLOB の最大許容サイズに到達することなく、ブロックの最大許容数に到達する場合があります。 たとえば、出力レートが高い場合は、表示されるブロックあたりのバイト数が増え、ファイルのサイズが増加します。 出力レートが低い場合は、各ブロックのデータが少なくなり、ファイルのサイズも小さくなります。
* 出力のスキーマが変更されている場合、および出力形式で固定スキーマが必要な場合 (CSV および Avro)。
* ジョブを停止し起動するユーザーが外部からジョブを再起動した場合、またはシステム メンテナンスやエラーの復旧のために内部的にジョブを再起動した場合。
* クエリが完全にパーティション分割されており、新しいファイルが出力パーティションごと作成される場合。
* ストレージ アカウントのファイルまたはコンテナーがユーザーによって削除された場合。
* 出力がパス プレフィックス パターンを使用して時間でパーティション分割されている場合は、クエリが次の時間に移動するときに、新しい BLOB が使用されます。
* カスタム フィールドで出力をパーティション分割する場合、BLOB が存在しない場合はパーティション キーごとに新しい BLOB が作成されます。
* パーティション キーのカーディナリティが 8,000 を超えるカスタム フィールドによって出力がパーティション分割される場合、パーティション キーごとに新しい BLOB が作成される場合があります。

## <a name="event-hub"></a>イベント ハブ
[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) サービスは、スケーラブルな発行-サブスクライブ型イベント インジェスターです。 1 秒あたり数百万のイベントを収集できます。 イベント ハブを出力として使用するのは、たとえば、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力になるときです。

イベント ハブのデータ ストリームを出力として構成するために必要なパラメーターがいくつかあります。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | クエリの出力をこのイベント ハブに出力するためにクエリで使用されるわかりやすい名前です。 |
| Event Hub 名前空間 |イベント ハブの名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成すると、イベント ハブの名前空間も作成されます。 |
| イベント ハブ名 | イベント ハブ出力の名前です。 |
| イベント ハブ ポリシー名 | [イベント ハブの構成] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| イベント ハブ ポリシー キー | イベント ハブの名前空間へのアクセスを認証するために使用する共有アクセス キーです。 |
| パーティション キー列 [省略可能] | この列には、Event Hub 出力のパーティション キーが含まれます。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。 |
| エンコード | CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 | JSON のシリアル化のみに適用されます。 [改行区切り] を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 [配列] を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。 |
| プロパティ列 [省略可能] | ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列。 この機能の詳細は "出力用のカスタム メタデータ プロパティ" セクションにあります |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) を使用し、分析結果の豊富な視覚化エクスペリエンスを提供できます。 この機能は、操作ダッシュボード、レポート生成、およびメトリック ドリブン レポート作成に使用できます。

Stream Analytics からの Power BI 出力は、現在、Azure 中国 (21Vianet) および Azure ドイツ (T-Systems International) リージョンでは利用できません。

### <a name="authorize-a-power-bi-account"></a>Power BI アカウントを承認する
1. [Power BI] を Azure Portal で出力として選択すると、既存の Power BI ユーザーを承認するか、新しい Power BI アカウントを作成するように求められます。
   
   ![出力を構成する Power BI ユーザーを承認する](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2. まだアカウントを持っていない場合は新しいアカウントを作成し、[今すぐ承認] をクリックします。 次のページが表示されます。
   
   ![Power BI に Azure アカウントで認証する](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3. このステップでは、Power BI の出力を承認するための、職場または学校のアカウントを指定します。 Power BI を使用するためのサインアップをまだ行っていない場合は、[今すぐサインアップ] を選択します。 Power BI で使用する職場または学校のアカウントは、現在ログインしている Azure サブスクリプション アカウントと異なるものを使用できます。

### <a name="configure-the-power-bi-output-properties"></a>Power BI 出力プロパティを構成する
Power BI アカウントを認証すると、Power BI 出力のプロパティを構成できます。 次の表は、Power BI 出力を構成するためのプロパティ名とその説明の一覧です。

| プロパティ名 | description |
| --- | --- |
| 出力エイリアス |クエリの出力をこの PowerBI 出力に出力するためにクエリで使用されるわかりやすい名前です。 |
| グループ ワークスペース |他の Power BI ユーザーとのデータの共有を有効にするには、Power BI アカウント内のグループを選択できます。グループに書き込む必要がない場合は、[個人用ワークスペース] を選択します。 既存のグループを更新するには、Power BI の認証を更新する必要があります。 |
| データセットの名前 |Power BI 出力で使用する必要なデータセット名を指定します。 |
| テーブル名 |Power BI 出力のデータセットの下にテーブル名を入力します。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。 |

Power BI の出力とダッシュボードの構成に関するチュートリアルについては、[Azure Stream Analytics と Power BI](stream-analytics-power-bi-dashboard.md) に関する記事を参照してください。

> [!NOTE]
> Power BI ダッシュボードにデータセットとテーブルを明示的に作成しないでください。 ジョブが開始され、このジョブによって出力が Power BI に流し込まれるときに、データセットとテーブルが自動的に設定されます。 ジョブ クエリで結果が生成されない場合、データセットとテーブルは作成されません。 また、この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされるので注意してください。
>

### <a name="schema-creation"></a>スキーマの作成
Power BI データセットとテーブルがまだ存在しない場合は、ユーザーに代わって Azure Stream Analytics がこれを作成します。 それ以外の場合、テーブルは新しい値で更新されます。 現時点では、1 つのデータセット内に存在できるテーブルが 1 つのみという制限があります。 Power BI では、FIFO のアイテム保持ポリシーが使用されます。 有効にすると、200,000 行に到達するまでデータがテーブルに収集されます。

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Stream Analytics から Power BI へのデータ型の変換
Azure Stream Analytics では、出力スキーマが変更されると、データ モデルが実行時に動的に更新されます。 列名の変更、列の型の変更、列の追加または削除は、すべて追跡されます。

次の表は、Power BI データセットとテーブルが存在しない場合の [Stream Analytics データ型](https://msdn.microsoft.com/library/azure/dn835065.aspx)から Power BI の [Entity Data Model (EDM) 型](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/)へのデータ型の変換を示します。

Stream Analytics から | Power BI へ
-----|-----
bigint | Int64
nvarchar(max) | String
Datetime | DateTime
float | Double
レコードの配列 | 文字列型、定数値 "IRecord" または "IArray"

### <a name="schema-update"></a>スキーマの更新
Stream Analytics では、出力内の最初のイベント セットに基づいてデータ モデルのスキーマを推論します。 その後、データ モデルのスキーマは、必要に応じて、元のスキーマに適合しない可能性のある受信イベントに対応できるように更新されます。

複数行にわたってスキーマが動的に更新されるのを防ぐために、`SELECT *` クエリの使用を避ける必要があります。 パフォーマンスが低下する可能性があるだけでなく、結果を得るまでの所要時間が不確実になる恐れもあります。 Power BI ダッシュボードに表示する必要があるフィールドを厳密に選択してください。 また、データ値が、選択したデータ型に準拠している必要があります。


以前/現在 | Int64 | String | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
DateTime | String | String |  DateTime | String


### <a name="renew-power-bi-authorization"></a>Power BI の承認を更新する
Stream Analytics ジョブが作成されてから、または前回の認証以降に Power BI アカウントのパスワードが変わった場合、Stream Analytics を再認証する必要があります。 Azure Active Directory (AAD) テナント上で Multi-Factor Authentication (MFA) が構成されている場合は、Power BI の承認を 2 週間ごとに更新することも必要になります。 この問題の症状として、ジョブ出力が返されないことや、操作ログで "ユーザーの認証エラー" が発生することが挙げられます。

  ![Power BI 認証ユーザー エラー](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。 **[承認の更新]** リンクを選択し、データの損失を避けるため、"**最後に停止した時刻**" からジョブを再開します。

  ![Power BI の出力の承認を更新する](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## <a name="table-storage"></a>Table Storage
[Azure テーブル ストレージ](../storage/common/storage-introduction.md) は高度な可用性を備えた非常にスケーラブルなストレージであるため、アプリケーションを需要に応じて自動的に拡張できます。 テーブル ストレージは Microsoft の NoSQL キー/属性ストアであり、スキーマに対する制約を抑えながら、構造化されたデータに活用できます。 Azure テーブル ストレージを使用すると、永続化と効率的な取得のためにデータを保持できます。

次の表に、テーブル出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | description |
| --- | --- |
| 出力エイリアス |クエリの出力をこのテーブル ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント |出力を送信するストレージ アカウントの名前。 |
| ストレージ アカウント キー |ストレージ アカウントに関連付けられているアクセス キー。 |
| テーブル名 |テーブルの名前。 テーブルが存在しない場合は、テーブルが作成されます。 |
| パーティション キー |パーティション キーが含まれる出力列の名前。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、特定のテーブル内のパーティションの一意の識別子です。 最大サイズが 1 KB の文字列値です。 |
| 行キー |行キーが含まれる出力列の名前。 行キーは、特定のパーティション内のエンティティを示す一意の識別子です。 エンティティのプライマリ キーの 2 番目の部分を形成します。 行キーは、最大サイズが 1 KB の文字列値です。 |
| バッチ サイズ |バッチ操作のレコードの数です。 ほとんどのジョブは既定値 (100) で十分です。 この設定の変更について詳しくは、[テーブル バッチ操作の仕様](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)に関するページを参照してください。 |

## <a name="service-bus-queues"></a>Service Bus キュー
[Service Bus キュー](https://msdn.microsoft.com/library/azure/hh367516.aspx)では、コンシューマーが競合している場合のメッセージ配信に先入先出法 (FIFO) を使用します。 通常、メッセージはキューに追加された順番に受信され、処理されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ 1 件につき 1 つだけです。

次の表に、キュー出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | description |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Service Bus キューに出力するためにクエリで使用されるわかりやすい名前です。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 |
| キュー名 |Service Bus キューの名前。 |
| キュー ポリシー名 |キューを作成するとき、[キューの構成] タブで共有アクセス ポリシーを作成することもできます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| キュー ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 |JSON 型のみに適用されます。 [改行区切り] を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 [配列] を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 |
| プロパティ列 [省略可能] | ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列。 この機能の詳細は "出力用のカスタム メタデータ プロパティ" セクションにあります |

パーティション数は、[Service Bus SKU とサイズに基づいています](../service-bus-messaging/service-bus-partitioning.md)。 パーティション キーは、各パーティションに固有の整数値です。

## <a name="service-bus-topics"></a>Service Bus トピック
Service Bus キューには、送信者から受信者への 1 対 1 の通信メソッドが備わっていますが、 [Service Bus トピック](https://msdn.microsoft.com/library/azure/hh367516.aspx) では 1 対多の形式で通信することができます。

次の表に、テーブル出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Service Bus トピックに出力するためにクエリで使用されるわかりやすい名前です。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 |
| トピック名 |トピックは、イベント ハブやキューと類似するメッセージ エンティティです。 多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。 トピックが作成されるときに、特定の名前も付けられます。 トピックに送信されるメッセージはサブスクリプションが作成されなければ使用できないため、トピックの下に 1 つ以上のサブスクリプションがあることを確認してください |
| トピック ポリシー名 |トピックを作成するとき、[トピックの構成] タブで共有アクセス ポリシーを作成することもできます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています |
| トピック ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV または JSON 形式を使用している場合は、エンコードを指定する必要があります。 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| プロパティ列 [省略可能] | [省略可能] ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列。 この機能の詳細は "出力用のカスタム メタデータ プロパティ" セクションにあります |

パーティション数は、[Service Bus SKU とサイズに基づいています](../service-bus-messaging/service-bus-partitioning.md)。 パーティション キーは、各パーティションに固有の整数値です。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) は世界各地に分散されているマルチモデル データベース サービスです。世界各地に対応する制限のない柔軟なスケーリング、スキーマに依存しないデータ モデルでの豊富なクエリと自動インデックス作成機能、低待機時間の保証、業界をリードする包括的な SLA を提供しています。 Stream Analytics の Cosmos DB コレクション オプションについては、[Cosmos DB への Stream Analytics の出力](stream-analytics-documentdb-output.md)に関する記事を参照してください。

Stream Analytics からの Azure Cosmos DB 出力は、現在、Azure 中国 (21Vianet) および Azure ドイツ (T-Systems International) リージョンでは利用できません。

> [!Note]
> 現時点では、Azure Stream Analytics は、**SQL API** を使用した CosmosDB への接続のみをサポートしています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Azure Stream Analytics を、その他のAPI で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。

次の表では、Azure Cosmos DB の出力を作成するプロパティについて説明します。

| プロパティ名 | description |
| --- | --- |
| 出力エイリアス | Stream Analytics クエリ内でこの出力を意味するエイリアス。 |
| シンク | Cosmos DB |
| インポート オプション | [Select Cosmos DB from your subscription]\(サブスクリプションから Cosmos DB を選択する\) または [Provide Cosmos DB settings manually]\(Cosmos DB 設定を手動で指定する\) を選択します。
| アカウント ID | Cosmos DB アカウントの名前またはエンドポイント URI。 |
| アカウント キー | Cosmos DB アカウントの共有アクセス キー。 |
| Database | Cosmos DB データベース名。 |
| コレクション名のパターン | 使用するコレクションのコレクション名またはそのパターン。 <br />コレクション名の形式は、オプションの {partition} トークンを使用して構成できます。この場合、パーティションは 0 から開始します。 2 つの例を挙げます。  <br />1._MyCollection_ – "MyCollection" という名前のコレクションが 1 つ必要です。  <br />2._MyCollection{partition}_ – パーティション列に基づきます。 <br />"MyCollection0"、"MyCollection1"、"MyCollection2" などのパーティション列のコレクションが存在する必要があります。 |
| パーティション キー | 省略可能。 コレクション名のパターンに {partition} トークンを使用している場合のみ必要です。<br /> コレクション間で出力をパーティション分割するためのキーの指定に使用される、出力イベント内のフィールドの名前。<br /> コレクションの出力が 1 つの場合は、任意の出力列を使用できます。 たとえば PartitionId などです。 |
| ドキュメント ID |省略可能。 挿入操作または更新操作の基準となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前。

## <a name="azure-functions"></a>Azure Functions
Azure Functions は、インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行できるサーバーレス コンピューティング サービスです。 これにより、Azure またはサード パーティのサービスで発生するイベントによってトリガーされるコードを実装できます。 このトリガーに応答する Azure Functions の機能によって、Azure Stream Analytics の自然な出力になります。 この出力アダプターを使用すると、ユーザーは Stream Analytics を Azure Functions に接続し、さまざまなイベントに応じてスクリプトまたはコードの一部を実行することができます。

Stream Analytics からの Azure Functions 出力は、現在、Azure 中国 (21Vianet) および Azure ドイツ (T-Systems International) リージョンでは利用できません。

Azure Stream Analytics では、HTTP トリガーを使用して Azure Functions を呼び出します。 新しい Azure 関数出力アダプターは、次の構成可能なプロパティで使用できます。

| プロパティ名 | description |
| --- | --- |
| 関数アプリ |Azure Functions アプリの名前 |
| 関数 |Azure Functions アプリ内の関数の名前 |
| キー |別のサブスクリプションの Azure 関数を使用するには、関数にアクセスするためのキーを指定します |
| 最大バッチ サイズ |このプロパティを使用して、Azure 関数に送信される出力バッチごとに最大サイズを設定できます。 入力の単位はバイトです。 既定値は 262,144 バイト (256 KB) です。 |
| 最大バッチ カウント  |名前が示すように、このプロパティでは、Azure Functions に送信される各バッチのイベントの最大数を指定できます。 既定の最大バッチ カウントの値は 100 です |

Azure Stream Analytics では、Azure 関数から 413 ("HTTP 要求エンティティが大きすぎる") 例外を受け取ると、Azure Functions に送信するバッチのサイズを削減します。 Azure 関数コードで、この例外を使用して、Azure Stream Analytics がサイズの大きいバッチを送信しないようにします。 また、関数で使用する最大バッチ カウントおよび最大バッチ サイズの値が Stream Analytics ポータルに入力した値と矛盾しないことを確認します。

また、時間枠内に開始するイベントがない場合も、出力は生成されません。 その結果、computeResult 関数は呼び出されません。 この動作は、組み込みのウィンドウ集計関数と一致します。

## <a name="custom-metadata-properties-for-output"></a>出力用のカスタム メタデータ プロパティ 

この機能では、送信メッセージにクエリ列をユーザー プロパティとして添付できます。 これらの列はペイロードに入りません。 これらのプロパティは、出力メッセージにディクショナリの形式で表示されます。 キーは列名で、値はプロパティ ディクショナリの列値です。 Record と Array を除き、すべての Stream Analytics データ型がサポートされています。  

サポートされている出力: 
* Service Bus キュー 
* Service Bus トピック 
* イベント ハブ 

例:次の例では、DeviceId と DeviceStatus という 2 つのフィールドをメタデータに追加します。 
* クエリ: `select *, DeviceId, DeviceStatus from iotHubInput`。
* 出力の構成: `DeviceId,DeviceStatus`。

![プロパティ列](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を利用して EventHub で検査される出力メッセージ プロパティ。

   ![イベント カスタム プロパティ](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>パーティション分割

次の表は、出力の種類ごとにパーティションのサポートと出力ライターの数をまとめた一覧です。

| 出力の種類 | パーティション分割のサポート | パーティション キー  | 出力ライターの数 |
| --- | --- | --- | --- |
| Azure Data Lake Store | はい | Path プレフィックス パターンに {date} および {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 | [完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure SQL Database | はい | クエリの PARTITION BY 句に基づいています。 | [完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 SQL Azure Database にデータを読み込む場合の書き込みスループット パフォーマンスの向上の詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。 |
| Azure BLOB ストレージ | はい | パス パターンでイベント フィールドからの {date} トークンと {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 BLOB 出力を 1 つのカスタム イベント属性 {fieldname} または {datetime:\<specifier>} でパーティション分割できます。 | [完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Event Hub | はい | はい | パーティションの配置によって異なります。<br /> 出力イベント ハブのパーティション キーが上流の (以前の) クエリ ステップと等間隔で配置されている場合、ライターの数は出力イベント ハブのパーティションの数と等しくなります。 各ライターは、EventHub の [EventHubSender クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)を使用して、特定のパーティションにイベントを送信します。 <br /> 出力イベント ハブのパーティション キーが上流の (以前の) クエリ ステップと等間隔で配置されていない場合、ライターの数は前のステップのパーティションの数と等しくなります。 各ライターは、EventHubClient の [SendBatchAsync クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)を使用して、すべての出力パーティションにイベントを送信します。 |
| Power BI | いいえ  | なし | 適用不可。 |
| Azure Table Storage | はい | 任意の出力列。  | [完全に並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Service Bus Topic | はい | 自動的に選択されます。 パーティション数は、[Service Bus SKU とサイズ](../service-bus-messaging/service-bus-partitioning.md)に基づいています。 パーティション キーは、各パーティションに固有の整数値です。| 出力トピック内のパーティションの数と同じです。  |
| Azure Service Bus キュー | はい | 自動的に選択されます。 パーティション数は、[Service Bus SKU とサイズ](../service-bus-messaging/service-bus-partitioning.md)に基づいています。 パーティション キーは、各パーティションに固有の整数値です。| 出力キュー内のパーティションの数と同じです。 |
| Azure Cosmos DB | はい | コレクション名パターンに {partition} トークンを使用します。 {partition} 値は、クエリの PARTITION BY 句に基づいています。 | [完全に並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Functions | いいえ  | なし | 適用不可。 |

出力アダプターがパーティション分割されていない場合は、ある入力パーティションにデータがないと、到着遅延時間までの遅延が発生します。  このような場合は、出力が 1 つのライタにマージされるため、パイプラインにボトルネックが発生する可能性があります。 到着遅延ポリシーの詳細については、「[Azure Stream Analytics のイベントの順序に関する考慮事項](stream-analytics-out-of-order-and-late-events.md)」を参照してください。

## <a name="output-batch-size"></a>出力バッチ サイズ
Azure Stream Analytics では、イベントを処理して出力に書き込むために、可変サイズのバッチを使用します。 通常、Stream Analytics エンジンでは、一度に 1 つのメッセージを書き込むことはせず、効率を上げるためにバッチを使用します。 受信と送信のイベント レートが両方とも高い場合は、大きいバッチを使用します。 エグレス レートが低い場合は、小さいバッチを使用して待ち時間が短い状態を維持します。

次の表では、出力のバッチ処理に対するいくつかの考慮事項について説明します。

| 出力の種類 | 最大メッセージ サイズ | バッチ サイズの最適化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 「[Data Lake Store の制限](../azure-subscription-service-limits.md#data-lake-store-limits)」を参照 | 書き込み操作ごとに最大 4 MB |
| Azure SQL Database | 1 つの一括挿入ごとに最大で 10,000 行<br />1 つの一括挿入ごとに最小で 100 行 <br />[Azure SQL の制限](../sql-database/sql-database-resource-limits.md)に関する記事も参照 |  バッチはすべて、最初に最大バッチ サイズで一括挿入され、SQL からの再試行可能なエラーに基づいて半分に分割されます (最小バッチ サイズまで)。 |
| Azure BLOB ストレージ | [Azure Storage の制限](../azure-subscription-service-limits.md#storage-limits)に関する記事を参照 | BLOB の最大ブロック サイズは 4 MB<br />BLOB の最大ブロック数は 50,000 |
| Azure Event Hub   | メッセージあたり 256 KB <br />[イベント ハブの制限](../event-hubs/event-hubs-quotas.md)に関する記事も参照 |   入出力のパーティション分割が揃っていない場合、各イベントは個別に EventData に格納され、最大メッセージ サイズ (Premium SKU では 1 MB) までのバッチで送信されます。 <br /><br />  入出力のパーティション分割が揃っている場合、最大メッセージ サイズまでの複数のイベントが単一の EventData に格納され、送信されます。  |
| Power BI | 「[Power BI REST API の制限事項](https://msdn.microsoft.com/library/dn950053.aspx)」を参照 |
| Azure Table Storage | [Azure Storage の制限](../azure-subscription-service-limits.md#storage-limits)に関する記事を参照 | 既定値は 1 つのトランザクションあたり 100 エンティティで、必要に応じて値をより小さく構成できます。 |
| Azure Service Bus キュー   | メッセージあたり 256 KB<br /> [Service Bus の制限](../service-bus-messaging/service-bus-quotas.md)に関する記事も参照 | メッセージごとに 1 つのイベント |
| Azure Service Bus トピック | メッセージあたり 256 KB<br /> [Service Bus の制限](../service-bus-messaging/service-bus-quotas.md)に関する記事も参照 | メッセージごとに 1 つのイベント |
| Azure Cosmos DB   | 「[Azure Cosmos DB の制限](../azure-subscription-service-limits.md#azure-cosmos-db-limits)」を参照 | バッチ サイズと書き込みの頻度は、Cosmos DB の応答に基づいて動的に調整されます。 <br /> Stream Analytics からの事前に定義された制限はありません。 |
| Azure Functions   | | 既定のバッチ サイズは 262,144 バイト (256 KB) です。 <br /> バッチごとの既定のイベント数は 100 です。 <br /> バッチ サイズは構成可能で、Stream Analytics の[出力オプション](#azure-functions)で増減させることができます。

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> 
> [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301

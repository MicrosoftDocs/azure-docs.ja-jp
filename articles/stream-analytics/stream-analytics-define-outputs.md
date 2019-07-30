---
title: Azure Stream Analytics からの出力を理解する
description: この記事では、分析結果への Power BI を含む、Azure Stream Analytics で使用可能なデータ出力オプションについて説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/31/2019
ms.openlocfilehash: 17214bb4904cc540de0a7d6f753b7e70abfa564c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443644"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Azure Stream Analytics からの出力を理解する

この記事では、Azure Stream Analytics ジョブで使用できる出力の種類について説明します。 出力を使用すると、Stream Analytics ジョブの結果を格納したり保存したりできます。 出力データを使用して、お手元のデータのビジネス分析をさらに進めたり、データ ウェアハウスを使用したりできます。

Stream Analytics のクエリを自分で作成するときは、[INTO 句](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics)を使用して出力の名前を参照します。 ジョブごとに 1 つの出力を使用できます。または、必要に応じて、クエリ内で複数の INTO 句を指定することによって、ストリーミング ジョブごとに複数の出力を使用できます。

Stream Analytics ジョブの出力を作成、編集、テストするには、[Azure portal](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet)、[REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)、および [Visual Studio](stream-analytics-quick-create-vs.md) を使用できます。

一部の出力の種類では、[パーティション分割](#partitioning)がサポートされています。 スループットを最適化するために、[出力のバッチ サイズ](#output-batch-size)は異なります。


## <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

Stream Analytics は、[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) をサポートしています。 Azure Data Lake Storage は、ビッグ データの分析ワークロードのためのエンタープライズ レベルのハイパースケール リポジトリです。 Data Lake Storage を使用すると、運用分析や調査分析のために任意のサイズ、種類、および取り込み速度のデータを格納できます。 Data Lake Storage にアクセスするには、Stream Analytics を承認する必要があります。

Stream Analytics からの Azure Data Lake Storage 出力は現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは使用できません。

次の表は、Data Lake Storage Gen 1 出力を構成するためのプロパティ名とその説明の一覧を示しています。   

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | クエリの出力を Data Lake Store に出力するためにクエリ内で使用されるフレンドリ名です。 |
| サブスクリプション | Azure Data Lake Storage アカウントを含むサブスクリプション。 |
| アカウント名 | ご自分の出力を送信する Data Lake Store アカウントの名前です。 ご自分のサブスクリプション内で利用できる Data Lake Store アカウントのドロップダウン リストが表示されます。 |
| パスのプレフィックス パターン | 指定した Data Lake Store アカウント内にご自分のファイルを書き込むために使用されるファイル パスです。 {date} および {time} 変数のインスタンスを 1 つ以上指定できます。<br /><ul><li>例 1: folder1/logs/{date}/{time}</li><li>例 2: folder1/logs/{date}</li></ul><br />作成されたフォルダー構造のタイム スタンプは、ローカル時間ではなく、UTC に従います。<br /><br />ファイル パス パターンの末尾にスラッシュ (/) が含まれていない場合、ファイル パスの最後のパターンがファイル名のプレフィックスとして扱われます。 <br /><br />次の状況では新しいファイルが作成されます。<ul><li>出力スキーマの変更</li><li>ジョブの外部または内部再起動</li></ul> |
| 日付の形式 | 省略可能。 日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例:YYYY/MM/DD |
|時刻の形式 | 省略可能。 時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。|
| エンコード | CSV または JSON 形式を使用する場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。|
| 区切り記号 | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。|
| 形式 | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。|
| 認証モード | [マネージド ID](stream-analytics-managed-identities-adls.md) またはユーザー トークンを使用して、Data Lake Storage アカウントへのアクセスを承認できます。 アクセス権を付与した後は、ユーザー アカウントのパスワードを変更するか、このジョブの Data Lake Storage 出力を削除するか、または Stream Analytics ジョブを削除することによってアクセスを取り消すことができます。 |

## <a name="sql-database"></a>SQL Database

[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、本質的にリレーショナルであるデータや、リレーショナル データベースにホストされているコンテンツに依存するアプリケーションの出力として使用できます。 Stream Analytics ジョブは、SQL Database の既存のテーブルに書き込みます。 テーブル スキーマは、ご自分のジョブの出力内のフィールドとその型に正確に一致する必要があります。 また、SQL Database 出力オプションを使用して、[Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) を出力として指定することもできます。 書き込みのスループットを向上させる方法については、[Azure SQL Database を出力として使用する Stream Analytics](stream-analytics-sql-output-perf.md) に関する記事を参照してください。

次の表に、SQL Database の出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
| Database | ご自分の出力を送信するデータベースの名前です。 |
| サーバー名 | SQL Database サーバー名です。 |
| ユーザー名 | データベースに書き込むためのアクセス権が割り当てられているユーザー名です。 Stream Analytics では、SQL 認証のみがサポートされます。 |
| パスワード | データベースに接続するためのパスワード。 |
| テーブル | 出力の書き込み先のテーブル名です。 テーブル名は、大文字と小文字が区別されます。 このテーブルのスキーマは、ご自分のジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。 |
|パーティション構成を継承する| ご自分の以前のクエリ ステップのパーティション構成を継承し、テーブルへの複数のライターによる完全な並列トポロジを有効にするためのオプションです。 詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。|
|最大バッチ カウント| 各一括挿入トランザクションで送信されるレコード数の推奨される上限です。|

> [!NOTE]
> Stream Analytics のジョブ出力では Azure SQL Database サービスがサポートされていますが、データベースが接続された SQL Server を実行している Azure 仮想マシンまたは SQL Azure マネージド インスタンス上の Azure 仮想マシンはサポートされていません。 これは、今後のリリースで変更されることがあります。

## <a name="blob-storage"></a>BLOB ストレージ

Azure Blob Storage を使用すると、大量の非構造化データをクラウドに保存する場合に、コスト効果の高いスケーラブルなソリューションを実現できます。 BLOB ストレージとその使用法の概要については、[Azure Portal を使用した BLOB のアップロード、ダウンロード、および一覧表示](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。

次の表に、BLOB 出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名       | 説明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 出力エイリアス        | クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント     | ご自分の出力を送信するストレージ アカウントの名前です。               |
| ストレージ アカウント キー | ストレージ アカウントに関連付けられている秘密キー。                              |
| ストレージ コンテナー   | Azure Blob service に格納される BLOB の論理グループです。 BLOB を Blob service にアップロードするとき、その BLOB のコンテナーを指定する必要があります。 |
| パスのパターン | 省略可能。 指定したコンテナー内のご自分の BLOB を書き込むために使用されるファイル パス パターンです。 <br /><br /> パス パターン内では、BLOB が書き込まれる頻度を指定するために、日付と時刻の変数のインスタンスを 1 つまたは複数使用できます。 <br /> {date}、{time} <br /><br />カスタム BLOB パーティション分割を使用して、イベント データの 1 つのカスタム {field} 名を指定することで、BLOB をパーティション分割できます。 このフィールド名は英数字であり、スペース、ハイフン、およびアンダースコアを含めることができます。 カスタム フィールドには次の制限事項が含まれます。 <ul><li>フィールド名は大文字小文字が区別されません。 たとえば、このサービスでは列 "ID" と列 "id" を区別できません。</li><li>入れ子になったフィールドは使用できません。 代わりに、ジョブ クエリ内で別名を使用して、フィールドを "フラット化" します。</li><li>式はフィールド名として使用できません。</li></ul> <br />この機能により、パスでカスタム日付/時刻書式指定子の構成を使用できるようになります。 カスタム日時書式は、一度に 1 つを {datetime:\<specifier>} キーワードで囲んで指定する必要があります。 \<specifier> の使用可能な入力は、yyyy、MM、M、dd、d、HH、H、mm、m、ss、または s です。 {datetime:\<specifier>} キーワードは、カスタムの日付/時刻の構成を形成するために、パス内で複数回使用できます。 <br /><br />次に例を示します。 <ul><li>例 1: cluster1/logs/{date}/{time}</li><li>例 2: cluster1/logs/{date}</li><li>例 3: cluster1/{client_id}/{date}/{time}</li><li>例 4: cluster1/{datetime:ss}/{myField}。この場合、クエリは次のようになります。SELECT data.myField AS myField FROM Input;</li><li>例 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />作成されたフォルダー構造のタイム スタンプは、ローカル時間ではなく、UTC に従います。<br /><br />ファイルの名前付けでは、次の規則を使用します。 <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br />出力ファイル例:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />この機能の詳細については、「[Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割](stream-analytics-custom-path-patterns-blob-storage-output.md)」を参照してください。 |
| 日付の形式 | 省略可能。 日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例:YYYY/MM/DD |
| 時刻の形式 | 省略可能。 時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。 |
| エンコード    | CSV または JSON 形式を使用する場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。 |
| 区切り記号   | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式      | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。 |

出力として Blob Storage を使用しているときに、BLOB に新しいファイルが作成されるのは、次の場合です。

* ファイルが許可されるブロックの最大数 (現在は 50,000) を超えている場合。 BLOB の最大許容サイズに到達することなく、ブロックの最大許容数に到達する場合があります。 たとえば、出力レートが高い場合は、表示されるブロックあたりのバイト数が増え、ファイルのサイズが増加します。 出力レートが低い場合は、各ブロックのデータが少なくなり、ファイルのサイズも小さくなります。
* 出力のスキーマが変更されている場合、および出力形式で固定スキーマが必要な場合 (CSV および Avro)。
* ジョブを停止し起動するユーザーが外部からジョブを再起動した場合、またはシステム メンテナンスやエラーの復旧のために内部的にジョブを再起動した場合。
* クエリが完全にパーティション分割されており、新しいファイルが出力パーティションごとに作成される場合。
* ストレージ アカウントのファイルまたはコンテナーがユーザーによって削除された場合。
* パス プレフィックス パターンを使用して時間で出力をパーティション分割する場合、クエリが次の時間に移動すると、新しい BLOB が使用されます。
* カスタム フィールドによって出力をパーティション分割する場合、BLOB が存在しないと、パーティション キーごとに新しい BLOB が作成されます。
* パーティション キーのカーディナリティが 8,000 を超えるカスタム フィールドによって出力をパーティション分割する場合、パーティション キーごとに新しい BLOB が作成されます。

## <a name="event-hubs"></a>Event Hubs

[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) サービスは、スケーラブルな発行-サブスクライブ型イベント インジェスターです。 1 秒あたり数百万のイベントを収集できます。 イベント ハブを出力として使用するのは、たとえば、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力になるときです。

イベント ハブのデータ ストリームを出力として構成するために必要なパラメーターがいくつかあります。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | クエリの出力をこのイベント ハブに出力するためにクエリ内で使用されるフレンドリ名です。 |
| イベント ハブの名前空間 | 一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成すると、イベント ハブの名前空間も作成されます。 |
| イベント ハブ名 | ご自分のイベント ハブ出力の名前です。 |
| イベント ハブ ポリシー名 | 共有アクセス ポリシーです。イベント ハブの **[構成]** タブで作成できます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| イベント ハブ ポリシー キー | イベント ハブの名前空間へのアクセスを認証するために使用される共有アクセス キーです。 |
| パーティション キー列 | 省略可能。 イベント ハブ出力のパーティション キーが含まれる列です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。 |
| エンコード | CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。 |
| プロパティ列 | 省略可能。 ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列です。 この機能の詳細は、「[出力用のカスタム メタデータ プロパティ](#custom-metadata-properties-for-output)」セクションにあります。 |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) を Stream Analytics ジョブの出力として使用して、分析結果の豊富な視覚化エクスペリエンスを提供できます。 この機能は、操作ダッシュボード、レポート生成、およびメトリックドリブン レポート作成に使用できます。

Stream Analytics からの Power BI 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

次の表に、ご自分の Power BI 出力を構成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Power BI 出力に出力するためにクエリ内で使用されるフレンドリ名を指定します。 |
| グループ ワークスペース |他の Power BI ユーザーとのデータの共有を有効にするには、ご自分の Power BI アカウント内のグループを選択できます。グループに書き込む必要がない場合は、 **[マイ ワークスペース]** を選択します。 既存のグループを更新するには、Power BI の認証を更新する必要があります。 |
| データセットの名前 |Power BI 出力で使用するデータセット名を指定します。 |
| テーブル名 |Power BI 出力のデータセットの下にテーブル名を入力します。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に 1 つのテーブルのみを保持できます。 |
| 接続の承認 | Power BI に出力設定を構成することを承認する必要があります。 この出力アクセスを Power BI ダッシュボードに付与した後は、ユーザー アカウントのパスワードを変更するか、ジョブ出力を削除するか、または Stream Analytics ジョブを削除することによってアクセスを取り消すことができます。 | 

Power BI の出力とダッシュボードを構成するチュートリアルについては、[Azure Stream Analytics と Power BI](stream-analytics-power-bi-dashboard.md) に関するチュートリアルを参照してください。

> [!NOTE]
> Power BI ダッシュボードにデータセットとテーブルを明示的に作成しないでください。 ジョブが開始され、このジョブによって出力が Power BI に流し込まれるときに、データセットとテーブルが自動的に設定されます。 ジョブ クエリで結果が生成されない場合、データセットとテーブルは作成されません。 また、この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされます。
>

### <a name="create-a-schema"></a>スキーマの作成
Power BI データセットとテーブル スキーマがまだ存在しない場合は、ユーザーのために Azure Stream Analytics がデータセットとテーブル スキーマを作成します。 それ以外の場合、テーブルは新しい値で更新されます。 現時点では、1 つのデータセット内に存在できるテーブルは 1 つのみです。 

Power BI では、先入れ先出し (FIFO) のアイテム保持ポリシーが使用されます。 200,000 行に到達するまでデータがテーブルに収集されます。

### <a name="convert-a-data-type-from-stream-analytics-to-power-bi"></a>Stream Analytics から Power BI にデータ型を変換する
Azure Stream Analytics では、出力スキーマが変更されると、データ モデルが実行時に動的に更新されます。 列名の変更、列の型の変更、列の追加または削除は、すべて追跡されます。

次の表は、Power BI データセットとテーブルが存在しない場合の、[Stream Analytics データ型](https://msdn.microsoft.com/library/azure/dn835065.aspx)から Power BI の [Entity Data Model (EDM) 型](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model)へのデータ型の変換を示します。

Stream Analytics から | Power BI へ
-----|-----
bigint | Int64
nvarchar(max) | String
Datetime | DateTime
float | Double
レコードの配列 | 文字列型、定数値 "IRecord" または "IArray"

### <a name="update-the-schema"></a>スキーマを更新する
Stream Analytics では、出力内の最初のイベント セットに基づいてデータ モデルのスキーマを推論します。 その後、データ モデルのスキーマは、必要に応じて、元のスキーマに適合しない可能性のある受信イベントに対応できるように更新されます。

複数行にわたってスキーマが動的に更新されるのを防ぐために、`SELECT *` クエリの使用を避ける必要があります。 パフォーマンスが低下する可能性があるだけでなく、結果を得るまでの所要時間が不確実になる恐れもあります。 Power BI ダッシュボードに表示する必要があるフィールドを厳密に選択してください。 また、データ値が、選択したデータ型に準拠している必要があります。


以前/現在 | Int64 | String | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | String | String | String | String 
DateTime | String | String |  DateTime | string

## <a name="table-storage"></a>テーブル ストレージ

[Azure テーブル ストレージ](../storage/common/storage-introduction.md) は高度な可用性を備えた非常にスケーラブルなストレージであるため、アプリケーションを需要に応じて自動的に拡張できます。 Table Storage は Microsoft の NoSQL キー/属性ストアであり、スキーマに対する制約を抑えながら、構造化されたデータに使用できます。 Azure テーブル ストレージを使用すると、永続化と効率的な取得のためにデータを保持できます。

次の表に、テーブルの出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこのテーブル ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント |ご自分の出力を送信するストレージ アカウントの名前です。 |
| ストレージ アカウント キー |ストレージ アカウントに関連付けられているアクセス キー。 |
| テーブル名 |テーブルの名前。 テーブルが存在しない場合は、テーブルが作成されます。 |
| パーティション キー |パーティション キーが含まれる出力列の名前です。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、テーブル内のパーティションの一意識別子です。 最大サイズが 1 KB の文字列値です。 |
| 行キー |行キーが含まれる出力列の名前です。 行キーは、パーティション内のエンティティの一意識別子です。 エンティティのプライマリ キーの 2 番目の部分を形成します。 行キーは、最大サイズが 1 KB の文字列値です。 |
| バッチ サイズ |バッチ操作のレコードの数です。 ほとんどのジョブは既定値 (100) で十分です。 この設定の変更について詳しくは、[テーブル バッチ操作の仕様](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.table._table_batch_operation)に関するページを参照してください。 |

## <a name="service-bus-queues"></a>Service Bus キュー

[Service Bus キュー](../service-bus-messaging/service-bus-queues-topics-subscriptions.md)では、競合している 1 つ以上のコンシューマーへのメッセージ配信に FIFO が使用されます。 通常、メッセージはキューに追加された時間的順序で受信者によって受信され、処理されます。 各メッセージを受信し、処理するメッセージ コンシューマーは 1 つのみです。

次の表に、キューの出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Service Bus キューに出力するためにクエリ内で使用されるフレンドリ名です。 |
| Service Bus 名前空間 |一連のメッセージング エンティティのコンテナーです。 |
| キュー名 |Service Bus キューの名前です。 |
| キュー ポリシー名 |キューを作成するとき、キューの **[構成]** タブで共有アクセス ポリシーを作成することもできます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| キュー ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 |JSON 型のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 |
| プロパティ列 | 省略可能。 ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列です。 この機能の詳細は、「[出力用のカスタム メタデータ プロパティ](#custom-metadata-properties-for-output)」セクションにあります。 |

パーティション数は、[Service Bus SKU とサイズに基づいています](../service-bus-messaging/service-bus-partitioning.md)。 パーティション キーは、各パーティションに固有の整数値です。

## <a name="service-bus-topics"></a>Service Bus トピック
Service Bus キューには、送信者から受信者への一対一の通信方法が用意されています。 [Service Bus のトピック](https://msdn.microsoft.com/library/azure/hh367516.aspx)では、一対多の形式の通信を行うことができます。

次の表に、Service Bus トピックの出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス |クエリの出力をこの Service Bus トピックに出力するためにクエリ内で使用されるフレンドリ名です。 |
| Service Bus 名前空間 |一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成したとき、Service Bus 名前空間も作成されました。 |
| トピック名 |トピックは、イベント ハブやキューと類似するメッセージ エンティティです。 デバイスやサービスからイベント ストリームを収集するように設計されています。 トピックが作成されるときに、特定の名前も付けられます。 トピックに送信されるメッセージはサブスクリプションが作成されなければ使用できないため、トピックの下に 1 つ以上のサブスクリプションがあることを確認してください。 |
| トピック ポリシー名 |Service Bus トピックを作成するとき、トピックの **[構成]** タブで共有アクセス ポリシーを作成することもできます。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 |
| トピック ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式です。 JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV または JSON 形式を使用する場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| プロパティ列 | 省略可能。 ペイロードではなく、送信メッセージのユーザー プロパティとして関連付ける必要があるコンマ区切りの列です。 この機能の詳細は、「[出力用のカスタム メタデータ プロパティ](#custom-metadata-properties-for-output)」セクションにあります。 |

パーティション数は、[Service Bus SKU とサイズに基づいています](../service-bus-messaging/service-bus-partitioning.md)。 パーティション キーは、各パーティションに固有の整数値です。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) はグローバル分散型データベース サービスです。世界各地に対応する、制限のないエラスティックなスケーリング、スキーマに依存しないデータ モデルでの豊富なクエリと自動インデックス作成機能を提供します。 Stream Analytics の Azure Cosmos DB コンテナー オプションについては、[Azure Cosmos DB を出力として使用する Stream Analytics](stream-analytics-documentdb-output.md) に関する記事を参照してください。

Stream Analytics からの Azure Cosmos DB 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

> [!Note]
> 現時点では、Azure Stream Analytics は、SQL API を使用した Azure Cosmos DB への接続のみをサポートしています。
> その他の Azure Cosmos DB API は、まだサポートされていません。 Azure Stream Analytics を、その他のAPI で作成した Azure Cosmos DB アカウントへ接続する場合は、データが正しく格納されない可能性があります。

次の表では、Azure Cosmos DB の出力を作成するプロパティについて説明します。

| プロパティ名 | 説明 |
| --- | --- |
| 出力エイリアス | Stream Analytics クエリ内でこの出力を意味するエイリアス。 |
| シンク | Azure Cosmos DB。 |
| インポート オプション | **[サブスクリプションから Cosmos DB を選択する]** または **[Cosmos DB 設定を手動で指定します]** を選択します。
| Account ID | Azure Cosmos DB アカウントの名前またはエンドポイント URI。 |
| アカウント キー | Azure Cosmos DB アカウントの共有アクセス キー。 |
| Database | Azure Cosmos DB データベース名。 |
| コンテナー名 | 使用するコンテナーの名前。Cosmos DB に存在する必要があります。 例:  <br /><ul><li> _MyContainer_: "MyContainer" という名前のコンテナーが存在する必要があります。</li>|
| ドキュメント ID |省略可能。 挿入操作または更新操作の基盤となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前です。

## <a name="azure-functions"></a>Azure Functions
Azure Functions は、インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行するために使用できるサーバーレス コンピューティング サービスです。 これにより、Azure またはパートナーのサービス内で発生するイベントによってトリガーされるコードを実装できます。 トリガーに応答する Azure Functions のこの機能によって、Azure Stream Analytics の自然な出力になります。 この出力アダプターを使用すると、ユーザーは Stream Analytics を Azure Functions に接続し、さまざまなイベントに応じてスクリプトまたはコードの一部を実行することができます。

Stream Analytics からの Azure Functions 出力は、現在、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。

Azure Stream Analytics では、HTTP トリガーを使用して Azure Functions を呼び出します。 Azure Functions 出力アダプターは、次の構成可能なプロパティで使用できます。

| プロパティ名 | 説明 |
| --- | --- |
| 関数アプリ |Azure Functions アプリの名前です。 |
| Function |ご自分の Azure Functions アプリ内にある関数の名前です。 |
| キー |別のサブスクリプションの Azure 関数を使用するには、ご自分の関数にアクセスするためのキーを指定します。 |
| 最大バッチ サイズ |ご自分の Azure 関数に送信される各出力バッチの最大サイズを設定できるプロパティです。 入力の単位はバイトです。 既定値は 262,144 バイト (256 KB) です。 |
| 最大バッチ カウント  |Azure Functions に送信される各バッチのイベントの最大数を指定できるプロパティです。 既定値は 100 です。 |

Azure Stream Analytics は、Azure 関数から 413 ("http の要求したエンティティが大きすぎる") 例外を受け取ると、Azure Functions に送信するバッチのサイズを縮小します。 Azure 関数コードで、この例外を使用して、Azure Stream Analytics がサイズの大きいバッチを送信しないようにします。 また、関数で使用する最大バッチ カウントおよび最大バッチ サイズの値が Stream Analytics ポータルに入力した値と矛盾しないことを確認します。

また、時間枠内に開始するイベントがない場合も、出力は生成されません。 その結果、**computeResult** 関数は呼び出されません。 この動作は、組み込みのウィンドウ集計関数と一致します。

## <a name="custom-metadata-properties-for-output"></a>出力用のカスタム メタデータ プロパティ 

ご自分の送信メッセージにクエリ列をユーザー プロパティとして添付できます。 これらの列はペイロードに入りません。 これらのプロパティは、出力メッセージにディクショナリの形式で表示されます。 "*キー*" は列名で、"*値*" はプロパティ ディクショナリの列値です。 Record と Array を除き、すべての Stream Analytics データ型がサポートされています。  

サポートされている出力: 
* Service Bus キュー 
* Service Bus トピック 
* イベント ハブ 

次の例では、2 つのフィールド `DeviceId` と `DeviceStatus` をメタデータに追加します。 
* クエリ: `select *, DeviceId, DeviceStatus from iotHubInput`
* 出力の構成: `DeviceId,DeviceStatus`

![プロパティ列](./media/stream-analytics-define-outputs/10-stream-analytics-property-columns.png)

次のスクリーンショットは、[Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) を利用して EventHub で検査される出力メッセージ プロパティを示しています。

![イベント カスタム プロパティ](./media/stream-analytics-define-outputs/09-stream-analytics-custom-properties.png)

## <a name="partitioning"></a>パーティション分割

次の表は、出力の種類ごとにパーティションのサポートと出力ライターの数をまとめた一覧です。

| 出力の種類 | パーティション分割のサポート | パーティション キー  | 出力ライターの数 |
| --- | --- | --- | --- |
| Azure Data Lake Store | はい | パス プレフィックス パターンに {date} トークンと {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 | [完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure SQL Database | はい。有効にする必要があります。 | クエリの PARTITION BY 句に基づきます。 | パーティション分割の継承オプションが有効になっている場合は、[完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 SQL Azure Database にデータを読み込むときの書き込みスループット パフォーマンスの向上の詳細については、「[Azure SQL Database への Azure Stream Analytics の出力](stream-analytics-sql-output-perf.md)」を参照してください。 |
| Azure BLOB ストレージ | はい | パス パターンにご自分のイベント フィールドからの {date} トークンと {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 BLOB 出力を 1 つのカスタム イベント属性 {fieldname} または {datetime:\<specifier>} でパーティション分割できます。 | [完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Event Hubs | はい | はい | パーティションの配置によって異なります。<br /> イベント ハブ出力のパーティション キーが上流の (以前の) クエリ ステップと等間隔で配置されている場合、ライターの数はイベント ハブ出力のパーティションの数と同じになります。 各ライターは、[EventHubSender クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet)を使用して、特定のパーティションにイベントを送信します。 <br /> イベント ハブ出力のパーティション キーが上流の (以前の) クエリ ステップに合わせて配置されていない場合、ライターの数はその前のステップのパーティションの数と同じになります。 各ライターは、**EventHubClient** の [SendBatchAsync クラス](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet)を使用して、すべての出力パーティションにイベントを送信します。 |
| Power BI | いいえ | なし | 適用不可。 |
| Azure Table Storage | はい | 任意の出力列。  | [完全に並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Service Bus トピック | はい | 自動的に選択されます。 パーティション数は、[Service Bus SKU とサイズ](../service-bus-messaging/service-bus-partitioning.md)に基づいています。 パーティション キーは、各パーティションに固有の整数値です。| 出力トピック内のパーティションの数と同じです。  |
| Azure Service Bus キュー | はい | 自動的に選択されます。 パーティション数は、[Service Bus SKU とサイズ](../service-bus-messaging/service-bus-partitioning.md)に基づいています。 パーティション キーは、各パーティションに固有の整数値です。| 出力キュー内のパーティションの数と同じです。 |
| Azure Cosmos DB | はい | クエリの PARTITION BY 句に基づきます。 | [完全に並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。 |
| Azure Functions | いいえ | なし | 適用不可。 |

出力ライターの数は、クエリ内の `INTO <partition count>` ([INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) に関するページを参照してください) 句を使用して制御することもできます。これは、目的のジョブ トポロジを実現するのに役立ちます。 出力アダプターがパーティション分割されていない場合は、ある入力パーティションにデータがないと、到着遅延時間までの遅延が発生します。 このような場合は、出力が 1 つのライターにマージされるため、ご使用のパイプラインにボトルネックが発生する可能性があります。 到着遅延ポリシーの詳細については、[Azure Stream Analytics のイベントの順序に関する考慮事項](stream-analytics-out-of-order-and-late-events.md)に関する記事を参照してください。

## <a name="output-batch-size"></a>出力バッチ サイズ
Azure Stream Analytics では、イベントを処理して出力に書き込むために、可変サイズのバッチを使用します。 通常、Stream Analytics エンジンでは、一度に 1 つのメッセージを書き込むことはせず、効率を上げるためにバッチを使用します。 受信イベントと送信イベントのレートが両方とも高い場合は、Stream Analytics では、大きいバッチを使用します。 エグレス レートが低い場合は、小さいバッチを使用して待ち時間が短い状態を維持します。

次の表では、出力のバッチ処理に関するいくつかの考慮事項について説明します。

| 出力の種類 | 最大メッセージ サイズ | バッチ サイズの最適化 |
| :--- | :--- | :--- |
| Azure Data Lake Store | 「[Data Lake Store の制限](../azure-subscription-service-limits.md#data-lake-store-limits)」を参照してください。 | 書き込み操作ごとに最大 4 MB を使用します。 |
| Azure SQL Database | 最大バッチ カウントを使用して構成できます。 既定では、1 つの一括挿入あたり最大で 10,000 行、最小で 100 行です。<br />[Azure SQL の制限](../sql-database/sql-database-resource-limits.md)に関する記事を参照してください。 |  すべてのバッチは、最初に最大バッチ カウントを使用して一括挿入されます。 バッチは、SQL の再試行可能なエラーに基づいて (最小バッチ カウントに達するまで) 半分に分割されます。 |
| Azure BLOB ストレージ | [Azure Storage の制限](../azure-subscription-service-limits.md#storage-limits)に関するセクションを参照してください。 | BLOB の最大ブロック サイズは 4 MB です。<br />BLOB の最大ブロック数は 50,000 です。 |
| Azure Event Hubs  | メッセージあたり 256 KB または 1 MB です。 <br />[Event Hubs の制限](../event-hubs/event-hubs-quotas.md)に関する記事を参照してください。 |  入出力のパーティション分割が揃っていない場合、各イベントは個別に `EventData` に格納され、最大メッセージ サイズまでのバッチで送信されます。 これは、[カスタム メタデータ プロパティ](#custom-metadata-properties-for-output)が使用されている場合にも発生します。 <br /><br />  入出力のパーティション分割が揃っている場合、複数のイベントが最大メッセージ サイズまで 1 つの `EventData` インスタンスに格納され、送信されます。 |
| Power BI | 「[Power BI REST API の制限事項](https://msdn.microsoft.com/library/dn950053.aspx)」を参照してください。 |
| Azure Table Storage | [Azure Storage の制限](../azure-subscription-service-limits.md#storage-limits)に関するセクションを参照してください。 | 既定では、1 つのトランザクションあたり 100 個のエンティティです。 必要に応じて、より小さい値を構成できます。 |
| Azure Service Bus キュー   | Standard レベルではメッセージあたり 256 KB、Premium レベルでは 1 MB。<br /> [Service Bus の制限](../service-bus-messaging/service-bus-quotas.md)に関する記事を参照してください。 | メッセージごとに 1 つのイベントを使用します。 |
| Azure Service Bus トピック | Standard レベルではメッセージあたり 256 KB、Premium レベルでは 1 MB。<br /> [Service Bus の制限](../service-bus-messaging/service-bus-quotas.md)に関する記事を参照してください。 | メッセージごとに 1 つのイベントを使用します。 |
| Azure Cosmos DB   | 「[Azure Cosmos DB の制限](../azure-subscription-service-limits.md#azure-cosmos-db-limits)」を参照してください。 | バッチ サイズと書き込みの頻度は、Azure Cosmos DB の応答に基づいて動的に調整されます。 <br /> Stream Analytics からの事前に定義された制限はありません。 |
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

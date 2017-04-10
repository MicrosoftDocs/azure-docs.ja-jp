---
title: "Stream Analytics の出力: ストレージのオプション、分析 | Microsoft Docs"
description: "分析結果の Power BI など、Stream Analytics データ出力の対象設定オプションについて説明します。"
keywords: "データ変換、分析結果、データ ストレージ オプション"
services: stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: ba6697ac-e90f-4be3-bafd-5cfcf4bd8f1f
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2b4a10c77ae02ac0e9eeecf6d7d6ade6e4c33115
ms.openlocfilehash: 9eb581e6180a7ae6a5f24b3a991376264b0ecef9
ms.lasthandoff: 01/25/2017


---
# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Stream Analytics の出力: ストレージのオプション、分析
Stream Analytics ジョブを作成するときは、生成されたデータがどのように使用されるかを考慮してください。 Stream Analytics ジョブの結果をどのように表示しますか。また、どこに保存しますか。

さまざまなアプリケーション パターンを有効にするために、Azure Stream Analytics には、出力を保存し、分析結果を表示するためのさまざまなオプションが用意されています。 そのため、ジョブ出力を簡単に確認したり、データのウェアハウジングやその他の用途でジョブ出力を柔軟に使用および保存したりすることができます。 ジョブで構成される出力は、ジョブが開始されてイベントがフローを開始する前に存在している必要があります。 たとえば、出力として BLOB Storage を使用する場合、ジョブはストレージ アカウントを自動的に作成しません。 ASA ジョブを開始する前に、ユーザーが作成する必要があります。

## <a name="azure-data-lake-store"></a>Azure Data Lake Store
Stream Analytics は [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)をサポートしています。 この記憶域を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、取り込み速度のデータを格納できます。 また、Data Lake Store にアクセスするには、Stream Analytics を承認する必要があります。 承認と (必要に応じて) Data Lake Store にサインアップする方法の詳細については、[Data Lake 出力の記事](stream-analytics-data-lake-output.md)を参照してください。

### <a name="authorize-an-azure-data-lake-store"></a>Azure Data Lake Store の承認
Data Lake Storage を Microsoft Azure 管理ポータルで出力として選択すると、既存の Data Lake Store への接続を承認するように求められます。  

![Data Lake Store の承認](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

その後、以下に示すように、Data Lake Store 出力のプロパティを入力します。

![Data Lake Store の承認](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

次の表に、Data Lake Store 出力を作成するためのプロパティ名とその説明を示します。

<table>
<tbody>
<tr>
<td><B>プロパティ名</B></td>
<td><B>説明</B></td>
</tr>
<tr>
<td>出力のエイリアス</td>
<td>クエリの出力をこの Data Lake Store に出力するためにクエリで使用されるわかりやすい名前です。</td>
</tr>
<tr>
<td>アカウント名</td>
<td>出力を送信する Data Lake Storage アカウントの名前。 ポータルにログインしたユーザーがアクセス権を持っている Data Lake Store アカウントのドロップダウン リストが表示されます。</td>
</tr>
<tr>
<td>パス プレフィックスのパターン [<I>省略可能</I>]</td>
<td>指定した Data Lake Store アカウント内のファイルを書き込むために使用するファイル パス。 <BR>{date}、{time}<BR>例 1: folder1/logs/{date}/{time}<BR>例 2: folder1/logs/{date}</td>
</tr>
<tr>
<td>日付形式 [<I>省略可能</I>]</td>
<td>日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例: YYYY/MM/DD</td>
</tr>
<tr>
<td>時刻形式 [<I>省略可能</I>]</td>
<td>時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。</td>
</tr>
<tr>
<td>イベントのシリアル化の形式</td>
<td>出力データのシリアル化形式。 JSON、CSV、Avro がサポートされています。</td>
</tr>
<tr>
<td>エンコード</td>
<td>CSV または JSON 形式の場合、エンコードを指定する必要があります。 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。</td>
</tr>
<tr>
<td>区切り記号</td>
<td>CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。</td>
</tr>
<tr>
<td>形式</td>
<td>JSON のシリアル化のみに適用されます。 行区切りを指定すると、各 JSON オブジェクトを新しい行で区切ることで、出力が書式設定されます。 配列を指定すると、出力が JSON オブジェクトの配列として書式設定されます。</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Data Lake Store の承認を更新する
ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Data Lake Store アカウントを再認証する必要があります。

![Data Lake Store の承認](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>SQL Database
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) は、本質的にリレーショナルであるデータや、リレーショナル データベースにホストされているコンテンツに依存するアプリケーションの出力として使用できます。 Stream Analytics ジョブは、Azure SQL Database の既存のテーブルに書き込みます。  テーブル スキーマは、ジョブから出力されるフィールドとその型に正確に一致する必要があります。 また、SQL Database 出力オプションを使用して、 [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) を出力として指定することもできます (これはプレビュー機能です)。 次の表に、SQL Database の出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこのデータベースに出力するためにクエリで使用されるわかりやすい名前です。 |
| データベース |出力を送信するデータベースの名前 |
| サーバー名 |SQL Database サーバー名 |
| ユーザー名 |データベースに書き込むためのアクセス権を持つユーザー名 |
| パスワード |データベースに接続するためのパスワード |
| テーブル |出力の書き込み先のテーブル名です。 テーブル名は大文字小文字が区別されます。このテーブルのスキーマは、ジョブの出力によって生成されるフィールドの数とその型に正確に一致する必要があります。 |

> [!NOTE]
> 現在、Azure SQL Database は Stream Analytics の出力ジョブでサポートされています。 ただし、データベースに接続された SQL Server を実行する Azure 仮想マシンはサポートされていません。 これは、今後のリリースで変更されることがあります。
> 
> 

## <a name="blob-storage"></a>BLOB ストレージ
BLOB ストレージを使用すると、大量の非構造化データをクラウドに保存する場合に、コスト効率の高いスケーラブルなソリューションを実現できます。  Azure BLOB ストレージとその使用法の説明については、「 [How to use Blobs (BLOB の使用方法)](../storage/storage-dotnet-how-to-use-blobs.md)」をご覧ください。

次の表に、BLOB 出力を作成するためのプロパティ名とその説明を示します。

<table>
<tbody>
<tr>
<td>プロパティ名</td>
<td>Description</td>
</tr>
<tr>
<td>出力のエイリアス</td>
<td>クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。</td>
</tr>
<tr>
<td>ストレージ アカウント</td>
<td>出力を送信するストレージ アカウントの名前。</td>
</tr>
<tr>
<td>ストレージ アカウント キー</td>
<td>ストレージ アカウントに関連付けられている秘密キー。</td>
</tr>
<tr>
<td>ストレージ コンテナー</td>
<td>コンテナーにより、Microsoft Azure BLOB サービスに格納される BLOB が論理的にグループ化されます。 BLOB を BLOB サービスにアップロードするとき、その BLOB のコンテナーを指定する必要があります。</td>
</tr>
<tr>
<td>パス プレフィックスのパターン [省略可能]</td>
<td>指定したコンテナー内の BLOB を書き込むために使用されるファイル パス。<BR>このパス内に、BLOB が書き込まれる頻度を指定するために、2 つの変数のいずれかまたは両方のインスタンスを使用できます。<BR>{date}、{time}<BR>例 1: cluster1/logs/{date}/{time}<BR>例 2: cluster1/logs/{date}</td>
</tr>
<tr>
<td>日付形式 [省略可能]</td>
<td>日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例: YYYY/MM/DD</td>
</tr>
<tr>
<td>時刻形式 [省略可能]</td>
<td>時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。</td>
</tr>
<tr>
<td>イベントのシリアル化の形式</td>
<td>出力データのシリアル化形式。  JSON、CSV、Avro がサポートされています。</td>
</tr>
<tr>
<td>エンコード</td>
<td>CSV または JSON 形式の場合、エンコードを指定する必要があります。 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。</td>
</tr>
<tr>
<td>区切り記号</td>
<td>CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。</td>
</tr>
<tr>
<td>形式</td>
<td>JSON のシリアル化のみに適用されます。 行区切りを指定すると、各 JSON オブジェクトを新しい行で区切ることで、出力が書式設定されます。 配列を指定すると、出力が JSON オブジェクトの配列として書式設定されます。</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>イベント ハブ
[Event Hubs](https://azure.microsoft.com/services/event-hubs/) はスケーラブルな発行/サブスクライブ イベント インジェスターです。 1 秒あたり数百万のイベントを収集できます。  イベント ハブを出力として使用する一例として、Stream Analytics ジョブの出力が別のストリーミング ジョブの入力となる場合があります。

イベント ハブのデータ ストリームを出力として構成するために必要なパラメーターがいくつかあります。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこのイベント ハブに出力するためにクエリで使用されるわかりやすい名前です。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 |
| イベント ハブ |イベント ハブ出力の名前 |
| イベント ハブ ポリシー名 |[イベント ハブの構成] タブで作成できる共有アクセス ポリシー。 各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。 |
| イベント ハブ ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| パーティション キー列 [省略可能] |この列には、Event Hub 出力のパーティション キーが含まれます。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式。  JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 |JSON 型のみに適用されます。 行区切りを指定すると、各 JSON オブジェクトを新しい行で区切ることで、出力が書式設定されます。 配列を指定すると、出力が JSON オブジェクトの配列として書式設定されます。 |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) を使用し、分析結果の豊富な視覚化エクスペリエンスを提供できます。 この機能は、操作ダッシュボード、レポート生成、およびメトリック ドリブン レポート作成に使用できます。

### <a name="authorize-a-power-bi-account"></a>Power BI アカウントを承認する
1. Power BI を Microsoft Azure 管理ポータルで出力として選択すると、既存の Power BI ユーザーを承認するか、新しい Power BI アカウントを作成するように求められます。  
   
   ![Authorize Power BI User](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  
2. まだアカウントを持っていない場合は新しいアカウントを作成し、[今すぐ承認] をクリックします。  次のような画面が表示されます。  
   
   ![Azure Account Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  
3. このステップでは、Power BI の出力を承認するための、職場または学校のアカウントを指定します。 Power BI を使用するためのサインアップをまだ行っていない場合は、[今すぐサインアップ] を選択します。 Power BI で使用する職場または学校のアカウントは、現在ログインしている Azure サブスクリプション アカウントと異なるものを使用できます。

### <a name="configure-the-power-bi-output-properties"></a>Power BI 出力プロパティを構成する
Power BI アカウントを認証すると、Power BI 出力のプロパティを構成できます。 次の表は、Power BI 出力を構成するためのプロパティ名とその説明の一覧です。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこの PowerBI 出力に出力するためにクエリで使用されるわかりやすい名前です。 |
| グループ ワークスペース |他の Power BI ユーザーとのデータの共有を有効にするには、Power BI アカウント内のグループを選択できます。グループに書き込む必要がない場合は、[個人用ワークスペース] を選択します。  既存のグループを更新するには、Power BI の認証を更新する必要があります。 |
| データセット名 |Power BI 出力で使用する必要なデータセット名を指定します。 |
| テーブル名 |Power BI 出力のデータセットの下にテーブル名を入力します。 現在、Stream Analytics ジョブからの Power BI 出力では、1 つのデータセット内に&1; つのテーブルのみを保持できます。 |

Power BI 出力およびダッシュボードの構成のチュートリアルについては、[Azure Stream Analytics & Power BI](stream-analytics-power-bi-dashboard.md) の記事を参照してください。

> [!NOTE]
> Power BI ダッシュボードにデータセットとテーブルを明示的に作成しないでください。 ジョブが開始され、このジョブによって出力が Power BI に流し込まれるときに、データセットとテーブルが自動的に設定されます。 ジョブ クエリで結果が生成されない場合、データセットとテーブルは作成されません。 また、この Stream Analytics ジョブで指定したものと同じ名前のデータセットとテーブルが Power BI に既に存在する場合は、既存のデータが上書きされるので注意してください。
> 
> 

### <a name="schema-creation"></a>スキーマの作成
Power BI データセットとテーブルがまだ存在しない場合は、ユーザーに代わって Azure Stream Analytics がこれを作成します。 それ以外の場合は、テーブルが新しい値で更新されます。現時点では、データセット内に存在できるテーブルの数は&1; つのみという制限があります。

### <a name="data-type-conversion-from-asa-to-power-bi"></a>ASA から Power BI へのデータ型の変換
Azure Stream Analytics では、出力スキーマが変更されると、データ モデルが実行時に動的に更新されます。 列名の変更、列の型の変更、列の追加または削除は、すべて追跡されます。

次の表は、Power BI データセットとテーブルが存在しない場合の [Stream Analytics データ型](https://msdn.microsoft.com/library/azure/dn835065.aspx)から Power BI の [Entity Data Model (EDM) 型](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/)へのデータ型の変換を示します。


Stream Analytics から | Power BI へ
-----|-----|------------
bigint | Int64
nvarchar(max) | String
datetime | DateTime
float | Double
レコードの配列 | 文字列型、定数値 "IRecord" または "IArray"

### <a name="schema-update"></a>スキーマの更新
Stream Analytics では、出力内の最初のイベント セットに基づいてデータ モデルのスキーマを推論します。 その後、データ モデルのスキーマは、必要に応じて、元のスキーマに適合しない可能性のある受信イベントに対応できるように更新されます。

複数行にわたってスキーマが動的に更新されるのを防ぐために、`SELECT *` クエリの使用を避ける必要があります。 パフォーマンスが低下する可能性があるだけでなく、結果を得るまでの所要時間が不確実になる恐れがあります。 Power BI ダッシュボードに表示する必要があるフィールドを厳密に選択してください。 また、データ値が、選択したデータ型に準拠している必要があります。


以前/現在 | Int64 | String | DateTime | Double
-----------------|-------|--------|----------|-------
Int64 | Int64 | String | String | Double
Double | Double | String | String | Double
String | 文字列 | 文字列 | 文字列 |  | String | 
DateTime | String | String |  DateTime | String


### <a name="renew-power-bi-authorization"></a>Power BI の承認を更新する
ジョブが作成されてから、または最後の認証以降にパスワードが変わっている場合、Power BI アカウントを再認証する必要があります。 Azure Active Directory (AAD) テナント上で Multi-Factor Authentication (MFA) が構成されている場合は、Power BI の承認を 2 週間ごとに更新することも必要になります。 この問題の症状として、ジョブ出力が返されないことや、操作ログで "ユーザーの認証エラー" が発生することが挙げられます。

  ![Power BI refresh token error](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

この問題を解決するには、実行中のジョブを停止し、Power BI 出力に移動します。  [承認の更新] リンクをクリックし、データの損失を避けるため、"最後に停止した時刻" からジョブを再開します。

  ![Power BI renew authorization](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Table Storage
[Azure テーブル ストレージ](../storage/storage-introduction.md)は高度な可用性を備えた非常にスケーラブルなストレージであるため、アプリケーションを需要に応じて自動的に拡張できます。 テーブル ストレージは Microsoft の NoSQL キー/属性ストアであり、スキーマに対する制約を抑えながら、構造化されたデータに活用できます。 Azure テーブル ストレージを使用すると、永続化と効率的な取得のためにデータを保持できます。

次の表に、テーブル出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこのテーブル ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント |出力を送信するストレージ アカウントの名前。 |
| ストレージ アカウント キー |ストレージ アカウントに関連付けられているアクセス キー。 |
| テーブル名 |テーブルの名前。 存在しない場合は、テーブルが作成されます。 |
| パーティション キー |パーティション キーが含まれる出力列の名前。 パーティション キーは、エンティティのプライマリ キーの最初の部分を形成する、特定のテーブル内のパーティションの一意の識別子です。 最大サイズが 1 KB の文字列値です。 |
| Row Key |行キーが含まれる出力列の名前。 行キーは、特定のパーティション内のエンティティを示す一意の識別子です。 エンティティのプライマリ キーの&2; 番目の部分を形成します。 行キーは、最大サイズが 1 KB の文字列値です。 |
| バッチ サイズ |バッチ操作のレコードの数です。 通常、大部分のジョブは既定値で十分です。この設定の変更の詳細については、[テーブル バッチ操作の仕様](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx)に関するページを参照してください。 |

## <a name="service-bus-queues"></a>Service Bus キュー
[Service Bus キュー](https://msdn.microsoft.com/library/azure/hh367516.aspx)では、コンシューマーが競合している場合のメッセージ配信に先入先出法 (FIFO) を使用します。 通常、メッセージはキューに追加された順番に受信され、処理されます。このとき、メッセージを受信して処理できるメッセージ コンシューマーは、メッセージ&1; 件につき&1; つだけです。

次の表に、キュー出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこの Service Bus キューに出力するためにクエリで使用されるわかりやすい名前です。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 |
| キュー名 |Service Bus キューの名前。 |
| キュー ポリシー名 |キューを作成するとき、[キューの構成] タブで共有アクセス ポリシーを作成することもできます。 各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。 |
| キュー ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式。  JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV と JSON では、現在のところ、UTF-8 が唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| 形式 |JSON 型のみに適用されます。 行区切りを指定すると、各 JSON オブジェクトを新しい行で区切ることで、出力が書式設定されます。 配列を指定すると、出力が JSON オブジェクトの配列として書式設定されます。 |

## <a name="service-bus-topics"></a>Service Bus トピック
Service Bus キューには、送信者から受信者への&1; 対&1; の通信メソッドが備わっていますが、 [Service Bus トピック](https://msdn.microsoft.com/library/azure/hh367516.aspx) では&1; 対多の形式で通信することができます。

次の表に、テーブル出力を作成するためのプロパティ名とその説明を示します。

| プロパティ名 | Description |
| --- | --- |
| 出力のエイリアス |クエリの出力をこの Service Bus トピックに出力するために、クエリで使用されるわかりやすい名前です。 |
| Service Bus 名前空間 |Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。 |
| トピック名 |トピックは、イベント ハブやキューと類似するメッセージ エンティティです。 多数のさまざまなデバイスやサービスからイベント ストリームを収集するように設計されています。 トピックが作成されるときに、特定の名前も付けられます。 トピックに送信されるメッセージはサブスクリプションが作成されなければ使用できないため、トピックの下に&1; つ以上のサブスクリプションがあることを確認してください |
| トピック ポリシー名 |トピックを作成するとき、[トピックの構成] タブで共有アクセス ポリシーを作成することもできます。 各共有アクセス ポリシーには、名前、設定したアクセス許可、およびアクセス キーが含まれます。 |
| トピック ポリシー キー |Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 |
| イベントのシリアル化の形式 |出力データのシリアル化形式。  JSON、CSV、Avro がサポートされています。 |
| エンコード |CSV または JSON 形式の場合、エンコードを指定する必要があります。 現在のところ、UTF-8 が、唯一サポートされているエンコード形式です。 |
| 区切り記号 |CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV 形式のデータをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |

## <a name="documentdb"></a>DocumentDB
[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) は完全に管理された NoSQL ドキュメント データベース サービスであり、スキーマのないデータに対するクエリとトランザクション、予測可能で信頼性のあるパフォーマンス、および迅速な開発を実現します。

以下に、DocumentDB 出力を作成するためのプロパティ名とその説明の詳細を示します。

* **出力のエイリアス** – ASA クエリ内でこの出力を意味するエイリアス。  
* **アカウント名** – DocumentDB アカウントの名前またはエンドポイント URI。  
* **アカウント キー** – DocumentDB アカウントの共有アクセス キー。  
* **データベース** – DocumentDB データベース名。  
* **コレクション名のパターン** – 使用するコレクションのコレクション名またはそのパターン。 コレクション名の形式は、オプションの {partition} トークンを使用して構成できます。この場合、パーティションは 0 から開始します。 有効な入力値のサンプルを次に示します。  
  1\) MyCollection – "MyCollection" という名前のコレクションが&1; つ必要です。  
  2\) MyCollection{partition} – "MyCollection0"、"MyCollection1"、"MyCollection2" などのコレクションが必要です。  
* **パーティション キー** – 省略可能。 コレクション名のパターンに {partition} トークンを使用している場合のみ必要です。 コレクション全体で出力をパーティション分割するためのキーを指定するために使用される、出力イベント内のフィールドの名前。 コレクションの出力が&1; つの場合は、PartitionId など、任意の出力列を使用できます。  
* **Document ID** – 省略可能です。 挿入操作または更新操作の基準となるプライマリ キーを指定するために使用される、出力イベント内のフィールドの名前。  


## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
モ ノのインターネットからのデータをストリーム分析する管理サービスである、 Stream Analytics の概要です。 このサービスの詳細については、以下の情報をご覧ください。

* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


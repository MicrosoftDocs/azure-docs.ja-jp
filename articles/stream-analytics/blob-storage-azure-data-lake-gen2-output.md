---
title: Azure Stream Analytics からの BLOB ストレージと Azure Data Lake Gen2 出力
description: この記事では、Azure Stream Analytics の出力としての BLOB ストレージと Azure Data Lake Gen 2 について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 3ce22837da2ca30249b399a297b6188d950b1ea4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935144"
---
# <a name="blob-storage-and-azure-data-lake-gen2-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの BLOB ストレージと Azure Data Lake Gen2 出力

Data Lake Storage Gen2 によって、Azure Storage は、Azure 上にエンタープライズ データ レイクを構築するための基盤となります。 Data Lake Storage Gen2 は、当初から、何百ものギガビット単位のスループットを維持しつつ、複数のペタバイト単位の情報を利用可能にする目的で設計されているため、大量のデータを簡単に管理することができます。 Data Lake Storage Gen2 の基礎部分は、BLOB ストレージに階層型名前空間を追加したものです。

Azure Blob Storage を使用すると、大量の非構造化データをクラウドに保存する場合に、コスト効果の高いスケーラブルなソリューションを実現できます。 BLOB ストレージとその使用法の概要については、[Azure Portal を使用した BLOB のアップロード、ダウンロード、および一覧表示](../storage/blobs/storage-quickstart-blobs-portal.md)に関するページを参照してください。

## <a name="output-configuration"></a>出力の構成

次の表に、BLOB または ADLS Gen2 出力を作成するためのプロパティの名前とその説明を示します。

| プロパティ名       | 説明                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| 出力エイリアス        | クエリの出力をこの BLOB ストレージに出力するためにクエリで使用されるわかりやすい名前です。 |
| ストレージ アカウント     | ご自分の出力を送信するストレージ アカウントの名前です。               |
| ストレージ アカウント キー | ストレージ アカウントに関連付けられている秘密キー。                              |
| ストレージ コンテナー   | Azure Blob service に格納される BLOB の論理グループです。 BLOB を Blob service にアップロードするとき、その BLOB のコンテナーを指定する必要があります。 |
| パスのパターン | 省略可能。 指定したコンテナー内のご自分の BLOB を書き込むために使用されるファイル パス パターンです。 <br /><br /> パス パターン内では、BLOB が書き込まれる頻度を指定するために、日付と時刻の変数のインスタンスを 1 つまたは複数使用できます。 <br /> {date}、{time} <br /><br />カスタム BLOB パーティション分割を使用して、イベント データの 1 つのカスタム {field} 名を指定することで、BLOB をパーティション分割できます。 このフィールド名は英数字であり、スペース、ハイフン、およびアンダースコアを含めることができます。 カスタム フィールドには次の制限事項が含まれます。 <ul><li>フィールド名は大文字小文字が区別されません。 たとえば、このサービスでは列 "ID" と列 "id" を区別できません。</li><li>入れ子になったフィールドは使用できません。 代わりに、ジョブ クエリ内で別名を使用して、フィールドを "フラット化" します。</li><li>式はフィールド名として使用できません。</li></ul> <br />この機能により、パスでカスタム日付/時刻書式指定子の構成を使用できるようになります。 カスタム日時書式は、一度に 1 つを {datetime:\<specifier>} キーワードで囲んで指定する必要があります。 \<specifier> の使用可能な入力は、yyyy、MM、M、dd、d、HH、H、mm、m、ss、または s です。 カスタムの日付/時刻の構成を形成するために、パス内で {datetime:\<specifier>} キーワードを複数回使用できます。 <br /><br />例 : <ul><li>例 1: cluster1/logs/{date}/{time}</li><li>例 2: cluster1/logs/{date}</li><li>例 3: cluster1/{client_id}/{date}/{time}</li><li>例 4: cluster1/{datetime:ss}/{myField}。この場合、クエリは次のようになります。SELECT data.myField AS myField FROM Input;</li><li>例 5: cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br />作成されたフォルダー構造のタイム スタンプは、ローカル時間ではなく、UTC に従います。 [System.Timestamp](./stream-analytics-time-handling.md#choose-the-best-starting-time) は、時間ベースのパーティション分割すべてに使用される時間です。<br /><br />ファイルの名前付けでは、次の規則を使用します。 <br /><br />{Path Prefix Pattern}/schemaHashcode_Guid_Number.extension<br /><br /> ここで、GUID は、BLOB ファイルに書き込むために作成された内部ライターに割り当てられた一意識別子を表します。 この数値は、BLOB ブロックのインデックスを表します。 <br /><br /> 出力ファイル例:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br />この機能の詳細については、「[Azure Stream Analytics でのカスタム BLOB 出力のパーティション分割](stream-analytics-custom-path-patterns-blob-storage-output.md)」を参照してください。 |
| 日付の形式 | 省略可能。 日付トークンがプレフィックス パスで使用されている場合、ファイルを編成する日付形式を選択できます。 例:YYYY/MM/DD |
| 時刻の形式 | 省略可能。 時刻トークンがプレフィックス パスで使用されている場合、ファイルを編成する時刻形式を指定します。 現在唯一サポートされている値は HH です。 |
| イベントのシリアル化の形式 | 出力データのシリアル化形式。 JSON、CSV、Avro、Parquet がサポートされています。 |
|最小行数 |バッチあたりの最小行数。 Parquet では、バッチごとに新しいファイルが作成されます。 現在の既定値は 2,000 行であり、最大許容値は 10,000 行です。|
|最大時間 |バッチあたりの最大待機時間。 この時間が経過すると、最小行数の要件が満たされていなくても、バッチは出力に書き込まれます。 現在の既定値は 1 分であり、最大許容値は 2 時間です。 BLOB 出力にパス パターンの頻度がある場合は、待機時間をパーティションの時間の範囲より長くすることはできません。|
| エンコード    | CSV または JSON 形式を使用する場合は、エンコードを指定する必要があります。 現時点でサポートされているエンコード形式は UTF-8 だけです。 |
| 区切り記号   | CSV のシリアル化のみに適用されます。 Stream Analytics は、CSV データをシリアル化するために、一般的な区切り記号をサポートしています。 サポートしている値は、コンマ、セミコロン、スペース、タブ、および縦棒です。 |
| Format      | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[改行区切り]** を選択した場合、JSON は一度に 1 オブジェクトずつ読み取られます。 コンテンツ全体は、それ自体では有効な JSON になりません。 **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。 |

## <a name="blob-output-files"></a>BLOB 出力ファイル

出力として Blob Storage を使用しているときに、BLOB に新しいファイルが作成されるのは、次の場合です。

* ファイルが許可されるブロックの最大数 (現在は 50,000) を超えている場合。 BLOB の最大許容サイズに到達することなく、ブロックの最大許容数に到達する場合があります。 たとえば、出力レートが高い場合は、表示されるブロックあたりのバイト数が増え、ファイルのサイズが増加します。 出力レートが低い場合は、各ブロックのデータが少なくなり、ファイルのサイズも小さくなります。
* 出力のスキーマが変更されている場合、および出力形式で固定スキーマが必要な場合 (CSV、Avro、Parquet)。
* ジョブを停止し起動するユーザーが外部からジョブを再起動した場合、またはシステム メンテナンスやエラーの復旧のために内部的にジョブを再起動した場合。
* クエリが完全にパーティション分割されており、新しいファイルが出力パーティションごとに作成される場合。
* ストレージ アカウントのファイルまたはコンテナーがユーザーによって削除された場合。
* パス プレフィックス パターンを使用して時間で出力をパーティション分割する場合、クエリが次の時間に移動すると、新しい BLOB が使用されます。
* カスタム フィールドによって出力をパーティション分割する場合、BLOB が存在しないと、パーティション キーごとに新しい BLOB が作成されます。
* パーティション キーのカーディナリティが 8,000 を超えるカスタム フィールドによって出力をパーティション分割する場合、パーティション キーごとに新しい BLOB が作成されます。

## <a name="partitioning"></a>パーティション分割

パーティション キーについては、パス パターンにご自分のイベント フィールドからの {date} および {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 BLOB 出力を 1 つのカスタム イベント属性 {fieldname} または {datetime:\<specifier>} でパーティション分割できます。 出力ライターの数は、[完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズについては、[Azure Storage の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)に関する記事を参照してください。 BLOB ブロックの最大サイズは 4 MB で、BLOB の最大ブロック数は 50,000 です。 |

## <a name="next-steps"></a>次のステップ

* [マネージド ID (プレビュー) を使用して、Azure Blob Storage に対して Azure Stream Analytics ジョブを認証する](blob-output-managed-identity.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)

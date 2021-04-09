---
title: Azure Stream Analytics からの Azure Data Lake Storage Gen 1 出力
description: この記事では、Azure Stream Analytics の出力オプションとしての Azure Data Lake Storage Gen 1 について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 629a154c89ad301a3e200b1d6cd04c62057d9959
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016543"
---
# <a name="azure-data-lake-storage-gen-1-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure Data Lake Storage Gen 1 出力

Stream Analytics は、[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md) 出力をサポートしています。 Azure Data Lake Storage は、ビッグ データの分析ワークロードのためのエンタープライズ レベルのハイパースケール リポジトリです。 Data Lake Storage を使用すると、運用分析や調査分析のために任意のサイズ、種類、および取り込み速度のデータを格納できます。 Data Lake Storage にアクセスするには、Stream Analytics を承認する必要があります。

Stream Analytics からの Azure Data Lake Storage 出力は、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは使用できません。

## <a name="output-configuration"></a>出力の構成

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
| Format | JSON のシリアル化のみに適用されます。 **[改行区切り]** を指定すると、各 JSON オブジェクトを改行で区切って、出力が書式設定されます。 **[改行区切り]** を選択した場合、JSON は一度に 1 オブジェクトずつ読み取られます。 コンテンツ全体は、それ自体では有効な JSON になりません。  **[配列]** を指定すると、JSON オブジェクトの配列として出力が書式設定されます。 この配列が閉じられるのは、ジョブが停止したとき、または Stream Analytics が次の時間枠に移動したときだけです。 一般に、改行区切りの JSON を使うことが推奨されます。そうすれば、出力ファイルがまだ書き込まれている間に、特別な処理は必要ありません。|
| 認証モード | [マネージド ID](stream-analytics-managed-identities-adls.md) (プレビュー) またはユーザー トークンを使用して、Data Lake Storage アカウントへのアクセスを認可できます。 アクセス権を付与した後は、ユーザー アカウントのパスワードを変更するか、このジョブの Data Lake Storage 出力を削除するか、または Stream Analytics ジョブを削除することによってアクセスを取り消すことができます。 |

## <a name="partitioning"></a>パーティション分割

パーティション キーの場合は、パス プレフィックス パターンに {date} トークンと {time} トークンを使用します。 YYYY/MM/DD、DD/MM/YYYY、MM-DD-YYYY などの日付形式を選択します。 時間形式には HH を使用します。 出力ライターの数は、[完全並列化可能なクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。

## <a name="output-batch-size"></a>出力バッチ サイズ

メッセージの最大サイズについては、[Data Lake Storage の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits)に関する記事を参照してください。 バッチ サイズを最適化するには、書き込み操作あたり最大 4 MB を使用します。

## <a name="next-steps"></a>次のステップ

* [マネージド ID を使用して Azure Data Lake Storage Gen1 に対して Stream Analytics を認証する (プレビュー)](stream-analytics-managed-identities-adls.md)
* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
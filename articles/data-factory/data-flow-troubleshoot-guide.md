---
title: データ フローのトラブルシューティング
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/16/2020
ms.openlocfilehash: 0a691b562ebf030712eb0c13a688ea9a52fdb164
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263471"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Azure Data Factory でのデータ フローのトラブルシューティング

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory のデータ フローの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合
- **推奨事項**:データセットで参照されているコンテナーが存在するかアクセス可能であることを確認してください。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian

- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**:論理プラン間の INNER join では暗黙的なデカルト積はサポートされていません。 結合で使用される列が一意のキーを作成する場合は、リレーションシップの両側から少なくとも 1 つの列が必要です。
- **推奨事項**:非等値ベースの結合では、CUSTOM CROSS JOIN を選択する必要があります。

### <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson

- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**:JSON ファイルで、次のような問題が発生している可能性があります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用
- **推奨事項**:JSON ファイルのエンコードがサポートされているかどうか確認します。 JSON データセットを使用しているソース変換で [JSON 設定] を展開し、[単一のドキュメント] をオンにします。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout

- **メッセージ**:ブロードキャスト結合のタイムアウトエラーが発生しました。ブロードキャスト ストリームが、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成することを確認してください
- **原因**:ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎると思われます。
- **推奨事項**:データ フロー変換の [最適化] タブで、結合、存在、および参照を確認します。 [Broadcast]\(ブロードキャスト\) の既定のオプションは "Auto" (自動) です。 これが設定されている場合、または "Fixed" (固定) で右側または左側でブロードキャストするよう手動で設定している場合は、より大きな Azure Integration Runtime 構成を設定するか、ブロードキャストをオフにすることができます。 データ フローで最適なパフォーマンスを得るための推奨されるアプローチは、"Auto" (自動) を使用して Spark がブロードキャストできるよう許可し、メモリ最適化 Azure IR を使用できるようにすることです。

### <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion

- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**:データの形式が正しくありません
- **推奨事項**:正しいデータ型を使用してください

### <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn

- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**:列名が指定されていませんでした
- **推奨事項**:min()/max() などの SQL 関数を使用している場合は、別名を設定してください。

### <a name="error-code-getcommand-outputasync-failed"></a>エラー コード:GetCommand OutputAsync に失敗しました

- **メッセージ**:データ フロー デバッグおよびデータ プレビューの実行中:GetCommand OutputAsync が次の理由で失敗しました。
- **原因**:これはバックエンド サービス エラーです。 操作を再試行し、デバッグ セッションを再起動することもできます。
- **推奨事項**:再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>エラー コード:予期しない例外が発生し、実行が失敗しました

- **メッセージ**:データ フロー アクティビティの実行中:予期しない例外が発生し、実行が失敗しました。
- **原因**:これはバックエンド サービス エラーです。 操作を再試行し、デバッグ セッションを再起動することもできます。
- **推奨事項**:再試行して再起動しても問題が解決しない場合は、カスタマー サポートにお問い合わせください。

### <a name="error-code-debug-data-preview-no-output-data-on-join"></a>エラー コード:デバッグ データのプレビューに結合時の出力データがありません

- **メッセージ**:サンプリングされた行数が少なすぎることが原因で発生した可能性がある、多数の null 値または不足値があります。 デバッグ行の制限を更新し、データを最新の情報に更新してみてください。
- **原因**:結合条件がどの行にも一致しなかったか、データ プレビュー中に多数の NULL が発生する結果になりました。
- **推奨事項**:[デバッグ設定] に移動し、ソース行の制限の行数を増やします。 より多くのデータを処理するのに十分なデータ フロー クラスターと共に Azure IR を選択したことを確認します。


## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス

1. データセット接続の状態を確認します。 ソース/シンクの変換ごとに、使用している各データベースのリンクされたサービスにアクセスし、接続をテストします。
1. データ フロー デザイナーからファイルとテーブルの接続の状態を確認します。 デバッグをオンに切り替え、ソース変換のデータ プレビューをクリックし、データにアクセスできることを確認します。
1. データ プレビューに問題がなければ、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。
*  [Data Factory ブログ](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)

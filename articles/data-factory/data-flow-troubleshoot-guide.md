---
title: マッピング データ フローのトラブルシューティング
description: Azure Data Factory でのデータ フローに関する問題のトラブルシューティングを行う方法について説明します。
services: data-factory
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/11/2020
ms.openlocfilehash: cc87694686bd5143b03d690286bd3171cf8b0e18
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222151"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Azure Data Factory でマッピング データ フローをトラブルシューティングする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory でマッピング データ フローのための一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-messages"></a>一般的なエラーとメッセージ

### <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合
- **推奨事項**:データセットで参照されているコンテナーが存在するかアクセス可能であることを確認してください。

### <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian

- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**:論理プラン間の INNER join では暗黙的なデカルト積はサポートされていません。 結合で使用される列によって一意のキーが作成される場合は、リレーションシップの両側から少なくとも 1 つの列が必要になります。
- **推奨事項**:非等値ベースの結合では、CUSTOM CROSS JOIN を選択する必要があります。

### <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson

- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**:JSON ファイルで、次のような問題が発生している可能性があります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用
- **推奨事項**:JSON ファイルのエンコードがサポートされているかどうか確認します。 JSON データセットを使用しているソース変換で [JSON 設定] を展開し、[単一のドキュメント] をオンにします。
 
### <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout

- **メッセージ**:ブロードキャスト結合のタイムアウトエラーが発生しました。ブロードキャスト ストリームが、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成することを確認してください
- **原因**:ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎると考えられます。
- **推奨事項**:データ フロー変換の [最適化] タブで、結合、存在、および参照を確認します。 [Broadcast]\(ブロードキャスト\) の既定のオプションは "Auto" (自動) です。 "Auto" (自動) が設定されている場合、または "Fixed" (固定) 下の左側または右側でブロードキャストするように手動で設定している場合は、より大きな Azure Integration Runtime 構成を設定するか、ブロードキャストをオフにすることができます。 データ フローで最適なパフォーマンスを得るための推奨されるアプローチは、"Auto" (自動) を使用して Spark がブロードキャストできるよう許可し、メモリ最適化 Azure IR を使用できるようにすることです。

デバッグ パイプライン実行からのデバッグ テスト実行でデータ フローを実行している場合、この条件がより頻繁に実行されることがあります。 これは、より高速のデバッグ エクスペリエンスを維持するために、ADF によってブロードキャスト タイムアウトが 60 秒に調整されるためです。 トリガーされた実行から 300 秒のタイムアウトまで拡張する場合は、[デバッグ] > [Use Activity Runtime]\(アクティビティ ランタイムを使用する\) オプションを使用して、データ フローの実行パイプライン アクティビティで定義された Azure IR を活用できます。

### <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion

- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**:データの形式が正しくありません
- **推奨事項**:正しいデータ型を使用してください

### <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn

- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**:列名が指定されていませんでした
- **推奨事項**:min()/max() などの SQL 関数を使用している場合は、別名を設定してください。

 ### <a name="error-code-df-executor-drivererror"></a>エラー コード:DF-Executor-DriverError
- **メッセージ**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。
- **原因**:ドライバー エラー
- **推奨事項**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。

 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>エラー コード:DF-Executor-BlockCountExceedsLimitError
- **メッセージ**:コミット前のブロック数は、ブロックの上限である 100,000 個を超えることはできません。 BLOB 構成を確認してください。
- **原因**:1 つの BLOB でのコミット前のブロックが、上限の 100,000 個になっている可能性があります。
- **推奨事項**:Microsoft 製品チームに、この問題についての詳細をお問い合わせください

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>エラー コード:DF-Executor-PartitionDirectoryError
- **メッセージ**:指定されたソース パスに、複数のパーティション ディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/<パーティション ルート ディレクトリ 2>/c=10/d=30)、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/Directory 2/file1) のいずれかが含まれています。ソース パスからパーティション ルート ディレクトリを削除して、別個のソース変換を利用して読み取ってください。
- **原因**:ソース パスに複数のパーティション ディレクトリ、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリのいずれかが含まれています。
- **推奨事項**:パーティション ルート ディレクトリをソース パスから削除し、別個のソース変換を使用して読み取ってください。

 ### <a name="error-code-df-executor-outofmemoryerror"></a>エラー コード:DF-Executor-OutOfMemoryError
- **メッセージ**:実行中に、クラスターにメモリ不足の問題が発生しました。コア数がより大きく、メモリが最適化されたコンピューティングの種類で統合ランタイムを使用して、もう一度お試しください
- **原因**:クラスターのメモリがメモリ不足しています
- **推奨事項**:デバッグ クラスターは、開発を目的とするものではありません。 データ サンプリング、適切なコンピューティングの種類、およびサイズを利用して、ペイロードを実行します。 最適なパフォーマンスを実現するための調整については、[マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)を参照してください。

 ### <a name="error-code-df-executor-illegalargument"></a>エラー コード:DF-Executor-illegalArgument
- **メッセージ**:リンクされたサービスのアクセス キーが正しいことを確認してください
- **原因**:アカウント名またはアクセス キーが正しくありません
- **推奨事項**:リンクされたサービスで指定されたアカウント名またはアクセス キーが正しいことを確認してください。 

 ### <a name="error-code-df-executor-invalidtype"></a>エラー コード:DF-Executor-InvalidType
- **メッセージ**:パラメーターの型が、渡された値の型と一致していることを確認してください。 パイプラインからの float パラメーターの引き渡しは現在、サポートされていません。
- **原因**:宣言された型と実際のパラメーター値の間のデータ型に、互換性がありません
- **推奨事項**:データ フローに渡されたパラメーター値が、宣言された型と一致することを確認してください。

 ### <a name="error-code-df-executor-columnunavailable"></a>エラー コード:DF-Executor-ColumnUnavailable
- **メッセージ**:式に使用されている列名が利用できないか、または無効です
- **原因**:式に使用されている列名が無効か、または利用できません
- **推奨事項**:式に使用されている列名を確認してください

 ### <a name="error-code-df-executor-parseerror"></a>エラー コード:DF-Executor-ParseError
- **メッセージ**:式を解析できません
- **原因**:書式設定が原因で、式の解析エラーが発生しています
- **推奨事項**:式の形式を確認してください

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
- **推奨事項**:[デバッグ設定] に移動し、ソース行の制限の行数を増やします。 より多くのデータを処理するのに十分なデータ フロー クラスターと併せ、Azure IR を選択したことを確認します。

### <a name="error-code-validation-error-at-source-with-multiline-csv-files"></a>エラー コード:複数行の CSV ファイルが含まれるソースで検証エラーが発生しました 

- **メッセージ**:次のエラー メッセージのいずれかが表示される場合があります。
   - 最後の列が null であるか、見つかりません。
   - ソースでスキーマの検証に失敗しました。
   - UX でスキーマのインポートが正しく表示されず、最後の列の名前に改行文字が含まれています。
- **原因**:マッピング データ フローでは、現在、複数行の CSV ソースが \r\n 行区切り記号として機能していません。 改行に余分な行があると、ソースの値が壊れる場合があります。 
- **推奨事項**:行区切り記号として \r\n ではなく、\n を使用してソースでファイルを生成します。 または、コピー アクティビティを使用して、行区切り文字として \r\n が含まれる CSV ファイルを \n に変換します。

### <a name="error-code-df-executor-sourceinvalidpayload"></a>エラー コード:DF-Executor-SourceInvalidPayload
- **メッセージ**:コンテナーが存在しないため、データ プレビュー、デバッグ、パイプライン データ フローの実行が失敗しました
- **原因**:ストレージに存在しないコンテナーがデータセットに含まれている場合
- **推奨事項**:データセットで参照されているコンテナーが存在するかアクセス可能であることを確認してください。


 ### <a name="error-code-df-executor-systemimplicitcartesian"></a>エラー コード:DF-Executor-SystemImplicitCartesian
- **メッセージ**:INNER join では暗黙的なデカルト積はサポートされていません。代わりに CROSS JOIN を使用してください。 結合で使用される列は、行に対して一意のキーを作成する必要があります。
- **原因**:論理プラン間の INNER join では暗黙的なデカルト積はサポートされていません。 結合で使用される列が一意のキーを作成する場合
- **推奨事項**:非等値ベースの結合では、CROSS JOIN を選択する必要があります。


 ### <a name="error-code-df-executor-systeminvalidjson"></a>エラー コード:DF-Executor-SystemInvalidJson
- **メッセージ**:JSON 解析エラー、サポートされていないエンコードまたは複数行が存在します
- **原因**:JSON ファイルで、次のような問題が発生している可能性があります。サポートされていないエンコード、バイトの破損、または入れ子になった多数の行での単一ドキュメントとしての JSON ソースの使用
- **推奨事項**:JSON ファイルのエンコードがサポートされているかどうか確認します。 JSON データセットを使用しているソース変換で [JSON 設定] を展開し、[単一のドキュメント] をオンにします。


 ### <a name="error-code-df-executor-broadcasttimeout"></a>エラー コード:DF-Executor-BroadcastTimeout
- **メッセージ**: ブロードキャスト結合のタイムアウト エラー。この問題を回避するには、結合/存在/参照変換でブロードキャスト オプションの [オフ] を選択します。 パフォーマンスを向上させるために結合オプションをブロードキャストする場合は、ブロードキャスト ストリームで、デバッグ実行では 60 秒以内に、ジョブ実行では 300 秒以内にデータを生成できることを確認します。
- **原因**:ブロードキャストの既定のタイムアウトは、デバッグ実行では 60 秒、ジョブ実行では 300 秒です。 ブロードキャスト結合の場合、ブロードキャスト用に選択されたストリームは、この制限内にデータを生成するには大きすぎると考えられます。 ブロードキャスト結合が使用されていない場合、データフローによって実行される既定のブロードキャストが同じ制限に達することがあります。
- **推奨事項**:ブロードキャスト オプションをオフにするか、処理に 60 秒を超える時間を要する可能性がある大規模なデータ ストリームのブロードキャストは避けてください。 代わりに、ブロードキャストするための小さいストリームを選択してください。 通常、大規模な SQL/DW テーブルとソース ファイルは適切な候補ではありません。 ブロードキャスト結合が存在しない場合、エラーが発生したときはより大きなクラスターを使用します。


 ### <a name="error-code-df-executor-conversion"></a>エラー コード:DF-Executor-Conversion
- **メッセージ**:無効な文字が原因で、日付または時刻への変換に失敗しました
- **原因**:データの形式が正しくありません
- **推奨事項**:正しいデータ型を使用してください


 ### <a name="error-code-df-executor-invalidcolumn"></a>エラー コード:DF-Executor-InvalidColumn
- **メッセージ**:クエリに列名を指定する必要があります。SQL 関数を使用している場合は別名を設定してください
- **原因**:列名が指定されていませんでした。


 ### <a name="error-code-df-executor-drivererror"></a>エラー コード:DF-Executor-DriverError
- **メッセージ**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。
- **原因**:ドライバー エラーです。
- **推奨事項**:INT96 は、ADF データフローではサポートされていない従来のタイムスタンプ型です。 列の型を最新の型にアップグレードすることを検討してください。


 ### <a name="error-code-df-executor-blockcountexceedslimiterror"></a>エラー コード:DF-Executor-BlockCountExceedsLimitError
- **メッセージ**:コミット前のブロック数は、ブロックの上限である 100,000 個を超えることはできません。 BLOB 構成を確認してください。
- **原因**:1 つの BLOB でのコミット前のブロックが、上限の 100,000 個になっている可能性があります。
- **推奨事項**:Microsoft 製品チームに、この問題についての詳細をお問い合わせください

 ### <a name="error-code-df-executor-partitiondirectoryerror"></a>エラー コード:DF-Executor-PartitionDirectoryError
- **メッセージ**: 指定されたソース パスに、複数のパーティション ディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/<パーティション ルート ディレクトリ 2>/c=10/d=30)、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリ (たとえば、<Source Path>/<パーティション ルート ディレクトリ 1>/a=10/b=20、<Source Path>/Directory 2/file1) のいずれかが含まれています。ソース パスからパーティション ルート ディレクトリを削除して、別個のソース変換を利用して読み取ってください。
- **原因**:ソース パスに複数のパーティション ディレクトリ、他のファイルを含むパーティション ディレクトリ、またはパーティション分割されていないディレクトリのいずれかが含まれています。
- **推奨事項**:パーティション ルート ディレクトリをソース パスから削除し、別個のソース変換を使用して読み取ってください。


 ### <a name="error-code-df-executor-outofmemoryerror"></a>エラー コード:DF-Executor-OutOfMemoryError
- **メッセージ**:実行中に、クラスターにメモリ不足の問題が発生しました。コア数がより大きく、メモリが最適化されたコンピューティングの種類で統合ランタイムを使用して、もう一度お試しください
- **原因**:クラスターのメモリがメモリ不足しています。
- **推奨事項**:デバッグ クラスターは、開発を目的とするものではありません。 データ サンプリング、適切なコンピューティングの種類、およびサイズを利用して、ペイロードを実行します。 最適なパフォーマンスを得るためにデータフローをチューニングするには、[データフロー パフォーマンスに関するガイド](https://docs.microsoft.com/azure/data-factory/concepts-data-flow-performance)を参照してください。


 ### <a name="error-code-df-executor-illegalargument"></a>エラー コード:DF-Executor-illegalArgument
- **メッセージ**: リンクされたサービスのアクセス キーが正しいことを確認してください。
- **原因**:アカウント名またはアクセス キーが正しくありません。
- **推奨事項**:正しいアカウント名またはアクセス キーを指定してください。


 ### <a name="error-code-df-executor-invalidtype"></a>エラー コード:DF-Executor-InvalidType
- **メッセージ**:パラメーターの型が、渡された値の型と一致していることを確認してください。 パイプラインからの float パラメーターの引き渡しは現在、サポートされていません。
- **原因**:宣言された型と実際のパラメーター値の間のデータ型に、互換性がありません
- **推奨事項**:適切なデータ型を指定してください。


 ### <a name="error-code-df-executor-columnunavailable"></a>エラー コード:DF-Executor-ColumnUnavailable
- **メッセージ**: 式に使用されている列名が利用できないか、または無効です。
- **原因**:式に使用されている列名が無効か、または利用できません。
- **推奨事項**:式に使用されている列名を確認してください。


 ### <a name="error-code-df-executor-parseerror"></a>エラー コード:DF-Executor-ParseError
- **メッセージ**: 式を解析できません。
- **原因**:書式設定が原因で、式の解析エラーが発生しています。
- **推奨事項**:式の形式を確認してください。


 ### <a name="error-code-df-executor-outofdiskspaceerror"></a>エラー コード:DF-Executor-OutOfDiskSpaceError
- **メッセージ**: 内部サーバー エラー
- **原因**:クラスターのディスク領域が不足しています。
- **推奨事項**:パイプラインを再試行してください。 問題が解決しない場合は、カスタマー サポートにお問い合わせください。


 ### <a name="error-code-df-executor-storeisnotdefined"></a>エラー コード:DF-Executor-StoreIsNotDefined
- **メッセージ**: ストアの構成が定義されていません。 このエラーは、パイプラインでの無効なパラメーター割り当てが原因である可能性があります。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:パイプラインでパラメーター値の割り当てを確認してください。 パラメーター式に無効な文字が含まれている可能性があります。


 ### <a name="error-code-df-excel-invalidconfiguration"></a>エラー コード:DF-Excel-InvalidConfiguration
- **メッセージ**: Excel シートの名前またはインデックスが必要です。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。


 ### <a name="error-code-df-excel-invalidconfiguration"></a>エラー コード:DF-Excel-InvalidConfiguration
- **メッセージ**: Excel シートの名前とインデックスが同時に存在することはできません。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:パラメーター値を確認し、Excel データを読み取るシート名またはインデックスを指定してください。


 ### <a name="error-code-df-excel-invalidconfiguration"></a>エラー コード:DF-Excel-InvalidConfiguration
- **メッセージ**: 無効な範囲が指定されています。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:パラメーター値を確認し、有効な範囲を参照渡しで指定してください。[Excel のプロパティ](https://docs.microsoft.com/azure/data-factory/format-excel#dataset-properties)。


 ### <a name="error-code-df-excel-invaliddata"></a>エラー コード:DF-Excel-InvalidData
- **メッセージ**: Excel ワークシートが存在しません。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:パラメーター値を確認し、Excel データを読み取る有効なシート名またはインデックスを指定してください。

 ### <a name="error-code-df-excel-invaliddata"></a>エラー コード:DF-Excel-InvalidData
- **メッセージ**: スキーマが異なる Excel ファイルの読み取りは、現時点ではサポートされていません。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:正しい Excel ファイルを使用します。


 ### <a name="error-code-df-excel-invaliddata"></a>エラー コード:DF-Excel-InvalidData
- **メッセージ**: データ型はサポートされていません。
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:Excel ファイルの適切なデータ型を使用します。

 ### <a name="error-code-df-excel-invalidconfiguration"></a>エラー コード:DF-Excel-InvalidConfiguration
- **メッセージ**: 無効な Excel ファイルが指定されていますが、.xlsx と .xls のみがサポートされています
- **原因**:[Undetermined]\(不明\)
- **推奨事項**:Excel ファイル拡張子が .xlsx または .xls であることを確認します。

## <a name="general-troubleshooting-guidance"></a>一般的なトラブルシューティング ガイダンス
1. データセット接続の状態を確認します。 ソース/シンクの変換ごとに、使用している各データベースのリンクされたサービスにアクセスし、接続をテストします。
2. データ フロー デザイナーからファイルとテーブルの接続の状態を確認します。 デバッグをオンに切り替え、ソース変換のデータ プレビューをクリックし、データにアクセスできることを確認します。
3. データ プレビューに問題がなければ、パイプライン デザイナーに進み、パイプライン アクティビティにデータ フローを配置します。 エンドツーエンド テストとしてパイプラインをデバッグします。


## <a name="next-steps"></a>次のステップ

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。
*  [Data Factory ブログ](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/videos)
*  [Microsoft Q&A 質問ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)

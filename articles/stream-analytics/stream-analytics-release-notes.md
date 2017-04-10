---
title: "Stream Analytics のリリース ノート | Microsoft Docs"
description: "Stream Analytics のリリース ノート"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 5e59f893-cd2c-43fb-9eca-c146ce637203
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/06/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2fe28e7b52af579038ec145c028a6d2796d6cc6e
ms.openlocfilehash: f47b719f7b1a78264e054ee15edacc32ca35aaf7
ms.lasthandoff: 02/01/2017


---
# <a name="stream-analytics-release-notes"></a>Stream Analytics のリリース ノート
## <a name="notes-for-02012017-release-of-stream-analytics"></a>Stream Analytics の 2017 年 2 月 1 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| JavaScript ユーザー定義関数 (UDF) の導入 |[Java ユーザー定義関数](stream-analytics-javascript-user-defined-functions.md)を使用して、さらに柔軟にクエリを作成できるようになりました。 |
| Visual Studio と Stream Analytics の導入 |[Tools for Visual Studio](stream-analytics-tools-for-visual-studio.md) が、デバッグとさらに優れたユーティリティで使用できるようになりました。 |
| 診断ログの導入 |[診断ログ](stream-analytics-job-diagnostic-logs.md)が、追加のトラブルシューティング オプションで使用できるようになりました。 |
| GeoSpatial 関数の導入 |[GeoSpatial 関数](http://msdn.microsoft.com/library/mt778980(Azure.100).aspx)が一般公開されました。 |

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Stream Analytics の 2016 年 4 月 15 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| Power BI 出力の一般提供 |[Power BI 出力](stream-analytics-power-bi-dashboard.md) の一般提供が開始しました。 Power BI の 90 日間の承認有効期限は削除されました。 承認の更新が必要となるシナリオの詳細については、Power BI ダッシュボードの作成に関するページの「 [承認の更新](stream-analytics-power-bi-dashboard.md#renew-authorization) 」セクションを参照してください。 |

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Stream Analytics の 2016 年 3 月 3 日付リリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| 新しい Stream Analytics クエリ言語項目 |SAQL には [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN ページ")、[TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN ページ")、および [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN ページ") があります。 |

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 12 月 10 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| REST API バージョン更新 |REST API バージョンが 2015-10-01 に更新されました。 詳細については、MSDN の [Stream Analytics 管理 REST API リファレンス](https://msdn.microsoft.com/library/azure/dn835031.aspx)、および「[Stream Analytics への Machine Learning の統合](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)」を参照してください。 |
| Azure Machine Learning 統合 |このリリースで、Azure Machine Learning ユーザー定義関数のサポートが付きます。 詳細については、[チュートリアル](stream-analytics-machine-learning-integration-tutorial.md)と、[ブログでの全般的なお知らせ](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx)を参照してください。 |

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 11 月 12 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| SELECT の新しい動作 |Stream Analytics の SELECT が拡張され、入れ子になったレコードのプロパティ アクセサーとして、"*" を使用できるようになりました。 詳細については、 [http://msdnがあります。microsoftがあります。com/library/mt622759があります。aspx](http://msdn.microsoft.com/library/mt622759.aspx "複合データ型")があります。 |

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 10 月 22 日のリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| Additional query language features (追加のクエリ言語機能) |Stream Analytics では、機能 [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx)、[CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx)、[EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx)、[FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx)、[POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx)、[SIGN](https://msdn.microsoft.com/library/azure/mt605290.aspx)、[SQUARE](https://msdn.microsoft.com/library/azure/mt605288.aspx)、および [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx) を含めることでクエリ言語が拡張されました。 |
| Removed aggregate limitations (集計の制限を削除) |このリリースでは、クエリにおける 15 の集計の制限が削除されます。 クエリごとの集計の数に制限がなくなりました。 |
| Added GROUP BY System.Timestamp feature (GROUP BY System.Timestamp 機能を追加) |[GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) 関数で、window_type または [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx) のいずれかの使用が可能になりました。 |
| Added OFFSET for Tumbling and Hopping windows (タンブリング ウィンドウとホッピング ウィンドウのオフセットを追加) |既定では、[タンブリング](https://msdn.microsoft.com/library/azure/dn835055.aspx) ウィンドウと[ホッピング](https://msdn.microsoft.com/library/azure/dn835041.aspx) ウィンドウは 0 時間にアラインされます (1/1/0001 12:00:00 AM UTC)。 新しい (省略可能) パラメーター 'offsetsize' では、カスタム オフセット (またはアライメント) を指定できます。 |

## <a name="notes-for-09292015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 9 月 29 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| Azure IoT Suite のパブリック プレビュー |Stream Analytics は、Azure IoT Suite のパブリック プレビューに含まれています。 |
| Azure ポータルの統合 |Microsoft Azure 管理ポータルで引き続き提供されている Stream Analytics が、 [Azure ポータル](https://azure.microsoft.com/overview/preview-portal/)にも統合されました。 現在、プレビュー ポータルの Stream Analytics 機能は Microsoft Azure 管理ポータルで提供されている機能のサブセットであり、ブラウザ内クエリ テスト、Power BI 出力構成、およびアクセス権を持つサブスクリプション内の新しい入力および出力リソースの参照や作成に関するサポートは提供されていません。 |
| DocumentDB 出力のサポート |Stream Analytics のジョブを [DocumentDB](https://azure.microsoft.com/services/documentdb/)に出力できるようになりました。 |
| IoT Hub 入力に対するサポート |Stream Analytics ジョブで、IoT Hub からデータを取り込めるようになりました。 |
| 多様なイベントに対する TIMESTAMP BY |単一のデータ ストリームに、異なるフィールドにタイムスタンプを持つ複数のイベント タイプが含まれている場合、式と共に [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) を使用してケースごとに異なるタイムスタンプ フィールドを指定できるようになりました。 |

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 9 月 10 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| PowerBI グループのサポート |他の Power BI ユーザーとデータを共有できるようにするために、Stream Analytics ジョブで Power BI アカウント内の [PowerBI グループ](stream-analytics-define-outputs.md#power-bi) に書き込むことができるようになりました。 |

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 8 月 20 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| Last 関数の追加 |Stream Analytics ジョブで [Last](http://msdn.microsoft.com/library/mt421186.aspx) 関数が使用できるようになりました。これにより、特定の期間内のイベント ストリームで最新のイベントを取得することができます。 |
| 新しい配列関数 |配列関数 [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx)、[GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx)、[GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) を使用できるようになりました。 |
| 新しいレコード関数 |レコード関数 [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx)、[GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) を使用できるようになりました。 |

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 7 月 30 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| Azure ID から切り離された Power BI の組織 ID |この機能により、任意の種類の Azure アカウント (Live ID または組織 ID) で ASA ジョブの [Power BI 出力](stream-analytics-power-bi-dashboard.md) が可能になりました。 さらに、Azure アカウントに対して&1; つの組織 ID を使用し、Power BI 出力の承認に対して別の ID を使用できます。 |
| Service Bus キューの出力のサポート |[Service Bus キュー](stream-analytics-define-outputs.md#service-bus-queues)の出力を、Stream Analytics ジョブで使用できるようになりました。 |
| Service Bus トピックの出力のサポート |[Service Bus トピック](stream-analytics-define-outputs.md#service-bus-topics)の出力を、Stream Analytics ジョブで使用できるようになりました。 |

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 9 月 7 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| カスタム Blob 出力のパーティション分割 |Blob ストレージ出力で、出力 blob が書き込まれる頻度、よび出力データ パス フォルダーの構造と形式を指定するオプションが使用できるようになりました。 |

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 5 月 3 日付けリリースに関するノート
このリリースには、次の更新プログラムが含まれています。

| タイトル | Description |
| --- | --- |
| 順不同許容範囲ウィンドウの最大値の増加 |順不同許容範囲 ウィンドウの最大サイズは 59:59 (MM:SS) になりました。 |
| JSON 出力形式: 行区切りまたは配列 |Blob ストレージまたは Event Hub に出力する際に、JSON オブジェクトの配列として、または JSON オブジェクトを改行で分割することにより、出力するオプションができました。 |

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Stream Analytics の 2015 年 4 月 16 日付けリリースに関するノート
| タイトル | Description |
| --- | --- |
| Azure Storage アカウント構成の遅延 |Stream Analytics ジョブをリージョンで初めて作成すると、そのリージョンで Stream Analytics ジョブを監視するために、新しいストレージ アカウントを作成するか、既存のアカウントを指定するように求められます。 監視の構成に待機時間が生じるせいで、同じリージョンで 30 分以内に別の Stream Analytics のジョブを作成すると、構成したばかりのアカウントが [監視ストレージ アカウント] ボックスの一覧に表示される代わりに、2 つ目のストレージ アカウントを指定するように求められます。 不要なストレージ アカウントの作成を回避するには、そのリージョンで初めてジョブを作成した後に 30 分待ってから、追加のジョブをプロビジョニングしてください。 |
| ジョブのアップグレード |現時点では、Stream Analytics は、実行中のジョブの定義または構成に対するライブ編集をサポートしていません。 実行中のジョブに対する入力、出力、クエリ、スケール、または構成を変更するには、先にジョブを停止する必要があります。 |
| 入力ソースから推論されるデータ型 |CREATE TABLE ステートメントを使用しない場合、入力の型は入力形式から派生します。たとえば、CSV からのフィールドはすべて文字列です。 型の不一致エラーを回避するために、CAST 関数を使用して、フィールドを適切な型に明示的に変換する必要があります。 |
| 存在しないフィールドが null 値として出力される |入力ソースに存在しないフィールドを参照すると、出力イベントで null 値となります。 |
| SELECT ステートメントの前に WITH ステートメントを配置する必要がある |クエリでは、SELECT ステートメントに続けて、WITH ステートメントで定義したサブクエリを指定する必要があります。 |
| メモリ不足の問題 |順序が適切でないイベントや複雑なクエリに対する Streaming Analytics ジョブの許容範囲が広く、保持している状態が大量になると、ジョブのメモリ不足が発生し、ジョブが再起動されることがあります。 開始と停止の操作は、ジョブの操作ログに記録されます。 この動作を回避するには、クエリを複数のパーティションにスケール アウトしてください。 今後のリリースでは、影響を受けるジョブを再起動する代わりにパフォーマンスを低下させることで、この制限事項が解消される予定です。 |
| ペイロード タイムスタンプのない大きな BLOB 入力によってメモリ不足の問題が発生する |TIMESTAMP BY でタイムスタンプ フィールドが指定されていない場合、BLOB ストレージから大きなファイルを使用すると、Stream Analytics ジョブがクラッシュすることがあります。 この問題を回避するには、各 BLOB のサイズを 10 MB 以下にしてください。 |
| SQL Database イベントのボリューム制限 |出力ターゲットとして SQL Database を使用すると、大量の出力データによって Stream Analytics ジョブがタイムアウトになる場合があります。 この問題を解決するには、集計またはフィルター演算子を使用して出力の量を削減するか、出力ターゲットとして Azure BLOB ストレージまたは Event Hubs を選択してください。 |
| PowerBI データセットにはテーブルを&1; つしか含めることができない |PowerBI では、特定のデータセット内で複数のテーブルがサポートされていません。 |

## <a name="get-help"></a>問い合わせ
さらにサポートが必要な場合は、 [Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics の概要](stream-analytics-introduction.md)
* [Azure Stream Analytics の使用](stream-analytics-get-started.md)
* [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


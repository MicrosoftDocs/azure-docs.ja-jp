---
title: Azure SQL Edge のリリース ノート
description: Azure SQL Edge イメージの新機能または変更点の詳細について説明したリリース ノートです。
keywords: SQL Edge のリリース ノート
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98696381"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge のリリース ノート 

この記事では、Azure SQL Edge の新しいビルドごとに、新機能と変更点について説明します。

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL エンジンのビルド 15.0.2000.1554

### <a name="fixes"></a>修正

- T-SQL ストリーミング  
   - ストリーミング オブジェクトの所有権とアクセス許可の修正
   - ログのローテーションとログのプレフィックスによるログ記録の改善
   - Azure Stream Analytics:ログ記録の改善、アダプターのエラー コードおよびエラー メッセージの改善 

- ONNX
    - 並列クエリ シナリオおよびモデルのクリーンアップの失敗に対するバグ修正
    - ONNX ランタイムの 1.5.1 へのアップグレード

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL エンジンのビルド 15.0.2000.1553

### <a name="whats-new"></a>新着記事

- 計算列での Date_Bucket 式の定義が許可されます。

### <a name="fixes"></a>修正

- 無限タイムアウトでアイテム保持ポリシーが有効にされたテーブルを削除するため、アイテム保持ポリシーを修正
- ストリーミング機能とアイテム保持ポリシーの機能のため、DacFx デプロイをサポート 
- SAS URL の入れ子になったフォルダーからのデプロイを有効にするため、DacFx デプロイを修正 
- エラーメッセージ内の長い列名をサポートするため、PREDICT を修正

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL エンジンのビルド 15.0.2000.1552

### <a name="whats-new"></a>新着記事
- Ubuntu 18.04 に基づくコンテナー イメージ 
- `LAST_VALUE()` および `FIRST_VALUE()` 関数を使用した `IGNORE NULL` および `RESPECT NULL` 構文のサポート 
- ONNX を使用した PREDICT の信頼性の向上
- データ保持ポリシーに基づくクリーンアップのサポート:
   - トラブルシューティングの保持クリーンアップ タスクのリング バッファー サポート
- 新機能のサポート: 
   - 高速復旧
   - クエリの自動チューニング
   - 並列実行シナリオ
- 省電力モードの電力節約機能強化
- ストリーミングの新機能のサポート: 
   - [スナップショット ウィンドウ](/stream-analytics-query/snapshot-window-azure-stream-analytics): 新しいウィンドウの種類を使用すると、同時に届くイベントをグループ化することができます。
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) および [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) を分析関数として有効にすることができます。 自分で選んだ列を基準にレコードを並べて返すことができます。 それらをウィンドウの一部にする必要がありません。 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) の機能強化。 

### <a name="fixes"></a>修正
- T-SQL ストリーミング操作のトラブルシューティングに関する追加のエラー メッセージと詳細 
- アイドル モードでバッテリー寿命を維持するための機能強化 
- T-SQL ストリーミング エンジンの修正: 
   - 停止したストリーミング ジョブのクリーンアップ 
   - ローカライズ用の修正プログラム 
   - Unicode 処理の強化 
   - SQL Edge T-SQL ストリーミングのデバッグ機能の強化。これにより、ユーザーは get_streaming_job からのジョブ失敗エラーに対してクエリを実行できます
- データ保持ポリシーに基づくクリーンアップ: 
    - アイテム保持ポリシーの作成とクリーンアップのシナリオの修正
- 低電力モードで省電力を向上させるためのバックグラウンド タイマー タスクの修正

### <a name="known-issues"></a>既知の問題 
- 計算列内で Date_Bucket T-SQL 関数を使用することはできません。


## <a name="ctp-23"></a>CTP 2.3
SQL エンジンのビルド 15.0.2000.1549
### <a name="whats-new"></a>新着記事
- Date_Bucket() 関数でのカスタム オリジンのサポート 
- SQL 展開の一部としての BACPAC ファイルのサポート
- データ保持ポリシーに基づくクリーンアップのサポート:      
   - アイテム保持ポリシーを有効にするための DDL のサポート 
   - ストアド プロシージャのクリーンアップとバックグラウンド クリーンアップ タスク
   - クリーンアップ タスクを監視する拡張イベント

### <a name="fixes"></a>修正
- T-SQL ストリーミング操作のトラブルシューティングに関する追加のエラー メッセージと詳細 
- アイドル モードでバッテリー寿命を維持するための機能強化 
- T-SQL ストリーミング エンジン: 
   - サブストリーム化されたホッピング ウィンドウでの透かしのスタックに関する修正 
   - ユーザーが対処できるエラーとして収集されるようにするためのフレームワークの例外処理に関する修正


## <a name="ctp-22"></a>CTP 2.2
SQL エンジンのビルド 15.0.2000.1546
### <a name="whats-new"></a>新着記事
- ルート以外のコンテナーのサポート 
- 使用状況と診断データの収集のサポート 
- T-SQL ストリーミングの更新:
   - ストリーム オブジェクト名の Unicode 文字のサポート

### <a name="fixes"></a>修正
- T-SQL ストリーミングの更新:
   - プロセス クリーンアップの機能強化
   - ログ記録と診断の機能強化
- データ インジェストのパフォーマンスの向上

## <a name="ctp-21"></a>CTP 2.1 
SQL エンジンのビルド 15.0.2000.1545
### <a name="fixes"></a>修正
- ONNX モデルを使用した PREDICT モデルによって、ARM での CPUID の問題を処理できるようにしました 
- T-SQL ストリーミングの開始時のエラー パスの処理を改善
- データが存在しない場合のジョブ メトリックでの透かしの遅延の値を修正
- アダプターにバッチ間の可変スキーマがある場合の出力アダプターでの問題を修正  

## <a name="ctp-20"></a>CTP 2.0 
SQL エンジンのビルド 15.0.2000.1401
### <a name="whats-new"></a>新着記事
-   製品名が *Azure SQL Edge* に更新されました
-  Date_Bucket 関数:
    - Date、Time、DateTime の各型のサポート
- ONNX を使用した PREDICT:
    - RUNTIME パラメーターの ONNX 要件  
- T-SQL ストリーミングのサポート (制限付きプレビュー) 
 
### <a name="known-issues"></a>既知の問題

- 問題点:タイミングの問題が原因で、起動時に DACPAC を適用すると失敗するおそれがあります。
- 対処法:SQL Server を再起動してください。 それ以外の場合は、コンテナーによって DACPAC の適用が再試行されます。

### <a name="request-support"></a>サポートの要求
[サポート ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)でサポートを依頼することができます。 次のフィールドを選択します: 
- **問題の種類**: *テクニカル* 
- **サービス**:*IoT Edge*
- **問題の種類**: "*問題が IoT Edge モジュールに関連する*"
- **問題のサブタイプ**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="サポート チケットのサンプルを示すスクリーンショット。":::

## <a name="ctp-15"></a>CTP 1.5
SQL エンジンのビルド 15.0.2000.1331
### <a name="whats-new"></a>新着記事
- Date_Bucket 関数:
    - DateTimeOffset 型のサポート
- ONNX モデルを使用した PREDICT:
  - NVARCHAR のサポート
 
## <a name="ctp-14"></a>CTP 1.4
SQL エンジンのビルド 15.0.2000.1247
### <a name="whats-new"></a>新着記事
-   ONNX モデルを使用した PREDICT:
    - VARCHAR のサポート
    - ONNX ランタイム バージョン 1.0 への移行 

- 次の機能が有効になっています:
  - CDC のサポート
  - 圧縮される履歴テーブル
  - 先読みログのより高い倍率
  - バッチ モード ES フィルターのプッシュダウン
  - 先読みの最適化
 
## <a name="ctp-13"></a>CTP 1.3
SQL エンジンのビルド 15.0.2000.1147
### <a name="whats-new"></a>新着記事
- Azure IoT ポータルのデプロイ: 
    - AMD64 および ARM の各イメージのデプロイのサポート
    - ストリーミング ジョブの作成のサポート
    - DACPAC のデプロイ
- ONNX モデルを使用した PREDICT:
    - 数値型のサポート
- 次の機能が有効になっています:
    - 列ストア スキャンのプッシュダウン集計
    - メリーゴーランド スキャン
- メモリ占有領域とメモリ消費の削減作業

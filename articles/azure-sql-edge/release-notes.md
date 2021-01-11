---
title: Azure SQL Edge のリリース ノート
description: Azure SQL Edge イメージの新機能または変更点の詳細について説明したリリース ノート
keywords: SQL Edge のリリース ノート
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 74e9772ada010d79e81ef36cae89ba586db73077
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553412"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge のリリース ノート 

この記事では、Azure SQL Edge の新しいビルドごとに、新機能と変更点について説明します。

## <a name="ctp-22"></a>CTP 2.2
### <a name="sql-engine-build-number---15020001546"></a>SQL エンジンのビルド番号 - 15.0.2000.1546
### <a name="fixes"></a>修正
1. ルート以外のコンテナーのサポート 
2. 使用状況と診断データの収集のサポート 
3. T-SQL ストリーミングの更新
   - ストリーム オブジェクト名の Unicode 文字のサポート
   - プロセス クリーンアップの機能強化
   - ログ記録と診断の機能強化
4. データ インジェストのパフォーマンスの向上

## <a name="ctp-21"></a>CTP 2.1 
### <a name="sql-engine-build-number---15020001545"></a>SQL エンジンのビルド番号 - 15.0.2000.1545
### <a name="fixes"></a>修正
1. ARM で CPUID の問題を処理するように ONNX モデルを使用した PREDICT を修正します 
2. TSQL ストリーミングの起動時のエラー パスの処理を改善するように修正します 
3. データが存在しない場合のジョブ メトリックの透かしの遅延の誤った値を修正します 
4. アダプターにバッチ間の可変スキーマがある場合の出力アダプターの問題を修正します。  

## <a name="ctp-20"></a>CTP 2.0 
### <a name="sql-engine-build-number---15020001401"></a>SQL エンジンのビルド番号 - 15.0.2000.1401
### <a name="whats-new"></a>新機能
1.  製品名が 'Azure SQL Edge' に更新されました
1.  Date_bucket 関数

    i.  Date、Time、DateTime 型のサポート
3.  ONNX を使用した PREDICT
    
    i.  ONNX の場合に RUNTIME パラメーターが必須になりました 
    
4.  TSQL ストリーミングのサポート (制限付きプレビュー) 
 
### <a name="known-issues"></a>既知の問題

1. <b>問題:</b>タイミングの問題が原因で、起動時に dacpac を適用すると失敗する可能性があります。

    <b>対処法:</b>SQL Server またはコンテナーを再起動すると、dacpac の適用が再試行されます。これで、おそらく問題が解決します。
### <a name="request-support"></a>サポートを依頼する
1. [サポート ページ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)でサポートを依頼することができます。

4. 次のフィールドが選択されていることを確認します。 
    * 問題の種類 - 技術 
    * サービス - IoT Edge
    * 問題の種類 - IoT Edge モジュールに関連する問題
    * 問題のサブタイプ - Azure SQL Edge

   ![サポート チケットのサンプル](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1.5
### <a name="sql-engine-build-number---15020001331"></a>SQL エンジンのビルド番号 - 15.0.2000.1331
### <a name="whats-new"></a>新機能
1. Date_bucket 関数
    
    i. DateTimeOffset 型のサポート
2. ONNX モデルを使用した PREDICT

    i. nvarchar のサポート
 
## <a name="ctp-14"></a>CTP 1.4
### <a name="sql-engine-build-number---15020001247"></a>SQL エンジンのビルド番号 - 15.0.2000.1247
### <a name="whats-new"></a>新機能
1.  ONNX モデルを使用した PREDICT
 
    i.  Varchar のサポート
    
    ii. ONNX ランタイム バージョン 1.0 への移行 
2.  機能のサポート - 次の機能が有効になっています。

    i.   CDC のサポート

    ii.  圧縮される履歴テーブル

    iii. 先読みログのより高い倍率

    iv.  バッチ モード ES フィルターのプッシュダウン

    v.   先読みの最適化
 
## <a name="ctp-13"></a>CTP 1.3
### <a name="sql-engine-build-number---15020001147"></a>SQL エンジンのビルド番号 -15.0.2000.1147
### <a name="whats-new"></a>新機能
1. Azure IOT ポータルのデプロイ 

    i.   AMD64 および ARM の各イメージのデプロイのサポート

    ii.  ストリーミング ジョブの作成のサポート

    iii. Dacpac のデプロイ
2. ONNX モデルを使用した PREDICT

    i. 数値型のサポート
3. 機能のサポート - 次の機能が有効になっています。

    i.  列ストア スキャンのプッシュダウン集計

    ii. メリーゴーランド スキャン
4. メモリ占有領域とメモリ消費の削減作業

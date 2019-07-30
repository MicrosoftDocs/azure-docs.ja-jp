---
title: Azure Stream Analytics の機能の比較
description: この記事では、Azure portal、Visual Studio、Visual Studio Code でサポートされている、Azure Stream Analytics のクラウド ジョブと IoT Edge ジョブの機能を比較します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509691"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics の機能の比較

Azure Stream Analytics では、[Azure portal](stream-analytics-quick-create-portal.md)、[Visual Studio](stream-analytics-quick-create-vs.md)、[Visual Studio Code](quick-create-vs-code.md) を使用して、クラウドおよび IoT Edge でストリーミング ソリューションを作成できます。 この記事の表は、両方のジョブの種類について、各プラットフォームでサポートされている機能を示しています。

## <a name="cloud-job-features"></a>クラウド ジョブの機能


|機能  |ポータル  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|クロス プラットフォーム     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|スクリプトの作成     |はい         |はい         |はい         |
|スクリプトの IntelliSense     |構文の強調表示         |構文の強調表示</br>コード補完</br>エラー マーカー         |構文の強調表示</br>コード補完</br>エラー マーカー         |
|入力、出力、およびジョブ構成の定義     |はい         |はい         |はい         |
|Blob 出力のパーティション分割     |はい         |はい         |はい         |
|出力としての Power BI     |はい         |はい         |いいえ         |
|SQL Database 参照データ     |はい         |はい         |はい         |
|カスタム メッセージ プロパティ     |はい         |いいえ         |いいえ         |
|複数のクエリでの入力と出力の共有     |いいえ         |可能         |はい         |
|JavaScript UDF および UDA     |はい         |はい         |Windows のみ         |
|Machine Learning のコールアウト     |はい。ただし、クエリはテストできません        |はい。ただし、ローカルではテストできません         |いいえ         |
|互換性レベル     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|組み込みの ML ベースの異常検出関数     |はい         |はい         |はい         |
|組み込みの地理空間関数     |はい         |はい         |はい         |
|サンプル ファイルを使用したクエリのテスト     |はい         |はい         |はい         |
|ライブ データでのローカル テスト     |いいえ         |はい         |いいえ         |
|ジョブを一覧表示してジョブ エンティティを表示する     |はい         |はい         |はい         |
|ジョブのローカル プロジェクトへのエクスポート     |いいえ         |可能         |はい         |
|ジョブの送信、開始、停止     |はい         |はい         |はい         |
|ソース管理     |いいえ         |可能         |はい         |
|CI/CD のサポート     |部分的         |はい         |はい         |
|ジョブ メトリックとジョブ ダイアグラムの表示     |はい         |はい         |ポータルで開く         |
|ジョブの実行時エラーの表示     |はい         |はい         |いいえ         |
|診断ログ     |はい         |いいえ         |いいえ         |


## <a name="iot-edge-job-features"></a>IoT Edge ジョブの機能

|機能  |ポータル  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|ジョブの作成     |はい         |はい         |いいえ         |
|ソース管理     |いいえ         |はい         |いいえ         |
|ジョブのローカル プロジェクトへのエクスポート     |いいえ         |はい         |いいえ         |
|サンプル ファイルを使用したクエリのテスト     |はい         |はい         |いいえ         |
|複数のクエリでの入力と出力の共有     |いいえ         |はい         |いいえ         |
|C# UDF     |いいえ         |はい         |いいえ         |
|ジョブの送信、開始、停止     |はい         |はい         |いいえ         |
|ジョブを一覧表示してジョブ エンティティを表示する     |はい         |はい         |いいえ         |
|ジョブ メトリックとジョブ ダイアグラムの表示     |はい         |部分的         |いいえ         |
|ジョブの実行時エラーの表示     |はい         |部分的         |いいえ         |
|CI/CD のサポート     |いいえ         |いいえ         |いいえ         |


## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics on IoT Edge](stream-analytics-edge.md)
* [チュートリアル:Azure Stream Analytics IoT Edge ジョブの C# ユーザー定義関数を記述する (プレビュー)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio Tools を使用して Stream Analytics IoT Edge ジョブを作成する](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio を使用して Azure Stream Analytics ジョブを表示する](stream-analytics-vs-tools.md)
* [Explore Azure Stream Analytics with Visual Studio Code (Preview)](vscode-explore-jobs.md) (Visual Studio Code で Azure Stream Analytics の詳細を確認する (プレビュー))



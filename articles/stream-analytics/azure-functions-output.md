---
title: Azure Stream Analytics からの Azure Functions 出力
description: この記事では、Azure Stream Analytics の出力としての Azure Functions について説明します。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019620"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Stream Analytics からの Azure Functions 出力

Azure Functions は、インフラストラクチャを明示的にプロビジョニングまたは管理することなく、オンデマンドでコードを実行するために使用できるサーバーレス コンピューティング サービスです。 これにより、Azure またはパートナーのサービス内で発生するイベントによってトリガーされるコードを実装できます。 トリガーに応答する Azure Functions のこの機能によって、Azure Stream Analytics の自然な出力になります。 この出力アダプターを使用すると、ユーザーは Stream Analytics を Azure Functions に接続し、さまざまなイベントに応じてスクリプトまたはコードの一部を実行することができます。

Stream Analytics からの Azure Functions 出力は、Azure China 21Vianet および Azure Germany (T-Systems International) リージョンでは利用できません。 マルチテナント クラスターで実行されている Stream Analytics ジョブから仮想ネットワーク (VNet) 内の Azure Functions への接続もサポートされていません。

Azure Stream Analytics では、HTTP トリガーを使用して Azure Functions を呼び出します。 Azure Functions 出力アダプターは、次の構成可能なプロパティで使用できます。

| プロパティ名 | 説明 |
| --- | --- |
| 関数アプリ |Azure Functions アプリの名前です。 |
| 機能 |ご自分の Azure Functions アプリ内にある関数の名前です。 |
| Key |別のサブスクリプションの Azure Functions を使用するには、ご自分の関数にアクセスするためのキーを指定します。 |
| 最大バッチ サイズ |ご自分の Azure Functions に送信される各出力バッチの最大サイズを設定できるプロパティです。 入力の単位はバイトです。 既定値は 262,144 バイト (256 KB) です。 |
| 最大バッチ カウント  |Azure Functions に送信される各バッチのイベントの最大数を指定できるプロパティです。 既定値は 100 です。 |

Azure Stream Analytics は、正常に処理されたバッチに対して Functions アプリから HTTP ステータス 200 を想定しています。

Azure Stream Analytics は、Azure Functions から 413 ("http の要求したエンティティが大きすぎる") 例外を受け取ると、Azure Functions に送信するバッチのサイズを縮小します。 Azure Functions コードで、この例外を使用して、Azure Stream Analytics がサイズの大きすぎるバッチを送信しないようにします。 また、関数で使用する最大バッチ カウントおよび最大バッチ サイズの値が Stream Analytics ポータルに入力した値と矛盾しないことを確認します。

> [!NOTE]
> テスト接続中、Stream Analytics から Azure Functions に空のバッチが送信され、この 2 つの間の接続が機能するかどうかがテストされます。 テスト接続に合格するように、Functions アプリで空のバッチ要求が処理されるようにします。

また、時間枠内に開始するイベントがない場合も、出力は生成されません。 その結果、**computeResult** 関数は呼び出されません。 この動作は、組み込みのウィンドウ集計関数と一致します。

## <a name="partitioning"></a>パーティション分割

パーティション キーは、クエリの PARTITION BY 句に基づきます。 出力ライターの数は、[完全並列化されたクエリ](stream-analytics-scale-jobs.md)に対する入力のパーティション分割に従います。

## <a name="output-batch-size"></a>出力バッチ サイズ

既定のバッチ サイズは 262,144 バイト (256 KB) です。 バッチごとの既定のイベント数は 100 です。 バッチ サイズは構成可能で、Stream Analytics の出力オプションで増減させることができます。

## <a name="next-steps"></a>次のステップ

* [クイック スタート: Azure Portal を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-portal.md)
* [クイック スタート:Azure CLI を使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-cli.md)
* [クイック スタート: ARM テンプレートを使用して Azure Stream Analytics ジョブを作成する](quick-create-azure-resource-manager.md)
* [クイック スタート: Azure PowerShell を使用して Stream Analytics ジョブを作成する](stream-analytics-quick-create-powershell.md)
* [クイック スタート:Visual Studio を使用して Azure Stream Analytics ジョブを作成する](stream-analytics-quick-create-vs.md)
* [クイック スタート: Visual Studio Code で Azure Stream Analytics ジョブを作成する](quick-create-visual-studio-code.md)
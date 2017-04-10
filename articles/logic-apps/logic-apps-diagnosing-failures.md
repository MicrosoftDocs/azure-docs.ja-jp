---
title: "障害の診断 - Azure Logic Apps | Microsoft Docs"
description: "ロジック アプリの障害発生箇所を把握する一般的な方法"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: a6727ebd-39bd-4298-9e68-2ae98738576e
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 25f4520bae3089ce9f11efcf6acd176255043e70
ms.lasthandoff: 03/10/2017


---
# <a name="diagnose-logic-app-failures"></a>ロジック アプリの障害の診断
ロジック アプリの機能でエラーや障害が発生した場合、障害の発生源を確認できる方法がいくつかあります。  

## <a name="azure-portal-tools"></a>Azure ポータル ツール
Azure ポータルには、各段階で各ロジック アプリを診断するためのツールがあります。

### <a name="trigger-history"></a>トリガー履歴

各ロジック アプリには少なくとも&1; つのトリガーがあります。 アプリが起動しない場合はまず、トリガーの履歴で詳細を確認してください。 トリガー履歴にはロジック アプリのメイン ブレードからアクセスできます。

![トリガー履歴の検索][1]

ロジック アプリで行われたトリガーの試行はすべて、トリガー履歴に一覧表示されます。 各トリガー試行をクリックして詳細を確認できます (具体的には、トリガー試行が生成された入力または出力が表示されます)。 トリガーの失敗が見つかった場合は、試行されたトリガーを選択し、**[出力]** リンクを選択すると、生成されたエラー メッセージ (FTP の資格情報が無効であるなど) を確認できます。

表示される状態:

* **スキップ済み**。 データをチェックするエンドポイントをポーリングし、使用可能なデータがないという応答を受信しました。
* **成功**。 データを使用できるという応答を受信しました。 この状態は、手動トリガー、繰り返しトリガー、またはポーリング トリガーに起因している可能性があります。 多くの場合、この状態には **Fired (起動)** の状態が付随しますが、条件やコード ビューの SplitOn コマンドが十分ではない場合、付随しません。
* **失敗**。 エラーが発生しました。

#### <a name="start-a-trigger-manually"></a>トリガーを手動で開始する

メイン ブレードの **[トリガーの選択]** をクリックすると、(次回の試行を待たずに) 利用できるトリガーをすぐに確認できます。 たとえば、Dropbox トリガーでこのリンクをクリックすると、新しいファイルに関するポーリングを Dropbox に実行するワークフローがすぐに開始されます。

### <a name="run-history"></a>実行履歴

"実行" は、起動された各トリガーの結果です。 実行の情報には、メイン ブレードからアクセスできます。メイン ブレードには、ワークフローの実行中に何が発生したかについて確認するのに役立つ、多くの情報が含まれます。

![実行履歴の検索][2]

実行では、次の状態のいずれかが表示されます。

* **成功**。 アクションはすべて成功しています。 失敗したアクションは、ワークフローの後続のアクションによって処理済みです。 つまり、失敗したアクションより後に実行されるように設定されたアクションによって処理されています。
* **失敗**。 少なくとも&1; つのアクションに失敗し、その失敗がワークフローの後続のアクションによって処理されていません。
* **取り消し済み**。 ワークフローは実行されていましたが、キャンセル要求を受け取りました。
* **実行中**。 ワークフローは現在実行中です。 この状態は、ワークフローがスロットルされていることが原因で (現行の価格プランが原因で) 発生することがあります。 詳細については、[価格設定ページのアクション制限](https://azure.microsoft.com/pricing/details/app-service/plans/)を参照してください。 診断の構成 (実行履歴の下にあるグラフ) でも、発生したスロットル イベントに関する情報が提供されます。

実行履歴を調べるときに、詳細を確認できます。  

#### <a name="trigger-outputs"></a>トリガー出力

トリガー出力には、トリガーから受け取ったデータが表示されます。 すべてのプロパティが予期したとおりに返されるかどうかを、これらの出力から確認できます。

> [!NOTE]
> コンテンツについて不明点がある場合は、Azure Logic Apps における[各種コンテンツの扱い](../logic-apps/logic-apps-content-type.md)に関するページを参照してください。
> 

![トリガーの出力例][3]

#### <a name="action-inputs-and-outputs"></a>アクションの入力と出力

アクションが受け取った入力と出力の詳細にアクセスできます。 このデータは出力の大きさと形を理解するために役立ちます。エラー メッセージが生成されていれば、それも確認できます。

![アクションの入力と出力][4]

## <a name="debug-workflow-runtime"></a>ワークフロー ランタイムのデバッグ

入力、出力、実行のトリガーを監視するだけでなく、デバッグに役立つ手順をワークフローに追加すると便利な場合があります。 
ワークフローに手順として追加できる強力なツールの&1; つが [RequestBin](http://requestb.in) です。 RequestBin を使用して、HTTP 要求の大きさ、形、書式を正確に特定する目的で、HTTP 要求インスペクターを設定できます。 RequestBin を作成してロジック アプリの HTTP POST アクションに URL を貼り付けることができます。本文にはあらゆるコンテンツ (式や別の手順の出力など) を指定し、テストできます。 ロジック アプリの実行後、RequestBin を更新し、Logic Apps エンジンから生成されたとき、要求がどのように書式設定されたかを確認できます。

<!-- image references -->
[1]: ./media/logic-apps-diagnosing-failures/triggerhistory.png
[2]: ./media/logic-apps-diagnosing-failures/runhistory.png
[3]: ./media/logic-apps-diagnosing-failures/triggeroutputslink.png
[4]: ./media/logic-apps-diagnosing-failures/actionoutputs.png


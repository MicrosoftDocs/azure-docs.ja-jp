---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: scottwhi-msft
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2018
ms.author: scottwhi
ms.custom: include file
ms.openlocfilehash: 6016b13fe7d3e1f3b673bd2446d2f68b04878cd6
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54193421"
---
Bing Statistics は、Bing Search API に分析情報を提供します。 分析情報には、呼び出しのボリューム、上位のクエリ文字列、地理的な分布などが含まれています。 Bing Search の有料サブスクリプションで Bing Statistics を有効にするには、[Azure ダッシュボード](https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7)に移動し、有料サブスクリプションを選択して、[Enable Bing Statistics]\(Bing Statistics を有効にする\) をクリックします。 Bing Statistics を有効にすると、サブスクリプションの料金が少し上がります ([価格](https://aka.ms/bingstatisticspricing)に関するページをご覧ください)。

> [!NOTE]
> Bing Statistics を利用できるのは有料サブスクリプションのみです。無料試用版サブスクリプションでは利用できません。

> [!NOTE]
> Bing Statistics ダッシュボードで入手できるデータを使用して、サード パーティに配布するためのアプリケーションを作成することはできません。

Bing は、24 時間ごとに分析データを更新し、最大 13 か月分の履歴を保持します。

## <a name="accessing-your-analytics"></a>分析情報へのアクセス

分析ダッシュボードにアクセスするには、 https://bingapistatistics.com に移動します。 有料サブスクリプションの取得に使用したものと同じ Microsoft アカウント (MSA) を使用してサインインしていることを確認します。

## <a name="filtering-the-data"></a>データのフィルター処理

既定では、チャートやグラフには、ユーザーがアクセスできるすべてのメトリック データが反映されます。 関心のあるリソース、市場、エンドポイント、レポート期間を選択して、チャートやグラフに表示されるデータをフィルター処理できます。 適用したフィルターを反映するように、チャートやグラフが変更されます。 以下では、変更できるフィルターについて説明します。

- **リソース ID**: Azure サブスクリプションを識別する一意のリソース ID です。 複数の Bing Search API 階層をサブスクライブしている場合、一覧には複数の ID が含まれます。 既定では、すべてのリソースが選択されています。  
  
- **市場**: 結果の取得元の市場です。 たとえば、en-us (英語、米国) などです。 既定では、すべての市場が選択されています。 呼び出しで市場が指定されておらず、Bing がユーザーの市場を特定できない場合、Bing は市場として en-WW を使用することに注意してください。  
  
- **エンドポイント**: Bing Search API のエンドポイントです。 一覧には、有料サブスクリプションを持っているすべてのエンドポイントが含まれます。 既定では、すべてのエンドポイントが選択されています。  

- **時間枠**: レポートの期間です。 以下を指定できます。
  - すべて &mdash; 最大 13 か月分のデータが含まれます  
  - 過去 24 時間 &mdash; 過去 24 時間の分析情報が含まれます  
  - 過去 1 週間 &mdash; 7 日前からの分析情報が含まれます  
  - 過去 1 か月 &mdash; 30 日前からの分析情報が含まれます  
  - カスタム日付範囲 &mdash; 使用可能な場合、指定した日付範囲からの分析情報が含まれます  

  > [!NOTE]  
  > メトリックがダッシュボードに表示されるまで、最大で 24 時間かかる場合があります。 ダッシュボードには、データが最後に更新された日時が表示されます。  

  > [!NOTE]  
  > メトリックは、Bing Statistics アドインを有効にした時点から利用できるようになります。

## <a name="charts-and-graphs"></a>チャートとグラフ

ダッシュボードには、選択したエンドポイントで使用可能なメトリックのチャートとグラフが表示されます。 すべてのエンドポイントですべてのメトリックを使用できるわけではありません。 各エンドポイントのチャートとグラフは静的です (チャートやグラフを選択して表示することはできません)。 ダッシュボードには、データが存在するチャートとグラフだけが表示されます。

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

次のメトリックを使用できます。 各メトリックは、エンドポイントの制限を示します。

- **[Call Volume]\(呼び出しボリューム\)**: レポート期間中に行われた呼び出しの数を示します。 レポート期間が 1 日の場合、グラフには 1 時間ごとの呼び出し数が示されます。 それ以外の場合、グラフにはレポート期間の 1 日ごとの呼び出し数が示されます。  
  
  > [!NOTE]
  > 呼び出しボリュームは課金レポートと異なる場合があります。通常、課金レポートには成功した呼び出しのみが含まれます。

- **[上位のクエリ ]**: レポート期間中の上位クエリと、各クエリの発生回数を示します。 表示されるクエリの数を構成することができます。 たとえば、上位 25 クエリ、50 クエリ、75 クエリなどを表示できます。 上位クエリは、次のエンドポイントでは使用できません。  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > メール アドレス、電話番号、SSN など、一部のクエリ用語は機密情報を除去するために抑制される場合があります。

- **[Geographic Distribution]\(地理的分布\)**: 結果の取得元の市場です。 たとえば、en-us (英語、米国) などです。 Bing は `mkt` クエリ パラメーターを使用して、市場を決定します (指定されている場合)。 指定されていない場合、Bing は呼び出し元の IP アドレスなどの信号を使用して市場を決定します。

- **[Response Code Distribution]\(応答コード分布\)**: レポート期間中のすべての呼び出しの HTTP 状態コードです。

- **[Call Origin Distribution]\(呼び出し元分布\)**: ユーザーによって使用されたブラウザーの種類です。 たとえば、Microsoft Edge、Chrome、Safari、FireFox などです。 ボット、Postman、コンソール アプリからの curl の使用など、ブラウザーの外部からの呼び出しは、[Libraries]\(ライブラリ\) にまとめられます。 呼び出し元は、要求の User-Agent ヘッダーの値を使用して判断されます。 要求に User-Agent ヘッダーが含まれていない場合、Bing は他の信号から呼び出し元の特定を試みます。  

- **[Safe Search Distribution]\(セーフ サーチの分布\)**: セーフ サーチの値の分布です。 たとえば、オフ、中程度、厳密などです。 `safeSearch` クエリ パラメーターに値が含まれます (指定されている場合)。 指定されていない場合の既定値は中程度です。  

- **[Answers Requested Distribution]\(要求された回答の分布\)**: Web Search API は、`responseFilter` クエリ パラメーターで要求されたユーザーに応答します。  

- **[Answers Returned Distribution]\(返された回答の分布\)**: Web Search API が応答で返した回答です。

- **[Response Server Distribution]\(応答サーバーの分布\)**: API 要求を処理したアプリケーション サーバーです。 可能性のある値は、Bing.com (デスクトップまたはラップトップ デバイスから送信されたトラフィックの場合) および Bing.com-mobile (モバイル デバイスから送信されたトラフィックの場合) です。 サーバーは、要求の User-Agent ヘッダーの値を使用して判断されます。 要求に User-Agent ヘッダーが含まれていない場合、Bing は他の信号からサーバーの特定を試みます。

各エンドポイントで利用可能な分析情報を次に示します。

![エンドポイント別サポート分布マトリックス](./media/cognitive-services-bing-statistics/bing-statistics-matrix.PNG)

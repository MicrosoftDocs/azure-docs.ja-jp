---
title: Bing Web Search API に分析を追加する
titleSuffix: Azure Cognitive Services
description: Bing Statistics は、Bing Image Search API に分析を提供します。 分析には、呼び出しのボリューム、上位のクエリ文字列、地理的な分布などが含まれます。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96349556"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Bing Search API に分析を追加する

> [!WARNING]
> Bing Search API は、Cognitive Services から Bing Search Services に移行されます。 **2020 年 10 月 30 日** 以降、Bing Search の新しいインスタンスは、[こちら](/bing/search-apis/bing-web-search/create-bing-search-service-resource)に記載されているプロセスに従ってプロビジョニングする必要があります。
> Cognitive Services を使用してプロビジョニングされた Bing Search API は、次の 3 年間、または Enterprise Agreement の終わり (どちらか先に発生した方) までサポートされます。
> 移行手順については、[Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) に関する記事を参照してください。

Bing Statistics は、Bing Search API に分析情報を提供します。 こうした分析には、呼び出しのボリューム、上位のクエリ文字列、地理的な分布などが含まれます。 [Azure portal](https://ms.portal.azure.com) で Bing Statistics を有効にするには、Azure リソースに移動し、 **[Enable Bing Statistics]\(Bing Statisticsを有効にする\)** をクリックします。

> [!IMPORTANT]
> * Bing Statistics は、無料の `F0` 価格レベルのリソースでは利用できません。
> * Bing Statistics ダッシュボードで入手できるデータを使用して、サード パーティに配布するためのアプリケーションを作成することはできません。
> * Bing Statistics を有効にすると、サブスクリプションの料金が少し上がります。 詳細については、[価格](https://aka.ms/bingstatisticspricing)のページを参照してください。


次の図は、各 Bing Search API エンドポイントで利用可能な分析を示しています。

![エンドポイント別サポート分布マトリックス](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>分析にアクセスする

Bing では、分析データが 24 時間ごとに更新され、[分析ダッシュボード](https://bingapistatistics.com)からアクセスできる最大 13 か月分の履歴が保持されます。 Bing Statistics のサインアップに使用したものと同じ Microsoft アカウント (MSA) を使用してサインインしていることを確認します。

> [!NOTE]  
> * メトリックがダッシュボードに表示されるまで、最大で 24 時間かかる場合があります。 ダッシュボードには、データが最後に更新された日時が表示されます。  
> * メトリックは、Bing Statistics アドインを有効にした時点から利用できるようになります。

## <a name="filter-the-data"></a>データをフィルター処理する

既定では、チャートやグラフには、ユーザーがアクセスできるすべてのメトリックとデータが表示されます。 関心のあるリソース、市場、エンドポイント、レポート期間を選択して、チャートやグラフに表示されるデータをフィルター処理できます。 次のフィルターを変更できます。

- **[リソース ID]** : Azure サブスクリプションを識別する一意のリソース ID です。 複数の Bing Search API 階層をサブスクライブしている場合、一覧には複数の ID が含まれます。 既定では、すべてのリソースが選択されています。  
  
- **市場**: 結果の取得元の市場です。 たとえば、en-us (英語、米国) などです。 既定では、すべての市場が選択されています。 呼び出しに市場が指定されておらず、Bing がユーザーの市場を特定できない場合、`en-WW` 市場が Bing で使用される市場になります。  
  
- **エンドポイント**: Bing Search API のエンドポイントです。 一覧には、有料サブスクリプションを持っているすべてのエンドポイントが含まれます。 既定では、すべてのエンドポイントが選択されています。  

- **時間枠**: レポートの期間です。 以下を指定できます。
  - **[すべて]** : 最大 13 か月分のデータが含まれます  
  - **過去 24 時間**:過去 24 時間の分析情報が含まれます  
  - **過去 1 週間**:7 日前からの分析情報が含まれます  
  - **過去 1 か月**:30 日前からの分析情報が含まれます  
  - **カスタム日付範囲**:使用可能な場合、指定した日付範囲からの分析情報が含まれます  

## <a name="charts-and-graphs"></a>チャートとグラフ

ダッシュボードには、選択したエンドポイントで使用可能なメトリックのチャートとグラフが表示されます。 すべてのエンドポイントですべてのメトリックを使用できるわけではありません。 各エンドポイントのチャートとグラフは静的です (チャートやグラフを選択して表示することはできません)。 ダッシュボードには、データが存在するチャートとグラフだけが表示されます。

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

可能性のあるメトリックとエンドポイントの制限を次に示します。

- **[Call Volume]\(呼び出しボリューム\)** : レポート期間中に行われた呼び出しの数を示します。 レポート期間が 1 日の場合、グラフには 1 時間ごとの呼び出し数が示されます。 それ以外の場合、グラフにはレポート期間の 1 日ごとの呼び出し数が示されます。  
  
  > [!NOTE]
  > 呼び出しボリュームは課金レポートと異なる場合があります。通常、課金レポートには成功した呼び出しのみが含まれます。

- **[上位のクエリ ]** : レポート期間中の上位クエリと、各クエリの発生回数を示します。 表示されるクエリの数を構成することができます。 たとえば、上位 25 クエリ、50 クエリ、75 クエリなどを表示できます。 上位クエリは、次のエンドポイントでは使用できません。  

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

- **[Geographic Distribution]\(地理的分布\)** : 検索結果が生成される市場。 たとえば、`en-us` (英語、米国) などです。 Bing は `mkt` クエリ パラメーターを使用して、市場を決定します (指定されている場合)。 指定されていない場合、Bing は呼び出し元の IP アドレスなどの信号を使用して市場を決定します。

- **[Response Code Distribution]\(応答コード分布\)** : レポート期間中のすべての呼び出しの HTTP 状態コードです。

- **[Call Origin Distribution]\(呼び出し元分布\)** : ユーザーによって使用されたブラウザーの種類です。 たとえば、Microsoft Edge、Chrome、Safari、FireFox などです。 ブラウザーの外部からの呼び出し (ボット、Postman、コンソール アプリからの curl の使用など) は、[ライブラリ] にまとめられます。 呼び出し元は、要求の User-Agent ヘッダーの値を使用して判断されます。 要求に User-Agent ヘッダーが含まれていない場合、Bing は他の信号から呼び出し元の特定を試みます。  

- **[Safe Search Distribution]\(セーフ サーチの分布\)** : セーフ サーチの値の分布です。 たとえば、オフ、中程度、厳密などです。 `safeSearch` クエリ パラメーターに値が含まれます (指定されている場合)。 指定されていない場合の既定値は中程度です。  

- **[Answers Requested Distribution]\(要求された回答の分布\)** : `responseFilter` クエリ パラメーターで要求した Web Search API の回答です。  

- **[Answers Returned Distribution]\(返された回答の分布\)** : Web Search API が応答で返した回答です。

- **[Response Server Distribution]\(応答サーバーの分布\)** : API 要求を処理したアプリケーション サーバーです。 可能性のある値は、Bing.com (デスクトップまたはラップトップ デバイスから送信されたトラフィックの場合) および Bing.com-mobile (モバイル デバイスから送信されたトラフィックの場合) です。 サーバーは、要求の User-Agent ヘッダーの値を使用して判断されます。 要求に User-Agent ヘッダーが含まれていない場合、Bing は他の信号からサーバーの特定を試みます。

## <a name="next-steps"></a>次のステップ

* [Bing Search API とは](bing-api-comparison.md)
* [Bing Search API の利用と表示の要件](use-display-requirements.md)
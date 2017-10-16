---
title: "Azure Time Series Insights 環境への参照データ セットの追加 | Microsoft Docs"
description: "このチュートリアルでは、Time Series Insights 環境に参照データ セットを追加します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/29/2017
ms.author: venkatja
ms.openlocfilehash: 574f3c5b3a889733f47d729c795ec39009f2ef4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-ibiza-portal"></a>Ibiza ポータルを使用して Time Series Insights 環境の参照データ セットを作成する

参照データ セットは、イベント ソースからのイベントによって増幅される項目の集まりです。 イベント ソースから受信したイベントは、Time Series Insights のイングレス エンジンによって、指定した参照データ セット内の項目と結合されます。 こうして増幅されたイベントをクエリで利用することができます。 この結合操作は、参照データ セットに定義されているキーに基づいて行われます。

## <a name="steps-to-add-a-reference-data-set-to-your-environment"></a>参照データ セットを環境に追加する手順

1. [Ibiza ポータル](https://portal.azure.com)にサインインします。
2. Ibiza ポータルの左側のメニューで [すべてのリソース] をクリックします。
3. Time Series Insights 環境を選択します。

    ![Time Series Insights の参照データ セットの作成](media/add-reference-data-set/getstarted-create-reference-data-set-1.png)

4. [参照データ セット] を選択し、[+ 追加] をクリックします。

    ![Time Series Insights の参照データ セットの作成 - 詳細](media/add-reference-data-set/getstarted-create-reference-data-set-2.png)

5. 参照データ セットの名前を指定します。
6. キーの名前とその型を指定します。 この名前と型は、指定されたイベント ソースのイベントから適切なプロパティを選ぶ目的で使用されます。 たとえばキー名に "DeviceId" を、型に "String" を指定した場合、Time Series Insights のイングレス エンジンは、受信したイベントの中から "String" 型の "DeviceId" という名前のプロパティを探します。 複数のキーを指定して、イベントと結合させることができます。 プロパティ名を突き合わせる際は、大文字と小文字が区別されます。

     ![Time Series Insights の参照データ セットの作成 - 詳細](media/add-reference-data-set/getstarted-create-reference-data-set-3.png)

7. [作成] をクリックします。

## <a name="next-steps"></a>次のステップ

* プログラムで[参照データを管理](time-series-insights-manage-reference-data-csharp.md)する。
* 詳細な API リファレンスについては、[参照データ API](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api) に関するドキュメントを参照してください。
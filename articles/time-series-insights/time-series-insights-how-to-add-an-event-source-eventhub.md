---
title: "Azure Time Series Insights 環境にイベント ハブ イベント ソースを追加する方法 | Microsoft Docs"
description: "このチュートリアルでは、イベント ハブに接続されたイベント ソースを Time Series Insights 環境に追加する方法を説明します。"
keywords: 
services: time-series-insights
documentationcenter: 
author: sandshadow
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/19/2017
ms.author: edett
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: f6a993b3858cfb94dd9795f5e55f15fa6ec7dcb2
ms.contentlocale: ja-jp
ms.lasthandoff: 08/31/2017

---
# <a name="how-to-add-an-event-hub-event-source"></a>イベント ハブ イベント ソースを追加する方法

このチュートリアルでは、Azure Portal を使用して、イベント ハブから読み取りを行うイベント ソースを Time Series Insights 環境に追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

イベント ハブを作成済みであり、これにイベントを書き込んでいること。 Event Hubs の詳細については、<https://azure.microsoft.com/services/event-hubs/> を参照してください。

> [コンシューマー グループ] 各 Time Series Insights イベント ソースには、他のコンシューマーと共有されない専用のコンシューマー グループが設定されている必要があります。 複数のリーダーが同じコンシューマー グループのイベントを消費すると、すべてのリーダーにエラーが発生する可能性があります。 Event Hub ごとに 20 個のコンシューマー グループという制限もある点に注意してください。 詳細については、「[Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)」をご覧ください。

## <a name="choose-an-import-option"></a>インポート オプションを選択する

イベント ソースの設定は手動で入力できます。また、使用可能なイベント ハブからいずれかのイベント ハブを選択することもできます。
**[インポート オプション]** セレクターで、次のいずれかのオプションを選択します。

* イベント ハブ設定を手動で行う
* 使用可能なサブスクリプションのイベント ハブを使用する

### <a name="select-an-available-event-hub"></a>使用可能なイベント ハブを選択する

次の表は、[新しいイベント ソース] タブの各オプションと、使用可能なイベント ハブをイベント ソースとして選択したときの説明を示しています。

| プロパティ名 | Description |
| --- | --- |
| イベント ソース名 | イベント ソースの名前。 この名前は、Time Series Insights 環境内で一意である必要があります。
| から | イベント ハブ イベント ソースを作成するには、**[イベント ハブ]** を選択します。
| サブスクリプション ID | このイベント ハブが作成されたサブスクリプションを選択します。
| Service Bus 名前空間 | イベント ハブが含まれている Service Bus 名前空間を選択します。
| イベント ハブ名 | イベント ハブの名前を選択します。
| イベント ハブ ポリシー名 | イベント ハブの [構成] タブで作成できる共有アクセス ポリシーを選択します。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
| イベント ハブ コンシューマー グループ | イベント ハブからデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。

### <a name="provide-event-hub-settings-manually"></a>イベント ハブ設定を手動で行う

次の表は、[新しいイベント ソース] タブの各プロパティと、設定を手動で入力したときの説明を示しています。

| プロパティ名 | Description |
| --- | --- |
| イベント ソース名 | イベント ソースの名前。 この名前は、Time Series Insights 環境内で一意である必要があります。
| から | イベント ハブ イベント ソースを作成するには、**[イベント ハブ]** を選択します。
| サブスクリプション ID | このイベント ハブが作成されたサブスクリプション。
| リソース グループ | このイベント ハブが作成されたサブスクリプション。
| Service Bus 名前空間 | Service Bus 名前空間は、一連のメッセージング エンティティのコンテナーです。 新しいイベント ハブを作成するときは、Service Bus 名前空間も作成します。
| イベント ハブ名 | イベント ハブの名前。 イベント ハブを作成したときに、固有の名前を設定しています。
| イベント ハブ ポリシー名 | [イベント ハブの構成] タブで作成できる共有アクセス ポリシー。各共有アクセス ポリシーには、名前、設定したアクセス許可、アクセス キーが含まれています。 イベント ソースの共有アクセス ポリシーには、**読み取り**アクセス許可の設定が "*必須*" です。
| イベント ハブ ポリシー キー | Service Bus 名前空間へのアクセスを認証するために使用する共有アクセス キー。 ここにプライマリ キーまたはセカンダリ キーを入力します。
| イベント ハブ コンシューマー グループ | イベント ハブからデータを読み取るためのコンシューマー グループ。 お使いのイベント ソース専用のコンシューマー グループを使用することを強くお勧めします。

## <a name="next-steps"></a>次のステップ

1. 環境にデータ アクセス ポリシーを追加するために[データ アクセス ポリシーを定義する](time-series-insights-data-access.md)
1. [Time Series Insights ポータル](https://insights.timeseries.azure.com)で環境にアクセスする

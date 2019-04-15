---
title: Azure Time Series プレビューのプロビジョニングと管理 | Microsoft Docs
description: Azure Time Series Insights Preview をプロビジョニングし、管理する方法について
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 85d5bb822bc9b89c68c70633a22a1bed74118f49
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58758178"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Azure Time Series Insights プレビューのプロビジョニングと管理

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights プレビュー環境を作成し、管理する方法について説明します。

## <a name="overview"></a>概要

Azure Time Series Insights プレビュー環境は、従量課金制 (PAYG) の環境です。

Azure Time Series Insights Preview 環境をプロビジョニングする際に、次の 2 つの Azure リソースを作成します。

* Azure Time Series Insights Preview 環境  
* Azure Storage 汎用 v1 アカウント
  
[環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。

>[!IMPORTANT]
> プレビューでは、Azure Storage の汎用 v1 (GPv1) アカウントを使用していることを確認してください。  GPv2 以降のサポートは近い将来に追加される予定です。  

オプションで、Azure Time Series Insights プレビューの各環境を 1 つのイベント ソースに関連付けることができます。 詳細については、[イベント ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)に関するページと [IoT ハブ ソースの追加方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページを参照してください。 この手順では、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定します。 この指定により、環境から適切なイベントへのアクセスが確実になります。

プロビジョニングが完了したら、ビジネスの要件に合わせ、アクセス ポリシーやその他の環境属性を変更できます。

## <a name="create-the-environment"></a>環境の作成

次の手順は、Azure Time Series Insights Preview 環境の作成方法です。

1. **[SKU]** メニューで **[PAYG]** ボタンを選択します。 環境名を入力し、使用するサブスクリプション グループとリソース グループを指定します。 次に、環境をホストする場所を選択します (サポートされている場所を選択します)。

   ![Azure Time Series Insights インスタンスを作成します。][1]

1. タイム シリーズ ID を入力します。

    >[!NOTE]
    > * タイム シリーズ ID では大文字と小文字が区別され、変更できません。 (設定後は変更できません。)
    > * タイム シリーズ ID は最大 3 つのキーから構成されます。
    > * タイム シリーズ ID の選択の詳細については、[タイム シリーズ ID の選択](./time-series-insights-update-how-to-id.md)に関するページをご覧ください。

1. ストレージ アカウント名を選択し、レプリケーションを指定することで Azure ストレージ アカウントを作成します。 この作業で Azure Storage 汎用 v1 アカウントが作成されます。 先に選択した Azure Time Series Insights プレビュー環境と同じリージョンで作成されます。

    ![インスタンス用の Azure ストレージ アカウントを作成する][5]

1. 任意で、イベント ソースを追加できます。

   * Time Series Insights では、オプションで [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) と [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) がサポートされています。 環境作成時はイベント ソースを 1 つしか追加できませんが、後でイベント ソースを追加できます。 すべてのイベントが Azure Time Series Insights プレビュー インスタンスに確実に表示されるように、一意のコンシューマー グループを作成することをお勧めします。 イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。

   * 適切なタイムスタンプ プロパティも選択してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、バッチ イベントや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

     ![[イベント ソース] タブ][2]

1. 希望の設定で環境がプロビジョニングされていることを確認します。

    ![[確認と作成] タブ][3]

## <a name="manage-the-environment"></a>環境の管理

Azure portal を使用して Azure Time Series Insights プレビュー環境を管理できます。 S1 または S2 環境と比較した、Azure portal を使用した PAYG Azure Time Series Insights プレビュー環境の管理には、次のような大きな違いがあります。

* Azure portal の **[概要]** ブレードは次の点を除いて Azure Time Series Insights でも同じです。
  * PAYG 環境には、容量という概念がないため削除されています。
  * タイム シリーズ ID プロパティが追加されています。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights Preview エクスプローラー](./time-series-insights-update-explorer.md)に移動できます。
  * お使いの Azure ストレージ アカウント名が表示されます。

* Azure portal の **[構成]** ブレードは Azure Time Series Insights プレビューでは削除されています。PAYG 環境は構成できないためです。

* Azure portal の **[参照データ]** ブレードは Azure Time Series Insights プレビューで削除されています。参照データは PAYG 環境の構成要素ではないためです。

![Azure portal の Time Series Insights プレビュー環境][4]

## <a name="next-steps"></a>次の手順

[環境の計画](./time-series-insights-update-plan.md)に関するページを参照してください。

[イベント ハブ ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-eventhub.md)に関するページを参照してください。

[IoT ハブ ソースを追加する方法](./time-series-insights-how-to-add-an-event-source-iothub.md)に関するページを参照してください。

<!-- Images -->
[1]: media/v2-update-manage/manage_one.PNG
[2]: media/v2-update-manage/manage_two.PNG
[3]: media/v2-update-manage/manage_three.PNG
[4]: media/v2-update-manage/manage_four.PNG
[5]: media/v2-update-manage/manage_five.PNG

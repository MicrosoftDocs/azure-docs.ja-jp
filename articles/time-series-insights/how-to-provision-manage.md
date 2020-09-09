---
title: Gen 2 環境のプロビジョニングと管理 - Azure Time Series | Microsoft Docs
description: Azure Time Series Insights Gen 2 環境をプロビジョニングし、管理する方法について説明します。
author: shipra1mishra
ms.author: shmishr
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: e71c426fee62187bb680c7b8bd1af92d87e85274
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288800"
---
# <a name="provision-and-manage-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen 2 のプロビジョニングと管理

このドキュメントでは、[Azure portal](https://portal.azure.com/) を使用して Azure Time Series Insights Gen 2 環境を作成し、管理する方法について説明します。

## <a name="overview"></a>概要

Azure Time Series Insights Gen 2 環境をプロビジョニングする際に、次の Azure リソースを作成します。

* 従量課金制の価格モデルに従う Azure Time Series Insights Gen2 環境
* Azure ストレージ アカウント
* 高速で無制限のクエリを行うためのオプションのウォームストア

> [!TIP]
> * [環境を計画する方法](./time-series-insights-update-plan.md)に関するページをご覧ください。
> * [イベント ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-eventhub.md)方法、または [IoT ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-iothub.md)方法に関する記事を参照してください。

学習内容:

1.  Azure Time Series Insights Gen 2 の各環境を 1 つのイベント ソースに関連付けます。 また、タイムスタンプ ID プロパティと一意のコンシューマー グループを指定し、環境が適切なイベントにアクセスできるようにします。

1. プロビジョニングが完了したら、ビジネス ニーズに合わせ、アクセス ポリシーやその他の環境属性を変更できます。

   > [!NOTE]
   > 最初の手順は、環境をプロビジョニングする時点では省略可能です。 この手順を省略した場合は、環境へのデータのフローを開始したり、クエリを使用してアクセスしたりできるように、後で環境にイベント ソースをアタッチする必要があります。

## <a name="create-the-environment"></a>環境の作成

Azure Time Series Insights Gen 2 環境を作成するには:
1. [Azure portal](https://portal.azure.com/)で、 *[モノのインターネット]* の下に Azure Time Series Insights のリソースを作成します。

1. **[階層]** として **[Gen2(L1)]** を選択します。 環境名を入力し、使用するサブスクリプション グループとリソース グループを選択します。 次に、環境をホストするサポートされている場所を選択します。

   [![Azure Time Series Insights インスタンスを作成する。](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. タイム シリーズ ID を入力します。

    > [!NOTE]
    > * タイム シリーズ ID では*大文字と小文字が区別され*、*変更できません*。 (設定後は変更できません。)
    > * タイム シリーズ ID は最大 *3 つ*のキーから構成されます。 この ID は、データベースの主キーと考えてください。これは、環境にデータを送信する各デバイス センサーを一意に表します。 1 つのプロパティ、または最大 3 つのプロパティの組み合わせにすることができます。
    > * 詳細は、[タイム シリーズ ID を選択する方法](time-series-insights-update-how-to-id.md)に関する記事を参照してください

1. ストレージ アカウント名とアカウントの種類を選択し、[レプリケーション](https://docs.microsoft.com/azure/storage/common/redundancy-migration?tabs=portal)の選択肢を指定して、Azure Storage アカウントを作成します。 これにより、Azure Storage アカウントが自動的に作成されます。 既定では、[汎用 v2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) アカウントが作成されます。 アカウントは、先に選択した Azure Time Series Insights Gen2 環境と同じリージョンで作成されます。 または、新しい Azure Time Series Gen2 環境を作成するときに、[Resource Manager テンプレート](./time-series-insights-manage-resources-using-azure-resource-manager-template.md)を使用して BYOS (Bring Your Own Storage) を行うこともできます。 

1. **(省略可能)** 環境内の最新のデータに対して高速かつ無制限のクエリが必要な場合は、環境に対してウォームストアを有効にします。 また、Azure Time Series Insights Gen2 環境を作成した後、左側のナビゲーション ウィンドウの **[ストレージ構成]** オプションを使用して、ウォーム ストアを作成または削除することもできます。

1. **(省略可能)** ここでイベント ソースを追加できます。 インスタンスがプロビジョニングされるまで待機することもできます。

   * Azure Time Series Insights では、イベント ソースのオプションとして [Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) と [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) がサポートされています。 環境を作成するときはイベント ソースを 1 つしか追加できませんが、後でイベント ソースを追加できます。 
   
     イベント ソースを追加するときは、既存のコンシューマー グループを選択するか、新しいコンシューマー グループを作成できます。 イベント ソースには、環境内にデータを読み取るために、一意のコンシューマー グループが必要であることに注意してください。

   * 適切なタイムスタンプ プロパティを選択してください。 既定では、Azure Time Series Insights では、各イベント ソースに対して、メッセージが待ち行列に入った時刻が使用されます。

     > [!TIP]
     > メッセージが待ち行列に入った時刻は、バッチ イベント シナリオや履歴データをアップロードする場合、最適な構成設定ではないことがあります。 そのような場合、タイムスタンプ プロパティを使用するかどうかの判断を確認してください。

     [![[イベント ソース] 構成タブ](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. 環境がプロビジョニングされ、必要な方法で構成されていることを確認します。

    [![[確認と作成]](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox) タブ

## <a name="manage-the-environment"></a>環境の管理

Azure portal を使用して Azure Time Series Insights Gen2 環境を管理できます。 Azure portal を使用して環境を管理する場合、Gen2 環境と Gen1 S1 環境または Gen1 S2 環境との間には、注意すべき重要な違いがいくつかあります。

* Azure portal Gen2 の **[概要]** ブレードには、次の変更点があります。

  * 容量は、Gen2 環境には適用されないため、削除されます。
  * **タイム シリーズ ID** プロパティが追加されます。 これによってデータのパーティション方法が決定されます。
  * 参照データ セットが削除されています。
  * 表示された URL から [Azure Time Series Insights エクスプローラー](./time-series-insights-update-explorer.md)に移動できます。
  * お使いの Azure Storage アカウント名が表示されます。

* スケール ユニットは Azure Time Series Insights Gen2 環境には適用されないため、Azure portal の **[構成]** ブレードは削除されています。 ただし、 **[ストレージの構成]** を使用して、新しく導入されたウォームストアを構成できます。

* 参照データの概念は[タイム シリーズ モデル (TSM)](/azure/time-series-insights/concepts-model-overview) に置き換えられたため、Azure portal の **[参照データ]** ブレードは Azure Time Series Insights Gen2 では削除されています。

[![Azure portal の Azure Time Series Insights Gen2 環境](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>次のステップ

- Azure Time Series Insights の一般に利用可能な環境と Gen2 環境の詳細については、[環境の計画](./time-series-insights-update-plan.md)に関する記事を参照してください。

- [イベント ハブ ソースを追加する](./time-series-insights-how-to-add-an-event-source-eventhub.md)方法を学習します。

- [IoT ハブ ソース](./time-series-insights-how-to-add-an-event-source-iothub.md)を構成します。

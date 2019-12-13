---
title: 土壌水分ヒートマップを生成する
description: Azure FarmBeats で土壌水分ヒートマップを生成する方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e11f560c2b101d86d41feb3f6c93cffe7c1a748d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851453"
---
# <a name="generate-soil-moisture-heatmap"></a>土壌水分ヒートマップを生成する

土壌水分は、土壌の粒子間に保持される水分です。 土壌水分ヒートマップを使用すると、ファーム (農場) 内のさまざまな深さの水分データを高解像度で明瞭に把握することができます。 正確で実用的な土壌水分ヒートマップを生成するためには、同じプロバイダーのセンサーを一様に配置する必要があります。 プロバイダーが異なると、土壌水分の測定方法が異なるほか、キャリブレーションにも差があります。 ヒートマップは、特定の深さを対象としたものであり、その深さで配置されたセンサーを使用して生成されます。

この記事では、Azure FarmBeats Accelerator を使用してファームの土壌水分ヒートマップを生成するプロセスについて説明します。 この記事では、次のことについて説明します。

- [ファームを作成する](#create-a-farm)
- [ファームにセンサーを割り当てる](#get-soil-moisture-sensor-data-from-partner)
- [土壌水分ヒートマップを生成する](#generate-soil-moisture-heatmap)

## <a name="before-you-begin"></a>開始する前に

以下のことを確認してください。  

- Azure サブスクリプション。
- 実行中の Azure FarmBeats のインスタンス。
- 利用可能な土壌水分センサーがファームに 3 つ以上あること。

## <a name="create-a-farm"></a>ファームを作成する

ファームは、土壌水分ヒートマップの作成対象となる地理的な関心領域です。 ファームは、[Farms API](https://aka.ms/FarmBeatsDatahubSwagger) または [FarmsBeats Accelerator UI](manage-farms.md#create-farms) を使用して作成できます。

## <a name="deploy-sensors"></a>センサーを配置する

土壌水分センサーは、ファームに対して物理的に配置する必要があります。 土壌水分センサーは、Microsoft によって承認された任意のパートナー ([Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/) および [Teralytic](https://teralytic.com/)) から購入できます。 ファームへの物理的なセットアップは、センサーのプロバイダーと連携して行ってください。

## <a name="get-soil-moisture-sensor-data-from-partner"></a>パートナーから土壌水分センサー データを入手する

Azure FarmBeats へのデータのストリーミングは、パートナー データ ダッシュボードに対してセンサーがデータのストリーミングを開始すると有効になります。 これは、パートナー アプリケーションから行うことができます。

たとえば Davis 製のセンサーを購入した場合、Azure FarmBeats へのデータのストリーミングを有効にするには、ウェザー リンク アカウントにログインして必要な資格情報を入力します。 必要な資格情報を入手するには、「[センサー データの取得](get-sensor-data-from-sensor-partner.md#get-sensor-data-from-sensor-partners)」の手順に従ってください。

自分の資格情報を入力し、パートナー アプリケーションで **[Submit]\(送信\)** を選択すると、Azure FarmBeats へのデータ フローを有効にできます。

### <a name="assign-soil-moisture-sensors-to-the-farm"></a>土壌水分センサーをファームに割り当てる

Azure FarmBeats にセンサー アカウントをリンクさせたら、対象となるファームに土壌水分センサーを割り当てる必要があります。

1.  ホーム ページのメニューから **[Farms]\(ファーム\)** を選択して、 **[Farms]\(ファーム\)** リスト ページを表示します。
2.  **[MyFarm]\(マイファーム\)**  >  **[Add Devices]\(デバイスの追加\)** の順に選択します。
3.  **[Add Devices]\(デバイスの追加\)** ウィンドウが表示されます。 自分のファームの土壌水分センサーにリンクされているデバイスを選択します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/add-devices-1.png)

4. **[Add Devices]\(デバイスの追加\)** を選択します。     

## <a name="generate-soil-moisture-heatmap"></a>土壌水分ヒートマップを生成する

ファームの土壌水分ヒートマップを生成するジョブ (または長時間実行処理) の作成手順を次に示します。

1.  ホーム ページの左側のナビゲーション メニューから **[Farms]\(ファーム\)** に移動してファーム ページを表示します。
2.  **[MyFarm]\(マイファーム\)** を選択します。
3.  **[Farm Details]\(ファームの詳細\)** ページで **[Generate Precision Map]\(プレシジョン マップの生成\)** を選択します。
4.  ドロップダウン メニューから **[Soil Moisture]\(土壌水分\)** を選択します。
5.  **[Soil Moisture]\(土壌水分\)** ウィンドウで **[This Week]\(今週\)** を選択します。
6.  **[Select Soil Moisture** **Sensor Measure**]\(土壌水分センサーのメジャーの選択\) で、マップに使用するメジャーを入力します。
    センサーのメジャーを検索するには、 **[Sensors]\(センサー\)** でいずれかの土壌水分センサーを選択します。 **[Sensor Properties]\(センサーのプロパティ\)** の **[Measure Name]\(メジャー名\)** の値を使用します。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)


7.  **[Generate Maps]\(マップの生成\)** を選択します。
    ジョブの詳細を含んだ確認メッセージが表示されます。 詳細については、[Jobs]\(ジョブ\) の [Job Status]\(ジョブの状態\) を参照してください。

    >[!NOTE]
    > ジョブの完了には、3 時間から 4 時間ほどかかります。

### <a name="download-the-soil-moisture-heatmap"></a>土壌水分ヒートマップをダウンロードする

次の手順に従います。

1. **[Jobs]\(ジョブ\)** ページで、直前の手順で作成したジョブの **[Job Status]\(ジョブの状態\)** を確認します。
2. ジョブの状態が *[Succeeded]\(成功\)* として表示されたら、メニューの **[Maps]\(マップ\)** をクリックします。
3. マップを作成日 (<soil-moisture_MyFarm_YYYY-MM-DD> 形式) で検索します。
4. **[Name]\(名前\)** 列でマップを選択すると、そのマップのプレビューがポップアップ ウィンドウに表示されます。
5. **[Download]** を選択します。 マップがダウンロードされてコンピューターのローカル フォルダーに格納されます。

    ![プロジェクト (Farm Beats)](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)

## <a name="next-steps"></a>次の手順

土壌水分ヒートマップが正しく生成されました。[センサー配置マップを生成する方法](generate-maps.md#sensor-placement-map)と[テレメトリ履歴データを取り込む方法](ingest-historical-telemetry-data.md)を参照してください。 

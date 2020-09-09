---
title: ファームの管理
description: ファームを管理する方法について説明します
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 09144c4c35ab911b60931849807123608f2c3cdd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847487"
---
# <a name="manage-farms"></a>ファームの管理

Azure FarmBeats ではファームを管理することができます。 この記事では、ファームを作成し、ファームの管理に役立つデバイス、センサー、ドローンを設置する方法について説明します。

## <a name="create-farms"></a>ファームを作成する

次の手順に従います。

1. Farm Accelerator にログインすると、 **[Farms]\(ファーム\)** ページが表示されます。
    **[Farms]\(ファーム\)** ページにはファームの一覧が表示されます (サブスクリプションに既に作成されている場合)。

    サンプル画像を次に示します。

    ![プロジェクト (Farm Beats)](./media/create-farms-in-azure-farmbeats/create-farm-main-page-1.png)


2. **[Create Farm]\(ファームの作成\)** を選択し、 **[Name]\(名前\)** 、 **[Crops]\(作物\)** 、 **[Address]\(住所\)** を指定します。
3. **[Define Farm Boundary]\(ファームの境界の定義\)** (必須フィールド) で、 **[Mark on Map]\(マップにマーク\)** または **[Paste GeoJSON code]\(GeoJSON コードの貼り付け\)** を選択します。

ファームの境界を定義するには、次の 2 つの方法があります。

1. **[Mark on Map]\(マップにマーク\)** : マップ管理ツールを使用して、ファームの境界線を描画してマークします。 境界をマークするには、![[Project Farm Beats]\(プロジェクト (Farm Beats)\)](./media/create-farms-in-azure-farmbeats/pencil-icon-1.png) で、正確な境界をマークします。

    ![プロジェクト (Farm Beats)](./media/create-farms-in-azure-farmbeats/create-farm-mark-on-map-1.png)

2. **[Paste GeoJSON code]\(GeoJSON コードの貼り付け\)** : GeoJSON は、JavaScript Object Notation (JSON) を使用して地理的なデータ構造をエンコードするための形式です。 このオプションによってテキスト ボックスが表示され、ファームの境界をマークするための GeoJSON 文字列を入力できます。 また、GeoJSON.io から GeoJSON コードを作成することもできます。
ヒントを使用すると、情報の入力に役立ちます。

    ![プロジェクト (Farm Beats)](./media/create-farms-in-azure-farmbeats/create-new-farm-1.png)

3.  **[送信]** を選択してファームを作成します。 新しいファームが作成され、 **[Farms]\(ファーム\)** ページに表示されます。

## <a name="view-farm"></a>ファームを表示する

ファームの一覧ページには、作成済みのファームの一覧が表示されます。 ファームを選択すると、以下の一覧が表示されます。

 - **[デバイス数]** : ファーム内に配置されているデバイスの数と状態が表示されます。
 - **[マップ]** : デバイスが配置されているファームのマップ。
 - **[Telemetry]\(テレメトリ\)** : ファームに配置されているセンサーからのテレメトリが表示されます。
 - **[Latest Precision Maps]\(最新のプレシジョン マップ\)** : 最新の衛星指数マップ (EVI、NDWI)、土壌水分ヒートマップ、およびセンサー配置マップが表示されます。

## <a name="edit-farm"></a>ファームを編集する

**[Farms]\(ファーム\)** ページには、作成済みのファームの一覧が表示されます。

1.  ファームを選択してそのファームを表示および編集します。
2.  ファーム情報を編集するには、 **[Edit Farm]\(ファームの編集\)** を選択します。 **[Farm Details]\(ファームの詳細\)** ウィンドウでは、 **[Name]\(名前\)** 、 **[Crops]\(作物\)** 、 **[Address]\(住所\)** の各フィールドを編集し、 **[Farm Boundary]\(ファームの境界\)** フィールドを定義できます。

    ![プロジェクト (Farm Beats)](./media/create-farms-in-azure-farmbeats/edit-farm-1.png)

3. **[送信]** を選択して、編集された詳細を保存します。

## <a name="delete-farm"></a>ファームを削除する

**[Farms]\(ファーム\)** ページには、作成したファームの一覧が表示されます。 ファームを削除するには、次の手順に従います。

1.  詳細を削除するファームを一覧から選択します。
2.  **[Delete Farm]\(ファームの削除\)** を選択して、ファームを削除します。

    ![プロジェクト (Farm Beats)](./media/create-farms-in-azure-farmbeats/delete-farm-1.png)

    > [!NOTE]
    > ファームを削除しても、そのファームに関連付けられているデバイスとマップは削除されません。 デバイスとマップに関連付けられているファーム情報は関連性がなくなります。 引き続き、FarmBeats サービスからデバイス、テレメトリ、およびマップを確認することができます。


## <a name="next-steps"></a>次のステップ

ファームを作成したので、次は、ファームに流れ込んでいる[センサー データを取得する](get-sensor-data-from-sensor-partner.md)方法について説明します。

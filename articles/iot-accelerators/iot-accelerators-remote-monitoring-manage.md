---
title: リモート監視ソリューションでデバイスを構成するチュートリアル - Azure | Microsoft Docs
description: このチュートリアルでは、リモート監視ソリューション アクセラレータに接続されているデバイスを構成する方法を示します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: d23b7c8fa10127094fec67535333ae169f0f38f0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58183392"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>チュートリアル:監視ソリューションに接続されているデバイスを構成する

このチュートリアルでは、リモート監視ソリューション アクセラレータを使用して、接続されている IoT デバイスを構成および管理します。 ソリューション アクセラレータに新しいデバイスを追加し、デバイスを構成します。

Contoso は自社の施設の 1 つを拡大するために、新しい機械を注文しました。 新しい機械が納品されるのを待つ間に、あなたはソリューションの動作をテストするためのシミュレーションを実施したいと考えています。 シミュレーションを実行するために、新しいシミュレートされたエンジン デバイスをリモート監視ソリューション アクセラレータに追加し、このシミュレートされたデバイスが構成の更新に正しく応答することをテストします。 このチュートリアルではシミュレートされたデバイスを使用していますが、デバイス開発者は、[リモート監視ソリューション アクセラレータに接続された実デバイス](iot-accelerators-connecting-devices.md)にダイレクト メソッドを実装できます。

このチュートリアルでは、次のことを行いました。

>[!div class="checklist"]
> * シミュレートされたデバイスをプロビジョニングする。
> * シミュレートされたデバイスをテストする。
> * デバイスを再構成する。
> * デバイスを整理する。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>シミュレートされたデバイスの追加

ソリューションの **[Device Explorer]** ページに移動して、**[+ 新規デバイス]** をクリックします。

[![シミュレートされたデバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

**[新規デバイス]** パネルで、**[Simulated]\(シミュレート済み\)** を選択し、プロビジョニングするデバイスの数を **1** のままにします。**[Faulty Engine]\(問題があるエンジン\)** デバイス モデルを選択し、**[適用]** を選択して、シミュレートされたデバイスを作成します。

[![シミュレートされたエンジン デバイスのプロビジョニング](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>シミュレートされたデバイスのテスト

シミュレートされたエンジン デバイスがテレメトリとレポートのプロパティ値を送信していることをテストするには、**[Device Explorer]** ページのデバイスの一覧でデバイスを選択します。 エンジンに関するライブ情報が **[デバイスの詳細]** パネルに表示されます。

[![新しくシミュレートされたエンジン デバイスの表示](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

**[デバイスの詳細]** で、新しいデバイスがテレメトリを送信していることを確認します。 デバイスからの振動テレメトリ ストリームを表示するには、**[振動]** をクリックします。

[![表示するテレメトリ ストリームの選択](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

**[デバイスの詳細]** パネルには、デバイスに関するその他の情報 (タグ値、デバイスがサポートするメソッド、デバイスから報告されるプロパティなど) が表示されます。

詳細な診断を表示するには、**[デバイスの詳細]** パネルで下へスクロールし、**[診断]** セクションを表示します。

## <a name="reconfigure-a-device"></a>デバイスの再構成

エンジンの構成プロパティを更新できることをテストするには、**[Device Explorer]** ページのデバイスの一覧でデバイスを選択します。 次に、**[ジョブ]** をクリックし、**[プロパティ]** を選択します。 [ジョブ] パネルには、選択したデバイスの更新可能なプロパティ値が表示されます。

[![デバイスの再構成](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

エンジンの場所を更新するために、ジョブ名を **UpdateEngineLocation**、経度を **-122.15**、場所を **Factory 2**、緯度を **47.62** に設定し、**[適用]** をクリックします。

[![デバイスのプロパティ値の更新](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

ジョブの状態を追跡するには、**[View job status]\(ジョブの状態の表示\)** をクリックします。

[![デバイスのプロパティ値の更新](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

ジョブが完了したら、**[ダッシュボード]** ページに戻ります。 エンジン デバイスがマップ上の新しい場所に表示されます。

[![エンジンの場所を表示する](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>デバイスを整理する

オペレーターがより簡単にデバイスを整理および管理できるように、チーム名でデバイスにタグを付けます。 Contoso には、フィールド サービス アクティビティのための 2 つの異なるチームがあります。

* Smart Vehicle (スマート車両) チームは、トラックとプロトタイプ作成デバイスを管理します。
* Smart Building (スマート ビルディング) チームは、冷却装置、エレベーター、エンジンを管理します。

すべてのデバイスを表示するには、**[Device Explorer]** ページに移動して **[すべてのデバイス]** フィルターを選択します。

[![すべてのデバイスを表示する](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>タグを追加する

すべての**トラック** デバイスと**プロトタイプ作成**デバイスを選択します。 次に、**[ジョブ]** をクリックします。

**[ジョブ]** パネルで **[タグ]** を選択し、ジョブ名を **AddConnectedVehicleTag** に設定します。**FieldService** という名前のテキスト タグを追加し、値を **ConnectedVehicle** に設定します。 次に、**[適用]** をクリックします。

[![プロトタイプ作成デバイスとトラック デバイスにタグを追加する](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

[デバイス] ページで、すべての**冷却装置**、**エレベーター**、および**エンジン** デバイスを選択します。 次に、**[ジョブ]** をクリックします。

**[ジョブ]** パネルで **[タグ]** を選択し、ジョブ名を **AddSmartBuildingTag** に設定します。**FieldService** という名前のテキスト タグを追加し、値を **SmartBuilding** に設定します。 次に、**[適用]** をクリックします。

[![冷却装置、エレベーター、およびエンジン デバイスにタグを追加する](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>フィルターの作成

タグ値を使用してフィルターを作成できます。 **[Device Explorer]** ページで、**[Manage device groups]\(デバイス グループの管理\)** をクリックします。

[![デバイス グループの管理](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

条件にタグ名 **FieldService** と値 **SmartBuilding** を使用してテキスト フィルターを作成します。 フィルターに **[Smart Building]\(スマート ビルディング\)** という名前を付けて保存します。

[![[Smart Building]\(スマート ビルディング\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

条件にタグ名 **FieldService** と値 **ConnectedVehicle** を使用してテキスト フィルターを作成します。 フィルターに **[Connected Vehicle]\(接続された車両\)** という名前を付けて保存します。

[![[Connected Vehicle]\(接続された車両\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

これで、Contoso のオペレーターは、運用チームに基づいてデバイスを照会することができます。

[![[Connected Vehicle]\(接続された車両\) フィルターを作成する](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、リモート監視ソリューション アクセラレータに接続されているデバイスを構成および管理する方法を示しました。 ソリューション アクセラレータを使用して予期しないアラートの根本原因分析を実行する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [アラートでの根本原因分析の実施](iot-accelerators-remote-monitoring-root-cause-analysis.md)

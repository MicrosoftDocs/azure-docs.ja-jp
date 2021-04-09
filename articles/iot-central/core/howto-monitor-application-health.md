---
title: Azure IoT Central アプリケーションの正常性状態を監視する | Microsoft Docs
description: オペレーターまたは管理者として、IoT Central アプリケーションに接続されているデバイスの全体的な正常性状態を監視します。
author: dominicbetts
ms.author: dobett
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: d0e59f73dd9b62b528c3d86d315b613312df7773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577045"
---
# <a name="monitor-the-overall-health-of-an-iot-central-application"></a>IoT Central アプリケーションの全体的な正常性状態を監視する

> [!NOTE]
> メトリックは、バージョン 3 の IoT Central アプリケーションでのみ使用できます。 アプリケーションのバージョンを確認する方法については、「[アプリケーションに関する情報](./howto-get-app-info.md)」を参照してください。

*この記事は、オペレーターおよび管理者に適用されます。*

この記事では、IoT Central で提供される一連のメトリックを使用して、IoT Central アプリケーションに接続されているデバイスの正常性、および実行中のデータ エクスポートの正常性を評価する方法について説明します。

メトリックは、IoT Central アプリケーションでは既定で有効になっており、[Azure portal](https://portal.azure.com/) からアクセスします。 [Azure Monitor データ プラットフォームでは、これらのメトリックが公開](../../azure-monitor/essentials/data-platform-metrics.md)されており、これらを対話方式で操作する複数の方法が用意されています。 たとえば、Azure portal、REST API、または PowerShell や Azure CLI のクエリでグラフを使用できます。

### <a name="trial-applications"></a>試用版アプリケーション

無料試用版プランを使用するアプリケーションには、関連付けられた Azure サブスクリプションがないため、Azure Monitor メトリックはサポートされません。 [アプリケーションを標準の料金プランに変換](./howto-view-bill.md#move-from-free-to-standard-pricing-plan)して、これらのメトリックにアクセスすることができます。

## <a name="view-metrics-in-the-azure-portal"></a>Azure portal でメトリックを表示する

次の手順では、[IoT Central アプリケーション](./quick-deploy-iot-central.md)に、[接続されているデバイス](./tutorial-connect-device.md)または実行中の[データ エクスポート](howto-export-data.md)があることを前提としています。

ポータルで IoT Central メトリックを表示するには:

1. ポータルで IoT Central アプリケーション リソースに移動します。 既定では、IoT Central リソースは **IOTC** という名前のリソース グループに配置されます。
1. アプリケーションのメトリックからグラフを作成するには、 **[監視]** セクションで **[メトリック]** を選択します。

![Azure メトリック](media/howto-monitor-application-health/metrics.png)

### <a name="azure-portal-permissions"></a>Azure portal アクセス許可

Azure portal のメトリックへのアクセスは [Azure ロールベースのアクセス制御](../../role-based-access-control/overview.md)によって管理されます。 Azure portal を使用して、IoT Central アプリケーション/リソース グループ/サブスクリプションにユーザーを追加し、アクセス権を付与します。 ユーザーは、既に IoT Central アプリケーションに追加されている場合でも、ポータルに追加する必要があります。 [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)を使用して、きめ細かなアクセス制御を行うことができます。

## <a name="iot-central-metrics"></a>IoT Central メトリック

IoT Central で現在使用できるメトリックの一覧については、「[Azure Monitor のサポートされるメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)」を参照してください。

### <a name="metrics-and-invoices"></a>メトリックと請求書

メトリックは、Azure IoT Central の請求書に表示される数値とは異なる場合があります。 こうした状況は、次のようなさまざまな原因によって発生します。

- IoT Central の[標準料金プラン](https://azure.microsoft.com/pricing/details/iot-central/)には、2 つのデバイスとさまざまなメッセージ クォータが無料で含まれています。 無料の項目は課金から除外されますが、メトリックには引き続きカウントされます。

- IoT Central は、アプリケーション内のデバイス テンプレートごとに 1 つのテスト デバイス ID を自動生成します。 このデバイス ID は、デバイス テンプレートの **[テスト デバイスの管理]** ページに表示されます。 ソリューション ビルダーは、これらのテスト デバイス ID を使用するコードを生成することによって、公開する前に[デバイス テンプレートを検証](./overview-iot-central.md#create-device-templates)することができます。 これらのデバイスは課金から除外されますが、メトリックには引き続きカウントされます。

- メトリックはデバイスからクラウドへの通信のサブセットを示す場合がありますが、デバイスとクラウド間のすべての通信は、[課金のメッセージとしてカウントされます](https://azure.microsoft.com/pricing/details/iot-central/)。

## <a name="next-steps"></a>次のステップ

ここでは、アプリケーション テンプレートの使用方法を学習しました。推奨される次の手順は、[Azure portal で IoT Central を管理する](howto-manage-iot-central-from-portal.md)方法を学習することです。
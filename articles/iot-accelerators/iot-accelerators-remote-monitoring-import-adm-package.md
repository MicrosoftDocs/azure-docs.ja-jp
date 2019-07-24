---
title: リモート監視ソリューション インポート パッケージ - Azure | Microsoft Docs
description: この記事では、自動デバイス管理パッケージをリモート監視ソリューション アクセラレータにインポートする方法について説明します
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59009854"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>自動デバイス管理パッケージをリモート監視ソリューション アクセラレータにインポートする

自動デバイス管理構成では、デバイスのグループに展開する構成の変更が定義されています。 この記事では、組織内の開発者が既に自動デバイス管理構成を作成していることを前提としています。 開発者が構成を作成する方法について詳しくは、IoT Hub の操作方法に関する次の記事のいずれかをご覧ください。

- [Azure portal を使って多数の IoT デバイスを構成および監視する](../iot-hub/iot-hub-auto-device-config.md)
- [Azure CLI を使って多数の IoT デバイスを構成および監視する](../iot-hub/iot-hub-auto-device-config-cli.md)

開発者は、開発環境で自動デバイス管理構成を作成してテストします。 準備ができたら、構成をリモート監視ソリューション アクセラレータにインポートできます。

## <a name="export-a-configuration"></a>構成をエクスポートする

開発環境から自動デバイス管理構成をエクスポートするには、Azure portal を使用します。

1. Azure portal で、IoT デバイスの開発とテストに使用している IoT ハブに移動します。 **[IoT デバイスの構成]** をクリックします。

    [IoT デバイスの構成![](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. 使用する構成をクリックします。 **[デバイス構成の詳細]** ページが表示されます。

    [![IoT デバイス構成の詳細](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. **[Download configuration file]\(構成ファイルのダウンロード\)** をクリックする。

    [![構成ファイルのダウンロード](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. JSON ファイルを **configuration.json** というローカル ファイルとして保存します。

自動デバイス管理構成を含むファイルができました。 次のセクションでは、この構成をリモート監視ソリューションにパッケージとしてインポートします。

## <a name="import-a-package"></a>パッケージのインポート

自動デバイス管理構成をパッケージとしてソリューションにインポートするには、次の手順のようにします。

1. リモート監視 Web UI で **[パッケージ]** ページに移動します。![[パッケージ] ページ](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. **[+ 新しいパッケージ]** をクリックし、パッケージの種類として **[構成]** を選択し、**[参照]** をクリックして、前のセクションで保存した **configuration.json** ファイルを選択します。

    ![構成を選択する](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. **[アップロード]** をクリックして、リモート監視ソリューションにパッケージを追加します。

    ![アップロードされたパッケージ](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

自動デバイス管理構成がパッケージとしてアップロードされました。 **[デプロイ]** ページで、このパッケージを接続されたデバイスに展開できます。

## <a name="next-steps"></a>次の手順

構成パッケージを作成してリモート監視ソリューションからにインポートする方法を習得したので、次に、[リモート監視に接続されているデバイスを一括管理する](iot-accelerators-remote-monitoring-bulk-configuration-update.md)方法を学習します。

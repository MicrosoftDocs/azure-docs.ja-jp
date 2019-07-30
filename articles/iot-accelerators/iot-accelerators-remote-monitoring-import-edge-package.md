---
title: リモート監視ソリューション インポート Edge パッケージ - Azure | Microsoft Docs
description: この記事では、IoT Edge パッケージをリモート監視ソリューション アクセラレータにインポートする方法について説明します。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443013"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>リモート監視ソリューション アクセラレータに IoT Edge パッケージをインポートする

配置マニフェストで IoT Edge デバイスに配置するモジュールを定義します。 この記事では、組織内の開発者が既に配置マニフェストを作成していることを前提としています。 開発者がマニフェストを作成する方法については、次の IoT Edge の攻略記事を参照してください。

- [Azure portal から Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-portal.md)
- [Azure CLI を使用して Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-cli.md)
- [Visual Studio Code から Azure IoT Edge モジュールをデプロイする](../iot-edge/how-to-deploy-modules-vscode.md)

開発者は、開発環境で配置マニフェストを作成してテストします。 準備ができたら、マニフェストをリモート監視ソリューション アクセラレータにインポートできます。

## <a name="export-a-manifest"></a>マニフェストをエクスポートする

Azure portal を使用して、開発環境から配置マニフェストをエクスポートします。

1. Azure portal で、IoT Edge デバイスの開発とテストに使用している IoT ハブに移動します。 **[IoT Edge]** 、 **[IoT Edge デプロイ]** の順にクリックします。![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. 使用する配置構成がある配置をクリックします。 **[デプロイの詳細]** ページが表示されます。![IoT Edge のデプロイの詳細](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. **[IoT Edge マニフェストをダウンロードする]** をクリックします。![デプロイのマニフェストをダウンロードする](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. JSON ファイルを **deploymentmanifest.json** というローカル ファイルとして保存します。

これで、配置マニフェストを含むファイルが作成されました。 次のセクションでは、このマニフェストをリモート監視ソリューションにパッケージとしてインポートします。

## <a name="import-a-package"></a>パッケージのインポート

以下の手順に従って、Edge 配置マニフェストをパッケージとしてソリューションにインポートします。

1. リモート監視 Web UI で **[パッケージ]** ページに移動します。![[パッケージ] ページ](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. **[+ 新しいパッケージ]** をクリックし、パッケージの種類として **[Edge Manifest]\(Edge マニフェスト\)** を選択し、 **[参照]** をクリックして前のセクションで保存した **deploymentmanifest.json** ファイルを選択します。![マニフェストの選択](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. **[アップロード]** をクリックして、リモート監視ソリューションにパッケージを追加します。![アップロードされたパッケージ](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

これで IoT Edge 配置マニフェストがパッケージとしてアップロードされました。 **[デプロイ]** ページで、このパッケージを接続された IoT Edge デバイスに配置できます。

## <a name="next-steps"></a>次の手順

ここでは、リモート監視ソリューションから IoT Edge デバイスにモジュールを配置する方法について説明しました。次の手順では [IoT Edge](../iot-edge/about-iot-edge.md) の詳細について説明します。

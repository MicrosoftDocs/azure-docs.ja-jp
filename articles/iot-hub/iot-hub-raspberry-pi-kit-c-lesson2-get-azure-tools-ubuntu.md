---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 2: Azure ツール (Ubuntu) | Microsoft Docs"
description: "Python と Azure コマンド ライン インターフェイス (Azure CLI) を Ubuntu にインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT クラウド サービス, Azure CLI"
ms.assetid: a03512f2-fabe-40c5-8505-b93eef8e5bec
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 4c31bb14f2b32d71f14a89576922b9701b226e66
ms.lasthandoff: 01/24/2017


---
# <a name="get-azure-tools-ubuntu-1604"></a>Azure ツールの入手 (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 以降](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>学習内容
Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* Azure CLI をインストールする方法。
* Azure CLI の IoT サブグループを追加する方法。

## <a name="what-you-need"></a>必要なもの
* インターネットに接続できる Ubuntu コンピューター。
* 有効な Azure サブスクリプション アカウントがない場合は、[無料試用版のアカウント](http://azure.microsoft.com/pricing/free-trial/)を数分で作成できます。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール
Azure CLI では、Azure 用のマルチプラットフォーム コマンド ライン機能が用意されているため、リソースのプロビジョニングと管理をコマンド ラインで直接行えます。

最新の Azure CLI をインストールするには、次の手順を実行します。

1. ターミナル ウィンドウで次のコマンドを実行します。 Azure CLI のインストールには&5; 分ほどかかる場合があります。

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. 次のコマンドを実行してインストールを確認します。

   ```bash
   az iot -h
   ```

インストールが成功した場合、次の出力が表示されます。

![処理が正常に完了したことを示す出力](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>概要
Azure CLI をインストールしました。 次のタスクでは、Azure CLI を使用して Azure IoT ハブとデバイス ID を作成します。

## <a name="next-steps"></a>次のステップ
[IoT ハブの作成と Raspberry Pi 3 の登録](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)



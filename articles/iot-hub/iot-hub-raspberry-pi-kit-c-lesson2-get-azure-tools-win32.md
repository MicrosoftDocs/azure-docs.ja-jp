---
title: "Azure IoT への Raspberry Pi (C) の接続 - レッスン 2: Azure ツール (Windows) | Microsoft Docs"
description: "Python と Azure コマンド ライン インターフェイス (Azure CLI) を、Windows 7 以降のバージョンにインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "IoT クラウド サービス, Azure CLI"
ms.assetid: a3c083b5-0d76-46af-bc77-2ad7d8aadc1e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 1ae210489b8d369a5fca33e1824b9dc224ad33d2
ms.lasthandoff: 01/24/2017


---
# <a name="get-azure-tools-windows-7-and-later"></a>Azure ツールの入手 (Windows 7 以降)
> [!div class="op_single_selector"]
> * [Windows 7 以降](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>学習内容
Python と Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。 問題が発生した場合は、[トラブルシューティングのページ](iot-hub-raspberry-pi-kit-c-troubleshooting.md)で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* Python のインストール方法。
* Azure CLI をインストールする方法。

## <a name="what-you-need"></a>必要なもの
* インターネットに接続できる Windows コンピューター。
* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](http://azure.microsoft.com/pricing/free-trial/)を数分で作成できます。

## <a name="install-python"></a>Python のインストール
Windows コンピューターに [Python をインストール](https://www.python.org/downloads/)します。 Python 2.7、3.4、または 3.5 をインストールできます。 このチュートリアルでは Python 2.7 を使用しています。 既に Python をインストールしている場合は、次のセクションに移動して Azure CLI をインストールします。

python.exe と pip.exe がインストールされているフォルダーのパスを、`PATH` システム環境変数に追加する必要もあります。 既定では、python.exe は `C:\Python27` に、pip.exe は `C:\Python27\Scripts` にインストールされます。

## <a name="install-the-azure-cli"></a>Azure CLI のインストール
Azure 用のマルチプラットフォーム コマンド ライン機能を提供する Azure CLI をインストールすると、 リソースのプロビジョニングと管理をコマンド ラインで直接行えます。

Azure CLI をインストールするには、次の手順を実行します。

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. 次のコマンドを実行してインストールを確認します。

   ```bash
   az iot -h
   ```

インストールが成功した場合、次の出力が表示されます。

![処理が正常に完了したことを示す出力](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>概要
Azure CLI をインストールしました。 次のタスクでは、Azure CLI を使用して Azure IoT ハブとデバイス ID を作成します。

## <a name="next-steps"></a>次のステップ
[IoT ハブの作成と Raspberry Pi 3 の登録](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)



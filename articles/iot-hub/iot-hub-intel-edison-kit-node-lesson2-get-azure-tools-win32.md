---
title: "Azure IoT への Intel Edison (Node) の接続 - レッスン 2: Azure ツール (Windows) | Microsoft Docs"
description: "Python と Azure コマンド ライン インターフェイス (Azure CLI) を、Windows 7 以降のバージョンにインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure CLI, IoT クラウド サービス, Arduino クラウド"
ms.assetid: 60631b54-6d2e-4e8a-88bf-7c2f8e7e1f29
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: 181d7113bda6e9d39bc24c1892940803fc03fd6b
ms.lasthandoff: 01/25/2017


---
# <a name="get-azure-tools-windows-7-and-later"></a>Azure ツールの入手 (Windows 7 以降)
> [!div class="op_single_selector"]
> * [Windows 7 以降][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>学習内容
Python と Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

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

   ```cmd
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. 次のコマンドを実行してインストールを確認します。

   ```cmd
   az iot -h
   ```

インストールが成功した場合、次の出力が表示されます。

![処理が正常に完了したことを示す出力](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_win.png)

## <a name="summary"></a>概要
Azure CLI をインストールしました。 次のタスクでは、Azure CLI を使用して Azure IoT ハブとデバイス ID を作成します。

## <a name="next-steps"></a>次のステップ
[IoT ハブを作成して Intel Edison を登録する][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-mac.md


---
title: "Azure IoT への Intel Edison (C) の接続 - レッスン 2: Azure ツール (macOS) | Microsoft Docs"
description: "Python と Azure コマンド ライン インターフェイス (Azure CLI) を macOS にインストールします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure CLI, IoT クラウド サービス, Arduino クラウド"
ms.assetid: d561680f-69cc-427a-820d-24f710ba05a8
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: c79f1595c0e1359044c8c3ec3b6e8bc87f4aa4fa
ms.lasthandoff: 01/25/2017


---
# <a name="get-azure-tools-macos-1010"></a>Azure ツールの入手 (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 以降][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>学習内容
Azure コマンド ライン インターフェイス (Azure CLI) をインストールします。 問題が発生した場合は、[トラブルシューティングのページ][troubleshooting]で解決策を探してください。

## <a name="what-you-will-learn"></a>学習内容
この記事では、次のことについて説明します。
* Azure CLI のインストール方法。
* Azure CLI の IoT サブグループを追加する方法。

## <a name="what-you-need"></a>必要なもの
* インターネットに接続できる Mac。
* 有効な Azure サブスクリプション Azure アカウントがない場合は、[無料試用版の Azure アカウント](http://azure.microsoft.com/pricing/free-trial/)を数分で作成できます。

## <a name="install-python"></a>Python のインストール
macOS には Python 2.7 が同梱されていますが、Homebrew で Python をインストールすることをお勧めします。 [macOS への Python のインストール](http://docs.python-guide.org/en/latest/starting/install/osx/)に関するページを参照してください。

次のコマンドを実行して Python と pip をインストールします。

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>Azure CLI のインストール
Azure 用のマルチプラットフォーム コマンド ライン機能を提供する Azure CLI をインストールすると、 リソースのプロビジョニングと管理をコマンド ラインで直接行えます。 

最新の Azure CLI をインストールするには、次の手順を実行します。

1. ターミナル ウィンドウで次のコマンドを実行します。 Azure CLI のインストールには&5; 分ほどかかる場合があります。

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. 次のコマンドを実行してインストールを確認します。

   ```bash
   az iot -h
   ```

インストールが成功した場合、次の出力が表示されます。

![処理が正常に完了したことを示す出力](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_osx.png)

## <a name="summary"></a>概要
Azure CLI をインストールしました。 次のタスクでは、Azure CLI を使用して Azure IoT ハブとデバイス ID を作成します。

## <a name="next-steps"></a>次のステップ
[IoT ハブを作成して Intel Edison を登録する][create-your-iot-hub-and-register-intel-edison]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md


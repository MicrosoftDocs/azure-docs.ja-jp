---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520856"
---
以下の手順では、次の構成を使用しました。

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |依存関係| strongSwan |


次のコマンドを使用して、必要な strongSwan 構成をインストールします。

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

次のコマンドを使用して、Azure コマンド ライン インターフェイスをインストールします。

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Azure CLI のインストール方法の詳細](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)

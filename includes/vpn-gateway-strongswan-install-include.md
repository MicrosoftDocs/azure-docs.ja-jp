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
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244883"
---
以下の手順では、次の構成を使用しました。

- コンピューター:Ubuntu Server 18.04
- 依存関係: strongSwan


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

[Azure CLI のインストール方法の詳細](/cli/azure/install-azure-cli-apt)
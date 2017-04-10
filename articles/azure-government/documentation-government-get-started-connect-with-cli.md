---
title: "Azure CLI を使用した Azure Government への接続 | Microsoft Docs"
description: "Azure CLI を使用して Azure Government に接続し、サブスクリプションを管理する方法"
services: azure-government
cloud: gov
documentationcenter: 
author: zakramer
manager: liki
ms.assetid: c7cbe993-375e-4aed-9aa5-2f4b2111f71c
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: zakramer
translationtype: Human Translation
ms.sourcegitcommit: 6b29e98aa3458a76e186234c2e9800294902bea9
ms.openlocfilehash: 6e070870a93d2636f076b339dd401358735a9bd0
ms.lasthandoff: 02/21/2017


---


# <a name="connect-to-azure-government-with-azure-cli"></a>Azure CLI を使用した Azure Government への接続

Azure CLI を使用するには、Azure パブリックではなく Azure Government に接続する必要があります。 Azure CLI を使用すると、スクリプトで大規模なサブスクリプションを管理したり、現在 Azure ポータルにはない機能を利用したりできます。 Azure パブリックで Azure CLI を使用した経験をお持ちであれば、操作の大部分は同じです。  Azure Government で異なるのは次の点です。

[Azure CLI をインストール](https://docs.microsoft.com/en-us/azure/xplat-cli-install)する方法はいくつかあります。 既にインストールされているノードがある場合は、npm パッケージをインストールするのが最も簡単です。

CLI を npm パッケージからインストールするには、[最新の Node.js と npm](https://nodejs.org/en/download/package-manager/) をダウンロードし、インストールしていることを確認してください。 次に、**npm install** を実行して、azure-cli パッケージをインストールします。

```bash
npm install -g azure-cli
```

Linux ディストリビューションの場合、**npm** コマンドを正常に実行するには、次のように **sudo** の使用が必要になる場合があります。

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Node.js と npm を Linux ディストリビューションまたは OS にインストールまたは更新する必要がある場合は、最新の Node.js LTS バージョン (4.x) をインストールすることをお勧めします。 以前のバージョンを使用すると、インストール エラーが発生する場合があります。


Azure CLI をインストールしたら、Azure Government にログインする必要があります。

```
azure login --username your-user-name@your-gov-tenant.onmicrosoft.com  --environment AzureUSGovernment
```

ログインした後は、通常どおりに Azure CLI コマンドを実行できます。

```
azure webapp list my-resource-group
```

---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884034"
---
この記事では、PowerShell コマンドレットを使用します。 コマンドレットを実行するには、Azure Cloud Shell を使用できます。 Azure Cloud Shell は、無料の対話型シェルで、一般的な Azure ツールがプリインストールされ、お客様のアカウントで使用するよう構成されています。 **[コピー]** をクリックしてコードをコピーし、Cloud Shell に貼り付けて Enter キーを押すだけで、コードを実行することができます。 Cloud Shell は、次のようにいくつかの方法で起動することができます。

|  |   |
|-----------------------------------------------|---|
| コード ブロックの右上隅にある **[使ってみる]** をクリックします。 | ![この記事の Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| ブラウザーで Cloud Shell を開きます。 | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Azure Portal の右上のメニューの **[Cloud Shell]** ボタンをクリックします。 | [![ポータルの Cloud Shell](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**ローカルでの PowerShell の実行**

Azure PowerShell コマンドレットは、コンピューターでローカルにインストールおよび実行することもできます。 PowerShell コマンドレットは、頻繁に更新されます。 最新バージョンを実行していないと、手順内で指定された値は失敗する場合があります。 ローカルで実行している PowerShell のバージョンを確認するには、`Get-Module -ListAvailable Az` コマンドレットを使用します。 インストールまたは更新するには、「[Install the Azure PowerShell module (Azure PowerShell モジュールのインストール)](/powershell/azure/install-az-ps)」を参照してください。
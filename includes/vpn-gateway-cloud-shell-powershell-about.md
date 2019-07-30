---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133188"
---
この記事では、PowerShell コマンドレットを使用します。 コマンドレットを実行するには、Azure でホストされ、ブラウザーを介して使用される対話型のシェル環境である Azure Cloud Shell を使用できます。 Azure Cloud Shell には、Azure PowerShell コマンドレットが事前にインストールされています。

この記事に含まれるコードを Azure Cloud Shell で実行するには、Cloud Shell セッションを開き、コード ブロック上の **[コピー]** ボタンを使ってコードをコピーし、__Ctrl + Shift + V__ キー (Windows と Linux) または __command + Shift + V__ キー (macOS) を押して Cloud Shell セッションに貼り付けます。 テキストを貼り付けても自動的には実行されません。コードを実行するには、**Enter** キーを押します。

Azure Cloud Shell は次の方法で起動できます。

|  |   |
|-----------------------------------------------|---|
| コード ブロックの右上隅にある **[使ってみる]** を選択します。 テキストが自動的に Cloud Shell にコピーされるわけでは__ありません__。 | ![Azure Cloud Shell の [使ってみる] の例](./media/cloud-shell-try-it/cli-try-it.png) |
| ブラウザーで [shell.azure.com](https://shell.azure.com) を開きます。 | [![Azure Cloud Shell の起動ボタン](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| [Azure Portal](https://portal.azure.com) の右上隅にあるメニューの **[Cloud Shell]** ボタンを選択します。 | ![Azure Portal の [Cloud Shell] ボタン](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**ローカルでの PowerShell の実行**

Azure PowerShell コマンドレットは、ローカルのコンピューターにインストールして実行することもできます。 PowerShell コマンドレットは、頻繁に更新されます。 最新バージョンを実行していないと、手順で指定されている値は失敗する場合があります。 コンピューターにインストールされている Azure PowerShell のバージョンを確認するには、`Get-Module -ListAvailable Az` コマンドレットを使用します。 インストールまたは更新するには、「[Install the Azure PowerShell module (Azure PowerShell モジュールのインストール)](/powershell/azure/install-az-ps)」を参照してください。

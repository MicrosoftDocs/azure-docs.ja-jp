---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "67180908"
---
 Azure Cloud Shell を使用している場合は、[使ってみる] をクリックすると、Azure アカウントに自動的にサインインします。 ローカルにサインインするには、昇格された特権で PowerShell コンソールを開き、コマンドレットを実行して接続します。

```azurepowershell
Connect-AzAccount
```

複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧を取得します。

```azurepowershell-interactive
Get-AzSubscription
```

使用するサブスクリプションを指定します。

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```

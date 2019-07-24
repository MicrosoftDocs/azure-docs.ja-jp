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
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418067"
---
昇格された特権で PowerShell コンソールを開きます。



Azure PowerShell をローカルで実行している場合は、お使いの Azure アカウントに接続してください。 *Connect-AzAccount* コマンドレットでは、資格情報の入力を求められます。 認証後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。 PowerShell をローカルで実行しているのではなく、ブラウザーで Azure Cloud Shell の "試してみる" を使用している場合は、この最初の手順はスキップしてください。 お使いの Azure アカウントに自動的に接続されます。

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
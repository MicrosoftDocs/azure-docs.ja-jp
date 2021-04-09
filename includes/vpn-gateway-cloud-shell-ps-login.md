---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93061630"
---
PowerShell をローカルで実行している場合は、昇格された特権で PowerShell コンソールを開き、Azure アカウントに接続します。 *Connect-AzAccount* コマンドレットでは、資格情報の入力を求められます。 認証後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

PowerShell をローカルで実行するのではなく Azure Cloud Shell を使用している場合は、*Connect-AzAccount* を実行する必要はありません。 **[試してみる]** を選択した後、Azure Cloud Shell によって、Azure アカウントに自動的に接続されます。

1. PowerShell をローカルで実行している場合は、サインインします。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 複数のサブスクリプションがある場合は、Azure サブスクリプションの一覧を取得します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. 使用するサブスクリプションを指定します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 188f70d17531a3a7da12444ce5f1c97385ea33bc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418043"
---
この構成を開始する前に、Azure アカウントにサインインする必要があります。 このコマンドレットは、Azure アカウントのサインイン資格情報をユーザーに求めます。 サインイン後にアカウント設定がダウンロードされるため、Azure PowerShell で使用できるようになります。 詳細については、「 [リソース マネージャーでの Windows PowerShell の使用](../articles/powershell-azure-resource-manager.md)」を参照してください。

サインインするには、昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Connect-AzAccount
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```powershell
Get-AzSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```

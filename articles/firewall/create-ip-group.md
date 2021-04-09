---
title: Azure Firewall で IP グループを作成する
description: IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93394528"
---
# <a name="create-ip-groups"></a>IP グループの作成

IP グループを使用すると、Azure Firewall 規則の IP アドレスをグループ化して管理できます。 1 つの IP アドレス、複数の IP アドレス、または 1 つ以上の IP アドレス範囲を設定できます。

## <a name="create-an-ip-group---azure-portal"></a>IP グループを作成する - Azure portal

Azure portal を使用して IP グループを作成するには、次のようにします。

1. Azure portal のホーム ページで **[リソースの作成]** を選択します。
1. 検索ボックスに「**IP グループ**」と入力し、 **[IP グループ]** を選択します。
1. **［作成］** を選択します
1. サブスクリプションを選択します。
1. リソース グループを選択するか、新しく作成します。
1. IP グループの一意の名前を入力し、リージョンを選択します。
1. **Next:次へ: IP アドレス** を選択します。
1. 1 つの IP アドレス、複数の IP アドレス、または IP アドレス範囲を入力します。

   IP アドレスを入力するには、2 つの方法があります。
   - 手動で入力できます
   - ファイルからインポートできます

   ファイルからインポートするには、 **[ファイルからインポート]** を選択します。 ファイルをボックスにドラッグするか、 **[ファイルの参照]** を選択します。 必要であれば、アップロードした IP アドレスを確認して、編集することができます。

   IP アドレスを入力すると、ポータルによって検証が行われ、オーバーラップ、重複、書式の問題が確認されます。

1. 終わったら、 **[確認と作成]** を選択します。
1. **［作成］** を選択します

## <a name="create-an-ip-group---azure-powershell"></a>IP グループを作成する - Azure PowerShell

次の例では、Azure PowerShell を使用して、アドレス プレフィックスと IP アドレスを指定して IP グループを作成します。

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>IP グループを作成する - Azure CLI

次の例では、Azure CLI を使用して、アドレス プレフィックスと IP アドレスを指定して IP グループを作成します。

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>次のステップ

- [IP グループについてさらに確認する](ip-groups.md)

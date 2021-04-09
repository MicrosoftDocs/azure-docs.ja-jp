---
title: メンテナンス構成を別の Azure リージョンに移動する
description: VM メンテナンス構成を別の Azure リージョンに移動する方法を説明します
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.tgt_pltfrm: vm
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 91a6adecc9cf0db56fa4c433f388b05aa1bdef6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98202914"
---
# <a name="move-a-maintenance-control-configuration-to-another-region"></a>メンテナンス コントロール構成を別のリージョンに移動する

メンテナンス コントロール構成を別の Azure リージョンに移動するには、この記事の手順に従ってください。 構成を移動する場合、さまざまな理由が考えられます。 たとえば、新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためなどがあります。

[メンテナンス コントロール](maintenance-control.md)を、カスタマイズされたメンテナンス構成と共に使用すると、プラットフォームの更新プログラムを VM、および Azure Dedicated Host に適用する方法を制御できます。 メンテナンス コントロールをリージョン間で移動するためのシナリオがいくつかあります。

- メンテナンス コントロール構成を移動するが、構成に関連付けられているリソースは移動しない場合、この記事の手順に従います。
- メンテナンス構成に関連付けられているリソースを移動するが、構成自体は移動しない場合、[こちらの手順](move-region-maintenance-configuration-resources.md)に従います。
- メンテナンス構成とそれに関連付けられているリソースの両方を移動する場合、最初にこの記事の手順に従います。 その後、[こちらの手順](move-region-maintenance-configuration-resources.md)に従います。

## <a name="prerequisites"></a>前提条件

メンテナンス コントロール構成の移動を開始する前に、次のことを行います。

- メンテナンス構成は、Azure VM または Azure Dedicated Host に関連付けられています。 開始する前に、新しいリージョンに VM またはホストのリソースが存在することを確認します。
- 識別する: 
    - 既存のメンテナンス コントロール構成。
    - 既存の構成が現在存在するリソース グループ。 
    - 新しいリージョンに移動した後に構成を追加するリソース グループ。 
    - 移動するメンテナンス構成に関連付けられているリソース。
    - 新しいリージョンのリソースが、現在のメンテナンス構成に関連付けられているリソースと同じであることを確認します。 新しいリージョンの構成の名前を古いリージョンと同じにすることはできますが、そうする必要はありません。

## <a name="prepare-and-move"></a>準備と移動 

1. 各サブスクリプションのすべてのメンテナンス構成を取得します。 このためには、CLI の [az maintenance configuration list](/cli/azure/ext/maintenance/maintenance/configuration#ext-maintenance-az-maintenance-configuration-list) コマンドを実行します。$subId を、ご使用のサブスクリプション ID に置き換えてください。

    ```
    az maintenance configuration list --subscription $subId --query "[*].{Name:name, Location:location, ResGroup:resourceGroup}" --output table
    ```
2. 返されたテーブル リストで、サブスクリプション内の構成レコードを確認します。 次に例を示します。 リストには、ご使用の環境固有の値が含まれます。

    **Name** | **Location** | **リソース グループ**
    --- | --- | ---
    Skip Maintenance | eastus2 | configuration-resource-group
    IgniteDemoConfig | eastus2 | configuration-resource-group
    defaultMaintenanceConfiguration-eastus | eastus | test-configuration
    

3. 参照用にリストを保存します。 構成を移動すると、すべてのものが移動されたかどうかを確認するのに役立ちます。
4. 参照として、各構成またはリソース グループを新しいリージョンの新しいリソース グループにマップします。
5. [PowerShell](../virtual-machines/maintenance-control-powershell.md#create-a-maintenance-configuration)、または [CLI](../virtual-machines/maintenance-control-cli.md#create-a-maintenance-configuration)を使用して、新しいリージョンで新しいメンテナンス構成を作成します。
6. [PowerShell](../virtual-machines/maintenance-control-powershell.md#assign-the-configuration)、または [CLI](../virtual-machines/maintenance-control-cli.md#assign-the-configuration)を使用して、新しいリージョンで構成をリソースと関連付けます。


## <a name="verify-the-move"></a>移動を確認する

構成を移動した後、新しいリージョンの構成とリソースを、作成したテーブル リストと比較します。


## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

移動した後、ソース リージョンの移動したメンテナンス構成を削除するかどうかを検討し、[PowerShell](../virtual-machines/maintenance-control-powershell.md#remove-a-maintenance-configuration)、または [CLI](../virtual-machines/maintenance-control-cli.md#delete-a-maintenance-configuration) を使用して削除します。


## <a name="next-steps"></a>次のステップ

メンテナンス構成に関連付けられたリソースを移動する必要がある場合、[こちらの手順](move-region-maintenance-configuration-resources.md)に従ってください。 

---
title: メンテナンス構成に関連付けられているリソースを別のリージョンに移動する
description: VM メンテナンス構成に関連付けられているリソースを別の Azure リージョンに移動する方法を説明します
author: shants123
ms.service: virtual-machines
ms.topic: how-to
ms.date: 03/04/2020
ms.author: shants
ms.openlocfilehash: 88082c441dafdc7571f2b9775bfc07ebe3ca5aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730508"
---
# <a name="move-resources-in-a-maintenance-control-configuration-to-another-region"></a>メンテナンス コントロール構成のリソースを別のリージョンに移動する

メンテナンス コントロール構成に関連付けられているリソースを別の Azure リージョンに移動するには、この記事に従ってください。 構成を移動する場合、さまざまな理由が考えられます。 たとえば、新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためなどがあります。

[メンテナンス コントロール](maintenance-control.md)を、カスタマイズされたメンテナンス構成と共に使用すると、プラットフォームの更新プログラムを VM、および Azure Dedicated Host に適用する方法を制御できます。 メンテナンス コントロールをリージョン間で移動するためのシナリオがいくつかあります。

- メンテナンス構成に関連付けられているリソースを移動するが、構成自体は移動しない場合、この記事に従います。
- メンテナンス コントロール構成を移動するが、構成に関連付けられているリソースは移動しない場合、[こちらの手順](move-region-maintenance-configuration.md)に従います。
- メンテナンス構成とそれに関連付けられているリソースの両方を移動する場合、最初に[こちらの手順](move-region-maintenance-configuration.md)に従います。 その後、この記事の手順に従います。

## <a name="prerequisites"></a>前提条件

メンテナンス コントロール構成に関連付けられているリソースの移動を開始する前に、次のことを行います。

- 開始する前に、移動するリソースが新しいリージョンに存在していることを確認します。
- 移動する Azure VM と Azure Dedicated Host に関連付けられているメンテナンス コントロール構成を検証します。 各リソースを個別にチェックします。 現在、複数のリソースの構成を取得する方法はありません。
- リソースの構成を取得する場合:
    - Azure Dedicated Host の ID ではなくアカウントのサブスクリプション ID を使用していることを確認します。
    - CLI:読みやすくするためだけではなく、削除や変更もできるように、出力テーブル パラメーターを使用します。
    - PowerShell:読みやすくするためだけではなく、削除や変更もできるように、Format-Table Name パラメーターを使用します。
    - PowerShell を使用する場合、関連付けられた構成がないリソースの構成をリストしようとすると、エラーが発生します。 次のようなエラーが表示されます。"操作が失敗しました。状態: '見つかりません'。 詳細:404 クライアント エラー: URL のリソースが見つかりません"。

    
## <a name="prepare-to-move"></a>移動の準備をする

1. 始める前に、次の変数を定義します。 それぞれの例を示します。

    **変数** | **詳細** | **例**
    --- | ---
    $subId | メンテナンス構成を含むサブスクリプションの ID | "our-subscription-ID"
    $rsrcGroupName | リソース グループ名 (Azure VM) | "VMResourceGroup"
    $vmName | VM リソース名 |  "myVM"
    $adhRsrcGroupName |  リソース グループ (Dedicated ホスト) | "HostResourceGroup"
    $adh | Dedicated ホスト名 | "myHost"
    $adhParentName | 親リソース名 | "HostGroup"
    
2. PowerShell の [Get-AZConfigurationAssignment](/powershell/module/az.maintenance/get-azconfigurationassignment) コマンドを使用してメンテナンス構成を取得する場合:

    - Azure Dedicated Host の場合、次のコマンドを実行します。
        ```
        Get-AzConfigurationAssignment -ResourceGroupName $adhRsrcGroupName -ResourceName $adh -ResourceType hosts -ProviderName Microsoft.Compute -ResourceParentName $adhParentName -ResourceParentType hostGroups | Format-Table Name
        ```

    - Azure VM の場合、次のコマンドを実行します。

        ```
        Get-AzConfigurationAssignment -ResourceGroupName $rgName -ResourceName $vmName -ProviderName Microsoft.Compute -ResourceType virtualMachines | Format-Table Name
        ```
3. CLI の [az maintenance assignment](/cli/azure/ext/maintenance/maintenance/assignment) コマンドを使用してメンテナンス構成を取得する場合:

    - Azure Dedicated Host の場合:

        ```
        az maintenance assignment list --subscription $subId --resource-group $adhRsrcGroupName --resource-name $adh --resource-type hosts --provider-name Microsoft.Compute --resource-parent-name $adhParentName --resource-parent-type hostGroups --query "[].{HostResourceGroup:resourceGroup,ConfigName:name}" --output table
        ```

    - Azure VM の場合:

        ```
        az maintenance assignment list --subscription $subId --provider-name Microsoft.Compute --resource-group $rsrcGroupName --resource-name $vmName --resource-type virtualMachines --query "[].{HostResourceGroup:resourceGroup, ConfigName:name}" --output table
        ```


## <a name="move"></a>詳細ビュー 

1. [こちらの手順に従って](../site-recovery/azure-to-azure-tutorial-migrate.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json) Azure VM を新しいリージョンに移動します。
2. リソースを移動した後、メンテナンス構成を移動したかどうかに応じて、必要があれば、新しいリージョンのリソースにメンテナンス構成を再適用します。 メンテナンス構成をリソースに適用するには、[PowerShell](../virtual-machines/maintenance-control-powershell.md) または [CLI](../virtual-machines/maintenance-control-cli.md) を使用します。


## <a name="verify-the-move"></a>移動を確認する

新しいリージョンでリソースを確認します。また、新しいリージョンでリソースに関連付けられている構成も確認します。 

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

移動後、移動したリソースをソース リージョンで削除するかどうかを検討します。


## <a name="next-steps"></a>次のステップ

メンテナンス構成を移動する必要がある場合は、[こちらの手順](move-region-maintenance-configuration.md)に従ってください。 

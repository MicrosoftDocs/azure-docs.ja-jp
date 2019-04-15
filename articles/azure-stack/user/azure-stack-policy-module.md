---
title: Azure Stack ポリシー モジュールの使用 | Microsoft Docs
description: Azure Stack サブスクリプションと同様に動作するように、Azure サブスクリプションを制限する方法を説明します。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 9fade97bbe783cf156f5b73523bc0834a34df926
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487449"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Azure Stack ポリシー モジュールを使用して Azure ポリシー管理する

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

Azure Stack ポリシー モジュールを使用すると、Azure Stack と同じバージョン管理とサービス可用性で、Azure サブスクリプションを構成できます。 このモジュールでは、[New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) PowerShell コマンドレットを使用して、サブスクリプションで使用できるリソースの種類とサービスを制限する Azure ポリシーを作成します。 次に、[New-azurermpolicyassignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) コマンドレットを使用して、適切なスコープ内でポリシーの割り当てを作成します。 ポリシーの構成が完了すると、Azure サブスクリプションを使用して Azure Stack を対象とするアプリを開発できます。

## <a name="install-the-module"></a>モジュールのインストール

1. 「[PowerShell for Azure Stack のインストール](azure-stack-powershell-install.md)」の手順 1 の説明に従って、AzureRM PowerShell モジュールの必要なバージョンをインストールします。
2. [GitHub から Azure Stack ツールをダウンロードします](azure-stack-powershell-download.md)。
3. [PowerShell を Azure Stack で使用するために構成します](azure-stack-powershell-configure-user.md)。
4. AzureStack.Policy.psm1 モジュールをインポートします。


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Azure サブスクリプションにポリシーを適用する

次のコマンドを使用して、Azure サブスクリプションに対して既定の Azure Stack ポリシーを適用できます。 このコマンドを実行する前に、`Azure subscription name` を自分の Azure サブスクリプションの名前に置き換えてください。

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>リソース グループにポリシーを適用する

さらに詳細なポリシーを適用する必要がある場合があります。 たとえば、同じサブスクリプションで他のリソースが実行されている場合があります。 ポリシー適用のスコープを特定のリソース グループに設定することで、Azure リソースを使用して Azure Stack のアプリをテストできます。 次のコマンドを実行する前に、`Azure subscription name` を自分の Azure サブスクリプションの名前に置き換えてください。

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>実行中のポリシー

Azure ポリシーをデプロイした後、このポリシーで禁止されているリソースをデプロイしようとすると、エラーが表示されます。

![ポリシーの制約によるリソースのデプロイの失敗の結果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>次の手順

* [PowerShell を使用したテンプレートのデプロイ](azure-stack-deploy-template-powershell.md)
* [Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)
* [Visual Studio を使用したテンプレートのデプロイ](azure-stack-deploy-template-visual-studio.md)

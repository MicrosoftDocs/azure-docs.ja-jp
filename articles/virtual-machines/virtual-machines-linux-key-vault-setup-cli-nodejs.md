---
title: "Azure CLI 1.0 を使用して Linux VM 向けに Key Vault を設定する| Microsoft Docs"
description: "Azure CLI 1.0 を使用して Azure Resource Manager 仮想マシン用に Key Vault を設定する方法。"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
translationtype: Human Translation
ms.sourcegitcommit: 810cab4121e9667d53f2a9bb580ea6e3f4614b77
ms.openlocfilehash: d1af7e2f0b0f6bbbd772cce9f97f64ca02b058ea
ms.lasthandoff: 02/27/2017


---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Azure CLI 1.0 を使用して Azure Resource Manager の仮想マシン用に Key Vault を設定する
Azure Resource Manager スタックでは、Key Vault のリソースプロバイダーにより提供されるリソースとしてシークレット/証明書がモデル化されます。 Azure Key Vault の詳細については、「 [Azure Key Vault とは](../key-vault/key-vault-whatis.md) Key Vault を Azure Resource Manager 仮想マシンと共に使用するには、Key Vault の *EnabledForDeployment* プロパティを True に設定する必要があります。 この設定は、さまざまなクライアントで実行できます。 この記事では、Azure Virtual Machines 用に Key Vault を設定する方法につちえ説明します。

## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
このタスクは、次のいずれかの CLI バージョンを使用して行うことができます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0](virtual-machines-linux-key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイメント モデル用の次世代 CLI

## <a name="use-cli-10-to-set-up-key-vault"></a>CLI 1.0 を使用して Key Vault を設定する
コマンド ライン インターフェイス (CLI) を使用して Key Vault を作成するには、「 [CLI を使用した Key Vault の管理](../key-vault/key-vault-manage-with-cli.md#create-a-key-vault)」を参照してください。

CLI 1.0 の場合、デプロイ ポリシーを割り当てる前に Key Vault を作成する必要があります。 作成後、次のコマンドを使用してポリシーを割り当てることができます。

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>テンプレートを使用して Key Vault を設定する
テンプレートを使用する場合、Key Vault リソースの `enabledForDeployment` プロパティを `true` に設定する必要があります。

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

テンプレートを使用して、Key Vault の作成時に構成できるその他のオプションについては、「 [Create a Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)」を参照してください。



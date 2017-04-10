---
title: "Azure テンプレートを使用して Linux VM を作成する | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して、Linux VM を Azure に作成します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/24/2016
ms.author: v-livech
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e692e87899e49f6ad681de8e682492ec69e47eac
ms.lasthandoff: 03/21/2017


---
# <a name="how-to-create-a-linux-vm-using-an-azure-resourec-manager-template"></a>Azure Resource Manager テンプレートを使用して Linux VM を作成する方法
この記事では、Azure テンプレートを使用してすばやく Linux 仮想マシンを Azure にデプロイする方法を紹介します。  この記事では以下が必要です。

* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* `azure login` でログインした [Azure CLI](../cli-install-nodejs.md)。
* Azure CLI は、Azure Resource Manager モード (`azure config mode arm`) である "*必要があります*"。

[Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して、Linux VM テンプレートをすばやくデプロイすることもできます。

## <a name="quick-command-summary"></a>クイック コマンドの概要
```azurecli
azure group create \
    -n myResourceGroup \
    -l westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル
テンプレートを使用すると、ユーザー名やホスト名など、起動時にカスタマイズされる設定で VM を Azure に作成できます。 この記事では、Ubuntu VM とネットワーク セキュリティ グループ (NSG) を使用する Azure テンプレートを紹介します。このテンプレートでは、ポート 22 が SSH 用に開かれています。

Azure Resource Manager テンプレートは、この記事で行う Ubuntu VM の起動のような、単純で&1; 回限りのタスクに使用できる JSON ファイルです。  Azure テンプレートは、テスト、開発、または運用デプロイ スタックのような、環境全体の複雑な Azure 構成の構築にも使用できます。

## <a name="create-the-linux-vm"></a>Linux VM の作成
次のコードは、`azure group create` を呼び出してリソース グループを作成すると同時に、SSH で保護された Linux VM をデプロイする例です。[こちらの Azure Resource Manager テンプレート](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)を使用しています。 この例で使われている各種の名前は、実際の環境に合わせて調整する必要があります。 この例では、リソース グループ名として `myResourceGroup` を、VM 名として `myVM` を使用しています。

```azurecli
azure group create \
    --name myResourceGroup \
    --location westus \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

出力は次のような出力ブロックになります。

```azurecli
info:    Executing command group create
+ Getting resource group myResourceGroup
+ Creating resource group myResourceGroup
info:    Created resource group myResourceGroup
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== myAdminUser@myVM
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/myResourceGroup
data:    Name:                myResourceGroup
data:    Location:            westus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

この例では、 `--template-uri` パラメーターを使用して VM をデプロイしています。  また、テンプレートをローカルでダウンロードまたは作成し、 `--template-file` パラメーターを使ってテンプレート ファイルへのパスを引数として指定して、そのテンプレートを渡すこともできます。 テンプレートで必要となるパラメーターは、Azure CLI のプロンプトで入力します。

## <a name="next-steps"></a>次のステップ
次にデプロイするアプリ フレームワークを見つけるには、 [テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/) を検索します。



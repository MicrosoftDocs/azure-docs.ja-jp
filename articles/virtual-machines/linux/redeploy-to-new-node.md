---
title: "Azure での Linux 仮想マシンの再デプロイ | Microsoft Docs"
description: "SSH 接続の問題を軽減するために Azure で Linux 仮想マシンを再デプロイする方法。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 7a8653a82775e718c38f65f246d997ba61f99d58
ms.contentlocale: ja-jp
ms.lasthandoff: 06/26/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>新しい Azure ノードへの Linux 仮想マシンの再デプロイ
Azure の Linux 仮想マシン (VM) への SSH またはアプリケーション アクセスに関するトラブルシューティングで問題があるときは、VM の再デプロイが有効な場合があります。 VM を再デプロイするときは、Azure インフラストラクチャ内の新しいノードに VM を移動してから、電源をオンにします。 すべての構成オプションと関連するリソースが保持されます。 この記事では、Azure CLI または Azure ポータルを使用して VM を再デプロイする方法について説明します。

> [!NOTE]
> VM を再デプロイすると、一時ディスクが失われ、仮想ネットワーク インターフェイスに関連付けられている動的 IP アドレスが更新されます。 

次のいずれかの方法を使用して VM を再デプロイできます。 VM を再デプロイする方法を 1 つだけ選択してください。

- [Azure CLI 2.0](#azure-cli-20)
- [Azure CLI 1.0](#azure-cli-10)
- [Azure ポータル](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Azure CLI 2.0 を使用する
最新の [Azure CLI 2.0](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/#login) を使用して Azure アカウントにログインします。

[az vm redeploy](/cli/azure/vm#redeploy) を使用して VM を再デプロイします。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を再デプロイします。

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Azure CLI 1.0 を使用する
[最新の Azure CLI 1.0](../../cli-install-nodejs.md) をインストールし、Azure アカウントにログインし、Resource Manager モード (`azure config mode arm`) になっていることを確認します。

次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という VM を再デプロイします。

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>次のステップ
VM への接続に関する問題が発生した場合は、[SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページまたは「[SSH の詳細なトラブルシューティング手順](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 VM で実行されるアプリケーションにアクセスできない場合は、[アプリケーションの問題のトラブルシューティング](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページもご覧ください。



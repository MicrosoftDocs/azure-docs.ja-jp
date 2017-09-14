---
title: "Azure CLI のサンプル スクリプト - Linux VM の暗号化 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Linux VM の暗号化"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/02/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 9388bce04e37d049301521f808cd8494c327e335
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017

---

# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Azure における Linux 仮想マシンの暗号化

このスクリプトは、安全な Azure Key Vault、暗号化キー、Azure Active Directory サービス プリンシパル、Linux 仮想マシン (VM) を作成します。 その後、Key Vault から取得した暗号化キーとサービス プリンシパルの資格情報を使って、作成した VM を暗号化します。

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[メイン](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "VM ディスクの暗号化")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、VM、すべての関連リソースを削除します。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、Azure Key Vault、サービス プリンシパル、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | セキュリティで保護されたデータ (暗号化キーなど) を格納する Azure Key Vault を作成します。 |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key#create) | Key Vault に暗号化キーを作成します。 |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp#create-for-rbac) | 暗号化キーへのアクセスを安全に認証して制御する Azure Active Directory サービス プリンシパルを作成します。 |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | サービス プリンシパルに暗号化キーへのアクセス権を付与するために、Key Vault に対するアクセス許可を設定します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption#enable) | サービス プリンシパルの資格情報と暗号化キーを使って VM に対する暗号化を有効にします。 |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption#show) | VM の暗号化処理の状態を表示します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。


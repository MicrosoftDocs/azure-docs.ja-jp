---
title: Azure CLI - ラボの仮想マシンを停止して削除する
description: この記事では、Azure DevTest Labs のラボの仮想マシンを停止して削除する Azure CLI スクリプトを紹介します。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3f3802837685281339f0ca355c677e1a0ceac067
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198202"
---
# <a name="use-azure-cli-to-stop-and-delete-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI を使用して Azure DevTest Labs のラボの仮想マシンを停止して削除する

この Azure CLI スクリプトは、ラボの仮想マシン (VM) を停止して削除します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/stop-delete-virtual-machine-in-lab/stop-delete-virtual-machine-in-lab.sh "Stop and delete a VM in a lab")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
|---|---|
| [az lab vm stop](/cli/azure/lab/vm#az-lab-vm-stop) | ラボの仮想マシン (VM) を停止します。 この操作を完了するにはしばらく時間がかかることがあります。 |
| [az lab vm delete](/cli/azure/lab/vm#az-lab-vm-delete) | ラボの仮想マシン (VM) を削除します。 この操作を完了するにはしばらく時間がかかることがあります。 |


## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure Lab Services のその他の CLI のサンプル スクリプトについては、[Azure Lab Services の CLI のサンプル](../samples-cli.md)に関する記事をご覧ください。

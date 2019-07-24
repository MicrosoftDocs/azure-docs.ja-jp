---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: beece95164f0d82b1aa7f22d56f4dce02f4bb38c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804469"
---
Azure CLI を使用すると、macOS、Linux、Windows 上の Azure リソースを作成および管理できます。 この記事では、仮想マシン (VM) の作成および管理に使用する最も一般的なコマンドの一部について詳しく説明します。

この記事では、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 ブラウザーから [Cloud Shell](/azure/cloud-shell/quickstart) を使用することもできます。

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI での 基本的な Azure Resource Manager コマンド
特定のコマンド ライン スイッチやオプションの詳細については、「`az <command> <subcommand> --help`」と入力して、コマンドのオンライン ヘルプとオプションを使用します。

### <a name="create-vms"></a>VM の作成
| タスク | Azure CLI コマンド |
| --- | --- |
| リソース グループの作成 | `az group create --name myResourceGroup --location eastus` |
| Linux VM の作成 | `az vm create --resource-group myResourceGroup --name myVM --image ubuntults` |
| Windows VM の作成 | `az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter` |

### <a name="manage-vm-state"></a>VM の状態の管理
| タスク | Azure CLI コマンド |
| --- | --- |
| VM の起動 | `az vm start --resource-group myResourceGroup --name myVM` |
| VM の停止 | `az vm stop --resource-group myResourceGroup --name myVM` |
| VM の割り当て解除 | `az vm deallocate --resource-group myResourceGroup --name myVM` |
| VM の再起動 | `az vm restart --resource-group myResourceGroup --name myVM` |
| VM を再デプロイする | `az vm redeploy --resource-group myResourceGroup --name myVM` |
| VM の削除 | `az vm delete --resource-group myResourceGroup --name myVM` |

### <a name="get-vm-info"></a>VM の情報の取得
| タスク | Azure CLI コマンド |
| --- | --- |
| VM の一覧表示 | `az vm list` |
| VM に関する情報の取得 | `az vm show --resource-group myResourceGroup --name myVM` |
| VM リソースの使用量の取得 | `az vm list-usage --location eastus` |
| 使用可能なすべての VM サイズの取得 | `az vm list-sizes --location eastus` |

## <a name="disks-and-images"></a>ディスクとイメージ
| タスク | Azure CLI コマンド |
| --- | --- |
| VM へのデータ ディスクの追加 | `az vm disk attach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk --size-gb 128 --new` |
| VM からのデータ ディスクの削除 | `az vm disk detach --resource-group myResourceGroup --vm-name myVM --disk myDataDisk` |
| ディスクのサイズの変更 | `az disk update --resource-group myResourceGroup --name myDataDisk --size-gb 256` |
| ディスクのスナップショットの作成 | `az snapshot create --resource-group myResourceGroup --name mySnapshot --source myDataDisk` |
| VM のイメージの作成 | `az image create --resource-group myResourceGroup --source myVM --name myImage` |
| イメージからの VM の作成 | `az vm create --resource-group myResourceGroup --name myNewVM --image myImage` |


## <a name="next-steps"></a>次の手順
CLI コマンドの他の例については、チュートリアル「[Azure CLI を使用した Linux VM の作成と管理](../articles/virtual-machines/linux/tutorial-manage-vm.md)」を参照してください。


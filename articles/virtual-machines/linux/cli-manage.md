---
title: 一般的な Azure CLI コマンド
description: Azure Resource Manager モードで VM を管理する際に使用する一般的な Azure CLI コマンドについて説明します
author: RicksterCDN
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/12/2017
ms.author: rclaus
ms.openlocfilehash: 2084d79ecbbc53ef9e3c75bae0664eae7de0eccb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559632"
---
# <a name="common-azure-cli-commands-for-managing-azure-resources"></a>Azure リソースを管理するための一般的な Azure CLI コマンド

Azure CLI を使用すると、macOS、Linux、Windows 上の Azure リソースを作成および管理できます。 この記事では、仮想マシン (VM) の作成および管理に使用する最も一般的なコマンドの一部について詳しく説明します。

この記事では、Azure CLI バージョン 2.0.4 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 アップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。 ブラウザーから [Cloud Shell](../../cloud-shell/quickstart.md) を使用することもできます。

## <a name="basic-azure-resource-manager-commands-in-azure-cli"></a>Azure CLI での 基本的な Azure Resource Manager コマンド
特定のコマンド ライン スイッチやオプションの詳細については、「`az <command> <subcommand> --help`」と入力して、コマンドのオンライン ヘルプとオプションを使用します。

### <a name="create-vms"></a>VM の作成
| タスク | Azure CLI コマンド |
| --- | --- |
| リソース グループを作成する | `az group create --name myResourceGroup --location eastus` |
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
| VM を削除する | `az vm delete --resource-group myResourceGroup --name myVM` |

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


## <a name="next-steps"></a>次のステップ
CLI コマンドの他の例については、チュートリアル「[Azure CLI を使用した Linux VM の作成と管理](tutorial-manage-vm.md)」を参照してください。

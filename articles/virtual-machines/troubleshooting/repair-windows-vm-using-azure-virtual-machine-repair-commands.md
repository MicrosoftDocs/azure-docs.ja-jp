---
title: Azure 仮想マシンの修復コマンドを使用して Windows VM を修復する | Microsoft Docs
description: この記事では、Azure VM の修復コマンドを使用してディスクを別の Windows VM に接続してエラーを修正した後、元の VM を修復する方法について詳しく説明します。
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 7addc87f3096a75a55d0ea3b5804fd0006d5cb8c
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526488"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Azure 仮想マシンの修復コマンドを使用して Windows VM を修復する

Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合は、ディスク自体で軽減策を実行する必要がある可能性があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure VM の修復コマンドを使用してディスクを別の Windows VM に接続してエラーを修正した後、元の VM を修復する方法について詳しく説明します。

> [!IMPORTANT]
> * この記事のスクリプトは、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) を使用している VM にのみ適用されます。
> * スクリプトを実行するには、VM からの送信接続 (ポート 443) が必要です。
> * 実行できるスクリプトは一度に 1 つだけです。
> * 実行中のスクリプトを取り消すことはできません。
> * スクリプトを実行できる最大時間は 90 分であり、それを過ぎるとタイムアウトします。
> * Azure Disk Encryption を使用する VM では、(KEK の有無にかかわらず) シングル パス暗号化で暗号化されたマネージド ディスクのみがサポートされます。


## <a name="repair-process-overview"></a>修復プロセスの概要

Azure VM の修復コマンドを使用して VM の OS ディスクを変更できるだけでなく、VM を削除して再作成する必要がなくなりました。

次のステップに従って、VM の問題のトラブルシューティングを行います。

1. Azure Cloud Shell を起動する
2. az extension add/update を実行する。
3. az vm repair create を実行する。
4. az vm repair run を実行する。
5. az vm repair restore を実行する。

その他のドキュメントと手順については、「[az vm repair](/cli/azure/ext/vm-repair/vm/repair)」をご覧ください。

## <a name="repair-process-example"></a>修復プロセスの例

1. Azure Cloud Shell を起動する

   Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

   Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com](https://shell.azure.com) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。

   **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell にコード貼り付けてから、 **[入力]** を選択して実行します。

   CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、``az --version`` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。
   
   現在 Azure portal にログインしているアカウントとは別のアカウントを使用して Cloud Shell にログインする必要がある場合は、``az login`` [az login reference](/cli/azure/reference-index?view=azure-cli-latest#az-login) を使用できます。  アカウントに関連付けられているサブスクリプションを切り替えるには、``az account set --subscription`` [az account set reference](/cli/azure/account?view=azure-cli-latest#az-account-set) を使用できます。

2. `az vm repair` コマンドを初めて使用する場合は、VM 修復 CLI 拡張機能を追加します。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   `az vm repair` コマンドを以前使用したことがある場合は、VM 修復拡張機能に更新プログラムを適用します。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. `az vm repair create` を実行します。 このコマンドでは、機能していない VM の OS ディスクのコピーが作成され、新しいリソース グループに修復 VM が作成されて、OS ディスクのコピーに接続されます。  修復 VM のサイズとリージョンは、指定された機能していない VM と同じになります。 すべての手順で使用されるリソース グループと VM の名前は、機能していない VM 用になります。 VM が Azure Disk Encryption を使用している場合、コマンドは、修復 VM に接続されているときにアクセスできるように、暗号化されたディスクのロックを解除しようとします。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. `az vm repair run` を実行します。 このコマンドでは、修復 VM を介して接続されているディスクで指定した修復スクリプトが実行されます。 使用しているトラブルシューティング ガイドで run-id を指定した場合は、ここでそれを使用します。それ以外の場合は、`az vm repair list-scripts` を使用して使用可能な修復スクリプトを確認できます。 ここで使用されるリソース グループと VM の名前は、手順 3 で使用された機能していない VM 用です。

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```

5. `az vm repair restore` を実行します。 このコマンドでは、修復された OS ディスクが VM の元の OS ディスクとスワップされます。 ここで使用されるリソース グループと VM の名前は、手順 3 で使用された機能していない VM 用です。

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>ブート診断を確認して有効にする

次の例では、``myResourceGroup`` という名前のリソース グループの ``myVMDeployed`` という名前の VM で診断拡張機能を有効にします。

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>次のステップ

* VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](./troubleshoot-rdp-connection.md)に関する記事をご覧ください。
* VM で実行されているアプリケーションへのアクセスに関する問題については、「[Azure 上の仮想マシンにおけるアプリケーション接続に関する問題のトラブルシューティング](./troubleshoot-app-connection.md)」をご覧ください。
* Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/management/overview.md)」をご覧ください。

---
title: Azure 仮想マシンの修復コマンドを使用して Linux VM を修復する | Microsoft Docs
description: この記事では、Azure 仮想マシンの修復コマンドを使用してディスクを別の Linux VM に接続してエラーを修正した後、元の VM をリビルドする方法について詳しく説明します。
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 56e420f9641638bfa79ff077be73132b00b934ab
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "71131294"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Azure 仮想マシンの修復コマンドを使用して Linux VM を修復する

Azure の Linux 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合は、ディスク自体で軽減策を実行する必要がある可能性があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure 仮想マシンの修復コマンドを使用してディスクを別の Linux VM に接続してエラーを修正した後、元の VM をリビルドする方法について詳しく説明します。

> [!IMPORTANT]
> この記事のスクリプトは、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使用している VM にのみ適用されます。

## <a name="repair-process-overview"></a>修復プロセスの概要

Azure 仮想マシンの修復コマンドを使用して VM の OS ディスクを変更できるだけでなく、VM を削除して再作成する必要がなくなりました。

次のステップに従って、VM の問題のトラブルシューティングを行います。

1. Azure Cloud Shell を起動する
2. az extension add/update を実行する
3. az vm repair create を実行する
4. 軽減ステップを実行する
5. az vm repair restore を実行する

その他のドキュメントと手順については、「[az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair)」をご覧ください。

## <a name="repair-process-example"></a>修復プロセスの例

1. Azure Cloud Shell を起動する

   Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

   Cloud Shell を開くには、コード ブロックの右上隅にある **[試してみる]** を選択します。 [https://shell.azure.com](https://shell.azure.com) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。

   **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell にコード貼り付けてから、 **[入力]** を選択して実行します。

   CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.30 以降が必要です。 バージョンを確認するには、``az --version`` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

2. `az vm repair` コマンドを初めて使用する場合は、VM 修復 CLI 拡張機能を追加します。

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   `az vm repair` コマンドを以前使用したことがある場合は、VM 修復拡張機能に更新プログラムを適用します。

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. `az vm repair create` を実行します。 このコマンドは、機能していない VM の OS ディスクのコピーを作成し、修復 VM を作成して、ディスクを接続します。

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. 必要に応じて作成した修復 VM で軽減ステップを実行し、ステップ 5 に進みます。

5. `az vm repair restore` を実行します。 このコマンドでは、修復された OS ディスクが VM の元の OS ディスクとスワップされます。

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>ブート診断を確認して有効にする

次の例では、``myResourceGroup`` という名前のリソース グループの ``myVMDeployed`` という名前の VM で診断拡張機能を有効にします。

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>次の手順

* VM への接続の問題が発生した場合は、[Azure 仮想マシンへの RDP 接続のトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)に関する記事をご覧ください。
* VM で実行されているアプリケーションへのアクセスに関する問題については、[Azure の仮想マシンでのアプリケーションの接続の問題のトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)に関する記事をご覧ください。
* Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)」をご覧ください。

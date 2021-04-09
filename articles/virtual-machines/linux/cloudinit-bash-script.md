---
title: cloud-init を使用して Azure 上の Linux VM で Bash スクリプトを実行する
description: Azure CLI による作成時に Linux VM で cloud-init を使用して Bash スクリプトを実行する方法
author: rickstercdn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1dabd7db85d4222ca2e9f8387b716af291ee6a69
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559428"
---
# <a name="use-cloud-init-to-run-a-bash-script-in-a-linux-vm-in-azure"></a>cloud-init を使用して Azure 上の Linux VM で Bash スクリプトを実行する
この記事では、Azure でのプロビジョニング時に、[cloud-init](https://cloudinit.readthedocs.io) を使用して、Linux 仮想マシン (VM) または仮想マシン スケール セット (VMSS) 上で既存の Bash スクリプトを実行する方法を示します。 これらの cloud-init スクリプトは、Azure によってリソースがプロビジョニングされた後の最初の起動時に実行されます。 cloud-init が Azure およびサポートされている Linux ディストリビューションでネイティブに動作する方法の詳細については、[cloud-init の概要](using-cloud-init.md)に関するページをご覧ください

## <a name="run-a-bash-script-with-cloud-init"></a>cloud-init を使用して Bash スクリプトを実行する
cloud-init では、既存のスクリプトを cloud-config に変換する必要はありません。cloud-init は、Bash スクリプトなどの複数の入力の種類を受け入れます。

Linux カスタム スクリプトの Azure 拡張機能を使用してスクリプトを実行している場合は、cloud-init を使用するようにそれらを移行できます。 ただし、Azure 拡張機能ではレポートが統合され、スクリプト エラーが警戒されましたが、cloud-init では、スクリプト エラーが発生してもイメージのデプロイは失敗しません。

この機能の動作を確認するために、テスト用のシンプルな Bash スクリプトを作成します。 cloud-init `#cloud-config` ファイルと同様、このスクリプトは、AzureCLI コマンドを実行して仮想マシンをプロビジョニングする場所に対してローカルである必要があります。  この例では、ローカル コンピューター上にない Cloud Shell でファイルを作成します。 任意のエディターを使用することができます。 `sensible-editor simple_bash.sh` を入力し、ファイルを作成して使用可能なエディターの一覧を確認します。 **nano** エディターを使用するには #1 を選びます。 cloud-init ファイル全体 (特に最初の行) が正しくコピーされたことを確認してください。  

```bash
#!/bin/sh
echo "this has been written via cloud-init" + $(date) >> /tmp/myScript.txt
```

このイメージをデプロイする前に、[az group create](/cli/azure/group) コマンドを使用してリソース グループを作成する必要があります。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

ここで、[az vm create](/cli/azure/vm) で VM を作成し、次のように `--custom-data simple_bash.sh` で Bash スクリプト ファイルを指定します。

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data simple_bash.sh \
  --generate-ssh-keys 
```
## <a name="verify-bash-script-has-run"></a>Bash スクリプトの実行の確認
前述のコマンドからの出力に示すように、VM のパブリック IP アドレスに SSH 接続します。 実際の **publicIpAddress** を次のように入力します。

```bash
ssh <publicIpAddress>
```

**/tmp** ディレクトリに移動し、myScript.txt ファイルがあること、中に適切な内容が記載されていることを確認します。  失敗している場合は、詳細について **/var/log/cloud-init.log** を調べることができます。  次のエントリを検索します。

```bash
Running config-scripts-user using lock Running command ['/var/lib/cloud/instance/scripts/part-001']
```

## <a name="next-steps"></a>次のステップ
構成変更の cloud-init の他の例については、以下をご覧ください。
 
- [VM に他の Linux ユーザーを追加する](cloudinit-add-user.md)
- [初回起動時にパッケージ マネージャーを実行して既存のパッケージを更新する](cloudinit-update-vm.md)
- [VM のローカル ホスト名を変更する](cloudinit-update-vm-hostname.md) 
- [アプリケーション パッケージのインストール、構成ファイルの更新、キーの挿入](tutorial-automate-vm-deployment.md)

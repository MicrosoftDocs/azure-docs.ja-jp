---
title: Azure VM への SSH 接続の問題に関するトラブルシューティング | Microsoft Docs
description: Linux を実行する Azure VM に対する "SSH 接続の失敗" や "SSH 接続の拒否" などの問題のトラブルシューティング方法。
keywords: ssh 接続が拒否される, ssh エラー, azure ssh, SSH 接続に失敗する
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 81e00c4a3b9490a05667d58952f7bdf8945bacdb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446577"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Azure Linux VM に対する SSH 接続の失敗、エラー、拒否のトラブルシューティング
この記事は、Linux 仮想マシン (VM) に接続しようとしたときに、Secure Shell (SSH) エラー、SSH 接続エラー、または SSH の拒否により発生する問題を見つけて修正するために役立ちます。 Azure Portal、Azure CLI、または Linux 用の VM アクセス拡張機能を使用して、接続の問題を解決できます。

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。

## <a name="quick-troubleshooting-steps"></a>簡単なトラブルシューティング手順
トラブルシューティングの各手順を実行した後、VM に再接続してみてください。

1. [SSH 構成をリセットします](#reset-config)。
2. ユーザーの[資格情報をリセットします](#reset-credentials)。
3. [ネットワーク セキュリティ グループ](../../virtual-network/security-overview.md) ルールで SSH トラフィックが許可されていることを確認します。
   * SSH トラフィックを許可する[ネットワーク セキュリティ グループ規則](#security-rules)が存在することを確認します (既定では TCP ポート 22)。
   * ポートのリダイレクト/マッピングは、Azure Load Balancer なしでは使用できません。
4. [VM リソースの正常性](../../resource-health/resource-health-overview.md)を確認します。
   * VM が正常であると報告されていることを確認します。
   * [ブート診断を有効にしている](boot-diagnostics.md)場合は、VM のブート エラーがログに報告されていないことを確認します。
5. [VM を再起動します](#restart-vm)。
6. [VM を再デプロイします](#redeploy-vm)。

詳しいトラブルシューティング手順と説明を引き続きお読みください。

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>SSH 接続の問題をトラブルシューティングするために使用できる方法
資格情報または SSH 構成は、次のいずれかの方法でリセットできます。

* [Azure Portal](#use-the-azure-portal) - SSH 構成または SSH キーをすばやくリセットする必要があり、Azure ツールをインストールしていない場合に最適です。
* [Azure VM シリアル コンソール](https://aka.ms/serialconsolelinux) - VM シリアル コンソールは、SSH 構成に関係なく動作し、VM への対話型コンソールを提供します。 実際に、シリアル コンソールは、"SSH 接続できない" 状況の解決を支援するように設計されています。 詳しくは、以下をご覧ください。
* [Azure CLI](#use-the-azure-cli) - 既にコマンド ラインにいる場合は、SSH 構成または資格情報をすばやくリセットします。 クラシック VM を使用している場合は、[Azure クラシック CLI](#use-the-azure-classic-cli) を使用できます。
* [Azure VMAccessForLinux 拡張機能](#use-the-vmaccess-extension) - json 定義ファイルを作成して再度使用して、SSH 構成またはユーザーの資格情報をリセットします。

トラブルシューティングの各手順を実行した後、再度 VM に接続してみてください。 それでも接続できない場合は、次の手順をお試しください。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用
Azure Portal では、ローカル コンピューターへのツールのインストールなしで SSH 構成またはユーザーの資格情報を簡単にリセットできます。

まず Azure portal で VM を選択します。 **[サポート + トラブルシューティング]** セクションまで下へスクロールし、**[パスワードのリセット]** を選択します (次の例を参照)。

![Azure Portal で SSH 構成または資格情報をリセットする](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />SSH 構成をリセットする
SSH 構成をリセットするには、上のスクリーンショットのように **[モード]** セクションで `Reset configuration only` を選択してから **[更新]** を選択します。 この操作を完了したら、VM にもう一度アクセスしてみます。

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />ユーザーの SSH 資格情報をリセットする
既存のユーザーの資格情報をリセットするには、上のスクリーンショットのように **[モード]** セクションで `Reset SSH public key` または `Reset password` を選択します。 ユーザー名と、SSH キーまたは新しいパスワードを指定し、**[更新]** を選択します。

このメニューから、VM に対して sudo 特権を持つユーザーを作成することもできます。 新しいユーザー名と、関連付けられているパスワードまたは SSH キーを入力し、**[更新]** を選択します。

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />セキュリティ規則を確認する

[IP フロー検証](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)を使用して、ネットワーク セキュリティ グループ規則によって、仮想マシンから送受信されるトラフィックがブロックされていないかどうかを確認します。 有効なセキュリティ グループ規則を確認して、SSH ポート (既定では 22) に対して受信 "許可" NSG 規則が存在し、優先されていることを確認することもできます。 詳細については、「[有効なセキュリティ規則を使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-traffic-filter-problem.md)」を参照してください。

### <a name="check-routing"></a>ルーティングを確認する

Network Watcher の[次ホップ](../../network-watcher/network-watcher-check-next-hop-portal.md)機能を使用して、ルートが仮想マシンとの間でトラフィックのルーティングを妨げていないことを確認します。 有効なルートを見直し、ネットワーク インターフェイスのすべての有効なルートを確認することもできます。 詳細については、「[有効なルートを使用した VM トラフィック フローのトラブルシューティング](../../virtual-network/diagnose-network-routing-problem.md)」を参照してください。

## <a name="use-the-azure-vm-serial-console"></a>Azure VM シリアル コンソールの使用
[Azure VM シリアル コンソール](./serial-console-linux.md)では、Linux 仮想マシンのテキスト ベースのコンソールにアクセスできます。 コンソールを使用して、対話型シェル内で SSH 接続のトラブルシューティングを行うことができます。 シリアル コンソールを使用するための[前提条件](./serial-console-linux.md#prerequisites)を満たしていることを確認し、次のコマンドを試して SSH 接続のトラブルシューティングをさらに行います。

### <a name="check-that-ssh-is-running"></a>SSH が実行されていることを確認する
次のコマンドを使用して、VM 上で SSH が実行されているかどうかを確認できます。
```
$ ps -aux | grep ssh
```
出力がある場合は、SSH が稼働しています。

### <a name="check-which-port-ssh-is-running-on"></a>SSH が実行されているポートの確認
次のコマンドを使用して、SSH が実行されているポートを確認できます。
```
$ sudo grep Port /etc/ssh/sshd_config
```
出力は次のようになります。
```
Port 22
```

## <a name="use-the-azure-cli"></a>Azure CLI の使用
まだインストールしていない場合は、最新の [Azure CLI](/cli/azure/install-az-cli2) をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにサインインします。

カスタム Linux ディスク イメージを作成してアップロードしている場合は、[Microsoft Azure Linux Agent](../extensions/agent-windows.md) のバージョン 2.0.5 以降がインストールされていることを確認します。 ギャラリー イメージを使用して作成された VM の場合、このアクセス拡張機能は既にインストールされ、自動的に構成されています。

### <a name="reset-ssh-configuration"></a>SSH 構成をリセットする
最初に SSH 構成を既定値にリセットして、VM 上の SSH サーバーを再起動します。 ユーザー アカウントの名前、パスワード、または SSH キーは変更されません。
次の例は、[az vm user reset-ssh](/cli/azure/vm/user) を使用して、`myResourceGroup` にある `myVM` という名前の VM で SSH 構成をリセットします。 実際の値を次のように使用します。

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
次の例は、[az vm user update](/cli/azure/vm/user) を使用して、`myResourceGroup` にある `myVM` という名前の VM で、`myUsername` の資格情報を `myPassword` に指定された値にリセットします。 実際の値を次のように使用します。

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

SSH キー認証を使用している場合は、特定のユーザーの SSH キーをリセットできます。 次の例では、**az vm access set-linux-user** を使用して、`myResourceGroup` 内の `myVM` という名前の VM で、`myUsername` という名前のユーザー用に `~/.ssh/id_rsa.pub` に保存された SSH キーを更新します。 実際の値を次のように使用します。

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>VMAccess 拡張機能を使用する
Linux 用の VM アクセス拡張機能は、実行するアクションを定義する json ファイルを読み取ります。これらのアクションには、SSHD のリセット、SSH キーのリセット、またはユーザーの追加が含まれます。 Azure CLI を使用して VMAccess 拡張機能を呼び出すこともできますが、json ファイルは、必要であれば複数の VM で再利用できます。 この方法では、特定のシナリオで呼び出すことができる json ファイルのリポジトリを作成できます。

### <a name="reset-sshd"></a>SSHD のリセット
次の内容を含む `settings.json` という名前のファイルを作成します。

```json
{
    "reset_ssh":"True"
}
```

Azure CLI を使用した後、json ファイルを指定して `VMAccessForLinux` 拡張機能を呼び出すことで SSHD 接続をリセットできます。 次の例は、[az vm extension set](/cli/azure/vm/extension) を使用して、`myResourceGroup` にある `myVM` という名前の VM で SSHD をリセットします。 実際の値を次のように使用します。

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
SSHD が正しく機能しているように思える場合は、特定のユーザーの資格情報をリセットできます。 ユーザーのパスワードをリセットするには、`settings.json` という名前のファイルを作成します。 次の例では、`myUsername` の資格情報を、`myPassword` に指定された値にリセットします。 次の行を、実際の値を使用して `settings.json` ファイルに入力します。

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

ユーザーの SSH キーをリセットするには、まず `settings.json` という名前のファイルを作成します。 次の例では、`myUsername` の資格情報を、`myResourceGroup` 内の `myVM` という名前の VM で `myPassword` に指定された値にリセットします。 次の行を、実際の値を使用して `settings.json` ファイルに入力します。

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

json ファイルを作成したら、Azure CLI でその json ファイルを指定して `VMAccessForLinux` 拡張機能を呼び出すことで、SSH ユーザーの資格情報をリセットします。 次の例では、`myResourceGroup` 内の `myVM` という名前の VM の資格情報をリセットします。 実際の値を次のように使用します。

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Azure クラシック CLI を使用する
まだインストールしていない場合は、[Azure クラシック CLI をインストールし、Azure サブスクリプションに接続します](../../cli-install-nodejs.md)。 次のコマンドを実行して、確実に Resource Manager モードを使用するようにします。

```azurecli
azure config mode arm
```

カスタム Linux ディスク イメージを作成してアップロードしている場合は、[Microsoft Azure Linux Agent](../extensions/agent-windows.md) のバージョン 2.0.5 以降がインストールされていることを確認します。 ギャラリー イメージを使用して作成された VM の場合、このアクセス拡張機能は既にインストールされ、自動的に構成されています。

### <a name="reset-ssh-configuration"></a>SSH 構成をリセットする
SSHD 構成自体が正しく構成されていない、またはサービスでエラーが発生した可能性があります。 SSHD をリセットして、SSH 構成自体が有効かを確認します。 最初に実行する必要があるトラブルシューティング手順は、SSHD をリセットすることです。

次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM の SSHD をリセットします。 実際の VM とリソース グループ名を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>ユーザーの SSH 資格情報をリセットする
SSHD が正しく機能しているように思える場合は、特定のユーザーのパスワードをリセットできます。 次の例では、`myUsername` の資格情報を、`myResourceGroup` 内の `myVM` という名前の VM で `myPassword` に指定された値にリセットします。 実際の値を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

SSH キー認証を使用している場合は、特定のユーザーの SSH キーをリセットできます。 次の例では、`myResourceGroup` 内の `myVM`という名前の VM で、`myUsername` という名前のユーザー用に `~/.ssh/id_rsa.pub` に保存された SSH キーを更新します。 実際の値を次のように使用します。

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />VM を再起動する
SSH 構成とユーザーの資格情報をリセットした場合、またはその際にエラーが発生した場合は、根本的なコンピューティングの問題に対処するために VM の再起動を試すことができます。

### <a name="azure-portal"></a>Azure ポータル
Azure portal を使用して VM を再起動するには、次の例のように、VM を選択して **[再起動]** を選択します。

![Azure Portal で VM を再起動する](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
次の例では、[az vm restart](/cli/azure/vm) を使用して、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再起動します。 実際の値を次のように使用します。

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure クラシック CLI
次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再起動します。 実際の値を次のように使用します。

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />VM を再デプロイする
Azure 内で VM を別のノードに再デプロイすると、基になるネットワーク問題を修正する場合があります。 VM の再デプロイについては、「[新しい Azure ノードへの仮想マシンの再デプロイ](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

> [!NOTE]
> この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
>
>

### <a name="azure-portal"></a>Azure ポータル
Azure Portal を使用して VM を再デプロイするには、VM を選択し、**[サポート + トラブルシューティング]** セクションまで下にスクロールします。 次の例のように、**[再デプロイ]** を選択します。

![Azure Portal で VM を再デプロイする](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
次の例では、[az vm redeploy](/cli/azure/vm) を使用して、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再デプロイします。 実際の値を次のように使用します。

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Azure クラシック CLI
次の例では、`myResourceGroup` という名前のリソース グループ内にある `myVM` という名前の VM を再デプロイします。 実際の値を次のように使用します。

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して作成された VM
クラシック デプロイ モデルを使用して作成された VM の一般的な SSH 接続エラーを解決するには、次の手順を試してください。 各手順を実行した後、VM に再接続してみてください。

* [Azure Portal](https://portal.azure.com) からリモート アクセスをリセットします。 Azure portal で VM を選択し、**[Reset Remote]\(リモートのリセット\)** を選択します。
* VM を再起動します。 [Azure portal](https://portal.azure.com) で VM を選択し、**[再起動]** を選択します。

* 仮想マシンを新しい Azure ノードに VM を再デプロイします。 VM の再デプロイ方法については、「[新しい Azure ノードへの仮想マシンの再デプロイ](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

    この操作を行うと、一時ディスクのデータが失われ、仮想マシンに関連付けられている動的 IP アドレスが更新されます。
* [Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](../linux/classic/reset-access-classic.md) の指示に従って、以下の操作を行います。

  * パスワードまたは SSH キーをリセットする
  * *sudo* ユーザー アカウントを作成する
  * SSH 構成をリセットします。
* プラットフォームの問題の有無について VM のリソースの状態を確認します。<br>
     VM を選択し、**[設定]** > **[正常性チェック]** まで下にスクロールします。

## <a name="additional-resources"></a>その他のリソース
* 上記の手順を実行しても VM に SSH 接続できない場合は、「[SSH の詳細なトラブルシューティング手順](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照して、問題を解決するための追加の手順を確認します。
* アプリケーションへのアクセスのトラブルシューティングに関する詳細については、「[Linux Azure 仮想マシンにおけるアプリケーション接続の問題のトラブルシューティング](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。
* クラシック デプロイ モデルを使用して作成された仮想マシンのトラブルシューティングの詳細については、 [Linux ベースの仮想マシンのパスワードまたは SSH をリセットする方法](../linux/classic/reset-access-classic.md)に関するページを参照してください。

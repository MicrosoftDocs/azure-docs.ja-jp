---
title: Azure の FreeBSD の概要
description: Azure での FreeBSD 仮想マシンの使用について説明します。
author: thomas1206
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/13/2017
ms.author: mimckitt
ms.openlocfilehash: 0451047cd0fa31d27553a33b0c9318e03ffa222e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558952"
---
# <a name="introduction-to-freebsd-on-azure"></a>Azure の FreeBSD の概要
この記事では、Azure での FreeBSD 仮想マシンの実行の概要を説明します。

## <a name="overview"></a>概要
Microsoft Azure の FreeBSD は、最新のサーバー、デスクトップ、組み込みプラットフォームを強化するために使用される先進のコンピューター オペレーティング システムです。

Microsoft Corporation では、Azure で利用可能な、[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) を事前構成した FreeBSD を用意しています。 現時点では、次のバージョンの FreeBSD がイメージとしてマイクロソフトから提供されています。

- Azure Marketplace の FreeBSD 10.4
- Azure Marketplace の FreeBSD 11.2
- Azure Marketplace の FreeBSD 12.0

エージェントは、初回使用時の VM のプロビジョニング (ユーザー名、パスワード、SSH キー、ホスト名など) や、選択的な VM 拡張機能の有効化などの操作で、FreeBSD VM と Azure ファブリック間の通信を担います。

FreeBSD の今後のバージョンについては、最新の機能に対応し、FreeBSD リリース エンジニアリング チームが最新版を公開したらすぐに提供することを目指しています。

### <a name="create-a-freebsd-vm-through-azure-cli-on-freebsd"></a>FreeBSD で Azure CLI を使用して FreeBSD VM を作成する
まず、FreeBSD コンピューターで次のコマンドを実行して、[Azure CLI](/cli/azure/get-started-with-azure-cli) をインストールする必要があります。

```bash 
curl -L https://aka.ms/InstallAzureCli | bash
```

Bash が FreeBSD コンピューターにインストールされていない場合は、インストールの前に次のコマンドを実行します。 

```bash
sudo pkg install bash
```

Python が FreeBSD コンピューターにインストールされていない場合は、インストールの前に次のコマンドを実行します。 

```bash
sudo pkg install python35
cd /usr/local/bin 
sudo rm /usr/local/bin/python 
sudo ln -s /usr/local/bin/python3.5 /usr/local/bin/python
```

インストール中に、`Modify profile to update your $PATH and enable shell/tab completion now? (Y/n)` が表示されます。 `y` と回答し、`a path to an rc file to update` として `/etc/rc.conf` を入力した場合、`ERROR: [Errno 13] Permission denied` が発生することがあります。 この問題を解決するには、`etc/rc.conf` ファイルに対する書き込み権限を現在のユーザーに与える必要があります。

これで、Azure にサインインし、FreeBSD VM を作成できます。 FreeBSD 11.0 VM を作成する例を以下に示します。 `--public-ip-address-dns-name` パラメーターを追加して、新しく作成されるパブリック IP に対するグローバルに一意な DNS 名を指定することもできます。 

```azurecli
az login 
az group create --name myResourceGroup --location eastus
az vm create --name myFreeBSD11 \
    --resource-group myResourceGroup \
    --image MicrosoftOSTC:FreeBSD:11.0:latest \
    --admin-username azureuser \
    --generate-ssh-keys
```

その後、上のデプロイで出力された IP アドレスを使用して、FreeBSD VM にサインインできます。 

```bash
ssh azureuser@xx.xx.xx.xx -i /etc/ssh/ssh_host_rsa_key
```   

## <a name="vm-extensions-for-freebsd"></a>FreeBSD の VM 拡張機能
FreeBSD でサポートされている VM 拡張機能を以下に示します。

### <a name="vmaccess"></a>VMAccess
[VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) 拡張機能では次のことが可能です。

* 元の sudo ユーザーのパスワードをリセットする。
* パスワードを指定して新しい sudo ユーザーを作成する。
* 指定したキーで公開ホスト キーを設定する。
* ホスト キーが指定されていない場合、VM のプロビジョニング時に指定された公開ホスト キーをリセットする。
* reset_ssh が true に設定されている場合に、SSH ポート (22) を開き、sshd_config を復元する。
* 既存のユーザーを削除する。
* ディスクをチェックする。
* 追加されたディスクを修復する。

### <a name="customscript"></a>CustomScript
[CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) 拡張機能では次のことが可能です。

* 指定した場合、Azure Storage または外部パブリック ストレージ (GitHub など) からカスタマイズされたスクリプトをダウンロードする。
* エントリ ポイント スクリプトを実行する。
* インライン コマンドをサポートする。
* シェル スクリプトと Python スクリプトで Windows 形式の改行を自動的に変換する。
* シェル スクリプトと Python スクリプトで BOM を自動的に削除する。
* CommandToExecute で機密データを保護する。

> [!NOTE]
> FreeBSD VM は、現在のところ CustomScript バージョン 1.x のみをサポートしています。  

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>認証: ユーザー名、パスワード、SSH キー
Azure Portal を使用して FreeBSD 仮想マシンを作成するときに、ユーザー名、パスワード、または SSH 公開キーを入力する必要があります。
Azure に FreeBSD 仮想マシンをデプロイするためのユーザー名は、仮想マシン ("root" など) に既に存在するシステム アカウント (UID は 100 未満) の名前とは同じにしないでください。
現在サポートされているのは、RSA SSH キーだけです。 複数行の SSH キーは、`---- BEGIN SSH2 PUBLIC KEY ----`で始まり、`---- END SSH2 PUBLIC KEY ----`で終わる必要があります。

## <a name="obtaining-superuser-privileges"></a>スーパーユーザー権限の取得
Azure での仮想マシン インスタンスをデプロイする際に指定したユーザー アカウントは、特権を持つアカウントです。 公開済みの FreeBSD イメージには、sudo のパッケージがインストールされています。
このユーザー アカウントを使用してログインすると、コマンド構文を使用して、root としてコマンドを実行できます。

```
$ sudo <COMMAND>
```

`sudo -s` を使用して root シェルを取得することもできます。

## <a name="known-issues"></a>既知の問題
[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) バージョン 2.2.2 については、Azure 上の FreeBSD VM に対してプロビジョニング エラーが発生するという[既知の問題](https://github.com/Azure/WALinuxAgent/pull/517)が確認されています。 修正プログラムは、[Azure VM Guest Agent](https://github.com/Azure/WALinuxAgent/) バージョン 2.2.3 以降のリリースによってキャプチャされました。 

## <a name="next-steps"></a>次のステップ
* [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/thefreebsdfoundation.freebsd-12_2?tab=Overview) に移動して、FreeBSD VM を作成します。

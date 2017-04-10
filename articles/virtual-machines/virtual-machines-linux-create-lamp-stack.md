---
title: "Azure の Linux 仮想マシンへの LAMP のデプロイ | Microsoft Docs"
description: "Azure の Linux VM に LAMP スタックをインストールする方法を説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: jluk
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 6c12603a-e391-4d3e-acce-442dd7ebb2fe
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: NA
ms.topic: article
ms.date: 2/21/2017
ms.author: juluk
translationtype: Human Translation
ms.sourcegitcommit: 61e8824fe1a63efb215c7be25d3e6d7c1649d8a5
ms.openlocfilehash: 3709add7fd8d97138fd858e2b260c23300dad378
ms.lasthandoff: 02/27/2017


---
# <a name="deploy-lamp-stack-on-azure"></a>Azure への LAMP スタックのデプロイ
この記事では、Apache Web サーバー、MySQL、PHP (LAMP スタック) を Azure にデプロイする方法について説明します。 Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/)) と [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2) が必要です。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-create-lamp-stack-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

## <a name="quick-command-summary"></a>クイック コマンドの概要

1. [azuredeploy.parameters.json ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) をローカル コンピューターに保存し、このファイルを編集して希望の設定に変更します。
2. 次の&2; つのコマンドを実行して、リソース グループを作成し、テンプレートをデプロイします。

```azurecli
az group create -l westus -n myResourceGroup
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

### <a name="deploy-lamp-on-existing-vm"></a>既存の VM に LAMP をデプロイする
次のコマンドを使用してパッケージを更新し、Apache、MySQL、PHP をインストールします。

```bash
sudo apt-get update
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>新しい VM に LAMP をデプロイするチュートリアル

1. [az group create](/cli/azure/group#create) を使用して、新しい VM を含めるリソース グループを作成します。

```azurecli
az group create -l westus -n myResourceGroup
```
VM 自体を作成するには、 [GitHub のこちら](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app)にある既に作成済みの Azure Resource Manager テンプレートを使用できます。

2. [azuredeploy.parameters.json ファイル](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.parameters.json) をローカル コンピューターに保存します。
3. **azuredeploy.parameters.json** ファイルを編集して、希望の入力値に変更します。
4. [az group deployment create] を使用して、ダウンロードした json ファイルを参照するテンプレートをデプロイします。

```azurecli
az group deployment create -g myResourceGroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json \
    --parameters @filepathToParameters.json
```

出力は次の例のようになります。

```json
{
"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Resources/deployments/azuredeploy",
"name": "azuredeploy",
"properties": {
    "correlationId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "debugSetting": null,
}
...
"provisioningState": "Succeeded",
"template": null,
"templateLink": {
    "contentVersion": "1.0.0.0",
    "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json"
    },
    "timestamp": "2017-02-22T00:05:51.860411+00:00"
},
"resourceGroup": "myResourceGroup"
}
```

これで、LAMP が既にインストールされている Linux VM が作成されました。 必要に応じて、「[LAMP が正常にインストールされていることを確認する](#verify-lamp-successfully-installed)」に移動して、インストールを確認できます。

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>既存の VM に LAMP をデプロイするチュートリアル
Linux VM の作成についてサポートが必要な場合は、Linux VM を作成する方法を[こちら](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-quick-create-cli)で確認します。 次に、Linux VM に SSH 接続する必要があります。 SSH キーの作成についてサポートが必要な場合は、Linux または Mac で SSH キーを作成する方法を[こちら](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)で確認します。
SSH キーが既にある場合は、先に進み、`ssh azureuser@mypublicdns.westus.cloudapp.azure.com` を使用してコマンド ラインから Linux VM に SSH 接続します。

現在、Linux VM 内で作業しているので、Debian ベースのディストリビューションに LAMP スタックをインストールする方法を説明します。 Linux ディストリビューションによって、コマンドが微妙に異なる場合があります。

#### <a name="installing-on-debianubuntu"></a>Debian または Ubuntu へのインストール
`apache2`、`mysql-server`、`php5`、`php5-mysql` の各パッケージをインストールする必要があります。 これらのパッケージをインストールするには、パッケージを直接取得するか、Tasksel を使用します。
インストールする前に、パッケージの一覧をダウンロードして更新する必要があります。

```bash
sudo apt-get update
```

##### <a name="individual-packages"></a>個々のパッケージ
apt-get の使用

```bash
sudo apt-get install apache2 mysql-server php5 php5-mysql
```

##### <a name="using-tasksel"></a>Tasksel の使用
または、Tasksel をダウンロードすることができます。これは、複数の関連パッケージを一連のタスクとしてシステムにインストールするための、Debian/Ubuntu ツールです。

```bash
sudo apt-get install tasksel
sudo tasksel install lamp-server
```

上記のいずれかのオプションを実行すると、これらのパッケージと他のさまざまな依存関係をインストールするよう求められます。 MySQL の管理パスワードを設定するには、y キー、Enter キーの順に押して続行し、他のプロンプトに従います。 このプロセスにより、MySQL で PHP を使用するための必要最小限の PHP 拡張機能がインストールされます。 

![][1]

パッケージとして利用可能な他の PHP 拡張機能を表示するには、下記のコマンドを実行します。

```bash
apt-cache search php5
```

#### <a name="create-infophp-document"></a>info.php ドキュメントを作成する
コマンドラインに `apache2 -v`、`mysql -v`、または `php -v` を入力して、使用している Apache、MySQL、PHP のバージョンを確認できるようになりました。

さらにテストしたい場合は、簡単な PHP 情報ページを作成して、ブラウザーで表示できます。 Nano テキスト エディターで次のコマンドを使用してファイルを作成します。

```bash
sudo nano /var/www/html/info.php
```

GNU Nano テキスト エディターに次の行を追加します。

```php
<?php
phpinfo();
?>
```

保存してテキスト エディターを終了します。

すべての新規インストールを有効にするために、次のコマンドを使用して Apache を再起動します。

```bash
sudo service apache2 restart
```

## <a name="verify-lamp-successfully-installed"></a>LAMPが正常にインストールされていることを確認する
作成した PHP 情報ページを確認するには、ブラウザーを開き、http://youruniqueDNS/info.php に移動します。 次のようなページが表示されます。

![][2]

http://youruniqueDNS/ に移動して Apache2 Ubuntu の既定のページを表示することで、Apache インストールを確認できます。 出力は次の例のようになります。

![][3]

これで、Azure VM 上に LAMP スタックを設定できました。

## <a name="next-steps"></a>次のステップ
LAMP スタックで Ubuntu のドキュメントを確認します。

* [https://help.ubuntu.com/community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png


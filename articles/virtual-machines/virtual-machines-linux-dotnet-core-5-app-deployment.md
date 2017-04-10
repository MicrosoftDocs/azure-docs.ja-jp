---
title: "仮想マシン拡張機能でアプリケーションのデプロイを自動化する | Microsoft Docs"
description: "Azure Virtual Machines DotNet Core チュートリアル"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 9fc8b1ba-60f5-410b-8190-9f1ff885e50e
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2016
ms.author: nepeters
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 031943c4d7b7736d249b507e2e59f0cc9896a54e
ms.lasthandoff: 03/03/2017


---
# <a name="application-deployment-with-azure-resource-manager-templates-for-linux-vms"></a>Linux VM での Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ

すべての Azure インフラストラクチャの要件を特定し、デプロイ テンプレートに変換したら、実際のアプリケーションのデプロイに対処する必要があります。 ここでは、アプリケーションのデプロイは、実際のアプリケーション バイナリを Azure リソースにインストールすることを指します。 ミュージック ストア サンプルでは、.Net Core、NGINX、および Supervisor を各仮想マシンにインストールして構成する必要があります。 ミュージック ストアのバイナリを仮想マシンにインストールし、ミュージック ストア データベースを事前に作成する必要があります。

このドキュメントでは、仮想マシン拡張機能で Azure 仮想マシンへのアプリケーションのデプロイと構成を自動化する方法について説明します。 すべての依存関係と固有の構成に焦点を当てます。 最善の結果を得るために、ソリューションのインスタンスを Azure サブスクリプションに事前にデプロイし、Azure Resource Manager テンプレートを手元に用意して取り組んでください。 完全なテンプレートは、こちら ( [Ubuntu のミュージック ストア デプロイ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)) にあります。

## <a name="configuration-script"></a>構成スクリプト
仮想マシン拡張機能は、仮想マシンに対して実行される特殊なプログラムで、構成を自動化します。 拡張機能は、ウイルス対策、ログの構成、Docker の構成など、特定のさまざまな目的に使うことができます。 カスタム スクリプト拡張機能を使うと、仮想マシンに対して任意のスクリプトを実行できます。 ミュージック ストア サンプルでは、カスタム スクリプト拡張機能によって、Ubuntu 仮想マシンが構成され、ミュージック ストア アプリケーションがインストールされます。 

Azure Resource Manager テンプレートで仮想マシン拡張機能を宣言する方法を説明する前に、実行されるスクリプトを確認します。 このスクリプトでは、ミュージック ストア アプリケーションをホストする Ubuntu 仮想マシンを構成します。 スクリプトを実行すると、必要なソフトウェアがすべてインストールされ、ソース管理からミュージック ストア アプリケーションがインストールされ、データベースが準備されます。 

Linux で .Net Core アプリケーションをホストする方法について詳しくは、「 [Publish to a Linux Production Environment (Linux 運用環境への発行)](https://docs.asp.net/en/latest/publishing/linuxproduction.html)」をご覧ください。

> このサンプルは、デモンストレーション用です。
> 
> 

```bash
#!/bin/bash

# install dotnet core
sudo sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/dotnet-release/ trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
sudo apt-get update
sudo apt-get install -y dotnet-dev-1.0.0-preview2-003121

# download application
sudo wget https://raw.github.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/music-store-azure-demo-pub.tar /
sudo mkdir /opt/music
sudo tar -xf music-store-azure-demo-pub.tar -C /opt/music

# install nginx, update config file
sudo apt-get install -y nginx
sudo service nginx start
sudo touch /etc/nginx/sites-available/default
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/nginx-config/default -O /etc/nginx/sites-available/default
sudo cp /opt/music/nginx-config/default /etc/nginx/sites-available/
sudo nginx -s reload

# update and secure music config file
sed -i "s/<replaceserver>/$1/g" /opt/music/config.json
sed -i "s/<replaceuser>/$2/g" /opt/music/config.json
sed -i "s/<replacepass>/$3/g" /opt/music/config.json
sudo chown $2 /opt/music/config.json
sudo chmod 0400 /opt/music/config.json

# config supervisor
sudo apt-get install -y supervisor
sudo touch /etc/supervisor/conf.d/music.conf
sudo wget https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/music-app/supervisor/music.conf -O /etc/supervisor/conf.d/music.conf
sudo service supervisor stop
sudo service supervisor start

# pre-create music store database
/usr/bin/dotnet /opt/music/MusicStore.dll &
```

## <a name="vm-script-extension"></a>VM スクリプト拡張機能
VM 拡張機能をビルド時に仮想マシンに対して実行するには、拡張機能リソースを Azure Resource Manager テンプレートに含めます。 拡張機能を追加するには、Visual Studio のリソースの追加ウィザードを使うか、有効な JSON をテンプレートに挿入します。 スクリプト拡張機能リソースは仮想マシン リソース内に入れ子になっています。これは、次の例で確認できます。

Resource Manager テンプレート内の JSON サンプルを確認するには、こちらのリンク ( [VM スクリプト拡張機能](https://github.com/Microsoft/dotnet-core-sample-templates/blob/master/dotnet-core-music-linux/azuredeploy.json#L359)) をご覧ください。 

以下の JSON では、スクリプトが GitHub に格納されていることに注目してください。 このスクリプトは、Azure Blob Storage に格納することもできます。 また、Azure Resource Manager テンプレートでは、スクリプト実行文字列を作成して、テンプレート パラメーター値をスクリプト実行のパラメーターとして使うこともできます。 その場合は、テンプレートのデプロイ時にデータを指定し、スクリプトの実行時にこれらの値を使うことができます。

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

カスタム スクリプト拡張機能の使用について詳しくは、 [Resource Manager テンプレートでのカスタム スクリプト拡張機能](virtual-machines-linux-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

## <a name="next-step"></a>次のステップ
<hr>

[その他の Azure Resource Manager テンプレートを確認する](https://github.com/Azure/azure-quickstart-templates)



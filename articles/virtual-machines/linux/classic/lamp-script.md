---
title: "Linux VM での CustomScript 拡張機能の使用 | Microsoft Docs"
description: "CustomScript 拡張機能を使って、クラシック デプロイ モデルを使用して作成された Azure 上の Linux Virtual Machines にアプリケーションをデプロイする方法について説明します。"
editor: tysonn
manager: timlt
documentationcenter: 
services: virtual-machines-linux
author: gbowerman
tags: azure-service-management
ms.assetid: e535241d-feca-4412-b07a-67c936ba88a0
ms.service: virtual-machines-linux
ms.workload: multiple
ms.tgt_pltfrm: linux
ms.devlang: na
ms.topic: article
ms.date: 09/13/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 3c1877f0dd560894099fb7c50c30846c2cc720b3
ms.lasthandoff: 03/27/2017


---
# <a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Linux 向けに Azure カスタム スクリプト拡張機能を使って LAMP アプリをデプロイする
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した LAMP スタックのデプロイ方法については、[こちら](../../virtual-machines-linux-create-lamp-stack.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)をご覧ください。

Linux 向け Microsoft Azure カスタム スクリプト拡張機能では、Python や Bash など、仮想マシン (VM) でサポートされているスクリプト言語で記述された任意のコードを実行する VM をカスタマイズすることができます。 これによって、非常に柔軟に複数のマシンにアプリケーションを自動的にデプロイすることが可能になります。

カスタム スクリプト拡張機能は、Azure クラシック ポータル、Windows PowerShell、Azure コマンド ライン インターフェイス (Azure CLI) を使ってデプロイできます。

この記事では、Azure CLI を使って、クラシック デプロイ モデルを使用して作成された Ubuntu VM に単純な LAMP アプリケーションをデプロイします。

## <a name="prerequisites"></a>前提条件
この例では、まず、Ubuntu 14.04 以降を実行している 2 つの Azure VM を作成します。 VM の名前は *script-vm* と *lamp-vm* です。 自分で VM を作成するときは、固有の名前を付けてください。 1 つは CLI コマンドの実行に使用し、もう 1 つは LAMP アプリのデプロイに使用します。

また、Azure ストレージ アカウントとアカウントにアクセスするキーが必要になります (Azure クラシック ポータルで取得できます)。

Azure での Linux VM の作成については、「[Linux を実行する仮想マシンの作成](createportal.md)」をご覧ください。

インストール コマンドでは Ubuntu が想定されますが、サポートされる Linux ディストリビューションに応じてインストールを調整できます。

仮想マシン script-vm では、Azure に接続できる Azure CLI がインストールされていることが必要です。 詳細については、 [Azure コマンド ライン インターフェイスのインストールと構成](../../../cli-install-nodejs.md)に関するページを参照してください。

## <a name="upload-a-script"></a>スクリプトのアップロード
ここでは、カスタム スクリプト拡張機能を使ってリモート VM でスクリプトを実行し、LAMP スタックをインストールして PHP ページを作成します。 Azure BLOB のようにアップロードすれば、どこからでもスクリプトにアクセスできます。

### <a name="script-overview"></a>スクリプトの概要
スクリプトの例では、LAMP スタックを Ubuntu にインストールし (MySQL のサイレント インストールの設定を含む)、簡単な PHP ファイルを記述して Apache を起動します。

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>スクリプトのアップロード
スクリプトを *install_lamp.sh* などとしてテキスト ファイルに保存し、Azure Storage にアップロードします。 Azure CLI を使えば簡単にできます。 次の例では、ファイルを「scripts」という名前のストレージ コンテナーにアップロードします。 コンテナーが存在しない場合は、まずコンテナーを作成する必要があります。

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

また、Azure Storage からスクリプトをダウンロードする方法を記述した JSON ファイルも作成します。 *public_config.json* という名前でこのファイルを保存します (「mystorage」をご利用のストレージ アカウントの名前に置き換えてください)。

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>拡張機能のデプロイ
次のコマンドを使用して、Azure CLI を使ってリモート VM に Linux カスタム スクリプト拡張機能をデプロイします。

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

前のコマンドによって、*install_lamp.sh* スクリプトが *lamp-vm* という名前の VM にダウンロードされて実行されます。

アプリに Web サーバーが含まれる場合は、次のコマンドを使用して、リモート VM で HTTP リスニング ポートが開かれていることを確認してください。

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング
リモート VM 上のログ ファイルを確認し、カスタム スクリプトの実行状況を確認できます。 次のコマンドを使用して、 *lamp-vm* に SSH で通信し、ログ ファイルの内容を確認します。

    cd /var/log/azure/customscript
    tail -f handler.log

CustomScript 拡張機能を実行すると、参照用に作成した PHP ページを参照できます。 たとえば、この記事の PHP ページは *http://lamp-vm.cloudapp.net/phpinfo.php* です。

## <a name="additional-resources"></a>その他のリソース
同じ基本的な手順で、より複雑なアプリをデプロイすることも可能です。 この例では、インストール スクリプトは Azure Storage のパブリック BLOB として保存されています。 インストール スクリプトを [Shared Access Signature](https://msdn.microsoft.com/library/azure/ee395415.aspx) (SAS) を使ったセキュアな BLOB として保存すると、より安全です。

次に、Azure CLI、Linux、およびカスタム スクリプト拡張機能のその他のリソースをリストします。

[Linux VM カスタム タスクをカスタム スクリプト拡張機能を使って自動化する](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Azure Linux 拡張機能 (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Azure での Linux とオープン ソース コンピューティング](../../virtual-machines-linux-opensource-links.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



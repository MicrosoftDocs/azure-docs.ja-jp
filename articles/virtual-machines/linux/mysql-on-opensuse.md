---
title: Azure で OpenSUSE VM に MySQL をインストールする | Microsoft Docs
description: Azure 上の OpenSUSE Linux 仮想マシンに MySQL をインストールする方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1594e10e-c314-455a-9efb-a89441de364b
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: baa52f4a17b06b6927013c88f37d4f2bc24744f3
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876013"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>Azure 上で OpenSUSE Linux を実行する仮想マシンへの MySQL のインストール

[MySQL](https://www.mysql.com) は広く普及しているオープン ソースの SQL データベースです。 このチュートリアルでは、OpenSUSE Linux を実行する仮想マシンを作成してから、MySQL をインストールする方法を説明します。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>OpenSUSE Linux を実行する仮想マシンの作成

まず、リソース グループを作成します。 この例では、リソース グループは "*mySQSUSEResourceGroup*" という名前で、"*米国東部*" リージョンに作成します。

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

VM を作成します。 この例では、VM は "*myVM*" という名前、VM サイズは "*Standard_D2s_v3*" ですが、ユーザーが実際のワークロードに最適であると考える [VM サイズ](sizes.md)を選ぶ必要があります。

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

また、ネットワーク セキュリティ グループに規則を追加して、MySQL のポート 3306 経由のトラフィックを許可する必要があります。

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>VM に接続します

VM に接続するには SSH を使います。 この例では、VM の パブリック IP アドレスは *10.111.112.113* です。 IP アドレスは、VM を作成したときの出力で確認できます。

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>VM を更新する
 
VM に接続した後、システムの更新プログラムと修正プログラムをインストールします。 
   
```bash
sudo zypper update
```

プロンプトに従って VM を更新します。

## <a name="install-mysql"></a>MySQL をインストールする 


SSH 経由で VM に MySQL をインストールします。 必要に応じてプロンプトに応答します。

```bash
sudo zypper install mysql
```
 
システム起動時に MySQL が開始するように設定します。 

```bash
sudo systemctl enable mysql
```
MySQL が有効になっていることを確認します。

```bash
systemctl is-enabled mysql
```

このコマンドで、enabled が返る必要があります。

サーバーを再起動します。

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL のパスワード

インストール後、既定では MySQL ルート パスワードは空になっています。 MySQL をセキュリティで保護するには、**mysql\_secure\_installation** スクリプトを実行します。 スクリプトを実行すると、MySQL ルート パスワードの変更、匿名のユーザー アカウントの削除、リモート ルート サインインの無効化、テスト データベースの削除、および権限テーブルの再読み込みを行うように求められます。 

サーバーを再起動したら、もう一度 VM に対して ssh を実行します。

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>MySQL にサインインする

サインインして、MySQL プロンプトに入れるようになりました。

```bash  
mysql -u root -p
```
MySQL のプロンプトに切り替わり、 SQL ステートメントを発行してデータベースとやり取りすることができます。

新しい MySQL ユーザーを作成します。

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
行末のセミコロン (;) は、コマンドの終わりを示すために必要です。


## <a name="create-a-database"></a>データベースを作成する


データベースを作成し、`mysqluser` ユーザーにアクセス許可を付与します。

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
データベースのユーザー名とパスワードは、データベースに接続するスクリプトのみが使います。  データベース ユーザー アカウントは、システム上の実際のユーザー アカウントを表しているとは限りません。

別のコンピューターからのサインインを有効にします。 この例では、サインインが許可された元のコンピューターの IP アドレスは "*10.112.113.114*" です。

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
MySQL データベース管理ユーティリティを終了するには、次のように入力します。

```    
quit
```


## <a name="next-steps"></a>次の手順
MySQL について詳しくは、[MySQL のドキュメント](https://dev.mysql.com/doc)をご覧ください。





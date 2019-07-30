---
title: Azure 上の Windows VM に MongoDB をインストールする | Microsoft Docs
description: Windows Server 2012 R2 を実行している、Resource Manager デプロイ モデルで作成された Azure VM に、MongoDB をインストールする方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 53faf630-8da5-4955-8d0b-6e829bf30cba
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 1436eadace2ff57bde9d67201d6b38d4aee8f523
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722667"
---
# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Azure の Windows VM に MongoDB をインストールして構成する
[MongoDB](https://www.mongodb.org) は、高いパフォーマンスを特徴とし、広く普及しているオープン ソースの NoSQL データベースです。 この記事では、Azure の Windows Server 2016 仮想マシン (VM) での MongoDB のインストールと構成について説明します。 [Azure の Linux VM に MongoDB をインストールする](../linux/install-mongodb.md)こともできます。

## <a name="prerequisites"></a>前提条件
MongoDB をインストールして構成する前に、VM を作成し、できればそれにデータ ディスクを追加する必要があります。 VM を作成し、データ ディスクを追加するには、以下の記事を参照してください。

* [Azure Portal](quick-create-portal.md) または [Azure PowerShell](quick-create-powershell.md) を使用して Windows Server VM を作成します。
* [Azure Portal](attach-managed-disk-portal.md) または [Azure PowerShell](attach-disk-ps.md) を使用して Windows Server VM にデータ ディスクを接続します。

MongoDB のインストールと構成を開始するには、リモート デスクトップを使用して [Windows Server VM にログオン](connect-logon.md)してください。

## <a name="install-mongodb"></a>MongoDB のインストール
> [!IMPORTANT]
> 認証、IP アドレス バインドなどの MongoDB セキュリティ機能は既定では有効になっていません。 MongoDB を運用環境に展開する前に、セキュリティ機能を有効にすることをお勧めします。 詳細については、[MongoDB のセキュリティと認証](https://www.mongodb.org/display/DOCS/Security+and+Authentication)に関するページを参照してください。


1. リモート デスクトップを使用して VM に接続したら、タスク バーから Internet Explorer を開きます。
2. Internet Explorer を初めて開くときは **[お勧めのセキュリティ、プライバシー、互換性の設定を使う]** を選択し、 **[OK]** をクリックします。
3. Internet Explorer の強化されたセキュリティ構成は、既定で有効になっています。 MongoDB Web サイトを、許可されたサイトの一覧に追加します。
   
   * 右上隅にある **[ツール]** アイコンを選択します。
   * **[インターネット オプション]** で、 **[セキュリティ]** タブ、 **[信頼済みサイト]** アイコンの順に選択します。
   * **[サイト]** ボタンをクリックします。 信頼済みサイトの一覧に *https://\*.mongodb.com* を追加し、ダイアログ ボックスを閉じます。
     
     ![Internet Explorer のセキュリティ設定の構成](./media/install-mongodb/configure-internet-explorer-security.png)
4. [MongoDB のダウンロード](https://www.mongodb.com/downloads) ページ (https://www.mongodb.com/downloads) ) を参照します。
5. 必要に応じて、**Community Server** エディションを選んでから、"*Windows Server 2008 R2 64 ビット以降*" 用の最新の安定版リリースを選びます。 インストーラーをダウンロードするには、 **[DOWNLOAD (msi) (ダウンロード (msi))]** をクリックします。
   
    ![MongoDB インストーラーのダウンロード](./media/install-mongodb/download-mongodb.png)
   
    ダウンロードが完了したら、インストーラーを実行します。
6. 使用許諾契約を読み、同意します。 メッセージが表示されたら、 **[Complete (完全)]** インストールを選択します。
7. 必要に応じて、MongoDB のグラフィカル インターフェイスである Compass をインストールすることもできます。
8. 最後の画面で **[Install (インストール)]** をクリックします。

## <a name="configure-the-vm-and-mongodb"></a>VM と MongoDB の構成
1. path 変数は、MongoDB インストーラーでは更新されません。 path 変数に MongoDB の `bin` の場所が指定されていない場合は、MongoDB 実行可能ファイルを使用するたびに完全パスを指定する必要があります。 path 変数に場所を追加する方法は、次のとおりです。
   
   * **[スタート]** メニューを右クリックし、 **[システム]** を選択します。
   * **[システムの詳細設定]** をクリックし、 **[環境変数]** をクリックします。
   * **[システム変数]** で、 **[Path]** 、 **[編集]** の順にクリックします。
     
     ![PATH 変数の構成](./media/install-mongodb/configure-path-variables.png)
     
     MongoDB の `bin` フォルダーへのパスを追加します。 MongoDB は、通常は *C:\Program Files\MongoDB* にインストールされます。 自分の VM でのインストール パスを確認してください。 次の例では、`PATH` 変数に MongoDB の既定のインストール場所を追加しています。
     
     ```
     ;C:\Program Files\MongoDB\Server\3.6\bin
     ```
     
     > [!NOTE]
     > `PATH` 変数に場所を追加していることを示すために、先頭にセミコロン (`;`) を追加してください。

2. データ ディスクに MongoDB データ ディレクトリとログ ディレクトリを作成します。 **[スタート]** メニューの **[コマンド プロンプト]** を選択します。 次の例では、ドライブ F: にディレクトリを作成しています。
   
    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```
3. 次のコマンドで、MongoDB インスタンスを開始します。データ ディレクトリとログ ディレクトリへのパスは、適宜調整してください。
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```
   
    MongoDB がジャーナル ファイルを割り当て、接続のリッスンを開始するまでには、数分かかる場合があります。 `mongod.exe` サーバーが開始され、ジャーナル ファイルを割り当てると、すべてのログ メッセージが *F:\MongoLogs\mongolog.log* ファイルにダイレクトされます。
   
   > [!NOTE]
   > MongoDB インスタンスが実行している間は、コマンド プロンプトのフォーカスがこのタスクに設定された状態になります。 MongoDB を実行し続けるためには、コマンド プロンプト ウィンドウを開いたままにしておく必要があります。 ただし、サービスとして MongoDB をインストールすれば、その必要はありません。詳しい手順については、以下で説明します。

4. MongoDB のエクスペリエンスをより堅牢にするには、`mongod.exe` をサービスとしてインストールします。 サービスを作成すると、MongoDB を使用するたびにコマンド プロンプトを実行したままにする必要がなくなります。 サービスは、次のようにして作成します。データ ディレクトリとログ ディレクトリへのパスは、適宜調整してください。
   
    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install
    ```
   
    上のコマンドにより、MongoDB という名前のサービスが、"Mongo DB" という説明付きで作成されます。 以下のパラメーターも指定されます。
   
   * `--dbpath` オプションは、データ ディレクトリの場所を指定します。
   * 実行中のサービスには出力を表示するコマンド ウィンドウがないので、`--logpath` オプションを使用して、ログ ファイルを指定する必要があります。
   * `--logappend` オプションを指定すると、サービスを再起動することで、既存のログ ファイルに出力が追加されるようになります。
   
   MongoDB サービスを開始するには、次のコマンドを実行します。
   
    ```
    net start MongoDB
    ```
   
    MongoDB サービスの作成の詳細については、「[Configure a Windows Service for MongoDB (MongoDB 用の Windows サービスの構成)](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service)」を参照してください。

## <a name="test-the-mongodb-instance"></a>MongoDB インスタンスのテスト
MongoDB を 1 つのインスタンスとして実行するか、サービスとしてインストールしたら、データベースの作成と使用を開始できます。 MongoDB 管理シェルを開始するには、 **[スタート]** メニューからもう 1 つコマンド プロンプト ウィンドウを開き、次のコマンドを入力します。

```
mongo  
```

`db` コマンドを使用すると、データベースを一覧表示できます。 データを挿入するには、次のようにします。

```
db.foo.insert( { a : 1 } )
```

データを検索するには、次のようにします。

```
db.foo.find()
```

出力は次の例のようになります。

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

`mongo` コンソールを終了するには、次のようにします。

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>ファイアウォールおよびネットワーク セキュリティ グループの規則の構成
これで MongoDB がインストールされました。この MongoDB は現在実行されています。次は MongoDB にリモート接続するために、Windows ファイアウォールのポートを開きます。 新しい受信規則を作成して TCP ポート 27017 を許可するには、管理 PowerShell プロンプトを開き、次のコマンドを入力します。

```powerahell
New-NetFirewallRule `
    -DisplayName "Allow MongoDB" `
    -Direction Inbound `
    -Protocol TCP `
    -LocalPort 27017 `
    -Action Allow
```

**セキュリティが強化された Windows ファイアウォール**のグラフィカル管理ツールを使用して、規則を作成することもできます。 TCP ポート 27017 を許可する新しい受信規則を作成します。

必要に応じて、既存の Azure 仮想ネットワーク サブネットの外部から MongoDB にアクセスすることを許可するネットワーク セキュリティ グループの規則を作成します。 ネットワーク セキュリティ グループの規則を作成するには、[Azure Portal](nsg-quickstart-portal.md) または [Azure PowerShell](nsg-quickstart-powershell.md) を使用します。 Windows ファイアウォール規則と同様に、TCP ポート 27017 から MongoDB VM の仮想ネットワーク インターフェイスへの接続を許可します。

> [!NOTE]
> TCP ポート 27017 は、MongoDB によって使用される既定のポートです。 このポートを変更するには、`mongod.exe` サービスを手動で開始するかサービスから開始するときに、`--port` パラメーターを使用します。 ポートを変更した場合には、前の手順の Windows ファイアウォールとネットワーク セキュリティ グループの規則を更新してください。


## <a name="next-steps"></a>次の手順
このチュートリアルでは、Windows VM に MongoDB をインストールして構成する方法を学習しました。 これで、[MongoDB のドキュメント](https://docs.mongodb.com/manual/)の高度なトピックに従って、Windows VM の MongoDB にアクセスできます。


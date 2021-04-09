---
title: Azure VM の接続の構成
titleSuffix: Azure SQL Managed Instance
description: Azure 仮想マシンから SQL Server Management Studio を使用して、Azure SQL Managed Instance に接続することができます。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, srbozovi, bonova
ms.date: 02/18/2019
ms.openlocfilehash: a1f496d59fa626dc8750493591128f7363afa40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91620258"
---
# <a name="quickstart-configure-an-azure-vm-to-connect-to-azure-sql-managed-instance"></a>クイック スタート:Azure SQL Managed Instance に接続するように Azure VM を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

このクイックスタートでは、SQL Server Management Studio (SSMS) を使用して、Azure SQL Managed Instance に接続するように Azure 仮想マシンを構成する方法について説明します。 


ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターから接続する方法を示すクイックスタートについては、代わりに[ポイント対サイト接続の構成](point-to-site-p2s-configure.md)に関するページをご覧ください。

## <a name="prerequisites"></a>前提条件

このクイックスタートでは、[マネージド インスタンスの作成](instance-create-quickstart.md)に関するページで作成したリソースを出発点として使用します。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com/) にサインインします。

## <a name="create-a-new-subnet-vnet"></a>新しいサブネット VNet の作成

次の手順では、Azure 仮想マシンがマネージド インスタンスに接続できるよう、SQL Managed Instance VNet に新しいサブネットを作成します。 この SQL Managed Instance サブネットは、マネージド インスタンス専用です。 このサブネットに他のリソース (Azure 仮想マシンなど) を作成することはできません。

1. [マネージド インスタンスの作成](instance-create-quickstart.md)に関するクイック スタートで作成したマネージド インスタンスのリソース グループを開きます。 お使いのマネージド インスタンス用の仮想ネットワークを選択します。

   ![SQL Managed Instance のリソース](./media/connect-vm-instance-configure/resources.png)

2. **[サブネット]** を選択し、 **[+ サブネット]** を選択して新しいサブネットを作成します。

   ![SQL Managed Instance のサブネット](./media/connect-vm-instance-configure/subnets.png)

3. 次の表の情報を参考にして、フォームに必要事項を入力します。

   | 設定| 推奨値 | 説明 |
   | ---------------- | ----------------- | ----------- |
   | **名前** | 有効な名前|有効な名前については、[名前付け規則と制限事項](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。|
   | **アドレス範囲 (CIDR ブロック)** | 有効な範囲 | このクイック スタートでは既定値で問題ありません。|
   | **ネットワーク セキュリティ グループ** | なし | このクイック スタートでは既定値で問題ありません。|
   | **ルート テーブル** | なし | このクイック スタートでは既定値で問題ありません。|
   | **サービス エンドポイント** | 0 が選択されています | このクイック スタートでは既定値で問題ありません。|
   | **サブネットの委任** | なし | このクイック スタートでは既定値で問題ありません。|

   ![クライアント VM 用の新しい SQL Managed Instance サブネット](./media/connect-vm-instance-configure/new-subnet.png)

4. **[OK]** を選択して、SQL Managed Instance VNet にこの追加のサブネットを作成します。

## <a name="create-a-vm-in-the-new-subnet"></a>新しいサブネットへの VM の作成 

以下の手順では、SQL Managed Instance に接続する新しいサブネットに仮想マシンを作成する方法について説明します。

## <a name="prepare-the-azure-virtual-machine"></a>Azure 仮想マシンを準備する

SQL Managed Instance はご自分のプライベートの仮想ネットワークに配置するため、SQL Server Management Studio や Azure Data Studio などの SQL クライアント ツールをインストールして Azure VM を作成する必要があります。 このツールを使用すると、SQL Managed Instance に接続してクエリを実行することができます。 このクイック スタートでは、SQL Server Management Studio を使用します。

必要なツールをすべて備えたクライアント仮想マシンを作成する最も簡単な方法は、Azure Resource Manager テンプレートを使用することです。

1. 別のブラウザー タブで Azure portal にサインインします。その後、次のボタンを選択してクライアント仮想マシンを作成し、SQL Server Management Studio をインストールします。

   [![[Deploy to Azure]\(Azure にデプロイ\) というラベルが付けられたボタンが表示されている画像。](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjovanpop-msft%2Fazure-quickstart-templates%2Fsql-win-vm-w-tools%2F201-vm-win-vnet-sql-tools%2Fazuredeploy.json)

2. 次の表の情報を参考にして、フォームに必要事項を入力します。

   | 設定| 推奨値 | 説明 |
   | ---------------- | ----------------- | ----------- |
   | **サブスクリプション** | 有効なサブスクリプション | 新しいリソースを作成するアクセス許可があるサブスクリプションでなければなりません。 |
   | **リソース グループ** |[SQL Managed Instance の作成](instance-create-quickstart.md)に関するクイックスタートで指定したリソース グループ。|VNet が存在するリソース グループでなければなりません。|
   | **場所** | リソース グループの場所 | この値は、選択したリソース グループに基づいて設定されます。 |
   | **仮想マシン名**  | 有効な名前 | 有効な名前については、[名前付け規則と制限事項](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。|
   |**管理ユーザー名**|有効なユーザー名|有効な名前については、[名前付け規則と制限事項](/azure/architecture/best-practices/resource-naming)に関するページを参照してください。 "serveradmin" は予約済みのサーバー レベルのロールであるため、使用しないでください。<br>[VM に接続](#connect-to-the-virtual-machine)するときは常に、このユーザー名を使用します。|
   |**パスワード**|有効なパスワード|パスワードは 12 文字以上で、[定義された複雑さの要件](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。<br>[VM に接続](#connect-to-the-virtual-machine)するときは常に、このパスワードを使用します。|
   | **仮想マシン サイズ** | 任意の有効なサイズ | このクイック スタートでは、このテンプレートの既定値 **Standard_B2s** で十分です。 |
   | **場所**|[resourceGroup().location]| この値は変更しないでください。 |
   | **仮想ネットワーク名**|マネージド インスタンスを作成した仮想ネットワーク|
   | **サブネット名**|前の手順で作成したサブネットの名前| マネージド インスタンスを作成したサブネットは選択しないでください。|
   | **artifacts Location (成果物の場所)** | [deployment().properties.templateLink.uri] | この値は変更しないでください。 |
   | **artifacts Location Sas token (成果物の場所 SAS トークン)** | 空白 | この値は変更しないでください。 |

   ![クライアント VM の作成](./media/connect-vm-instance-configure/create-client-sql-vm.png)

   [ご自分の SQL Managed Instance を作成する](instance-create-quickstart.md)に関するページで推奨される VNet 名と既定のサブネットを使用した場合、最後の 2 つのパラメーターは変更する必要がありません。 それ以外の場合は、ネットワーク環境を設定するときに入力した値に、これらの値を変更する必要があります。

3. **[上記の使用条件に同意する]** チェック ボックスをオンにします。
4. **[購入]** を選択して、ネットワークに Azure VM をデプロイします。
5. **[通知]** アイコンを選択してデプロイの状態を表示します。

> [!IMPORTANT]
> 仮想マシンが作成されてから約 15 分経過するまでは、作成後のスクリプトで SQL Server Management Studio をインストールするための時間を確保するために、次の処理に進まないでください。

## <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

次の手順では、リモート デスクトップ接続を使用してご自分の新しく作成した仮想マシンに接続する手順について説明します。

1. デプロイが完了したら、仮想マシン リソースに移動します。

    ![スクリーンショットでは、仮想マシンの [概要] ページが選択され、[接続] が強調表示されている Azure portal が示されています。](./media/connect-vm-instance-configure/vm.png)  

2. **[接続]** を選択します。

   リモート デスクトップ プロトコル ファイル (.rdp ファイル) のフォームが表示され、仮想マシンのパブリック IP アドレスとポート番号が示されます。

   ![RDP フォーム](./media/connect-vm-instance-configure/rdp.png)  

3. **[RDP ファイルのダウンロード]** を選択します。

   > [!NOTE]
   > SSH を使用して VM に接続することもできます。

4. **[仮想マシンに接続する]** フォームを閉じます。
5. VM に接続するには、ダウンロードした RDP ファイルを開きます。
6. メッセージが表示されたら、 **[Connect]** を選択します。 Mac には、この[リモート デスクトップ クライアント](https://apps.apple.com/app/microsoft-remote-desktop-10/id1295203466?mt=12)のような RDP クライアントを、Mac App Store から入手する必要があります。

7. 仮想マシンの作成時に指定したユーザー名とパスワードを入力し、 **[OK]** を選択します。

8. サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** または **[続行]** を選択して接続処理を続行します。

サーバー マネージャー ダッシュボードには、仮想マシンに接続済みと表示されます。

## <a name="connect-to-sql-managed-instance"></a>SQL Managed Instance への接続 

1. 仮想マシンで、SQL Server Management Studio を開きます。

   SSMS の初回起動時は、構成を完了する必要があるために起動にしばらく時間がかかります。
2. **[サーバーに接続]** ダイアログ ボックスで、 **[サーバー名]** ボックスにご自分のマネージド インスタンスの完全修飾 **ホスト名** を入力します。 **[SQL Server 認証]** を選択し、ユーザー名とパスワードを入力して、 **[接続]** を選択します。

    ![SSMS 接続](./media/connect-vm-instance-configure/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、および XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="next-steps"></a>次のステップ

- ポイント対サイト接続を使用して、オンプレミスのクライアント コンピューターから接続する方法を示すクイック スタートについては、[ポイント対サイト接続の構成](point-to-site-p2s-configure.md)に関するページをご覧ください。
- アプリケーションの接続オプションの概要については、[SQL Managed Instance にお使いのアプリケーションを接続する](connect-application-instance.md)方法に関するページを参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行のために Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) を使用するか、データベース バックアップ ファイルから復元するために [T-SQL RESTORE コマンド](restore-sample-database-quickstart.md)を使用します。

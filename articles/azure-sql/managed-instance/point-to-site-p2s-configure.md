---
title: SSMS を使用してポイント対サイト接続を構成する
titleSuffix: Azure SQL Managed Instance
description: SQL Server Management Studio (SSMS) を使用して Azure SQL Managed Instance に接続します。オンプレミスのクライアント コンピューターからポイント対サイト接続を行います。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 3baf2d7ed7c326895ae40948fc2d0a4cc03021f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92788368"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>クイック スタート:オンプレミスから Azure SQL Managed Instance へのポイント対サイト接続を構成する
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

このクイック スタートでは、オンプレミスのクライアント コンピューターからポイント対サイト接続を行い、[SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用して Azure SQL Managed Instance に接続する方法を示します。 ポイント対サイト接続の詳細については、「[ポイント対サイト VPN について](../../vpn-gateway/point-to-site-about.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタート:

- 「[マネージド インスタンスを作成する](instance-create-quickstart.md)」で作成したリソースを出発点として使用します。
- オンプレミスのクライアント コンピューターには、PowerShell 5.1 と Azure PowerShell 1.4.0 以降をインストールしておく必要があります。 必要に応じて、[Azure PowerShell モジュールをインストールする](/powershell/azure/install-az-ps#install-the-azure-powershell-module)手順を参照してください。
- オンプレミスのクライアント コンピューターには、最新版の [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) をインストールしておく必要があります。

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>VPN ゲートウェイを仮想ネットワークに接続する

1. オンプレミスのクライアント コンピューターで PowerShell を開きます。

2. この PowerShell スクリプトをコピーします。 このスクリプトにより、[マネージド インスタンスの作成](instance-create-quickstart.md)のクイック スタートで作成した SQL Managed Instance 仮想ネットワークに VPN ゲートウェイが接続されます。 このスクリプトでは Azure PowerShell Az Module が使用されます。Windows または Linux ベースのホストの場合、次を行います。

   - クライアント コンピューターに証明書を作成してインストールします
   - 今後の VPN ゲートウェイ サブネット IP 範囲が計算されます
   - ゲートウェイ サブネットを作成します
   - VPN ゲートウェイを VPN サブネットに接続する Azure Resource Manager テンプレートがデプロイされます

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. このスクリプトを PowerShell ウィンドウに貼り付けて、必要なパラメーターを指定します。 `<subscriptionId>`、`<resourceGroup>`、`<virtualNetworkName>` の値は、[マネージド インスタンスの作成](instance-create-quickstart.md)のクイック スタートで使用したものと一致している必要があります。 `<certificateNamePrefix>` の値は、自分で選択した文字列にすることができます。

4. PowerShell スクリプトを実行します。

> [!IMPORTANT]
> PowerShell スクリプトが完了するまで、次の手順には進まないでください。

## <a name="create-a-vpn-connection"></a>VPN 接続を作成する

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 仮想ネットワーク ゲートウェイを作成したリソース グループを開き、仮想ネットワーク ゲートウェイ リソースを開きます。
3. **[ポイント対サイトの構成]** を選択し、 **[VPN クライアントのダウンロード]** を選択します。

    ![VPN クライアントのダウンロード](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. オンプレミスのクライアント コンピューターで、ZIP ファイルから必要なファイルを抽出し、抽出されたファイルのあるフォルダーを開きます。
5. **WindowsAmd64** フォルダーを開き、**VpnClientSetupAmd64.exe** ファイルを開きます。
6. **[Windows によって PC が保護されました]** というメッセージが表示されたら、 **[詳細]** をクリックし、 **[実行]** をクリックします。

    ![VPN クライアントをインストールします](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. [ユーザー アカウント制御] ダイアログ ボックスで **[はい]** をクリックして続行します。
8. 仮想ネットワークを参照するダイアログ ボックスで **[はい]** を選択して仮想ネットワークの VPN クライアントをインストールします。

## <a name="connect-to-the-vpn-connection"></a>VPN で接続する

1. オンプレミスのクライアント コンピューターで **[ネットワークとインターネット]** の **[VPN]** に移動し、お使いの SQL Managed Instance 仮想ネットワークを選択して、その VNet との接続を確立します。 次の画像では、**MyNewVNet** という名前の VNet が該当します。

    ![VPN 接続](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. **[接続]** を選択します。
3. ダイアログ ボックスで **[接続]** を選択します。

    ![[接続] ボタンが強調表示されているスクリーンショット。](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. 接続マネージャーで昇格された特権がルート テーブルの更新に必要であると表示されたら、 **[続行]** を選択します。
5. [ユーザー アカウント制御] ダイアログ ボックスで **[はい]** を選択して続行します。

   SQL Managed Instance VNet への VPN 接続が確立されました。

    ![接続を確立したときの、接続済みのメッセージが強調表示されているスクリーンショット。](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>SSMS との接続

1. オンプレミスのクライアント コンピューターで、SQL Server Management Studio を開きます。
2. **[サーバーに接続]** ダイアログ ボックスで、 **[サーバー名]** ボックスにマネージド インスタンスの完全修飾 **ホスト名** を入力します。
3. **[SQL Server 認証]** を選択し、ユーザー名とパスワードを入力して、 **[接続]** を選択します。

    ![SSMS 接続](./media/point-to-site-p2s-configure/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。 また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="next-steps"></a>次のステップ

- Azure 仮想マシンから接続する方法のクイック スタートが必要であれば、[ポイント対サイト接続の構成](point-to-site-p2s-configure.md)に関するページを参照してください。
- アプリケーションの接続オプションの概要については、[SQL Managed Instance にアプリケーションを接続する](connect-application-instance.md)方法に関するページを参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行のために Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) を使用するか、データベース バックアップ ファイルから復元するために [T-SQL RESTORE コマンド](restore-sample-database-quickstart.md)を使用します。
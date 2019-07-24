---
title: P2S の構成 - Azure SQL Database Managed Instance | Microsoft Docs
description: SQL Server Management Studio を使用して Azure SQL Database Managed Instance に接続します。オンプレミスのクライアント コンピューターからポイント対サイト接続を行います。
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
manager: craigg
ms.date: 03/13/2019
ms.openlocfilehash: 9ca9506ad9ca826349b2815e504e9ba8bf976faa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59788064"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-an-azure-sql-database-managed-instance-from-on-premises"></a>クイック スタート:オンプレミスから Azure SQL Database Managed Instance へのポイント対サイト接続を構成する

このクイック スタートでは、オンプレミスのクライアント コンピューターからポイント対サイト接続を行い、[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) を使用して Azure SQL Database Managed Instance に接続する方法を示します。 ポイント対サイト接続の詳細については、「[ポイント対サイト VPN について](../vpn-gateway/point-to-site-about.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタート:

- 「[マネージド インスタンスを作成する](sql-database-managed-instance-get-started.md)」で作成したリソースを出発点として使用します。
- オンプレミスのクライアント コンピューターには、PowerShell 5.1 と AZ PowerShell 1.4.0 以降をインストールしておく必要があります。 必要に応じて、[Azure PowerShell モジュールをインストールする](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)手順を参照してください。
- オンプレミスのクライアント コンピューターには、最新版の [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) をインストールしておく必要があります。

## <a name="attach-a-vpn-gateway-to-your-managed-instance-virtual-network"></a>マネージド インスタンス仮想ネットワークに VPN Gateway を接続する

1. オンプレミスのクライアント コンピューターで PowerShell を開きます。

2. この PowerShell スクリプトをコピーします。 このスクリプトにより、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)のクイック スタートで作成したマネージド インスタンス仮想ネットワークに VPN Gateway が接続されます。 このスクリプトでは、次の処理が実行されます。

   - 証明書が作成され、クライアント コンピューターにインストールされます
   - 今後の VPN Gateway サブネット IP 範囲が計算されます
   - ゲートウェイ サブネットが作成されます
   - VPN Gateway を VPN サブネットに接続する Azure Resource Manager テンプレートがデプロイされます

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGatewayAz.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

     > [!IMPORTANT]
     > Az モジュール以外の Azure PowerShell Resource Manager モジュールを使用するには、`attachVPNGatewayAz.ps1` コマンドレットではなく `attachVPNGateway.ps1` コマンドレットを使用してください。

3. このスクリプトを PowerShell ウィンドウに貼り付けて、必要なパラメーターを指定します。 `<subscriptionId>`、`<resourceGroup>`、`<virtualNetworkName>` の値は、[マネージド インスタンスの作成](sql-database-managed-instance-get-started.md)のクイック スタートで使用されている値に一致する必要があります。 `<certificateNamePrefix>` の値は、自分で選択した文字列にすることができます。

4. PowerShell スクリプトを実行します。

> [!IMPORTANT]
> PowerShell スクリプトが完了するまで、次の手順には進まないでください。

## <a name="create-a-vpn-connection-to-your-managed-instance"></a>マネージド インスタンスへの VPN 接続を作成する

1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. 仮想ネットワーク ゲートウェイを作成したリソース グループを開き、仮想ネットワーク ゲートウェイ リソースを開きます。
3. **[ポイント対サイトの構成]** を選択し、**[VPN クライアントのダウンロード]** を選択します。

    ![VPN クライアントのダウンロード](./media/sql-database-managed-instance-configure-p2s/download-vpn-client.png)  
4. オンプレミスのクライアント コンピューターで、ZIP ファイルから必要なファイルを抽出し、抽出されたファイルのあるフォルダーを開きます。
5. **WindowsAmd64** フォルダーを開き、**VpnClientSetupAmd64.exe** ファイルを開きます。
6. **[Windows によって PC が保護されました]** というメッセージが表示されたら、**[詳細]** をクリックし、**[実行]** をクリックします。

    ![VPN クライアントをインストールします](./media/sql-database-managed-instance-configure-p2s/vpn-client-defender.png)\
7. [ユーザー アカウント制御] ダイアログ ボックスで **[はい]** をクリックして続行します。
8. 仮想ネットワークを参照するダイアログ ボックスで **[はい]** を選択して仮想ネットワークの VPN クライアントをインストールします。

## <a name="connect-to-the-vpn-connection"></a>VPN で接続する

1. オンプレミスのクライアント コンピューターで **[ネットワークとインターネット]** の **[VPN]** に移動し、お使いの Managed Instance 仮想ネットワークを選択して、その VNet との接続を確立します。 次の画像では、**MyNewVNet** という名前の VNet が該当します。

    ![VPN 接続](./media/sql-database-managed-instance-configure-p2s/vpn-connection.png)  
2. **[接続]** を選択します。
3. ダイアログ ボックスで **[接続]** を選択します。

    ![VPN 接続](./media/sql-database-managed-instance-configure-p2s/vpn-connection2.png)  
4. 接続マネージャーで昇格された特権がルート テーブルの更新に必要であると表示されたら、**[続行]** を選択します。
5. [ユーザー アカウント制御] ダイアログ ボックスで **[はい]** を選択して続行します。

   マネージド インスタンス VNet への VPN 接続が確立されました。

    ![VPN 接続](./media/sql-database-managed-instance-configure-p2s/vpn-connection-succeeded.png)  

## <a name="use-ssms-to-connect-to-the-managed-instance"></a>SSMS を使用してマネージド インスタンスに接続する

1. オンプレミスのクライアント コンピューターで、SQL Server Management Studio (SSMS) を開きます。
2. **[サーバーに接続]** ダイアログ ボックスで、**[サーバー名]** ボックスにマネージド インスタンスの完全修飾**ホスト名**を入力します。
3. **[SQL Server 認証]** を選択し、ユーザー名とパスワードを入力して、**[接続]** を選択します。

    ![SSMS 接続](./media/sql-database-managed-instance-configure-vm/ssms-connect.png)  

接続後は、データベース ノード内のシステム データベースとユーザー データベースを確認できます。 また、セキュリティ、サーバー オブジェクト、レプリケーション、管理、SQL Server エージェント、XEvent プロファイラー ノードのさまざまなオブジェクトを確認できます。

## <a name="next-steps"></a>次の手順

- Azure 仮想マシンから接続する方法のクイック スタートが必要であれば、[ポイント対サイト接続の構成](sql-database-managed-instance-configure-p2s.md)に関するページを参照してください。
- アプリケーションの接続オプションの概要については、[マネージド インスタンスにアプリケーションを接続する](sql-database-managed-instance-connect-app.md)方法に関するページを参照してください。
- 既存の SQL Server データベースをオンプレミスからマネージド インスタンスに復元するには、[移行用の Azure Database Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md) または [T-SQL RESTORE コマンド](sql-database-managed-instance-get-started-restore.md)を使用してデータベース バックアップ ファイルから復元します。

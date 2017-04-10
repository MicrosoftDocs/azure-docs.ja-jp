---
title: "ハイブリッド接続の作成と管理 | Microsoft Docs"
description: "ハイブリッド接続の作成、接続の管理、Hybrid Connection Manager のインストールの方法について説明します。 MABS、WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: ad6e548b5998d5ee0b66e3d33da9fa97efb7c97c


---
# <a name="create-and-manage-hybrid-connections"></a>ハイブリッド接続の作成と管理
## <a name="overview-of-the-steps"></a>手順の概要
1. プライベート ネットワークのオンプレミス リソースの **host name** いずれか **FQDN** of the on-premises resource in your private netwいずれかk.
2. ハイブリッド接続に Azure Web Apps または Azure Mobile Apps を関連付ける。
3. オンプレミス リソースに Hybrid Connection Manager をインストールし、特定のハイブリッド接続に接続する。 Azure ポータルでは 1 回のクリックでインストールと接続を実行できます。
4. ハイブリッド接続を管理し、その接続キーを管理します。

このトピックではこれらの手順について説明します。 

> [!IMPORTANT]
> ハイブリッド接続のエンドポイントを IP アドレスに設定することができます。 IP アドレスを使用する場合、クライアントに応じて、オンプレミス リソースにアクセスできる場合とできない場合があります。 ハイブリッド接続は、DNS 参照を行うクライアントに依存します。 ほとんどの場合、 **クライアント** はアプリケーション コードです。 クライアントが DNS 参照を実行しない場合 (IP アドレスをドメイン名 (x.x.x.x) であるかのように解決しようとしない場合)、トラフィックはハイブリッド接続を介して送信されません。
> 
> たとえば (擬似コードの場合)、オンプレミス ホストとして **10.4.5.6** を定義します。
> 
> **次のシナリオは動作します。**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **次のシナリオは動作しません。**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="a-namecreatehybridconnectionacreate-a-hybrid-connection"></a><a name="CreateHybridConnection"></a>ハイブリッド接続の作成
ハイブリッド接続は、Azure ポータルで Web Apps と BizTalk Services の **いずれか** を使用して作成できます。 

**Web Apps を使用してハイブリッド接続を作成する**には、「[Azure Web アプリをオンプレミス リソースに接続する](../app-service-web/web-sites-hybrid-connection-get-started.md)」を参照してください。 Web アプリケーションから Hybrid Connection Manager (HCM) をインストールすることもできます。この方法をお勧めします。 

**BizTalk Services でハイブリッド接続を作成する**には、以下の手順に従います。

1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、 **[BizTalk サービス]** をクリックし、BizTalk Services を選択します。 
   
    既存の BizTalk サービスがない場合は、 [BizTalk サービスを作成する](biztalk-provision-services.md)ことができます。
3. **[ハイブリッド接続]** タブを選択します。  
   ![[ハイブリッド接続] タブ][HybridConnectionTab]
4. **[ハイブリッド接続の作成]** を選択するか、タスク バーの **[追加]** ボタンを選択します。 次のように入力します。
   
   | プロパティ | Description |
   | --- | --- |
   | 名前 |ハイブリッド接続名は一意である必要があり、BizTalk サービスと同じ名前にすることはできません。 任意の名前を入力できますが、目的を具体的に示した名前にしてください。 たとえば、次のようになります。<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer* |
   | ホスト名 |内部設置型リソースの完全修飾ホスト名、ホスト名のみ、または、IPv4 アドレスを入力します。 たとえば、次のようになります。<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10<br/><br/>IPv4 アドレスを使用する場合、クライアントまたはアプリケーション コードが IP アドレスを解決できない可能性があるので注意してください。 このトピックの上部にある重要な注意事項を参照してください。 |
   | ポート |オンプレミス リソースのポート番号を入力します。 たとえば、Web Appsを使用している場合、ポート 80 またはポート 443 を入力します。 SQL Server を使用している場合、ポート 1433 を入力します。 |
5. チェック マークをオンにしてセットアップを完了します。 

#### <a name="additional"></a>その他
* 複数のハイブリッド接続を作成することができます。 許可されている接続数については「 [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](biztalk-editions-feature-chart.md) 」を参照してください。 
* それぞれのハイブリッド接続は、送信用のアプリケーション キーとリッスン用のオンプレミス キーで構成される接続文字列のペアと共に作成されます。 各ペアにはプライマリ キーとセカンダリ キーが含まれています。 

## <a name="a-namelinkwebsitealink-your-azure-app-service-web-app-or-mobile-app"></a><a name="LinkWebSite"></a>Azure App Service の Web アプリまたはモバイル アプリを関連付ける
既存のハイブリッド接続に Azure App Service の Web アプリまたはモバイル アプリを関連付けるには、ハイブリッド接続ブレードで **[既存のハイブリッド接続を使用する]** を選択します。 「[Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする](../app-service-web/web-sites-hybrid-connection-get-started.md)」をご覧ください。

## <a name="a-nameinstallhcmainstall-the-hybrid-connection-manager-on-premises"></a><a name="InstallHCM"></a>オンプレミスへの Hybrid Connection Manager のインストール
ハイブリッド接続の作成後、オンプレミス リソースに Hybrid Connection Manager をインストールします。 Hybrid Connection Manager は Azure Web Apps または BizTalk サービスからダウンロードできます。 BizTalk Services の手順は以下のとおりです。 

1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、 **[BizTalk サービス]** をクリックし、BizTalk Services を選択します。 
3. **[ハイブリッド接続]** タブを選択します。  
   ![[ハイブリッド接続] タブ][HybridConnectionTab]
4. タスク バーで **[オンプレミス設定]** を選択します。  
   ![[オンプレミス設定]][HCOnPremSetup]
5. **[インストールと構成]** を選択して、オンプレミスのシステムで Hybrid Connection Manager を実行またはダウンロードします。 
6. チェック マークを選択して、インストールを開始します。 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>その他
* ハイブリッド接続マネージャーは次のオペレーティング システムにインストールできます。
  
  * Windows Server 2008 R2 (.NET Framework 4.5 以降および Windows Management Framework 4.0 以上が必要)
  * Windows Server 2012 (Windows Management Framework 4.0 以上が必要)
  * Windows Server 2012 R2
* Hybrid Connection Manager をインストールすると、以下のことが行われます。 
  
  * プライマリ アプリケーション接続文字列が使用されるように、Azure がホストするハイブリッド接続が自動的に構成されます。 
  * プライマリ オンプレミス接続文字列が使用されるように、オンプレミス リソースが自動的に構成されます。
* Hybrid Connection Manager は、承認のために有効なオンプレミスの接続文字列を使用する必要があります。 Azure Web Apps または Mobile Apps は、承認のために有効なアプリケーション接続文字列を使用する必要があります。
* Hybrid Connection Manager の別のインスタンスを別のサーバーにインストールすることにより、ハイブリッド接続を拡張できます。 最初のオンプレミス リスナーと同じアドレスを使用するように、オンプレミス リスナーを構成します。 この状況では、トラフィックは複数のアクティブなオンプレミス リスナーにランダムに分散されます (ラウンド ロビン方式)。 

## <a name="a-namemanagehybridconnectionamanage-hybrid-connections"></a><a name="ManageHybridConnection"></a>ハイブリッド接続の管理
ハイブリッド接続を管理するために、以下のことを実行できます。

* Azure ポータルを使用して BizTalk サービスに移動する。 
* [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)を使用する。

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>ハイブリッド接続文字列をコピー、再生成する
1. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、 **[BizTalk サービス]** をクリックし、BizTalk Services を選択します。 
3. **[ハイブリッド接続]** タブを選択します。  
   ![[ハイブリッド接続] タブ][HybridConnectionTab]
4. [ハイブリッド接続] を選択します。 タスク バーで **[接続の管理]** を選択します。  
   ![Manage Options][HCManageConnection]
   
    **[接続の管理]** でアプリケーション接続文字列と内部設置型接続文字列が一覧表示されます。 接続文字列をコピーすることも、接続文字列で使用するアクセス キーを再生成することもできます。 
   
    **[再生成]**を選択すると、接続文字列内で使用される共有アクセス キーが変更されます。 以下の手順を実行します。
   
   * Azure クラシック ポータルで、Azure アプリケーションの **[キーの同期]** を選択します。
   * **[オンプレミス設定]**を再実行します。 [オンプレミス設定] を再実行すると、更新されたプライマリ接続文字列がオンプレミス リソースによって使用されるように自動的に構成されます。

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>ハイブリッド接続で使用するオンプレミス リソースを管理するグループ ポリシーの使用
1. [Hybrid Connection Manager の管理用テンプレート](http://www.microsoft.com/download/details.aspx?id=42963)をダウンロードします。
2. ファイルを解凍します。
3. グループ ポリシーを変更するコンピューターで、以下の手順を実行します。  
   
   * .ADMX ファイルを *%WINROOT%\PolicyDefinitions* フォルダーにコピーします。
   * .ADML ファイルを *%WINROOT%\PolicyDefinitions\en-us* フォルダーにコピーします。

コピーが終了すると、グループ ポリシー エディターを使用してポリシーを変更できます。

## <a name="next"></a>次へ
[Azure Web アプリをオンプレミス リソースに接続する](../app-service-web/web-sites-hybrid-connection-get-started.md)  
[Azure Web Apps からオンプレミスの SQL Server に接続する](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)   
[ハイブリッド接続の概要](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>関連項目
[Microsoft Azure での BizTalk Services 管理の REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](biztalk-editions-feature-chart.md)  
[Azure クラシック ポータルを使用して BizTalk サービスを作成する](biztalk-provision-services.md)  
[BizTalk Services: [ダッシュボード]、[監視]、および [スケール] タブ](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 



<!--HONumber=Dec16_HO2-->



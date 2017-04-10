---
title: "Azure Data Catalog リリース ノート | Microsoft Docs"
description: "Azure Data Catalog のリリース ノートです。"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 60abdc490cefc7362ca6ed0dec7dbfd53984201f


---
# <a name="azure-data-catalog-release-notes"></a>Azure Data Catalog リリース ノート
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Azure Data Catalog の 2015 年 11 月 20 日リリースのノート
### <a name="opening-data-sources-in-power-bi-desktop"></a>Power BI Desktop でデータ ソースを開く
**Azure Data Catalog** ポータルから [Power BI Desktop で開く] オプションを使用したときに、Power BI Desktop アプリケーションで次の 2 つの問題のどちらかが発生することがあります。

* [ドキュメントを表示できません] というタイトルのダイアログ ボックスが表示される
* Power BI Desktop アプリケーションは開くが、ファイルが表示されない

どちらの状況でも、 [PowerBI.com](https://powerbi.com)から Power BI Desktop の最新バージョンをインストールすることで問題を解決できます。

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Azure Data Catalog の 2015 年 11 月 13 日リリースのノート
### <a name="registering-and-connecting-to-teradata"></a>Teradata への登録と接続
Teradata データ ソースに接続する場合は、使用するソフトウェアのビット (32 ビットまたは 64 ビット) と一致する適切な Teradata ODBC ドライバーをインストールする必要があります。

この ADC のリリース日の時点では、最新の [Windows 用の Teradata ODBC ドライバー (バージョン 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) は Office 2013 に対応しますが、Office 2016 には対応していません。

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Azure Data Catalog の 2015 年 7 月 13 日リリースのノート
### <a name="registering-and-connecting-to-oracle-database"></a>Oracle Database への登録と接続
Oracle Database データ ソースに接続する場合、ユーザーは、使用しているソフトウェアのビット (32 ビットか 64 ビット) に一致する正しい Oracle ドライバーをインストールしている必要があります。

* 32 ビット Windows を実行するコンピューター上の Oracle データ ソースを登録する場合は、32 ビット Oracle ドライバーを使用します。
* 64 ビット Windows を実行するコンピューター上の Oracle データ ソースを登録する場合は、64 ビット Oracle ドライバーを使用します。
* 64 ビット Windows に含まれる 32 ビット バージョンの Microsoft Office を実行するコンピューター上の Excel を使用して、Oracle データ ソースに接続する場合、32 ビット Oracle ドライバーを使用します。
* 64 ビット バージョンの Microsoft Office を実行するコンピューター上の Excel を使用して、Oracle データ ソースに接続する場合、64 ビット Oracle ドライバーを使用します。

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>SQL Server Reporting Services への登録と接続
現在、SQL Server Reporting Services (SSRS) データ ソースのサポートは、ネイティブ モード サーバーのみに制限されます。 SSRS の SharePoint モードでのサポートは、今後のリリースで追加される予定です。

### <a name="opening-data-assets-in-excel"></a>Excel でのデータ資産のオープン
**Azure Data Catalog** ポータルから Microsoft Excel でデータ資産を開くと、**[Microsoft Excel のセキュリティに関する通知]** ダイアログ ボックスにメッセージが表示されることがあります。 これは標準の予想される動作であり、ユーザーは、[ **有効にする** ] を選択して続行できます。

詳細については、「 [不審な Web サイトへのリンクやそうしたサイトからのファイルについてのセキュリティの警告を有効または無効にする](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE)」を参照してください。

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>プロキシおよびポリシーの構成とデータ ソースの登録
ユーザーは、Azure Data Catalog ポータルにログオンできる状況が発生することがありますが、データ ソース登録ツールにログオンしようとすると、ログオンを妨げるエラー メッセージが表示されます。

この問題の動作には 2 つの可能性のある原因があります。

**原因 1: Active Directory Federation Services の構成**。データ ソース登録ツールは、フォーム認証を使用して、Active Directory に対するユーザー ログオンを検証します。 ログオンを成功させるには、Active Directory 管理者によって、グローバル認証ポリシーでフォーム認証が有効にされている必要があります。

特定の状況で、ユーザーが企業ネットワーク上にいる場合のみ、またはユーザーが企業ネットワークの外部から接続している場合のみ、このエラー動作が発生することがあります。 グローバル認証ポリシーでは、イントラネット接続とエクストラネット接続で個別に認証方法を有効にすることができます。 ログオン エラーは、ユーザーの接続元のネットワークで、フォーム認証が有効にされていない場合に発生する可能性があります。

詳細については、「 [認証ポリシーの構成](https://technet.microsoft.com/library/dn486781.aspx)」を参照してください。

**原因 2: ネットワーク プロキシの構成**。企業ネットワークでプロキシ サーバーを使用している場合、登録ツールが、プロキシ経由で Azure Active Directory に接続できないことがあります。 ユーザーは、ツールの構成ファイルを編集し、このセクションをファイルに追加して、登録ツールが確実に接続できるようにします。

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


RegistrationTool.exe.config ファイルを見つけるには、登録ツールを起動し、Windows タスク マネージャー ユーティリティを開きます。 タスク マネージャーの [詳細] タブで、[RegistrationTool.exe] を右クリックし、ポップアップ メニューから [ファイルの場所を開く] を選択します。



<!--HONumber=Nov16_HO3-->



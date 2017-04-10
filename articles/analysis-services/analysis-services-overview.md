---
title: "Azure Analysis Services とは | Microsoft Docs"
description: "Azure での Analysis Services の概要を説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 3539da8afe5781d74cbf723090050b767373f268
ms.lasthandoff: 03/25/2017


---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services とは
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Microsoft SQL Server Analysis Services の実績のある分析エンジン上に構築された Azure Analysis Services は、クラウド上でエンタープライズ級のデータ モデリングを提供します。 

このビデオでは、Azure Analysis Services がマイクロソフトの BI 機能全体にどのように組み込まれ、セマンティック モデルのクラウドへの移行によりどのようなメリットが得られるのかを詳しく説明しています。

>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesGettingStarted/player]
>
>

> [!IMPORTANT]
> Azure Analysis Services は**プレビュー**段階です。 まだ動作しない部分がいくつかあります。 後の「[プレビューでの考慮事項](#preview-expectations)」をご覧ください。 また、[Azure Analysis Services ブログ](https://go.microsoft.com/fwlink/?linkid=830920)で最新情報に注意してください。
> 
> 

## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services が基礎
Azure Analysis Services は SQL Server 2016 Analysis Services Enterprise Edition と互換性があります。 Azure Analysis Services は、互換性レベル 1200 の表形式モデルをサポートしています。 DirectQuery、パーティション、行レベル セキュリティ、双方向リレーションシップ、翻訳をすべてサポートします。

## <a name="use-the-tools-you-already-know"></a>既に知っているツールの使用
![BI 開発者用ツール](./media/analysis-services-overview/aas-overview-dev-tools.png)

Azure Analysis Services のデータ モデルを作成するときは、SQL Server Analysis Services と同じツールを使います。 表形式モデルの作成とデプロイには、最新バージョンの [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)、または [Azure Powershell](/powershell/azureps-cmdlets-docs) と [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) の [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) テンプレートを使います。

## <a name="connect-to-data-sources"></a>データ ソースへの接続
Azure のサーバーにデプロイされるデータ モデルは、組織のオンプレミス データ ソースまたはクラウドのデータ ソースへの接続をサポートします。 ハイブリッド BI ソリューションでは、オンプレミスとクラウド両方のデータ ソースのデータを組み合わせます。

![データ ソース](./media/analysis-services-overview/aas-overview-data-sources.png)

サーバーはクラウド内にあるため、クラウドのデータ ソースへの接続はシームレスです。 オンプレミス データ ソースに接続するときは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)によってクラウドの Analysis Services サーバーとの高速で安全な接続が保証されます。  

 \* プレビューでは一部のデータ ソースはまだサポートされていません。 詳しくは、後の「[プレビューでの考慮事項](#preview-expectations)」をご覧ください。

## <a name="explore-your-data-from-anywhere"></a>任意の場所からデータを取得
ほとんどどこからでもサーバーに接続して[データを取得](analysis-services-connect.md)します。 Azure Analysis Services は、Power BI Desktop、Excel、カスタム アプリ、ブラウザー ベースのツールからの接続をサポートします。

![データ可視化](./media/analysis-services-overview/aas-overview-visualization.png)

 \* Power BI Embedded はプレビューではまだサポートされていません。

## <a name="secure"></a>セキュリティ保護
#### <a name="user-authentication"></a>ユーザー認証
Azure Analysis Services のユーザー認証は、[Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) によって処理されます。 ユーザーが Azure Analysis Services データベースにログインするときは、対象のデータベースへのアクセス権がある組織のアカウント ID を使います。 これらのユーザー ID は、Azure Analysis Services サーバーが存在しているサブスクリプションの既定の Azure Active Directory のメンバーである必要があります。 AAD とオンプレミスの Active Directory の間の[ディレクトリ統合](https://technet.microsoft.com/library/jj573653.aspx)は、オンプレミスのユーザーが Azure Analysis Services データベースにアクセスできるようになる優れた方法ですが、すべてのシナリオに必要なわけではありません。

ユーザーは、アカウントのユーザー プリンシパル名 (UPN) とパスワードを使ってログインします。 オンプレミスの Active Directory と同期されている場合は通常、ユーザーの UPN は組織のメール アドレスです。

Azure Analysis Services サーバーのリソースを管理するためのアクセス許可は、ユーザーを Azure サブスクリプション内のロールに割り当てることによって処理されます。 既定では、サブスクリプションの管理者は Azure のサーバー リソースに対する所有者アクセス許可を持っています。 Azure Resource Manager を使って他のユーザーを追加できます。

#### <a name="data-security"></a>データのセキュリティ
Azure Analysis Services は、Azure Blob Storage を利用して、Analysis Services データベースのストレージとメタデータを保持します。 BLOB 内のデータ ファイルは、Azure Blob Server Side Encryption (SSE) を使って暗号化されます。 直接クエリ モードを使うと、メタデータのみが格納されます。実際のデータは、クエリ時にデータ ソースからアクセスされます。

#### <a name="on-premises-data-sources"></a>オンプレミス データ ソース
組織のオンプレミス データへの安全なアクセスは、[オンプレミス データ ゲートウェイ](analysis-services-gateway.md)をインストールして構成することにより実現できます。 ゲートウェイは、直接クエリ モードとメモリ内モード両方のデータへのアクセスを提供します。 Azure Analysis Services モデルがオンプレミス データ ソースに接続すると、オンプレミス データ ソースに対する暗号化された資格情報を含むクエリが作成されます。 ゲートウェイ クラウド サービスはクエリを分析して、Azure Service Bus に要求をプッシュします。 オンプレミス ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。 その後、ゲートウェイはクエリを取得して、資格情報を復号化し、データ ソースに接続して実行します。 結果は、データ ソースからゲートウェイ経由で Azure Analysis Services データベースに返送されます。

Azure Analysis Services は、[Microsoft オンライン サービス使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)および[Microsoft オンライン サービス プライバシー表明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)によって管理されます。
Azure のセキュリティについて詳しくは、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/Security/AzureSecurity)をご覧ください。

## <a name="get-help"></a>問い合わせ
Azure Analysis Services は簡単にセットアップして管理できます。 サーバーの作成と管理に必要なすべての情報はこちらで確認できます。 データ モデルの作成とサーバーへのデプロイは、オンプレミスのサーバーにデプロイするデータ モデルを作成する場合とほぼ同じです。 概念、手順、チュートリアル、リファレンスの記事の広範なライブラリが、[Analysis Services に関する MSDN](https://msdn.microsoft.com/library/bb522607.aspx) にあります。

また、[Azure Analysis Services に関する Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) には役に立つビデオが多数あります。

内容は急速に変化しています。 最新情報は [Azure Analysis Services のブログ](https://go.microsoft.com/fwlink/?linkid=830920)で常に入手できます。

## <a name="community"></a>コミュニティ
Analysis Services には活気のあるユーザー コミュニティがあります。 [Azure Analysis Services フォーラム](https://aka.ms/azureanalysisservicesforum)の会話に参加してください。

## <a name="feedback"></a>フィードバック
提案や機能の要求がある場合は、 [Azure Analysis Services のフィードバック](https://aka.ms/azureanalysisservicesfeedback)にコメントをお送りください。

ドキュメントに関する指摘がある場合は、 各記事の下部にある DISQUS を使ってコメントを追加できます。

## <a name="preview-expectations"></a>プレビューでの考慮事項
Azure Analysis Services は現在プレビュー段階です。 いくつかの注意事項があります。

##### <a name="server-modes"></a>サーバー モード
現在、Azure Analysis Services は互換性レベル 1200 の表形式モデルの表形式モードをサポートしています。 多次元およびデータ マイニング モードと SharePoint モードの Power Pivot はサポートされていません。

##### <a name="data-sources"></a>データ ソース
プレビューでは、次のデータ ソースは Azure Analysis Services サーバーにデプロイされた表形式の 1200 モデルでサポートされます。

| **クラウド** | **オンプレミス (データ ゲートウェイが必要)** |
| --- | --- |
| SQL データベース |SQL Server |
| SQL Data Warehouse |APS |
| Oracle | Oracle |
| Teradata | |

### <a name="data-source-providers"></a>データ ソース プロバイダー
Azure Analysis Services のデータ モデルでデータ ソースに接続するには、SQL Server Analysis Services のデータ モデルとは異なるデータ プロバイダーが必要な場合があります。 データ プロバイダーの要件は、データ ソースがクラウドとオンプレミスのどちらに存在するか、およびデータ モデルの種類 (メモリか直接クエリか) によって異なります。 詳しくは、「[データ ソースの接続](analysis-services-datasource.md)」をご覧ください。

### <a name="client-connections"></a>クライアント接続

クライアント アプリケーションには、Azure Analysis Services に接続するための最新のクライアント [データ プロバイダー](analysis-services-data-providers.md) が必要です。

Azure Analysis Services サーバーへのライブ接続を含み、OneDrive または SharePoint Online に保存される Excel ブックはサポートされていません。

## <a name="next-steps"></a>次のステップ
Azure Analysis Services について理解できたら、実際に始めてみてください。 Azure で[サーバーを作成する](analysis-services-create-server.md)方法およびサーバーに[表形式モデルをデプロイする](analysis-services-deploy.md)方法を学習してください。



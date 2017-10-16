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
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2017
ms.author: owend
ms.openlocfilehash: 8b3a538fc0fe34d0b7af8a99510cfb19abd32d55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-analysis-services"></a>Azure Analysis Services とは
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services は、クラウドにおけるエンタープライズ レベルのデータ モデリングを実現します。 これは、完全管理型のサービスとしてのプラットフォーム (PaaS) であり、Azure のデータ プラットフォーム サービスと統合されています。 

Analysis Services では、複数のソースからのデータをマッシュアップして結合し、メトリックを定義して、単一の信頼されたセマンティック データ モデルで、データのセキュリティを確保することができます。 このデータ モデルにより、ユーザーはクライアント アプリケーション (Power BI、Excel、Reporting Services、サードパーティ アプリ、カスタム アプリなど) から、大量のデータを従来よりも簡単に、高速に参照できるようになります。

![データ ソース](./media/analysis-services-overview/aas-overview-data-sources.png)

[このビデオ](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4)では、Azure Analysis Services が Microsoft の BI 機能全体にどのように組み込まれ、データ モデルのクラウドへの移行によりどのようなメリットが得られるのかを説明しています。

## <a name="built-on-sql-server-analysis-services"></a>SQL Server Analysis Services が基礎
Azure Analysis Services には、SQL Server Analysis Services Enterprise Edition が既に備えている数多くの優れた機能との互換性があります。 Azure Analysis Services は、[互換性レベル](analysis-services-compat-level.md) 1200 と 1400 の表形式モデルをサポートしています。 パーティション、行レベルセキュリティ、双方向リレーションシップ、翻訳はすべてサポートされています。 インメモリ モードと DirectQuery モードは、大規模かつ複雑なデータセットに対する高速なクエリを意味します。

表形式モデルは開発に要する時間が短く、高度なカスタマイズも可能です。 開発者向けに、表形式モデルには、モデル オブジェクトを記述するための表形式オブジェクト モデル (TOM) が用意されています。 TOM は、[Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) を通じて JSON 形式で公開され、[Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 名前空間を通じて AMO データ定義言語で公開されています。

## <a name="better-with-azure"></a>Azure との連携強化
Azure Analysis Services とさまざまな Azure サービスを統合することにより、高度な分析ソリューションを構築できます。 [Azure Active Directory](../active-directory/active-directory-whatis.md) との統合によって、重要なデータのセキュリティをロールベースのアクセスによって確保することができます。 [Azure Data Factory](../data-factory/introduction.md) パイプラインには、モデルにデータを読み込むアクティビティを含めることによって統合します。 [Azure Automation](../automation/automation-intro.md) や [Azure Functions](../azure-functions/functions-overview.md) を使用すると、カスタム コードによるモデルの軽量オーケストレーションを行うことができます。

## <a name="get-up-and-running-quickly"></a>スピーディな起動と実行
Azure Portal から数分で[サーバーを作成する](analysis-services-create-server.md)ことができます。 また、Azure Resource Manager テンプレートと PowerShell により、宣言型の[テンプレート](../azure-resource-manager/resource-manager-create-first-template.md)を使用してサーバーをプロビジョニングすることができます。 1 つのテンプレートを使用して、他の Azure コンポーネント (ストレージ アカウント、Azure Functions など) と共に複数のサービスをデプロイできます。 

サーバーが作成されたら、Azure Portal ですぐに表形式モデルを作成できます。 新しい (プレビュー) [Web デザイナー機能](analysis-services-create-model-portal.md)を使用して、Azure SQL Database や Azure SQL Data Warehouse データ ソースに接続したり、Power BI Desktop の .pbix ファイルをインポートしたりできます。 テーブル間のリレーションシップは自動的に作成され、ご利用のブラウザーから直接、メジャーを作成したり、JSON 形式の model.bim ファイルを編集したりできます。

## <a name="scale-to-your-needs"></a>ニーズに合わせてスケール可能
Azure Analysis Services は、Developer レベル、Basic レベル、Standard レベルでご利用いただけます。 各レベルのプランのコストは、処理能力、QPU、メモリ サイズによって異なります。 サーバーの作成時に、レベル内のプランを選択します。 プランは、同一レベル内で変更することも、上位レベルにアップグレードすることもできますが、上位レベルから下位レベルにダウングレードすることはできません。

サーバーをスケールアップ、スケールダウン、または一時停止します。 Azure Portal を使用するか、PowerShell を使用して即座に全体を制御します。 料金は使用した分だけになります。 各種プランとレベルの詳細については、「[Azure Analysis Services の価格](https://azure.microsoft.com/pricing/details/analysis-services/)」を参照してください。価格計算ツールを使用して、ご自身に合ったプランを判断することができます。

## <a name="keep-your-data-close"></a>データを近くに維持
Azure Analysis Services サーバーは、次の [Azure リージョン](https://azure.microsoft.com/regions/)で作成できます。

| アメリカ | ヨーロッパ | アジア太平洋 |
|----------|--------|--------------|
|  ブラジル南部<br> カナダ中部<br> 米国東部 2<br> 米国中北部<br> 米国中南部<br> 米国中西部<br> 米国西部 | 北ヨーロッパ<br> 英国南部<br> 西ヨーロッパ |   オーストラリア南東部<br> 東日本<br> 東南アジア<br> インド西部  |

新しいリージョンが随時追加されるため、この一覧は不十分である可能性があります。 場所は、Azure Portal または Azure Resource Manager テンプレートでサーバーを作成するときに選択します。 パフォーマンスを最大限に高めるには、最大のユーザー ベースに最も近い場所を選びます。 [高可用性](analysis-services-bcdr.md)を確保するには、モデルを複数リージョンの冗長サーバーにデプロイします。

## <a name="migrate-your-existing-tabular-models"></a>既存の表形式モデルの移行
既存のオンプレミスの SQL Server Analysis Services モデル ソリューションがある場合は、大幅な変更をしなくても Azure Analysis Services に移行できます。 移行するには、SSDT を使用してモデルをサーバーにデプロイします。 または SSMS で、バックアップと復元か、TMSL を使用できます。

オンプレミスのデータ ソースがある場合は、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールして構成する必要があります。 ロールとロール メンバーを既に構成済みの場合、ロールは移行されますが、ロール メンバーは SSMS または PowerShell を使用して追加し直す必要があります。

## <a name="connect-to-popular-data-sources"></a>広く使われているデータ ソースに接続
Azure Analysis Services では、組織のオンプレミス データ ソースとクラウドの[データ ソースへの接続](analysis-services-datasource.md)がサポートされています。 ハイブリッド ソリューションでは、オンプレミスとクラウド両方のデータ ソースのデータを組み合わせます。 

互換性レベル 1400 の新しい表形式モデルでは、M 数式クエリ言語に基づいた、SSDT の最新の Get Data 機能を使用します。 Get Data を使用すると、より多くのデータ変換機能やデータ マッシュアップ機能に加え、独自の高度な M 数式言語クエリの作成および編集機能も利用できます。 たとえば、互換性レベル 1400 の表形式モデルを使用すると、Azure Blob Storage 内のデータ ファイルをモデル化できます。

## <a name="use-the-tools-you-already-know"></a>既に知っているツールの使用

![BI 開発者用ツール](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) for Visual Studio
無料の [SQL Server Data Tools (SSDT) for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) を使用して、モデルを開発してデプロイします。 SSDT には、スピーディに開発を行うための Analysis Services プロジェクト テンプレートが用意されています。 現在、SSDT には、互換性レベル 1400 の表形式モデルに対応した最新の Get Data のデータ ソース クエリとマッシュアップ機能が含まれています。 Power BI Desktop と Excel 2016 の Get Data に慣れていれば、高度にカスタマイズされたデータ ソース クエリの作成が非常に簡単であることは既にわかっています。

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
[SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) を使って、サーバーとモデル データベースを管理します。 クラウド内のサーバーに接続します。 XMLA クエリ ウィンドウから直接 TMSL スクリプトを実行したり、TMSL スクリプトを使用してタスクを自動化したりできます。 新しい機能が次々と登場するため、SSMS は毎月更新されます。

#### <a name="powershell"></a>PowerShell
サーバーの作成、サーバー操作の中断/再開、サービス レベルの変更などのサーバー リソース管理タスクでは、Azure Resource Manager (AzureRM) コマンドレットが使用されます。 ロール メンバーの追加と削除、処理、TMSL スクリプトの実行など、その他のデータベース管理タスクでは、SqlServer モジュールのコマンドレットが使用されます。 AzureRM モジュールと SQLServer モジュールはどちらも、[PowerShell ギャラリー](https://www.powershellgallery.com/)で入手できます。


## <a name="your-data-is-secure"></a>データのセキュリティを確保
![データ可視化](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>認証
Azure Analysis Services のユーザー認証は、[Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) によって処理されます。 ユーザーが Azure Analysis Services データベースにログインするときは、対象のデータベースへのアクセス権がある組織のアカウント ID を使います。 これらのユーザー ID は、Azure Analysis Services サーバーが存在しているサブスクリプションの既定の Azure Active Directory のメンバーである必要があります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。

#### <a name="data-security"></a>データのセキュリティ
Azure Analysis Services は、Azure Blob Storage を利用して、Analysis Services データベースのストレージとメタデータを保持します。 BLOB 内のデータ ファイルは、Azure Blob Server Side Encryption (SSE) を使って暗号化されます。 直接クエリ モードを使用している場合は、メタデータのみが格納されます。 実際のデータは、クエリ時にデータ ソースからアクセスされます。

#### <a name="on-premises-data-sources"></a>オンプレミス データ ソース
組織のオンプレミス データへの安全なアクセスは、[オンプレミスのデータ ゲートウェイ](analysis-services-gateway.md)をインストールして構成することにより実現します。 ゲートウェイは、直接クエリ モードとメモリ内モード両方のデータへのアクセスを提供します。 Azure Analysis Services モデルがオンプレミス データ ソースに接続すると、オンプレミス データ ソースに対する暗号化された資格情報を含むクエリが作成されます。 ゲートウェイ クラウド サービスはクエリを分析して、Azure Service Bus に要求をプッシュします。 オンプレミス ゲートウェイは、保留中の要求がないか Azure Service Bus をポーリングします。 その後、ゲートウェイはクエリを取得して、資格情報を復号化し、データ ソースに接続して実行します。 結果は、データ ソースからゲートウェイ経由で Azure Analysis Services データベースに返送されます。

Azure Analysis Services は、[Microsoft オンライン サービス使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)および[Microsoft オンライン サービス プライバシー表明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)によって管理されます。
Azure のセキュリティについて詳しくは、[Microsoft セキュリティ センター](https://www.microsoft.com/trustcenter/Security/AzureSecurity)をご覧ください。

## <a name="supports-the-latest-client-tools"></a>最新のクライアント ツールをサポート
![データ可視化](./media/analysis-services-overview/aas-overview-clients.png)

Power BI、Excel、サードパーティ製ツールなど、データの探索と視覚化用の最新ツールにより、ユーザーは、モデル データに対して非常にインタラクティブで視覚的に優れた洞察を得ることができます。

クライアントは MSOLAP、AMO、または ADOMD の[クライアント ライブラリ](analysis-services-data-providers.md)を使用して、Analysis Services サーバーに接続します。 Power BI Desktop や Excel などの Microsoft クライアント アプリケーションでは、3 つのクライアント ライブラリがすべてインストールされます。 ただし、更新のバージョンや頻度によっては、クライアント ライブラリが Azure Analysis Services で必要な最新バージョンでない可能性があることに注意してください。 これは、カスタム アプリケーションまたは AsCmd、TOM、ADOMD.NET など他のインターフェイスでも同様です。 通常、これらのアプリケーションでは、パッケージの一部としてライブラリを手動でインストールする必要があります。


## <a name="get-help"></a>問い合わせ

#### <a name="documentation"></a>ドキュメント
Azure Analysis Services は簡単にセットアップして管理できます。 サーバーのサービスの作成と管理に必要なすべての情報はこちらで確認できます。 データ モデルの作成とサーバーへのデプロイは、オンプレミスのサーバーにデプロイするデータ モデルを作成する場合とほぼ同じです。 概念、手順、チュートリアル、リファレンスの記事の広範なライブラリが、[Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) にあります。

#### <a name="videos"></a>ビデオ
[Azure Analysis Services に関する Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services) にある、役立つビデオをご確認ください。

#### <a name="blogs"></a>ブログ
内容は急速に変化しています。 最新情報は、[Analysis Services チーム ブログ](https://blogs.msdn.microsoft.com/analysisservices/)および [Azure ブログ](https://azure.microsoft.com/blog/)で常に入手できます。

#### <a name="community"></a>コミュニティ
Analysis Services には活気のあるユーザー コミュニティがあります。 [Azure Analysis Services フォーラム](https://aka.ms/azureanalysisservicesforum)の会話に参加してください。

## <a name="feedback"></a>フィードバック
提案や機能の要求がある場合は、 [Azure Analysis Services のフィードバック](https://aka.ms/azureanalysisservicesfeedback)にコメントをお送りください。

ドキュメントに関する指摘がある場合は、 各記事の下部にある Livefyre を使ってコメントを追加できます。

## <a name="next-steps"></a>次のステップ
Azure Analysis Services について理解できたら、実際に始めてみてください。 Azure で[サーバーを作成する](analysis-services-create-server.md)方法について説明します。 サーバーの準備ができたら、[Adventure Works チュートリアル](tutorials/aas-adventure-works-tutorial.md)を進めて、完全に機能する表形式モデルを作成してサーバーにデプロイする方法を学習してください。

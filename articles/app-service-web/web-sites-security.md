---
title: "Azure App Service でのアプリのセキュリティ保護"
description: "Azure App Service で Web アプリ、モバイル アプリ バックエンド、API アプリをセキュリティで保護する方法について説明します。"
services: app-service
documentationcenter: 
author: cephalin
manager: erikre
editor: 
ms.assetid: 5ce560b4-42d7-4b20-935c-0445fd539e39
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: eaf3df69428124127ef3daf134bc948cd0988ec6
ms.openlocfilehash: c1956e97444077f197ab5d0fd67097ddea0f7244
ms.lasthandoff: 03/02/2017


---
# <a name="secure-an-app-in-azure-app-service"></a>Azure App Service でのアプリのセキュリティ保護
この記事は、Azure App Service で Web アプリ、モバイル アプリ バックエンド、API アプリをセキュリティで保護するときに役立ちます。 

Azure App Service のセキュリティには、次の&2; つのレベルがあります。 

* **インフラストラクチャとプラットフォームのセキュリティ** - 実際にクラウドで安全に操作を行うために必要な Azure のサービスを信頼して利用します。
* **アプリケーションのセキュリティ** - アプリ自体を安全に設計する必要があります。 これには、Azure Active Directory と統合する方法、証明書を管理する方法、さまざまなサービスと安全に通信できることを確認する方法が含まれます。 

#### <a name="infrastructure-and-platform-security"></a>インフラストラクチャとプラットフォームのセキュリティ
App Service は、Azure VM、ストレージ、ネットワーク接続、Web フレームワーク、管理機能、統合機能などを保持しているため、積極的にセキュリティ保護と強化が行われます。また、積極的なコンプライアンスが実行され、継続的に次のことを確認します。

* App Service アプリは、インターネットと他の顧客の Azure リソースの両方から分離されていること。
* App Service アプリとリソース グループ内の他の Azure リソース (SQL Database など) との間でのシークレット (接続文字列など) の伝達は Azure 内に留まり、ネットワーク境界を越えないこと。 シークレットは常に暗号化されます。
* App Service アプリと外部リソース (PowerShell 管理、コマンドライン インターフェイス、Azure SDK、REST API、ハイブリッド接続など) の間のすべての通信が適切に暗号化されていること。
* 24 時間体制の脅威管理によって、App Service のリソースがマルウェア、分散型サービス拒否 (DDoS)、man-in-the-middle (MITM) などの脅威から保護されていること。 

Azure のインフラストラクチャとプラットフォームのセキュリティの詳細については、 [Azure セキュリティ センター](/support/trust-center/security/)を参照してください。

#### <a name="application-security"></a>アプリケーションのセキュリティ
アプリケーションが実行されるインフラストラクチャとプラットフォームをセキュリティで保護することは Azure の役割ですが、アプリケーション自体をセキュリティで保護することはユーザーの役割です。 つまり、ユーザーは、アプリケーションのコードとコンテンツを安全な方法で開発、デプロイ、管理する必要があります。 これが実現されなければ、アプリケーションのコードまたはコンテンツは、次のような脅威に対して脆弱なままになる可能性があります。

* SQL インジェクション
* セッション ハイジャック
* クロス サイト スクリプティング
* アプリケーションレベルの MITM
* アプリケーションレベルの DDoS

Web ベースのアプリケーションにおけるセキュリティ上の考慮事項に関する詳細な説明は、このドキュメントの対象範囲外です。 アプリケーションをセキュリティで保護するためのさらなるガイダンスを得るための出発点として、「[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page)」、特に [top 10 プロジェクト](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)のページを参照してください。OWASP メンバーが選んだ、Web アプリケーションにおける重要なセキュリティ リスクの現在のトップ 10 が記載されています。

## <a name="perform-penetration-testing-on-your-app"></a>アプリに対して侵入テストを実行する
App Service アプリの脆弱性のテストを開始する最も簡単な方法は、[Tinfoil Security との統合](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)を使用して、1 回のクリックでアプリに対して脆弱性のスキャンを実行することです。 テスト結果はわかりやすいレポートで表示され、詳しい手順に従ってそれぞれの脆弱性を修正する方法が説明されます。

独自の侵入テストを実行するか、別のスキャナー スイートまたはプロバイダーを使用する場合は、 [Azure 侵入テストの承認プロセス](https://security-forms.azure.com/penetration-testing/terms) に従い、事前の承認を得たうえで目的の侵入テストを実行する必要があります。

## <a name="https"></a> 顧客との通信をセキュリティで保護する
App Service アプリ用に作成された **\*.azurewebsites.net** ドメイン名を使用する場合は、SSL 証明書がすべての **\*.azurewebsites.net** ドメイン名に提供されているため、すぐに HTTPS を使用できます。 サイトで[カスタム ドメイン名](web-sites-custom-domain-name.md)を使用している場合は、SSL 証明書をアップロードし、カスタム ドメインに対して [HTTPS を有効にする](web-sites-configure-ssl-certificate.md)ことができます。

[HTTPS](https://en.wikipedia.org/wiki/HTTPS) を有効にすると、アプリとそのユーザー間の通信を狙った MITM 攻撃から保護することができます。

## <a name="secure-data-tier"></a>データ層をセキュリティで保護する
App Service は SQL Database と高度に統合されます。たとえば、すべての接続文字列はボード全体で暗号化され、アプリが実行される VM で、*アプリの実行時にのみ*暗号化が解除されます。 さらに、Azure SQL Database には、[保存暗号化](https://msdn.microsoft.com/library/dn948096.aspx)、[Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)、[動的データ マスク](../sql-database/sql-database-dynamic-data-masking-get-started.md)、[脅威検出](../sql-database/sql-database-threat-detection.md)など、サイバー攻撃の脅威からアプリケーション データを保護するのに役立つ多くのセキュリティ機能が用意されています。 機密データやコンプライアンスの要件がある場合、データをセキュリティで保護する方法の詳細については、「[SQL Database の保護](../sql-database/sql-database-security-overview.md)」をご覧ください。

ClearDB などのサードパーティのデータベース プロバイダーを使用する場合は、プロバイダーのマニュアルを直接参照してセキュリティのベスト プラクティスを確認する必要があります。  

## <a name="develop"></a> 開発とデプロイをセキュリティで保護する
### <a name="publishing-profiles-and-publish-settings"></a>プロファイルの発行と発行の設定
**Visual Studio**、**Web Matrix**、**Azure PowerShell**、**Azure コマンドライン インターフェイス (Azure CLI)** などのユーティリティを使用して、アプリケーションの開発、管理タスクの実行、タスクの自動化を行っている場合は、*発行設定*ファイルまたは*発行プロファイル*を使用できます。 どちらの種類のファイルも Azure に対する認証に使用されるため、許可されていないアクセスを防ぐためにセキュリティで保護する必要があります。

* **発行設定** ファイルには以下のものが含まれます。
  
  * Azure サブスクリプション ID
  * *アカウント名やパスワードを提供する必要なく*サブスクリプションの管理タスクを実行できるようにする管理証明書。
* **発行プロファイル** ファイルには以下のものが含まれます。
  
  * アプリへ発行するための情報

発行設定ファイルまたは発行プロファイル ファイルを使用するユーティリティを使用している場合は、発行設定または発行プロファイルを含むファイルをユーティリティにインポートし、その後、そのファイルを **削除** します。 ファイルを保持する必要がある場合、たとえば、プロジェクトに携わる他のユーザーと共有するには、アクセス許可が限定された *暗号化* されたディレクトリなど、セキュリティで保護された場所にファイルを保存します。

さらに、インポートされた資格情報が保護されていることを確認する必要があります。 たとえば、**Azure PowerShell** と **Azure コマンドライン インターフェイス (Azure CLI)** はどちらも、インポートされた情報を**ホーム ディレクトリ** (Linux または OS X システムでは *~*、Windows システムでは */users/yourusername*) に保存します。希望する場合は、さらにセキュリティを強化するために、オペレーティング システムに対応した暗号化ツールを使用してこれらの場所を**暗号化**できます。

### <a name="configuration-settings-and-connection-strings"></a>構成設定と接続文字列
接続文字列、認証資格情報、およびその他の機密情報は構成ファイルに保存するのが一般的な方法です。 残念ながら、これらのファイルは Web サイト上で公開される場合やパブリック リポジトリにチェックインされる場合があり、結果としてこの情報が危険にさらされる可能性があります。 たとえば、 [GitHub](https://github.com)で単純な検索を実行すると、パブリック リポジトリにシークレットが公開されている構成ファイルが無数に見つかります。

この情報は、アプリの構成ファイルとは別に保持しておくことをお勧めします。 App Service では、ランタイム環境の一部である構成情報を**アプリ設定**および**接続文字列**として保存できます。 大半のプログラミング言語では、値は、 *環境変数* を介して実行時にアプリケーションに公開されます。 .NET アプリケーションの場合、これらの値は実行時に .NET 構成に挿入されます。 このような状況とは別に、これらの構成設定は、 [Azure ポータル](https://portal.azure.com) またはユーティリティ (PowerShell や Azure CLI など) を使用して表示または構成する場合を除いて、暗号化されたままになります。 

App Service に構成情報を保存すると、アプリの管理者は、運用アプリの機密情報をロックダウンできます。 開発者は、アプリの開発用に別の構成設定のセットを使用できます。この設定は、App Service で構成されている設定に自動的に置き換わります。 開発者であっても、運用アプリ用に構成された機密情報を知る必要はありません。 App Service のアプリ設定と接続文字列の構成の詳細については、「[Azure App Service での Web アプリの構成](web-sites-configure.md)」を参照してください。

### <a name="ftps"></a>FTPS
Azure App Service は、 **FTPS**を介してアプリのファイル システムへの保護された FTP アクセスを提供します。 これによって、Web アプリ上のアプリケーション コードや診断ログに安全にアクセスできます。 FTP ではなく FTPS を常に使用することをお勧めします。 

アプリの FTPS リンクは、次の手順で見つけることができます。

1. [Azure ポータル](https://portal.azure.com)を開きます。
2. **[すべて参照]**を選択します。
3. **[参照]** ブレードで、**[App Services]** を選択します。
4. **[App Services]** ブレードで、目的のアプリを選択します。
5. アプリのブレードで、 **[すべての設定]**を選択します。
6. **[設定]** ブレードで、**[プロパティ]** を選択します。
7. **[設定]** ブレードに、FTP リンクと FTPS リンクが表示されます。 

FTPS の詳細については、「 [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure プラットフォームのセキュリティ、**セキュリティ インシデントまたは迷惑行為**の報告方法、またはサイトの**侵入テスト**の実施予定を Microsoft に通知する方法の詳細については、[Microsoft Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/security/)のセキュリティのセクションを参照してください。

App Service アプリの **web.config** ファイルまたは **applicationhost.config** ファイルの詳細については、「[Azure App Service Web Apps でロック解除される構成オプション](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)」を参照してください。

攻撃の検出に役立つことのある、App Service アプリのログ情報については、「 [Azure App Service の Web アプリの診断ログの有効化](web-sites-enable-diagnostic-log.md)」を参照してください。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)



---
title: 登録リストの種類別要件 | Azure
description: この記事では､パートナーが Azure Marketplace にアプリを公開しようとするときに理解しておく必要がある適格性条件と公開要件を説明します｡
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: jm-aditi-ms
manager: pabutler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 12/19/2018
ms.author: ellacroi
ms.openlocfilehash: ebe344d9f596f862fe5ffbfef083725e6527d0d3
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58650208"
---
# <a name="requirements-by-listing-type"></a>登録リストの要件  
技術要件とマーケティング コンテンツ要件は、ネットショップ、オファーの種類、登録リストの種類によって異なります。 次の仕様を再確認して、要件を満たしているか確認してください。  
1. ネットショップの要件:  
    *   [AppSource](#storefront-requirements-appsource)  
    *   [Azure Marketplace](#storefront-requirements-azure-marketplace)  
2. 登録リストの種類とオファーの種類に関する要件:  
    *   登録リストの種類とオファーの種類について詳しくは、「ソリューションの登録リストの種類を選定する」([docs.microsoft.com/azure/marketplace/determine-your-listing-type](./determine-your-listing-type.md)) を参照してください。  

## <a name="storefront-requirements-appsource"></a>ネットショップの要件:AppSource  
次の表では、公開先が AppSource である場合の前提条件の要件について説明しています。  

| 要件 | 詳細 | 必須または推奨 |  
|:--- |:--- |:--- |  
| ***Azure Active Directory (Azure AD)*** | アプリでは、同意が有効な Azure Active Directory フェデレーション シングル サインオン (Azure AD フェデレーション SSO) を許可する必要があります。<ul> <li>Azure AD フェデレーション SSO を有効にする方法の詳細については、「Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成」([docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)) を参照してください。</li> </ul> | 必須 |   
| ***Microsoft クラウド サービスとの統合*** | アプリを、Microsoft Power BI、Cortana Intelligence、または Microsoft Azure サービスなど、他の Microsoft クラウド サービスと統合する必要があります。<ul> <li>Microsoft Cloud サービスの例として、モノのインターネット (IoT) があります。</li> </ul> | 推奨 |  
| ***対象ユーザー*** | アプリの対象は、基幹業務のユーザーやビジネス オーナーである必要があります。 | 必須 | 
| ***ビジネス向けサービスとしてのソフトウェア (SaaS) アプリ*** | アプリは次の要件を満たしている必要があります。<ul> <li>基幹業務 SaaS アプリ</li> <li>業務プロセスに重点を置いている</li> <li>企業のお客様を対象とする</li> <li>ユーザーが勤務先の資格情報 (ユーザー名とパスワードなど) を使用してサインインできる</li> </ul> | 必須 |  
| ***無料試用期間および試用エクスペリエンス*** | 顧客が限られた期間無料でアプリを使用するために、次のいずれかの選択肢が用意されている必要があります。<ul> <li>`try` 方式を用意し、顧客が AppSource 内からアプリの試用版を開始できるようにする</li> <li>AppSource に `request trial` の選択肢を用意し、顧客がアプリの試用版を要求できるようにする</li> </ul>提供する無料試用版には、顧客が追加料金なしでアプリを試用できる期間をあらかじめ設ける必要があります。 | 必須 |  
| ***簡単に構成できてすぐに使用できるソリューション*** | アプリは、カスタマイズが不要で、簡単に短時間で構成およびセットアップできる必要があります。 | 必須 |  
| ***リード管理*** | ネットショップから潜在顧客を受け取るには、お使いの CRM で潜在顧客データの受け付けを有効にします。<ul> <li>CRM の例としては、Marketo、Microsoft Dynamics、Salesforce があります。</li> </ul> | 必須 |  
| ***プライバシー ポリシーと使用条件*** | プライバシー ポリシー ページへのリンクを公開 URL を使用して提供する必要があります。 使用条件は、公開時にテキストとして入力する必要があります。 | 必須 |  
| ***サポート*** | カスタマー サポート ページへのリンクを公開 URL を使用して提供する必要があります。 アプリが試用版である場合、試用期間中は追加料金なしでサポートする必要があります。 | 必須 |  

## <a name="storefront-requirements-azure-marketplace"></a>ネットショップの要件:Azure Marketplace  
次に、Azure Marketplace における登録リストの種類について前提条件の要件を示します。  

| 要件 | 詳細 | 登録リストの種類 |  
|:--- |:--- |:--- |  
| ***参加ポリシー*** | アプリは、Azure Marketplace 参加ポリシーに準拠している必要があります。<ul> <li>参加ポリシーについて詳しくは、Azure Marketplace 参加ポリシーに関するページ ([azure.microsoft.com/support/legal/marketplace/participation-policies](https://azure.microsoft.com/support/legal/marketplace/participation-policies)) をご覧ください。</li></ul> | list<br />トランザクション<br />試用 |  
| ***Microsoft との統合*** | オファーは、コンピューティング、ネットワーク、ストレージなどの Microsoft Azure サービス タイプを使用または拡張する必要があります。 データベース、セキュリティ、ネットワークなど、既存の Azure Marketplace カテゴリにオファーが合致している必要があります。<ul> <li>Marketplace オファーについて詳しくは、Marketplace アプリのページ ([azuremarketplace.microsoft.com/marketplace/apps](https://azuremarketplace.microsoft.com/marketplace/apps)) をご覧ください。</li> </ul> | list<br />トランザクション<br />試用 |  
| ***対象ユーザー*** | オファーは、IT プロフェッショナル、クラウド開発者、または顧客のその他の技術的役割を対象としたものでなければなりません。 | list<br />トランザクション<br />試用 |  
| ***リード管理*** | ネットショップから潜在顧客を受け取るには、お使いの CRM (Marketo、Microsoft Dynamics、または Salesforce) で潜在顧客データの受け付けを有効にします。 | list<br />トランザクション<br />試用 |  
| ***プライバシー ポリシーと使用条件*** | プライバシー ポリシー ページへのリンクを公開 URL を使用して提供する必要があります。 使用条件は、公開時にテキストとして入力する必要があります。 | list<br />トランザクション<br />試用 |  
| ***サポート*** | カスタマー サポート ページへのリンクを公開 URL を使用して提供する必要があります。 オファーが試用版である場合、試用期間中は追加料金なしでサポートする必要があります。 | トランザクション<br />試用 |    

## <a name="non-transact-listings"></a>トランザクション以外の登録リスト  
ここでは、使用する登録リストの種類がトランザクション以外であるすべての種類のオファーについて説明します。 

### <a name="list"></a>List  
登録リストの種類としての "リスト" には、マーケットプレース内のネットショップに存在する次の種類のオファーが含まれます。  

| プランの種類 | ネットショップ | 詳細 |  
|:---        |:---        |:---     |  
| コンサルティング サービス | AppSource | 要件:AppSource:リスト:コンサルティング サービス |  
| コンサルティング サービス | Azure Marketplace | 要件:Azure Marketplace:リスト:コンサルティング サービス |  
| 連絡する | AppSource | [](#) |  
| 連絡する | Azure Marketplace | 要件:AppSource:リスト:連絡する |  

#### <a name="requirements-appsource-list-consulting-service"></a>要件:AppSource:リスト:コンサルティング サービス  

| 必要条件 | 詳細 |  
|:--- |:--- |  
| サービス オファーの特性 | コンサルティング サービスは、次の条件を満たしている必要があります。<ul> <li>固定スコープ、固定期間、固定価格 (または無料) の契約として提供される。</li> <li>基本的に購入前が優先される。</li> <li>単一顧客に限定される。</li> <li>サイト上で実施される。</li> </ul> |  
| コンサルティング サービスのパートナー要件 | サービスの対象となる分野の条件を満たす必要があります。<table><tr><th>ソリューション分野</th><th>条件</th></tr><tr><td>Dynamics 365 for Customer Engagement</td><td>Silver または Gold クラウド カスタマー リレーションシップ マネジメント コンピテンシーを備える。</td></tr><tr><td>Dynamics 365 for Finance and Operations (Enterprise Edition)</td><td>Silver または Gold のエンタープライズ リソース プラニング コンピテンシーを有しており、過去 12 か月間にクラウド運用で 25,000 ドル以上の収益がある。</td></tr><tr><td>Dynamics 365 for Finance and Operations (Business Edition)</td><td>クラウド サービス プロバイダー (CSP) または デジタル指名パートナー (DPOR) として顧客にサービスを提供している。</td></tr><tr><td>Power BI</td><td>ソリューション パートナーの条件を満たしている。</td></tr><tr><td>PowerApps</td><td>パートナー ショーケース ソリューションを備える。</td></tr></table><ul> <li>カスタマー リレーションシップ マネジメントの詳細については、「Cloud Customer Relationship Management」([partner.microsoft.com/membership/cloud-customer-relationship-management-competency](https://partner.microsoft.com/membership/cloud-customer-relationship-management-competency)) を参照してください。</li> <li>リソース プラニングの詳細については、「Enterprise Resource Planning」([partner.microsoft.com/membership/enterprise-resource-planning-competency](https://partner.microsoft.com/membership/enterprise-resource-planning-competency)) を参照してください。</li> <li>CSP の詳細については、クラウド サービス プロバイダーに関するページ ([partner.microsoft.com/cloud-solution-provider](https://partner.microsoft.com/cloud-solution-provider)) を参照してください。</li> <li>DPOR の詳細については、「デジタル指名パートナーとパートナー アソシエーション」([partner.microsoft.com/membership/digital-partner-of-record](https://partner.microsoft.com/membership/digital-partner-of-record)) を参照してください。</li> <li>ソリューション パートナーの条件の詳細については、ソリューション パートナーの概要とインセンティブ ドキュメント ([www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf](https://www.microsoftpartnerserverandcloud.com/_layouts/download.aspx?SourceUrl=Hosted%20Documents/Power%20BI%20Program%20Overview%20%26%20Incentives.pdf)) を参照してください。</li> <li>パートナー ショーケースの詳細については、「パートナー ショーケース」([powerapps.microsoft.com/partner-showcase](https://powerapps.microsoft.com/partner-showcase)) を参照してください。</li> </ul> |  

#### <a name="requirements-azure-marketplace-list-consulting-service"></a>要件:Azure Marketplace:リスト:コンサルティング サービス  

| 必要条件 | 詳細 |  
|:--- |:--- |  
| サービス オファーの特性 | コンサルティング サービスは、次の条件を満たしている必要があります。<ul> <li>固定スコープ、固定期間、固定価格 (または無料) の契約として提供される。</li> <li>基本的に購入前が優先される。</li> <li>単一顧客に限定される。</li> <li>サイト上で実施される。</li> </ul> |  
| コンサルティング サービスのパートナー要件 | サービスの対象となる分野について、以下に示したコンピテンシーの 1 つで、シルバーまたはゴールドの資格を有している必要があります。 <table><tr><th>ソリューション分野</th><th>コンピテンシー</th></tr><td>クラウド プラットフォームとインフラストラクチャ</td><td>クラウド プラットフォーム<br />データ センター</td><tr><td>アプリケーション開発と ISV</td><td>アプリケーション開発<br />アプリケーションの統合<br />DevOps</td></tr><tr><td>データ管理と分析</td><td>データ分析<br />データ プラットフォーム</td></tr></table><ul> <li>コンピテンシーの詳細については、Microsoft Partner Network を通じてコンピテンシーを取得する方法に関するページ ([partner.microsoft.com/membership/competencies](https://partner.microsoft.com/membership/competencies)) を参照してください。</li> <li>登録情報の詳細については、「Azure Marketplace コンサルティング サービス」([docs.microsoft.com/azure/marketplace/consulting-services](https://docs.microsoft.com/azure/marketplace/consulting-services)) を参照してください。</li></ul> |  

<!-- #### Requirements: Azure Marketplace: List: Contact Me -->

---

### <a name="trial"></a>試用版  

| プランの種類 | ネットショップ | 詳細 |  
|:---        |:---        |:---     |  
| 無料/SaaS 試用版 | AppSource | 登録リストの種類の要件:試用版 |  
| 無料/SaaS 試用版 | Azure Marketplace | 要件:Azure Marketplace:試用版:無料試用版/SaaS 試用版 |  
| 対話型デモ | AppSource | 登録リストの種類の要件:試用版 |  
| 対話型デモ | Azure Marketplace | [要件:Azure Marketplace:試用版:対話型デモ](#requirements-azure-marketplace-trial-interactive-demo) |  
| 体験版 | AppSource | 登録リストの種類の要件:試用版 |  
| 体験版 | Azure Marketplace | [要件:Azure Marketplace:試用版:体験版](#requirements-azure-marketplace-trial-test-drive) |  

#### <a name="requirements-azure-marketplace-trial"></a>要件:Azure Marketplace:試用版  

| 要件 | 詳細 |  
|:--- |:--- |  
| 無料試用期間および試用エクスペリエンス | 顧客は、限られた期間、アプリを無料で使用することができます。<br /><br />オファーまたはアプリに関して、顧客にはライセンス料金やサブスクリプション料金の支払いを一切要求しません。 基本となる Microsoft のファーストパーティ製品またはサービスの対価を顧客には一切要求しません。 すべての試用版オプションは、公開元の Azure サブスクリプションにデプロイされます。 試用版のコストの最適化と管理は、公開元によってのみ決められます。<br /><br />無料試用版、対話型デモ、または体験版を選ぶことができます。 何を選んでも、無料試用版では、追加コストなしでアプリを試すための所定の期間を顧客に提供する必要があります。<ul> <li>体験版の作成を始めるには、[amp-testdrive@microsoft.com](mailto:amp-testdrive@microsoft.com) までメールをお送りください。</li> </ul>注:Azure Marketplace の SaaS 試用エクスペリエンスでは、勤務先の資格情報を使用したサインインを顧客に許可する必要があります。<ul> <li>詳細については、[docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified#appsource-trial-experiences) の「AppSource の試用エクスペリエンス」セクションを参照してください。</li> </ul> |  
| 簡単に構成できてすぐに使用できるソリューション | アプリは、短時間で簡単に構成およびセットアップできる必要があります。 |  
| 可用性/アップタイム | SaaS アプリまたはプラットフォームのアップタイムは 99.9% 以上である必要があります。 |  
| Azure Active Directory | オファーでは、同意が有効な Azure Active Directory (Azure AD) フェデレーション シングル サインオン (SSO) (Azure AD フェデレーション SSO) を許可する必要があります。 |  

#### <a name="requirements-azure-marketplace-trial-free-trial--saas-trial"></a>要件:Azure Marketplace:試用版:無料試用版/SaaS 試用版  

| 長所 | 要件 |  
|:--- |:--- |  
| 顧客が製品を試用したうえで、有料での使用に自動で切り替わる方式により購入できます。 また、顧客の概念実証を可能にし、Microsoft 販売チームによるエンゲージメントに参画します。 | ソリューションが仮想マシンまたはソリューション テンプレートの場合<br /><br />ソリューションが SaaS オファリングであり、マルチテナント SaaS 製品を提供する場合<br /><br />顧客を誘って迅速に実行させる最初の実行エクスペリエンスがある場合<br /><br />1 つのテナントがあるが、ゲスト ユーザーとして顧客を追加している場合 |  

#### <a name="requirements-azure-marketplace-trial-interactive-demo"></a>要件:Azure Marketplace:試用版:対話型デモ  

| 長所 | 要件 |  
|:--- |:--- |  
| 複雑なセットアップなしで動作するソリューションを顧客に見せることができる。 | 試用期間に実現するのが困難な複雑なセットアップを必要とするソリューションの場合 |  

#### <a name="requirements-azure-marketplace-trial-test-drive"></a>要件:Azure Marketplace:試用版:体験版  

| 長所 | 要件 |  
|:--- |:--- |  
| 購入前に、顧客が製品を試すことができる。<br /><br />事前構成済みの設定を使用してソリューションのガイド付きエクスペリエンスが得られる。<br /><br />他にも、体験版には次の利点があります。<ul> <li>Marketplace でのユーザー検索の 27% は、ユーザーが体験版でオファリングを見るだけで改善されます。</li> <li>体験版ありのオファリングは、体験版なしのオファリングよりリードが 38% 増えます。</li> <li>Marketplace での新規顧客獲得の 36% は体験版を利用した顧客からです。</li> <li>体験版では、Microsoft の現場販売者は共同販売作業のためにパートナーの製品をよりよく理解できます。</li> </ul> | ソリューションがシングルテナントの仮想マシン、ソリューション、テンプレート、SaaS アプリである場合、またはソリューションのプロビジョニングが複雑である場合 <br /><br />試用版を有料のオファーに変換する方法がない場合 |  

---

## <a name="transact-specific-listings"></a>トランザクション固有の登録リスト

### <a name="transact"></a>トランザクション  

| プランの種類 | ネットショップ | 詳細 |   
|:---        |:---        | :--- |  
| Azure アプリ:マネージド アプリ | Azure Marketplace | 要件:Azure Marketplace:トランザクション:Azure アプリ:マネージド アプリ |  
| Azure アプリ:ソリューション テンプレート | Azure Marketplace | 要件:Azure Marketplace:トランザクション:Azure アプリ:ソリューション テンプレート |  
| Containers | Azure Marketplace | [要件:Azure Marketplace:トランザクション:コンテナー](#requirements-azure-marketplace-transact-container) |  
| SaaS アプリ  | Azure Marketplace | [要件:Azure Marketplace:トランザクション:Saas アプリ](#requirements-azure-marketplace-transact-saas-app) |  
| 仮想マシン | Azure Marketplace | [要件:Azure Marketplace:トランザクション:仮想マシン](#requirements-azure-marketplace-transact-virtual-machine) |  

<!-- #### Requirements: Azure Marketplace: Transact: Azure apps: Managed app  

#### Requirements: Azure Marketplace: Transact: Azure apps: Solution template   -->

#### <a name="requirements-azure-marketplace-transact-container"></a>要件:Azure Marketplace:トランザクション:コンテナー  

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | 無料または BYOL のどちらかの課金モデルをサポートします。 |  
| Docker ベースのイメージ | コンテナー イメージは、Docker イメージの形式に基づき、Azure Container Registry から取得される必要があります。 |  

#### <a name="requirements-azure-marketplace-transact-saas-app"></a>要件:Azure Marketplace:トランザクション:SaaS アプリ  

| 要件 | 詳細 |  
|:--- |:--- |  
| 請求/メータリング | オファーについては、毎月均一料金で課金されます。 使用量ベースの価格と使用量ベースの "*補正発注*" オプションは、現時点でサポートされていません。 |  
| キャンセル | オファーは、いつでも顧客が解約できます。 |  
| トランザクションのランディング ページ | Azure ブランド提携トランザクションのランディング ページをホストします。 顧客はそのランディング ページから、SaaS サービス アカウントを作成したり管理したりすることができます。 |  
| SaaS サブスクリプション API | SaaS サブスクリプションと対話することによってユーザー アカウントとサービス プランを作成、更新、削除するサービスを提供します。 重要な API の変更には、すべて 24 時間以内に対応する必要があります。 重要でない API の変更は定期的に更新されます。 |  

#### <a name="requirements-azure-marketplace-transact-virtual-machine"></a>要件:Azure Marketplace:トランザクション:仮想マシン  

| 要件 | 詳細 |  
|:--- |:--- | 
| 請求/メータリング | 対象の VM は、BYOL または従量課金制の月額請求をサポートしている必要があります。 |  
| Azure と互換性がある仮想ハード ディスク (VHD) | VM は、Windows または Linux 上に構築されている必要があります。<ul> <li>Linux VHD の作成の詳細については、「[Azure で動作保証済みの Linux ディストリビューション](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)」を参照してください。</li> <li>Windows VHD の作成の詳細については、「[Azure と互換性のある VHD の作成](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md)」を参照してください。</li> </ul> |  

## <a name="next-steps"></a>次の手順
*   「[Azure Marketplace と AppSource のパブリッシャー ガイド](./marketplace-publishers-guide.md)」ページを参照してください。  


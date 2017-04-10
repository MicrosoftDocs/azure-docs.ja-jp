---
title: "Azure Government DoD の概要 | Microsoft Docs"
description: "Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Azure Government における国防総省 (DoD)
## <a name="overview"></a>概要
Azure Government は、国防総省 (DoD) の各機関により、<a href="http://iasecontent.disa.mil/cloud/SRG/index.html">『DoD Cloud Computing Security Requirements Guide (DoD クラウド コンピューティング セキュリティ要件ガイド)』のバージョン 1、リリース 2</a> に記載されている Impact Level 4 (L4) および Impact Level 5 (L5) のワークロードなど、多岐にわたるワークロードやソリューションをデプロイするために利用されています。

Azure Government は、米国国防情報システム局から Information Impact Level 5 DoD Provisional Authorization を取得した、最初かつ唯一のハイパースケール商用クラウド サービスです。 さらに、米国国防総省のお客様のワークロード専用の Azure Government リージョンが一般公開されました。

DoD にとって、クラウドへの移行を進めるうえで重要な要因の&1; つとなるのは、各組織がそれぞれの業務に集中できるように、組織内 IT ソリューションの構築や管理にかかる手間を最小限に抑える手段の実現です。

Azure Government ベースのクラウド アーキテクチャを利用することで、SharePoint などの IT コモディティ サービスやその他のアプリケーション ワークロードの管理が可能になり、DoD の職員は業務上の目的に集中して取り組むことができます。  これにより、アプリケーションのデプロイ、分析、サイバー セキュリティに重点を置いて、重要な IT リソースを再配置できるようになります。

Azure によって得られる弾力性と柔軟性は、DoD のお客様にとって大きなメリットとなります。 クラウドでのワークロードのスケールアップは、オンプレミス環境や DoD のデータ センターを使用している場合に従来のハードウェアとサービスの調達プロセスを通じてスケールアップを行う方法よりも、単純かつ迅速で、コスト効率に優れています。 たとえば、新しいマルチサーバー ハードウェアを調達するには、テスト環境向けであっても数か月かかる場合があり、多大な設備投資費の承認を受ける必要もあります。 一方で Azure を利用した場合、既存のワークロードのテスト移行は、コスト効率に優れた方法で数週間または数日で構成できます (テストが終わると、移行環境は破棄できるので、継続的なコストは発生しません)。

この柔軟性の効果は絶大です。 DoD のお客様は、Azure への移行によって費用を節約できるだけでなく、クラウドを利用して新たな機会を得ることができます。 たとえば、簡単にテスト環境を立ち上げて新しいテクノロジを分析できるため、アプリケーションを移行して Azure でテストしてから、クラウド上の運用環境へのデプロイを開始できます。 このため、よりコスト効率に優れた選択肢を、簡単かつリスクを伴わない方法で検討することができます。

もう&1; つの重要な分野はセキュリティです。あらゆるクラウド デプロイには、安全で信頼性の高いサービスを確実に提供するため、適切な計画立案が求められますが、実際のところ、最適に構成された Azure Government のクラウドベース ワークロード (L4 までのワークロード) は、DoD の所在地やデータ センターにおける多くの従来型デプロイ環境よりも安全です。 これは、防衛機関にはあらゆる資産を物理的に保護するための経験と専門知識があるものの、IT に面した領域においては別の課題が生じるためです。 サイバー セキュリティは急速に変化する分野であり、専門的なスキルと、必要に応じてすばやく対策を講じ、それを展開できる能力が求められます。 現在の Azure プラットフォームでは、商用と政府機関向けの両方で数十万のお客様をサポートしています。このような顧客規模により、Microsoft では、進化を続ける攻撃ベクトルを迅速に検知し、適切な防御措置の速やかな開発と実装にリソースを投入することができます。

## <a name="dod-region-qa"></a>DoD リージョンに関する Q&A

### <a name="what-are-the-azure-government-dod-regions"></a>Azure Government DoD リージョンとは何ですか。 
US DoD East および US DoD Central リージョンは、米国国防総省 (DoD) のクラウド コンピューティング (特に、『DoD Cloud Computing Security Requirements Guide (DoD クラウド コンピューティング セキュリティ要件ガイド)』(SRG) に基づき DoD Impact Level 5 として指定されたデータ) のセキュリティ要件を満たすように設計された、物理的に分離された Microsoft Azure のリージョンです。   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Azure Government リージョンと Azure Government DoD リージョンの違いは何ですか。 
Azure Government は、連邦政府、州、地方自治体のお客様、部族政府、ITAR の対象となる機関、およびこれらの組織に代わって業務を遂行するソリューション プロバイダー向けにサービスを提供する米国政府コミュニティ クラウドです。 どの Azure Government リージョンも、DoD Impact Level 5 のデータおよび FedRAMP High 標準のセキュリティ要件を満たすよう設計および運用されています。

Azure Government DoD リージョンは、Impact Level 5 のデータに求められる物理的な分離の要件をサポートするように設計されています。このサポートのために、DoD のお客様専用のコンピューティングおよびストレージのインフラストラクチャが提供されます。  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>Impact Level 4 のデータと Impact Level 5 のデータの違いは何ですか。  
Impact Level 4 のデータとは、管理された非機密情報 (CUI) のことを指し、輸出規制、個人情報保護医療情報、および明示的な CUI 指定 (For Official Use Only、Law Enforcement Sensitive、Sensitive Security Information など) が必要なその他のデータを含みます。

Impact Level 5 のデータには、情報の所有者、公法、またはその他の政府の規制によって、さらに高いレベルの保護が必要と認められた、管理された非機密情報 (CUI) が含まれます。  Impact Level 5 のデータには非機密の国家安全保障システムが含まれます。  SRG の Impact Level および各レベルに固有の要件と特性の詳細については、『DoD Cloud Computing Security Requirements Guide (DoD クラウド コンピューティング セキュリティ要件ガイド)』のセクション 3 を参照してください。  

### <a name="what-data-is-categorized-as-impact-level-5"></a>どのようなデータが Impact Level 5 に分類されますか。 
Level 5 のデータには、情報の所有者、公法、またはその他の政府の規制によって、Level 4 の場合よりもさらに高いレベルの保護が必要と認められた、管理された非機密情報 (CUI) が含まれます。 Level 5 では、非機密の国家安全保障システム (NSS) もサポートされます。  このレベルには、CNSSI-1253 に基づく NSS と CUI の情報分類 (中程度の機密性と中程度の整合性 (M-M-x) まで) が含まれています。

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>Impact Level 5 のデータをサポートするために、Microsoft が特別に行っていることは何ですか。 
Impact Level 5 のデータは、その定義から言って、DoD のお客様が連邦政府以外のテナントから物理的に分離されていることを保証している専用インフラストラクチャでしか処理できません。  Microsoft は、US DoD East および US DoD Central リージョンの提供にあたって、DoD のお客様に専用のサービスを提供しています。このサービスは、DoD により設定された要件よりもさらに厳しい要件を満たしており、他のどのハイパースケール商用クラウド ソリューションも提供していないレベルでの保護および機能実装を実現しています。

### <a name="do-these-regions-support-classified-data-requirements"></a>これらのリージョンは、機密データの要件をサポートしていますか。 
これらの Azure Government DoD リージョンは、Impact Level 5 以下の非機密データのみをサポートしています。  Impact Level 6 のデータが、Secret までの機密情報として定義されています。

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>Azure Government DoD リージョンを使用できる DoD の組織はどれですか。 
US DoD East および US DoD Central リージョンは、米国国防総省のお客様ベースをサポートするために構築されたものです。  次のトピックがあります。
* 国防長官府
* 統合参謀本部
* 統合参謀事務局
* 国防局
* 国防現業部門
* 陸軍省
* 海軍省 (米国海兵隊を含む)
* 空軍省
* 米国沿岸警備隊
* 統合軍
* 上記の承認されたいずれかの機関の管理下または監督下にあるその他の府、局、活動部署、部隊

### <a name="are-the-dod-regions-more-secure"></a>DoD リージョンの方が安全性が高いのでしょうか。 
Microsoft は、すべての Azure データセンターおよびサポート用のインフラストラクチャの運用において、セキュリティとコンプライアンスに関するローカル標準および国際標準に適合しています。これにより、すべての商用クラウド プラットフォームでのコンプライアンスへの投資とその達成を支援しています。  これらの新しい DoD リージョンでは、クラウド コンピューティングに関する DoD の SRG に定義された要件を満たすための特定の保証および契約を提供しています。

### <a name="why-are-there-multiple-dod-regions"></a>複数の DoD リージョンが存在するのはなぜですか。 
Microsoft では、複数の DoD リージョンを用意することで、リージョンをまたぐ障害復旧シナリオに対応したソリューション設計と、これによるビジネス継続性の確保およびシステム認定要件への適合を支援しています。  また、お客様の物理的な所在地に最も近い場所にソリューションをデプロイすることでパフォーマンスを最適化することも可能です。

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>これらの DoD リージョンは NIPRNet に接続されていますか。 
DoD では、CUI を扱う商用クラウド サービスがユーザーに接続する場合、クラウド アクセス ポイント (CAP) を介するよう定めています。  そのため、Azure DoD リージョンは、地理的に分散された複数の CAP への冗長接続を通じて NIPRNet に接続されます。  DoD CAP は、DoD 情報システムのネットワークとサービスを保護する、ネットワーク境界保護およびデバイス監視のシステムです。

### <a name="what-does-general-availability-mean"></a>一般公開とは何ですか。 
一般公開とは、運用環境のワークロードのサポートに Azure Government の DoD リージョンが使用できること、およびすべてのサービス (リージョンにデプロイされており、かつ一般公開されているもの) で SLA に基づいた価格設定がサポートされることを意味します。

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>DoD のお客様が Azure Government DoD のサービスを入手するにはどうすればいいですか。 
承認された機関は、Azure Government と同じリセラー チャネルから Azure Government DoD サービスを購入することができます。  クラウド サービスの入手計画とコストの見積もりを簡易化するための Microsoft のコミットメントの&1; つとして、Azure Government DoD リージョンの価格は、一般公開のタイミングで Azure 料金計算ツールに含まれる予定です。  Azure Government DoD サービスでは、要求に応じて簡単にスケールアップやスケールダウンができるので、支払いは使用した分だけで済みます。
既に Azure Government を使用中で、エンタープライズ契約を済ませているお客様については、契約の変更は必要ありません。  

### <a name="how-are-the-dod-regions-priced"></a>DoD リージョンの価格はどのように決定されていますか。 
DoD リージョンではリージョン ベースの価格体系を採用しています。  つまり、検証済みの DoD のお客様に対するサービス コストは、ワークロードを実行する Azure Government リージョンに基づいたものとなります。  価格情報の詳細については、Microsoft アカウント責任者にお問い合わせください。  DoD リージョンの価格は、将来的に Azure.com の料金計算ツールで確認できるようになります。

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>DoD の組織が Azure Government DoD リージョンを使用するには、どのような検証を受ける必要がありますか。 
Azure DoD リージョンにアクセスするには、お客様は組織および Azure DoD 環境の使用目的を確認するための事前の資格審査プロセスを完了する必要があります。  事前の資格審査プロセスの完了後、Microsoft はサブスクリプションの作成、環境へのアクセス、および組織の他のメンバーに対するロールベースのアクセス制御の提供に関する詳細な手順を組織の申請者に提供します。

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>Azure 上で構築を行っている独立系ソフトウェア ベンダーやソリューション プロバイダーは、Azure Government DoD リージョンにソリューションをデプロイできますか。 
Azure 上で構築したクラウド サービスを扱うソリューション プロバイダーは、Azure Government DoD リージョンで DoD 専用のシングル テナント ソリューションとマルチ テナント ソリューションを運用できます。  これらのプロバイダーは、まず、承認された DoD の機関との契約に関する文書化された証拠か、承認された DoD の機関から提供される証明書を提出することで、資格を示す必要があります。  Azure Government DoD リージョンでサービスを提供するプロバイダーは、Impact Level 5 の情報をサービスで扱うにあたり、コンピューター ネットワークの防御、インシデント レポート、選ばれた職員によるソリューションの運用を行う必要があります。  ソリューション プロバイダー向けの詳しいガイダンスについては、『DoD Cloud Computing Security Requirements Guide (DoD クラウド コンピューティング セキュリティ要件ガイド)』を参照してください。

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>Office 365 や Microsoft Dynamics 365 はこのプランの一部ですか。 
Microsoft では、このプランと共に、Impact Level 5 の DoD 向け Office 365 サービスを提供しています。  Dynamics 365 については、将来的に Azure DoD リージョンから Impact Level 5 のサービスを提供する予定です。

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>サブスクリプションを入手した後、DoD リージョンにはどのように接続すればよいですか。 
Azure government の DoD リージョンは、Azure Government 管理ポータルから使用できます。  使用が認められた DoD のお客様には、使用可能なサービスをデプロイする際に、使用可能なオプションとしてリージョンの一覧が表示されます。  Azure Government のサブスクリプション管理に関する一般的なガイダンスについては、Microsoft のドキュメントを参照してください。

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>Impact Level 5 認定スコープに含まれるサービスには、どのようなものがありますか。 
Azure では新しいサービスや機能が毎週追加されますので、スコープ内となるサービスの数は定期的に増加します。  最新情報については、<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft セキュリティ センターをご確認ください。


## <a name="next-steps"></a>次のステップ:
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA">Microsoft セキュリティ センター - DoD Web ページ</a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html">DoD クラウド コンピューティング セキュリティ要件ガイド、バージョン 1、リリース 2</a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/">Azure Government のリセラー チャネル

<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>




<!--HONumber=Jan17_HO3-->



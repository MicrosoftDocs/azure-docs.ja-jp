---
title: Azure セキュリティの技術的な機能 | Microsoft Docs
description: クラウドベースのコンピューティング サービスについて学びます。これには、アプリケーションまたはエンタープライスのニーズを満たすために自動的にスケールアップとスケールダウンを行うことができる、コンピューティング インスタンスとサービスの多様な選択肢が含まれます。
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2017
ms.author: TomSh
ms.openlocfilehash: c904d8306c145bad57ca7d77b139a2ad6d216b0b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500255"
---
# <a name="azure-security-technical-capabilities"></a>Azure セキュリティの技術的な機能

Azure の現在の利用者と今後の利用予定者が、Azure プラットフォーム内やその周辺で提供されるさまざまなセキュリティ関連機能について理解し、使用できるように、マイクロソフトは一連のホワイト ペーパー、セキュリティ概要、ベスト プラクティス、およびチェックリストを作成しました。 このトピックの内容は広範囲かつ詳細であることから定期的に更新されています。 このドキュメントは、以下の要約のセクションで概要を示している一連の内容の一部です。 この Azure セキュリティ シリーズについて詳しくは、(URL) をご覧ください。

## <a name="azure-platform"></a>Azure プラットフォーム

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) は、インフラストラクチャとアプリケーション サービスで構成されるクラウド プラットフォームで、データ サービスと高度な分析機能が統合されており、開発者用ツールとサービスがマイクロソフトのパブリック クラウド データ センター内にホストされています。 Azure は基本的な演算、ネットワーク、ストレージから、モバイルおよび Web アプリ サービス、モノのインターネットなどのフル クラウドのシナリオまで、数多くの機能やシナリオで使用されます。他にも、オープン ソース技術とともに使用したり、ハイブリッド クラウドとしてデプロイしたり、お客様のデータ センター内にホストしたりできます。 Azure には、コストを下げ、革新を促し、システムをプロアクティブに管理するための構成ブロックとして使用できる、クラウド テクノロジーが用意されています。 IT 資産を構築し、クラウド プロバイダーに移行したとしましょう。このとき、移行したアプリケーションやデータをどこまで保護できるかは、採用したプロバイダーがクラウド ベースの資産のセキュリティ管理のためにどのようなサービスと体制を用意しているかに応じて変わってきます。

Microsoft Azure は、チームがクラウド スキルセットおよびプロジェクトの複雑レベルが異なる中で作業するための、セキュリティで保護された一貫性のあるアプリケーション プラットフォームおよびサービスとしてのインフラストラクチャを提供する、唯一のクラウド コンピューティング プロバイダーです。Azure にはデータ サービスおよび分析機能が統合されており、マイクロソフトおよびマイクロソフト以外のプラットフォームの両方のあらゆる場所にあるデータからインテリジェンスを発見します。また、オープン フレームワークやツールにより、オンプレミスにクラウドを統合したり、Azure のクラウド サービスをオンプレミスのデータセンターにデプロイしたりできます。 マイクロソフトの Trusted Cloud の一部として、お客様は Azure の業界をリードするセキュリティ、信頼性、コンプライアンス、プライバシー、およびクラウドで組織をサポートするユーザー、パートナー、およびプロセスの広大なネットワークに信頼を置いています。

Microsoft Azure では、次のことを行うことができます。

- クラウドを活用してイノベーションを加速する。

- 洞察によりビジネス上の意思決定やアプリを強化する。

- 自由に構築して任意の場所にデプロイする。

- ビジネスを保護する。

## <a name="scope"></a>Scope (スコープ)

このホワイトペーパーは主に、セキュリティや [Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-introduction)、[Microsoft Azure SQL Database](https://docs.microsoft.com/azure/sql-database/)、[Microsoft Azure の仮想マシン モデル](https://docs.microsoft.com/azure/virtual-machines/)といった Microsoft Azure のコア コンポーネントをサポートする機能、およびそれらをすべて管理するツールやインフラストラクチャについて取り上げます。 このホワイトペーパーは、お客様のデータのセキュリティやプライバシーを保護する役割を担う、Microsoft Azure の技術的な機能に焦点を当てます。

この責任共有モデルを理解することは、クラウドへの移行を進めているお客様にとって重要です。 クラウド プロバイダーはセキュリティやコンプライアンスの取り組みにおいて多大なメリットを提供しますが、これらの利点はお客様からユーザー、アプリケーション、およびサービス内容を保護する責務を解除するわけではありません。

IaaS ソリューションにおいて、お客様はオペレーティング システム、ネットワーク構成、アプリケーション、ID、クライアント、およびデータのセキュリティを確保および管理する責任や共有責任を負っています。  IaaS デプロイメント上に構築されている PaaS ソリューションにおいても、お客様はアプリケーション、ID、クライアント、およびデータのセキュリティを確保および管理する責任や共有責任を負っています。 SaaS ソリューションにおいてもなお、お客様には引き続き責任があります。 データが正しく分類されていることを確認する必要があり、ユーザーおよびエンドポイント デバイスを管理する責任を共有します。

このドキュメントは、Azure Web Sites、Azure Active Directory、HDInsight、Media Services などの Microsoft Azure プラットフォームの関連コンポーネント、およびコア コンポーネントの上の層にある他のサービスの詳細については取り上げません。 一般的な情報は最低限のものしか提供されていませんが、読者はマイクロソフトが提供およびこのホワイトペーパーからリンクされている他の参考資料で説明されている Azure の基本概念を理解していると見なされます。

## <a name="available-security-technical-capabilities-to-fulfil-user-customer-responsibility---big-picture"></a>ユーザー (お客様) が責任を満たすために使用できるセキュリティの技術的な機能 - 全体像

Microsoft Azure には、お客様がセキュリティ、プライバシー、およびコンプライアンスのニーズを満たすのに役立つサービスが用意されています。 次の図は、ユーザーが業界標準に基づいてセキュリティで保護およびコンプライアンスに準拠しているアプリケーション インフラストラクチャを構築するために使用できる、各種 Azure サービスについて説明するのに役立ちます。

![セキュリティの技術的な機能 - 全体像](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access-protect"></a>ID とユーザーのアクセスを管理および制御する (保護)

Azure を使用すると、ユーザー ID や資格情報を管理し、アクセスを制御してビジネスおよび個人の情報を保護するのに役立ちます。

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft ID およびアクセス管理ソリューションは、IT が企業のデータ センター全体とクラウドのアプリケーションとリソースへのアクセスを保護するのに役立ち、他要素認証や条件付きアクセスポリシーなどの追加レベルの検証を可能にします。 高度なセキュリティ報告、監査、および警告によって疑わしいアクティビティを監視し、潜在的なセキュリティ上の問題を軽減できます。 [Azure Active Directory Premium](https://docs.microsoft.com/azure/active-directory/active-directory-editions) は、何千ものクラウドへのシングル サインオン (SaaS) アプリケーション、およびオンプレミスで実行する Web アプリケーションへのアクセスを提供します。

Azure Active Directory (Azure AD) のセキュリティ上の利点は次のとおりです。

- ハイブリッドのエンタープライズ全体の各ユーザーに個別の ID を作成して管理し、ユーザー、グループ、およびデバイスが同期された状態を維持する

- 何千もの事前統合された SaaS アプリを含むアプリケーションにシングル サインオン アクセスを提供します。

- ルール ベースの多要素認証をオンプレミス アプリケーションとクラウド アプリケーションの両方に適用することによって、アプリケーション アクセスのセキュリティを有効にする。

- Azure AD アプリケーション プロキシを通じてオンプレミス Web アプリケーションへの安全なリモート アクセスをプロビジョニングする。

[Azure Active Directory ポータル](https://aad.portal.azure.com/)を Azure Portal の一部として使用できます。 このダッシュボードから組織の状態の概要を確認し、ディレクトリ、ユーザー、またはアプリケーションのアクセスを簡単に管理できます。

![Azure Active Directory](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig2.png)

Azure ID 管理のコア機能は次のとおりです。

- シングル サインオン

- 多要素認証

- セキュリティの監視、アラート、および機械学習ベースのレポート

- コンシューマーの ID とアクセスの管理

- デバイス登録

- Privileged Identity Management

- Identity Protection

#### <a name="single-sign-on"></a>シングル サインオン

[シングル サインオン (SSO)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) とは、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリケーションとリソースにアクセスできる機能です。 いったんサインインすると、もう一度認証 (パスワードの入力など) を求められることなく、必要なすべてのアプリケーションにアクセスできます。

多くの組織では、エンド ユーザーの生産性向上のため、Office 365、Box、Salesforce などのサービスとしてのソフトウェア (SaaS) アプリケーションに依存しています。 従来は、IT スタッフが各 SaaS アプリケーションのユーザー アカウントを個別に作成し、更新する必要がありました。さらに、ユーザーは、各 SaaS アプリケーションのパスワードを覚える必要がありました。

[Azure AD はオンプレミスの Active Directory をクラウドに拡張](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)して、ユーザーがプライマリ組織アカウントを使用してドメイン参加デバイスおよび会社のリソースにサインインするだけでなく、それぞれの業務に必要なすべての Web アプリケーションおよび SaaS アプリケーションにもサインインできるようにします。

これにより、ユーザーが複数のユーザー名とパスワードのセットを管理する必要がなくなるだけでなく、組織のグループや従業員としての地位に基づいてアプリケーションのアクセスを自動的にプロビジョニングまたはプロビジョニング解除することが可能になります。 [Azure AD にはセキュリティおよびアクセス管理コントロールが導入](https://docs.microsoft.com/azure/active-directory/active-directory-sso-integrate-saas-apps)されており、SaaS アプリケーション間でユーザー アクセスを一元的に管理できます。

#### <a name="multi-factor-authentication"></a>多要素認証

[Azure Multi-Factor Authentication (MFA)](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication) は、複数の確認方法の使用を要求することで、ユーザーのサインインとトランザクションに重要な 2 つ目のセキュリティ レイヤーを追加する認証方法です。 MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを[効果的に保護する](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)ことができます。 電話やテキスト メッセージ、モバイル アプリによる通知のほか、確認コードやサード パーティの OAuth トークンなど、一連の照合方法を通じて確実な認証を行うことができます。

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>セキュリティの監視、アラート、および機械学習ベースのレポート

セキュリティの監視とアラートや、整合性のないアクセス パターンを識別する機械学習ベースのレポートを使用して、ビジネスを保護できます。 Azure Active Directory のアクセスおよび使用状況レポートを使用すると、組織のディレクトリの整合性とセキュリティを表示できます。 ディレクトリ管理者は、この情報を使用して、リスクを軽減するために適切に計画できるように、セキュリティ上のリスクがある箇所をより適切に確認できます。

[レポート](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide)は、Azure Portal または [Azure Active Directory ポータル](https://aad.portal.azure.com/)で、次の方法で分類されます。

- 異常レポート: 異常と考えられるサインイン イベントが含まれます。 この目的は、このようなアクティビティを認識し、イベントが不審であるかどうかを判断できるようにすることです。

- 統合アプリケーション レポート – 組織内のクラウド アプリケーションの使用状況を明らかにします。 Azure Active Directory は、何千ものクラウド アプリケーションとの統合を提供します。

- エラー レポート – 外部アプリケーションにアカウントをプロビジョニングするときに発生することがあるエラーを示します。

- ユーザー固有レポート – 特定のユーザーのデバイス/サインイン アクティビティ データを示します。

- アクティビティ ログ: 過去 24 時間、過去 7 日間、または過去 30 日間のすべての監査イベントの記録、グループのアクティビティの変更、およびパスワードのリセットと登録のアクティビティが含まれます。

#### <a name="consumer-identity-and-access-management"></a>コンシューマーの ID とアクセスの管理

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) は、数億個の ID を扱うコンシューマー向けアプリケーション用の高可用性グローバル ID 管理サービスです。 モバイルと Web の両方のプラットフォームにわたる統合を実現できます。 コンシューマーは、既に持っているソーシャル アカウントを使用するか、新たな資格情報を作成して、すべてのアプリケーションにログオンできます。その際のエクスペリエンスは、カスタマイズすることができます。

これまで、開発したアプリケーションに[コンシューマーがサインアップおよびサインインする](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)ことを望むアプリケーション開発者は、独自のコードを記述していました。 開発者は、オンプレミスのデータベースまたはシステムを使用して、ユーザー名とパスワードを保存していました。 Azure Active Directory B2C では、セキュリティ保護された標準ベースのプラットフォームと機能豊富で拡張可能なポリシーのセットを使用して、より簡単にコンシューマーの ID 管理をアプリケーションに統合できます。

Azure Active Directory B2C を使用すると、コンシューマーは、既存のソーシャル アカウント (Facebook、Google、Amazon、LinkedIn) を使用するか、または新しい資格情報 (電子メール アドレスとパスワードまたはユーザー名とパスワード) を作成することによって、アプリケーションにサインアップできます。

#### <a name="device-registration"></a>デバイス登録

[Azure AD Device Registration](https://docs.microsoft.com/azure/active-directory/device-management-introduction) は、デバイスに基づいて[条件付きでアクセス](https://docs.microsoft.com/azure/active-directory/active-directory-device-registration-on-premises-setup)を許可するというシナリオの基礎となる機能です。 デバイスが登録されると、ユーザーがサインインしたときにデバイスを認証するために使用される ID が、Azure AD のデバイス登録によって指定されます。 認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。

Intune などの[モバイル デバイス管理 (MDM)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) ソリューションと組み合わせて使用すると、Azure Active Directory のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Azure Active Directory (AD) Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) を使用すると、特権 ID と、Azure AD や他の Microsoft オンライン サービス (Office 365 や Microsoft Intune など) のリソースへのアクセスを管理、制御、監視できます。

ユーザーは、Azure や Office 365 のリソース、または他の SaaS アプリで、特権操作を実行することが必要になる場合があります。 通常は、組織がユーザーに Azure AD で永続的な特権アクセスを付与する必要があります。 しかし、この措置では、ユーザーが管理者特権を使用して実行している内容を組織が十分に監視できないため、クラウドでホストされているリソースのセキュリティ リスクが増大します。 また、特権アクセスを持つユーザー アカウントが侵害された場合に、その 1 つの侵害がクラウド セキュリティ全体に影響を与える可能性もあります。 Azure AD Privileged Identity Management はこのリスクの解決に役立ちます。

Azure AD Privileged Identity Management では、次のことが可能です。

- Azure AD の管理者であるユーザーを特定する

- Office 365 や Intune などの Microsoft Online Services へのオンデマンドの "ジャスト イン タイム" な管理アクセスを可能にする

- 管理者のアクセス履歴と管理者の割り当ての変更に関するレポートを取得する

- 特権ロールへのアクセスに関するアラートを受け取る

#### <a name="identity-protection"></a>Identity Protection

[Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) は、リスク イベントや組織の ID に影響する潜在的な脆弱性に関する統合ビューを提供するセキュリティ サービスです。 Identity Protection は、既存の Azure Active Directory 異常検出機能 (Azure AD の異常アクティビティ レポートで利用可能) を利用し、リアルタイムで異常を検出できる新しいリスク イベントの種類が導入されています。

## <a name="secured-resource-access-in-azure"></a>Azure でのセキュリティが保護されたリソース アクセス

Azure のアクセス制御では、最初に課金に注目します。 [Azure アカウント センター](https://account.windowsazure.com/subscriptions)からアクセスされる、Azure アカウントの所有者は、アカウント管理者 (AA) です。 サブスクリプションは課金用のコンテナーですが、セキュリティの境界としても機能します。各サブスクリプションはサービス管理者 (SA) を有し、SA は Azure Portal を使ってそのサブスクリプションの Azure リソースを追加、削除、変更することができます。 新しいサブスクリプションの既定の SA は AA ですが、AA は Azure アカウント センターで SA を変更できます。

![Azure でのセキュリティが保護されたリソース アクセス](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig3.png)

サブスクリプションは、ディレクトリとも関連付けられています。 ディレクトリでは、一連のユーザーを定義します。 たとえば、ディレクトリを作成した職場や学校のユーザーや、外部ユーザー (つまり、Microsoft アカウント) として定義できます。 サブスクリプションは、サービス管理者 (SA) または共同管理者 (CA) のいずれかとして割り当てられているディレクトリ ユーザーのサブセットからアクセス可能です。唯一の例外は、従来版との兼ね合いから、Microsoft アカウント (旧 Windows Live ID) はディレクトリに存在しなくても SA または CA として割り当てることができる、という点です 。

セキュリティを重視する企業は、実際に必要となるアクセス許可を従業員に付与することに注力する必要があります。 アクセス許可が多すぎると、アカウントが攻撃者による悪用の対象になりかねません。 アクセス許可が少なすぎると、従業員が業務を効率的に遂行できなくなる可能性があります。 [Azure のロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) は、Azure のアクセス許可を詳細に管理を実現することでこの問題に対処できます。

![セキュリティで保護されたリソース アクセス](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig4.png)

RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。 すべてのユーザーに Azure サブスクリプションまたはリソースで無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 たとえば、RBAC を使用して、ある従業員にはサブスクリプションで仮想マシンを管理できるようにし、他の従業員にも同じサブスクリプション内で SQL データベースを管理できるようにします。

![Azure でのセキュリティが確保されたリソース アクセス (RBAC)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="azure-data-security-and-encryption-protect"></a>Azure のデータ セキュリティと暗号化 (保護)

クラウドにおけるデータ保護で重要なポイントの 1 つは、データが置かれうる状態と、その状態でどのような制御を使用できるのかを把握することです。 Azure のデータ セキュリティと暗号化のベスト プラクティスでは、次のデータの状態に関する推奨事項が定められています。

- 保存: ストレージ オブジェクト、コンテナー、データ型など、物理メディア (磁気ディスクまたは光学ディスク) に静的な状態で存在しているすべての情報が該当します。

- 転送中: ネットワークやサービス バスなどを経由して、コンポーネント間、場所間、プログラム間でデータが転送されているとき (オンプレミスとクラウド間の転送、ExpressRoute などのハイブリッド接続を含む)、または入出力処理の間、データが転送中であると見なされます。

### <a name="encryption-at-rest"></a>保存時の暗号化

次のそれぞれで保存データの暗号化を実現するには:

データの暗号化に、次の表に示す推奨される暗号化モデルを 1 つ以上サポートします。

| 暗号化モデル |  |  |  |
| ----------------  | ----------------- | ----------------- | --------------- |
| サーバーの暗号化 | サーバーの暗号化 | サーバーの暗号化 | クライアントの暗号化
| サービス管理キーを使用したサーバー側暗号化 | ユーザーが管理する Azure Key Vault キーを使用したサーバー側暗号化 | オンプレミスの顧客管理キーを使用したサーバー側暗号化 |
| • Azure リソース プロバイダーが暗号化操作と解読操作を実行する <br> •    マイクロソフトがキーを管理する <br>•    完全なクラウド機能 | •    Azure リソース プロバイダーが暗号化操作と解読操作を実行する<br>• お客様が Azure Key Vault を使用してキーを制御する<br>• 完全なクラウド機能 | •    Azure リソース プロバイダーが暗号化操作と解読操作を実行する <br>• お客様がオンプレミスのキーを管理する <br> •   完全なクラウド機能| • Azure のサービスは解読されたデータを表示できない <br>• お客様がオンプレミス (または別のセキュリティで保護されたストア) にキーを保持する。 キーは Azure のサービスでは使用できません <br>• 制限されたクラウド機能|

### <a name="enabling-encryption-at-rest"></a>保存時の暗号化を有効にする

**データを保存するすべての場所を特定する**

保存データの暗号化の目的は、すべてのデータを暗号化することです。 これにより、重要なデータや保持されているすべての場所が失われる可能性がなくなります。 アプリケーションによって格納されたすべてのデータを列挙します。 

> [!Note] 
> "アプリケーション データ" や "PII" だけでなく、アカウント メタデータ (サブスクリプション マッピング、コントラクト情報、PII) などのアプリケーションに関連するすべてのデータが対象です。

データの格納に使用しているストアを検討します。 例: 

- 外部ストレージ (例: SQL Azure、Document DB、HDInsights、Data Lake など)

- 一時的なストレージ (テナントのデータを含むすべてのローカル キャッシュ)

- メモリ内キャッシュ (ページ ファイルに挿入される場合がある)

### <a name="leverage-the-existing-encryption-at-rest-support-in-azure"></a>Azure で既存の保存データの暗号化サポートを活用する

使用する各ストアで、既存の保存データの暗号化サポートを活用します。

- Azure Storage:[保存データに対する Azure Storage Service Encryption](https://docs.microsoft.com/azure/storage/storage-service-encryption)に関するページを参照してください

- SQL Azure: [透過的なデータ暗号化 (TDE)、SQL Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx)に関するページを参照してください

- VM とローカル ディスク ストレージ ([Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption))

サポートされている場合、VM とローカル ディスク ストレージには、Azure Disk Encryption を使用します。

#### <a name="iaas"></a>IaaS

IaaS VM のサービス (Windows または Linux) では、[Azure Disk Encryption](https://microsoft.sharepoint.com/teams/AzureSecurityCompliance/Security/SitePages/Azure%20Disk%20Encryption.aspx) を使用して顧客データが含まれるボリュームを暗号化してください。

#### <a name="paas-v2"></a>PaaS v2

Service Fabric を使用して PaaS v2 で実行されているサービスでは、仮想マシン スケール セット [VMSS] 用の Azure Disk Encryption を使用して、PaaS v2 の VM を暗号化できます。

#### <a name="paas-v1"></a>PaaS v1

現在、Azure Disk Encryption は PaaS v1 ではサポートされていません。 このため、保持されている保存データを暗号化するには、アプリケーション レベルの暗号化を使用する必要があります。  これにはアプリケーション データ、一時ファイル、ログ、クラッシュ ダンプなどが含まれますが、これらに限られません。

ほとんどのサービスはストレージ リソース プロバイダーの暗号化の活用を試行します。 一部のサービスでは明示的な暗号化を実行する必要があります。たとえば、保持されたキー マテリアル (証明書、ルート/マスター キー) はすべて Key Vault に格納する必要があります。

顧客管理キーを使用したサービス側暗号化をサポートしている場合、お客様がマイクロソフトにキーを渡す手段が必要です。 サポートおよび推奨されている方法は、Azure Key Valut (AKV) を使用して統合する方法です。 この場合、Azure Key Vault を使用してキーを追加および管理できます。 AKV を使用する方法について詳しくは、「[Key Vault の概要](https://go.microsoft.com/fwlink/?linkid=521402)」をご覧ください。

Azure Key Vault と統合するには、解読に必要なときに AKV からキーをリクエストするためのコードを追加します。

- AKV と統合する方法について詳しくは、「[Azure Key Vault – Step by Step](https://blogs.technet.microsoft.com/kv/2015/06/02/azure-key-vault-step-by-step/)」 (Azure Key Vault - ステップ バイ ステップ) をご覧ください。

顧客管理キーをサポートする場合、そのお客様に UX を提供して、使用する Key Vault (または Key Vault URI) を指定する必要があります。

保存データの暗号化にはホスト、インフラストラクチャ、およびテナント データの暗号化が関与するため、システム エラーや悪意のある行動によってキーが失われると、暗号化データがすべて失われることがあります。 このため、保存データの暗号化ソリューションに、システム エラーや悪意のある行動に耐性がある、包括的なディザスター リカバリー ストーリーが必要です。

保存データの暗号化が実装されているサービスは通常、暗号化キーやホスト ドライブ (ホスト OS のページ ファイルなど) で暗号化されずに残っているデータの影響を引き続き受けやすい状態にあります。そのため、サービスはホスト ボリュームが暗号化されていることを確認する必要があります。 これを容易にするために、Compute チームは、[BitLocker](https://technet.microsoft.com/library/dn306081.aspx) NKP と DCM サービスとエージェントの拡張機能を使用してホスト ボリュームを暗号化する、ホスト暗号化のデプロイを有効にしました。

ほとんどのサービスは標準の Azure VM に実装されています。 このようなサービスでは、Compute が有効にすると[ホスト暗号化](https://docs.microsoft.com/azure/security/azure-security-disk-encryption)を自動的に入手します。 Compute 管理のクラスターで実行されているサービスでは、Windows Server 2016 がロールアウトされた段階でホスト暗号化が自動的に有効になっています。

### <a name="encryption-in-transit"></a>転送中の暗号化

転送中のデータの保護は、データ保護戦略に欠かせない要素です。 データはさまざまな場所を経由して転送されるため、一般的には常時 SSL/TLS プロトコルを使用してデータをやり取りすることが推奨されています。 状況によっては、オンプレミスとクラウド インフラストラクチャ間の通信チャネル全体を、仮想プライベート ネットワーク (VPN) を使用して隔離する必要があります。

オンプレミス インフラストラクチャと Azure 間のデータ移動については、HTTPS や VPN などの適切なセキュリティ対策を検討してください。

オンプレミスにある複数のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある場合には、[Azure のサイト間 VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-site-to-site-create) を使用します。

オンプレミスにある 1 個のワークステーションから Azure へのアクセスをセキュリティ保護する必要がある場合には、[ポイント対サイト VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-point-to-site-create) を使用します。

大規模なデータ セットは、[ExpressRoute](https://azure.microsoft.com/services/expressroute/) などの専用高速 WAN リンクを利用して移動できます。 ExpressRoute を使用する場合、[SSL/TLS](https://support.microsoft.com/kb/257591) などのプロトコルを使用してアプリケーション レベルでデータを暗号化することで、さらにセキュリティを強化できます。

Azure Portal で Azure Storage を操作する場合、すべてのトランザクションは HTTPS 経由で行われます。 HTTPS 経由の [Storage REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) も、[Azure Storage](https://azure.microsoft.com/services/storage/) と [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) の操作に使用できます。

転送中のデータを保護しない場合、[man-in-the-middle 攻撃](https://technet.microsoft.com/library/gg195821.aspx)、[盗聴](https://technet.microsoft.com/library/gg195641.aspx)、セッション ハイジャックに対して脆弱になります。 このような攻撃は、機密データへのアクセスを取得するための最初の手順として実行される場合があります。

Azure VPN オプションの詳細については、「[VPN Gateway の計画と設計](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)」を参照してください。

### <a name="enforce-file-level-data-encryption"></a>ファイル レベルのデータ暗号化を適用する

[Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) は、暗号化、ID、承認ポリシーを使用してファイルとメールを保護します。 Azure RMS は組織内と組織外の両方でデータを保護できるため、携帯電話、タブレット、PC などの複数のデバイスに適用できます。 これが可能なのは、データが組織外に出たとしても Azure RMS による保護がデータに残るためです。

Azure RMS を使用してファイルを保護する場合、[FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) を完全にサポートする業界標準の暗号化技術が適用されます。 データ保護に Azure RMS を利用すると、クラウド ストレージ サービスのような IT 部門の管轄外のストレージにファイルがコピーされたとしても、ファイルを確実に保護できます。 メールでファイルを共有する場合も同じです。メール メッセージの添付ファイルとしてファイルを保護し、保護された添付ファイルを開く方法をメール本文に記載しておきます。
Azure RMS の導入を計画するときは、次の準備を行うことをお勧めします。

- [RMS 共有アプリ](https://technet.microsoft.com/library/dn339006.aspx)をインストールします。 Office アドインをインストールすることによって、このアプリが Office アプリケーションと統合され、ユーザーが直接簡単にファイルを保護できるようになります。

- Azure RMS をサポートするようにアプリケーションとサービスを構成します。

- ビジネス要件を反映した[カスタム テンプレート](https://technet.microsoft.com/library/dn642472.aspx)を作成します。 たとえば、すべての極秘メールに適用する、極秘データ用テンプレートを作成します。

[データ分類](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf)やデータ保護が不十分な組織は、データ漏洩のリスクが高くなる可能性があります。 ファイルを適切に保護しなければ、ビジネスを分析し、不正使用を監視し、ファイルへの悪意のあるアクセスを防ぐことはできません。

> [!Note]
> Azure RMS の詳細については、[Azure Rights Management の概要](https://technet.microsoft.com/library/jj585016.aspx)に関するページをご覧ください。

## <a name="secure-your-application-protect"></a>アプリケーションをセキュリティで保護する (保護)
アプリケーションが実行されるインフラストラクチャとプラットフォームをセキュリティで保護することは Azure の役割ですが、アプリケーション自体をセキュリティで保護することはユーザーの役割です。 つまり、ユーザーは、アプリケーションのコードとコンテンツを安全な方法で開発、デプロイ、管理する必要があります。 これが実現されなければ、アプリケーションのコードまたはコンテンツは、脅威に対して脆弱なままになる可能性があります。

### <a name="web-application-firewall-waf"></a>Web アプリケーション ファイアウォール (WAF)
[Web アプリケーション ファイアウォール (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) の機能です。

Web アプリケーション ファイアウォールは、[OWASP コア ルール セット](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 または 2.2.9 の規則に基づいています。 Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の的になるケースが増えています。 よくある攻撃の例として、SQL インジェクション攻撃やクロス サイト スクリプティング攻撃が挙げられます。 アプリケーション コードでこのような攻撃を防ぐことは困難な場合があり、厳格な保守、パッチの適用、アプリケーション トポロジの複数のレイヤーの監視が必要になることもあります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になり、アプリケーション管理者にとっては侵入の脅威からより確実に保護されるようになります。 また、WAF のソリューションは、1 か所に既知の脆弱性の修正プログラムを適用することで、個々の Web アプリケーションをセキュリティで保護する場合と比較して、さらに迅速にセキュリティの脅威に対応できます。 既存のアプリケーション ゲートウェイは、Web アプリケーション ファイアウォールに対応したアプリケーション ゲートウェイに簡単に変換できます。

Web アプリケーション ファイアウォールで保護される一般的な Web の脆弱性の一部を以下に示します。

- SQL インジェクションからの保護

- クロス サイト スクリプティングからの保護

- 一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)

- HTTP プロトコル違反に対する保護

- HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)

- ボット、クローラー、スキャナーの防止

- 一般的なアプリケーション構成ミスの検出 (Apache、IIS など)

> [!Note]
> 規則と保護の詳細な一覧については、次の「[コア ルール セット](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview#core-rule-sets)」をご覧ください。

また、Azure にはアプリの受信トラフィックと送信トラフィックの両方をセキュリティ保護するための使いやすい機能が複数用意されています。 他にも、Azure にはお客様のアプリケーション コードをセキュリティ保護するために、Web アプリケーションの脆弱性をスキャンする、外部から提供された機能が用意されています。

- [アプリに対して Azure Active Directory 認証をセットアップする](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [トランスポート層セキュリティ (TLS/SSL) - HTTPS を有効にして、アプリへのトラフィックをセキュリティで保護する](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)

  - [すべての着信トラフィックに対して HTTPS 接続を経由することを強制する](http://microsoftazurewebsitescheatsheet.info/)

  - [Strict Transport Security (HSTS) を有効にする](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [クライアントの IP アドレスによってアプリへのアクセスを制限する](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [クライアントの動作 (要求の頻度とコンカレンシー) によってアプリへのアクセスを制限する](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Tinfoil Security Scanning を使って Web アプリ コードをスキャンして脆弱性を検出する](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [TLS 相互認証を構成して、Web アプリに接続する際にクライアント証明書を要求する](https://docs.microsoft.com/azure/app-service/app-service-web-configure-tls-mutual-auth)

- [アプリから外部リソースに安全に接続できるようにするために使用するクライアント証明書を構成する](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [標準的なサーバー ヘッダーを削除することによって、ツールがアプリにフィンガープリントを残すことを回避する](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [ポイント対サイト VPN を使用して、プライベート ネットワーク内のリソースとアプリケーションを安全に接続します。](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)

- [ハイブリッド接続を使用して、アプリをプライベート ネットワーク内のリソースに安全に接続する](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

Azure App Service では、Azure の Cloud Services および Virtual Machines で使用されるのと同じマルウェア対策ソリューションを使用します。 詳しくは、[マルウェア対策についてのドキュメント](https://docs.microsoft.com/azure/security/azure-security-antimalware)をご覧ください。

## <a name="secure-your-network-protect"></a>ネットワークをセキュリティで保護する (保護)
Microsoft Azure には、アプリケーションとサービスの接続要件をサポートする堅牢なネットワーク インフラストラクチャが組み込まれています。 ネットワーク接続は、Azure に配置されているリソース間、オンプレミスのリソースと Azure でホストされているリソース間、インターネットと Azure 間で可能です。

[Azure のネットワーク インフラストラクチャ](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines)では、[仮想ネットワーク (VNet)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) を使用して Azure リソースを安全に相互接続することができます。 VNet とは、クラウド内のユーザー独自のネットワークを表したものです。 特定のサブスクリプション専用に Azure クラウド ネットワークが論理的に分離されています。 VNet は、オンプレミス ネットワークに接続できます。

![ネットワークをセキュリティで保護する (保護)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig6.png)

基本レベルのネットワーク アクセス制御 (IP アドレス、TCP または UDP プロトコルに基づくもの) を必要とする場合には、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)を使用できます。 ネットワーク セキュリティ グループ (NSG) とは基本的なステートフル パケット フィルタリング ファイアウォールであり、 [5 タプル](https://www.techopedia.com/definition/28190/5-tuple)に基づいてアクセスを制御します。

Azure ネットワークは、Azure Virtual Network 上のネットワーク トラフィックのルーティング動作をカスタマイズする機能をサポートしています。 これは、Azure で [ユーザー定義のルート](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) を構成することで実現できます。

[強制トンネリング](https://www.petri.com/azure-forced-tunneling)は、サービスがインターネット上のデバイスとの接続を開始する許可を得られないようにするメカニズムです。

Azure は [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) を使用してオンプレミス ネットワークおよび Azure Virtual Network への専用の WAN リンクの接続をサポートします。 Azure とユーザー サイト間のリンクは、パブリック インターネットを経由しない専用接続を使用します。 Azure アプリケーションが複数のデータセンターで実行されている場合は、[Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) を使用して、アプリケーションのインスタンスにユーザーからの要求をインテリジェントにルーティングできます。 また、サービスがインターネットからアクセス可能な場合、Azure 内で実行されていないサービスへトラフィックをルーティングすることもできます。

## <a name="virtual-machine-security-protect"></a>仮想マシンのセキュリティ (保護)

[Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/) を使用すると、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。 Microsoft Windows、Linux、Microsoft SQL Server、Oracle、IBM、SAP、Azure BizTalk Services に対応しており、ほぼすべてのオペレーティング システムですべてのワークロード、すべての言語をデプロイできます。

Azure では、Microsoft、Symantec、Trend Micro、Kaspersky などのセキュリティ ベンダーが提供する[マルウェア対策ソフトウェア](https://docs.microsoft.com/azure/security/azure-security-antimalware)を利用できます。これにより、悪意のあるファイルやアドウェアなどの脅威から仮想マシンを保護できます。

Azure Cloud Services および 仮想マシン に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護機能です。 Microsoft Antimalware は、既知の悪意あるまたは望ましくないソフトウェアが Azure システム上に自動でインストールまたは実行されそうになった場合に、構成可能なアラートを提供します。

[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) は、設備投資なしで、また最小限の運用コストでアプリケーション データを保護できる、スケーラブルなソリューションです。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれる危険があります。 Azure Backup により、Windows と Linux で実行されている仮想マシンが保護されます。

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) は、ワークロードとアプリのレプリケーション、フェールオーバー、および復旧の調整に役立ちます。これにより、1 次拠点がダウンした場合でも 2 次拠点からワークロードとアプリを利用できます。

## <a name="ensure-compliance-cloud-services-due-diligence-checklist-protect"></a>コンプライアンスを確保する: クラウド サービス向けデリジェンス チェックリスト (保護)

マイクロソフトは、クラウドへの移行を検討している組織がデリジェンスを訓練するのに役立つ、[Cloud Services 向けデュー デリジェンス チェックリスト](https://aka.ms/cloudchecklist.download)を作成しました。 民間企業から行政機関、非営利組織などの公的機関まで、あらゆる規模および種類の組織の構造体を提供し、自社のパフォーマンス、サービス、データ管理、およびガバナンス目標や要件を特定できます。 これにより、異なるクラウド サービス プロバイダーのサービスを比較し、最終的なクラウド サービス契約の基礎を築くことができます。

チェックリストには、各条項をクラウド サービス契約に関する新しい国際規格 (ISO/IEC 19086) に合わせるためのフレームワークが用意されています。 この規格には、クラウドを採用する上で組織が意思決定を行うのに役立つ統一された考慮事項のセットが用意されており、クラウド サービスのオファリングを比較する共通の土台を作成します。

チェックリストは十分に吟味した上でのクラウドへの移行を促進し、クラウド サービス プロバイダーを選択するための構造化されたガイダンスと一貫した反復可能なアプローチを提供します。

クラウドの採用は、単なる技術的な意思決定ではなくなりました。 チェックリストの要件は組織のあらゆる側面に言及しているため、CIO や CISO だけでなく、社内の法務、リスク管理、購買、コンプライアンスの各部門のプロフェッショナルの意思も関与します。 これにより意思決定プロセスの効率性が上がり、理に適った意思決定を行うことができるため、採用する上で不測の事態が発生する確率が下がります。

他にも、チェックリストには次の役割があります。

- クラウド採用プロセスの初期段階で、意思決定者による議論が必要なトピックを明らかにする。

- 規制や組織独自のプライバシー、個人を特定できる情報 (PII)、およびデータ セキュリティに関するビジネス上の徹底的な議論を促進する。

- クラウド プロジェクトに影響を及ぼす可能性のある潜在的な問題を組織が特定するのに役立つ。

- 同じ用語、定義、メトリック、およびサービスを使用した一貫したセットの質問を各プロバイダー用に用意し、異なるクラウド サービス プロバイダーからのオファリングを比較するプロセスを簡略化する。

## <a name="azure-infrastructure-and-application-security-validation-detect"></a>Azure インフラストラクチャとアプリケーション セキュリティの検証 (検出)

[Azure で運用可能なセキュリティ](https://docs.microsoft.com/azure/security/azure-operational-security)とは、ユーザーのデータ、アプリケーション、および Microsoft Azure にあるその他の資産を保護するために使用できる、サービス、コントロール、機能を指します。

![セキュリティの検証 (検出)](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure で運用可能なセキュリティは、Microsoft セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)、Microsoft セキュリティ レスポンス センター プログラム、およびサイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有のさまざまな機能の使用経験から得られた知識が組み込まれたフレームワーク上に構築されています。

### <a name="microsoft-azure-monitor"></a>Microsoft Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) は、ハイブリッド クラウド向けの IT 管理ソリューションです。 Azure Monitor ログは単独で使用されるか、System Center の既存のデプロイを拡張するために使用され、ご自分のインフラストラクチャをクラウドベースで管理するための柔軟性と制御を最大限に実現します。

![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig8.png)

Azure Monitor を使用すれば、オンプレミス型、Azure、AWS、Windows Server、Linux、VMware、OpenStack など、あらゆるクラウドのインスタンスを競合ソリューションよりも低コストで管理できます。 クラウド中心に構築された Azure Monitor は、新しいビジネス課題に対応し、新しいワークロード、アプリケーション、およびクラウド環境にも対応する最も高速でコスト効果の良い新たな企業の管理方法を提供します。

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

[Azure Monitor ログ](https://azure.microsoft.com/documentation/services/log-analytics)は、管理対象リソースから中央リポジトリにデータを収集する監視サービスです。 このデータには、API 経由で提供されたイベント、パフォーマンス データ、カスタム データが含まれます。 一度収集されたデータは、アラート、分析、エクスポートに使用できます。

![Azure Monitor ログ](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig9.png)

この方法を使用すると、さまざまなソースからのデータを統合できるため、Azure サービスから得たデータを既存のオンプレミス環境と組み合わせることが可能です。 さらに、データの収集とそのデータに対して実行される操作は明確に分離されているため、あらゆる種類のデータにすべての操作を実行できます。

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) は、Azure リソースのセキュリティを高度に視覚化し、制御することで脅威を回避、検出し、それに対応できるようにします。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

セキュリティ センターは Azure リソースのセキュリティの状態を分析して、潜在的なセキュリティ脆弱性を特定します。 推奨事項の一覧では、必要な制御を構成する手順を説明します。

たとえば、次のようになります。

- 悪意のあるソフトウェアを識別して削除するためのマルウェア対策をプロビジョニングする

- VM へのトラフィックを制御するためにネットワーク セキュリティ グループとルールを構成する

- Web アプリケーションを対象とする攻撃から保護するための Web アプリケーション ファイアウォールをプロビジョニングする

- 不足しているシステムの更新をデプロイする

- 推奨基準と一致しない OS 構成に対処する

セキュリティ センターは、Azure リソース、ネットワーク、パートナー ソリューション (マルウェア対策プログラム、ファイアウォールなど) からログ データを自動的に収集、分析、統合します。 脅威が検出されると、セキュリティの警告が作成されます。 例には次の検出が含まれます。

- 既知の悪意のある IP アドレスと通信する、セキュリティ侵害された VM

- Windows エラー報告を使用して検出された高度なマルウェア

- VM に対するブルート フォース攻撃

- 統合されたマルウェア対策プログラムとファイアウォールからのセキュリティのアラート

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) では、特定の種類のリソースについての詳しい情報を提供しています。 Azure インフラストラクチャ (アクティビティ ログ) と個々の Azure リソース (診断ログ) から得られたデータの視覚化、クエリ、ルーティング、アラート、自動スケール、自動化を実行します。

クラウド アプリケーションは、動的なパーツを多数使った複雑な構成になっています。 監視では、アプリケーションを正常な状態で稼働させ続けるためのデータを取得できます。 また、潜在的な問題を防止したり、発生した問題をトラブルシューティングするのにも役立ちます。

さらに、![Azure Monitor](media/azure-security-technical-capabilities/azure-security-technical-capabilities-fig10.png) では監視データを使用して、アプリケーションに関する深い洞察を得ることもできます。 そのような知識は、アプリケーションのパフォーマンスや保守容易性を向上させたり、手作業での介入が必要な操作を自動化したりするうえで役立ちます。

ネットワークの脆弱性を検出し、IT セキュリティおよび規制ガバナンス モデルへのコンプライアンスを確保するためには、ネットワーク セキュリティの監査が不可欠です。 セキュリティ グループ ビューでは、構成したネットワーク セキュリティ グループとセキュリティ ルール、および有効なセキュリティ ルールを取得できます。 適用済みのルール一覧に基づいて、開いているポートやネットワークの脆弱性を特定できます。

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) は地域サービスであり、ネットワーク レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。

### <a name="storage-analytics"></a>Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) では、ストレージ サービスへの要求に関して集計されたトランザクション統計情報と容量データを含むメトリックを格納できます。 トランザクションに関しては、API 操作レベルとストレージ サービス レベルの両方でレポートされます。容量に関しては、ストレージ サービス レベルでレポートされます。 メトリック データは、ストレージ サービスの使用状況の分析、ストレージ サービスに対する要求に関する問題の診断、サービスを使用するアプリケーションのパフォーマンスの向上に利用できます。

### <a name="application-insights"></a>Application Insights

[Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている強力な分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。 オンプレミスまたはクラウドでホストされる .NET、Node.js、Java EE などのさまざまなプラットフォーム上のアプリで機能します。 devOps プロセスと統合され、さまざまな開発ツールへの接続ポイントを備えています。

以下を監視します。

- **要求レート、応答時間、およびエラー率**: 最も人気のあるページがどの時間帯にどの場所のユーザーからアクセスされているかを調べます。 最もパフォーマンスの高いページを確認します。 要求が多いときに、応答時間と失敗率が高くなる場合は、おそらくリソースに問題があります。

- **依存率、応答時間、およびエラー率**: 外部サービスによって応答が遅くなっているかどうかを調べます。

- **例外**: 集計された統計を分析します。または特定のインスタンスを選択し、スタック トレースと関連する要求を調べます。 サーバーとブラウザーの両方の例外が報告されます。

- **ページ ビューと読み込みのパフォーマンス**: ユーザーのブラウザーから報告されます。

- Web ページからの **AJAX 呼び出し**: レート、応答時間、およびエラー率。

- **ユーザー数とセッション数**。

- Windows または Linux サーバー コンピューターの CPU、メモリ、ネットワーク使用率などの**パフォーマンス カウンター**。

- Docker または Azure の**ホスト診断**。

- アプリの**診断トレース ログ**: これにより、トレース イベントを要求に関連付けることができます。

- 販売された品目や勝利したゲームなどのビジネス イベントを追跡するためにクライアントまたはサーバーのコード内に書き込んだ**カスタム イベントとメトリック**。

アプリケーションのインフラストラクチャは通常、仮想マシン、ストレージ アカウント、仮想ネットワーク、Web アプリ、データベース、データベース サーバー、サード パーティのサービスなど、複数のコンポーネントで構成されます。 これらのコンポーネントは別々のエンティティではなく、1 つのエンティティの中で互いに関連付けられ相互依存しています。 これらのコンポーネントを、1 つのグループとしてデプロイ、管理、および監視するのが好ましいです。 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) を使用すると、ソリューション内の複数のリソースを 1 つのグループとして作業できます。

ソリューションのこれらすべてのリソースを、1 回の連携した操作でデプロイ、更新、または削除できます。 デプロイにはテンプレートを使用しますが、このテンプレートは、テスト、ステージング、運用環境などのさまざまな環境に使用できます。 Resource Manager には、デプロイ後のリソースの管理に役立つ、セキュリティ、監査、タグ付けの機能が用意されています。

**Resource Manager を使用する利点**

リソース マネージャーには、いくつかの利点があります。

- ソリューションのリソースを個別に処理するのではなく、すべてのリソースをグループとしてデプロイ、管理、監視できます。

- ソリューションを開発のライフサイクル全体で繰り返しデプロイできます。また、常にリソースが一貫した状態でデプロイされます。

- スクリプトではなく宣言型のテンプレートを使用してインフラストラクチャを管理できます。

- 正しい順序でデプロイされるようにリソース間の依存性を定義できます。

- ロールベースのアクセス制御 (RBAC) が管理プラットフォームにネイティブ統合されるため、リソース グループのすべてのサービスにアクセス制御を適用できます。

- タグをリソースに適用し、サブスクリプションのすべてのリソースを論理的に整理できます。

- 同じタグを共有するリソース グループのコストを表示することで、組織の課金をわかりやすくすることができます。

> [!Note]
> リソース マネージャーには、ソリューションをデプロイして管理するための新しい方法が用意されています。 以前のデプロイメント モデルを使用していて、変更の詳細を確認する場合は、[Resource Manager デプロイメントとクラシック デプロイメント](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

セキュリティについて詳しくは、セキュリティについて掘り下げた次のトピックをご覧ください。

- [監査とログ記録](https://www.microsoft.com/en-us/trustcenter/security/auditingandlogging)

- [サイバー犯罪](https://www.microsoft.com/en-us/trustcenter/security/cybercrime)

- [設計および運用面のセキュリティ](https://www.microsoft.com/en-us/trustcenter/security/designopsecurity)

- [暗号化](https://www.microsoft.com/en-us/trustcenter/security/encryption)

- [ID 管理とアクセス管理](https://www.microsoft.com/en-us/trustcenter/security/identity)

- [ネットワークのセキュリティ](https://www.microsoft.com/en-us/trustcenter/security/networksecurity)

- [脅威管理](https://www.microsoft.com/en-us/trustcenter/security/threatmanagement)

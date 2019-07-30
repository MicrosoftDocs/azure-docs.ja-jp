---
title: デプロイ計画 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory の機能を多数デプロイする方法に関するエンド ツー エンドのガイダンスです。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f695d9f0240f8c27ea0bedba7e532d37a177752
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304756"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory のデプロイ計画
ここでは、Azure Active Directory (Azure AD) の各種機能をデプロイする方法についてのエンド ツー エンドのガイダンスを紹介しています。 以下のデプロイ計画では、Azure AD のいくつかの代表的な機能について、そのビジネス上の価値や、正しく展開するうえで必要な計画の考慮事項、設計、運用手順をひととおり説明しています。 

これらのドキュメントには、メール テンプレートやシステム アーキテクチャ図、一般的なテスト ケースなどが含まれています。 

ドキュメントについてのご意見をぜひお寄せください。 ドキュメントがお役に立てたかどうかについて、簡単な[アンケート](https://aka.ms/deploymentplanfeedback)にご協力いただければ幸いです。 

## <a name="include-the-right-stakeholders"></a>適切な関係者を含める

新しい機能のデプロイ計画を始めるときは、組織全体の主要な関係者を含めることが重要です。 次の各役割を果たす個人を特定して文書化し、それらの人と協力してプロジェクトへの関与方法を判断することをお勧めします。  

次のような役割があります 

|Role |説明 |
|-|-|
|エンドユーザー|機能を実装する対象となるユーザーの代表的なグループ。 多くの場合、パイロット プログラムの変更をプレビューします。
|IT サポート マネージャー|ヘルプデスクの観点から、この変更のサポート可能性に関する情報を提供できる、IT サポート組織の代表。  
|ID アーキテクトまたは Azure グローバル管理者|この変更をどのように組織内の主要な ID 管理インフラストラクチャに合わせるかを定義する責任がある、ID 管理チームの代表。|
|アプリケーション ビジネス所有者 |影響を受けるアプリケーションの全体的なビジネス所有者。アクセスの管理を含む場合があります。  エンドユーザーの観点から、この変更のユーザー エクスペリエンスと有用性についての情報も提供できます。
|セキュリティ所有者|計画が組織のセキュリティ要件を満たしていることをサインオフできるセキュリティ チームの代表。|
|Compliance Manager|企業、業界、または政府の要件に確実に準拠する責任を負う組織内の個人。|

**次のような関与のレベルがあります。**

- **R** プロジェクト計画と結果の実施に責任を負う 

- **A** プロジェクト計画と成果の承認 

- **C** プロジェクト計画と成果への貢献者 

- **I** プロジェクト計画と成果を知らされる
 
## <a name="deployment-plans"></a>デプロイ計画



|シナリオ |説明 |
|-|-|
|[Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)|Azure Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 管理者が承認した認証方式を使用することにより、Azure MFA では、データやアプリケーションへのアクセスを保護しながら、シンプルなサインイン プロセスへの要求に応えることができます。|
|[条件付きアクセス](https://aka.ms/deploymentplans/ca)|条件付きアクセスを使用すると、クラウド アプリへのアクセスをだれに許可するかを各種の条件に基づいて自動的に判断するアクセスの制御を実装できます。|
|[セルフサービスのパスワード リセット](https://aka.ms/deploymentplans/sspr)|セルフサービスによるパスワードのリセットにより、ユーザーは、いつでも、どこでも、管理者の介入なしでパスワードをリセットできます。|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) は、Azure AD、Azure リソース、およびその他の Microsoft Online Services 全体の特権管理者ロールを管理するのに役立ちます。 PIM では Just-In-Time アクセス、承認要求ワークフロー、完全に統合されたアクセス レビューなどのソリューションが提供されるので、リアルタイムに特権ロールの悪意のあるアクティビティを特定し、明らかにして防ぐことができます。|
|[シングル サインオン](https://aka.ms/deploymentplans/sso)|シングル サインオンは、1 つのユーザー アカウントを使って 1 回サインインするだけで作業に必要なすべてのアプリとリソースにアクセスできる機能です。 いったんサインインすれば、再度認証 (パスワードの入力など) を求められることなく、Microsoft Office から SalesForce や Box へと移動することができます。|
|[シームレス SSO](https://aka.ms/SeamlessSSODPDownload)|Azure Active Directory シームレス シングル サインオン (Azure AD シームレス SSO) では、ユーザーが企業ネットワークに接続される会社のデバイスを使用するときに、自動的にサインインを行います。 この機能を有効にすると、ユーザーは Azure AD にサインインするためにパスワードを入力する必要がなくなります。また、通常はユーザー名の入力も不要です。 この機能により、追加のオンプレミス コンポーネントを必要とせずに、ユーザーはクラウド ベースのアプリケーションに簡単にアクセスできるようになります。|
|[アクセス パネル](https://aka.ms/AccessPanelDPDownload)|すべてのアプリケーションを検出し、それにアクセスするための単純なハブをユーザーに提供します。 ユーザーが、新しいアプリやグループへのアクセスを要求したり、他のユーザーに代わってこれらのリソースへのアクセスを管理したりする機能などのセルフサービス機能を使用して生産性を向上できるようにします。|
|[ADFS からパスワード ハッシュ同期への移行](https://aka.ms/deploymentplans/adfs2phs)|パスワード ハッシュ同期では、ユーザー パスワードのハッシュがオンプレミスの Active Directory から Azure AD に同期されます。これにより Azure AD は、オンプレミスの Active Directory とやり取りすることなくユーザーを認証することができます。|
|[ADFS からパススルー認証への移行](https://aka.ms/deploymentplans/adfs2pta)|Azure AD パススルー認証を使用すると、ユーザーは同じパスワードを使用して、オンプレミスのアプリケーションとクラウド ベースのアプリケーションの両方にサインインできます。 この機能により、ユーザー エクスペリエンスが向上します。ユーザーは、覚えておくパスワードが 1 つ少なくなり、ユーザーがサインイン方法を忘れる可能性が低くなるため IT ヘルプデスクのコストが削減します。 この機能により、ユーザーが Azure AD を使用してサインインするとき、ユーザーのパスワードがオンプレミスの Active Directory に対して直接検証されます。|
|[Azure AD アプリケーション プロキシ](https://aka.ms/deploymentplans/appproxy)|現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 タブレット、電話、ラップトップを問わず、自分のデバイスで作業したいと考えています。 そして、すべてのアプリケーション (クラウドにある SaaS アプリとオンプレミスの社内アプリの両方) にアクセスできることを期待しています。 オンプレミスのアプリケーションへのアクセス提供には、従来、仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ) が必要でした。 これらのソリューションは、複雑でセキュリティ保護が困難であるだけでなく、設定と管理にコストがかかります。 もっと良い方法があります。 それが Azure AD アプリケーション プロキシです。|
|[ユーザー プロビジョニング](https://aka.ms/UserProvisioningDPDownload)|Azure AD を使用すると、Dropbox、Salesforce、ServiceNow などのクラウド (SaaS) アプリケーションで、ユーザー ID の作成、保守、削除を自動化できます。|
|[Workday 主導の受信ユーザー プロビジョニング](https://aka.ms/WorkdayDeploymentPlan)|Active Directory への Workday 主導の受信ユーザー プロビジョニングによって、継続的な ID ガバナンスのための基礎が作成され、権限のある ID データに依存するビジネス プロセスの品質が向上します。 この機能を使用すると、Joiner-Mover-Leaver プロセス (新規採用、退職、異動など) を IT プロビジョニング アクション (アカウントの作成、有効化、無効化、削除など) にマッピングする規則を構成することによって、従業員や臨時社員の ID ライフサイクルをシームレスに管理できます。|

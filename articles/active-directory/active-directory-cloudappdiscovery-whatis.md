---
title: "管理されていないクラウド アプリケーションを Cloud App Discovery で検出する | Microsoft Docs"
description: "Cloud App Discovery でアプリケーションを検索および管理する利点と機能について説明します。"
services: active-directory
keywords: "Cloud App Discovery, アプリケーションの管理"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 011cdf5f1e1b78832a8e4f18f4eef0f376860c45
ms.lasthandoff: 12/29/2016


---
# <a name="finding-unmanaged-cloud-applications-with-cloud-app-discovery"></a>管理されていないクラウド アプリケーションを Cloud App Discovery で検出する
## <a name="overview"></a>Overview
現代の企業では、IT 部門が、組織のメンバーが作業のために使用しているクラウド アプリケーションを部分的にしか認識できていないことがよくあります。 管理者が企業データへの不正アクセスを心配している理由は容易に理解できます。データの漏洩やその他のセキュリティ リスクが発生するおそれがあるからです。 このような認識不足は、セキュリティ リスクに対処するための計画策定が困難であるように思わせる可能性があります。

Cloud App Discovery は、Azure Active Directory (AD) Premium の機能で、組織の従業員が使用しているクラウド アプリケーションを検出することができます。

**Cloud App Discovery を使用すると次のことができます。**

* 使用されているクラウド アプリケーションを検出し、ユーザー数、トラフィック量、アプリケーションに対する Web 要求数別に使用状況を測定する
* アプリケーションを使用しているユーザーを特定する
* オフライン分析用にデータをエクスポートする
* これらのアプリケーションを IT 部門の管理下に置き、ユーザー管理のためのシングル サインオンを有効にする

## <a name="how-it-works"></a>動作のしくみ
1. アプリケーションの使用状況に対するエージェントがユーザーのコンピューターにインストールされます。
2. エージェントによってキャプチャされるアプリケーションの使用状況情報は、セキュリティで保護され、暗号化されたチャネル経由で Cloud App Discovery サービスに送信されます。
3. Cloud App Discovery サービスでは、データが評価され、レポートが生成されます。

![Cloud App Discovery の図](./media/active-directory-cloudappdiscovery/cad01.png)

Cloud App Discovery の使用を開始するには、 [Cloud App Discovery の概要に関するページ](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

## <a name="related-articles"></a>関連記事
* [Cloud App Discovery のセキュリティとプライバシーの考慮事項](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery のグループ ポリシーのデプロイメント ガイド](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)
* [Cloud App Discovery の System Center デプロイメント ガイドに](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)
* [Cloud App Discovery のカスタム ポートを使用するプロキシ サービス用レジストリ設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery エージェントの変更ログ ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Cloud App Discovery に関してよく寄せられる質問](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)



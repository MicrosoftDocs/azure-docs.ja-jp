---
title: "管理されていないクラウド アプリケーションを Azure Active Directory の Cloud App Discovery で検出する | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 35b898aa3c03aeef914a7df574ac65a22a6c7bec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="find-unmanaged-cloud-applications-with-cloud-app-discovery"></a>管理されていないクラウド アプリケーションを Cloud App Discovery で検出する
## <a name="summary"></a>概要

Cloud App Discovery は、組織内のユーザーによって使用されている管理されていないクラウド アプリケーションを検出できるようにする Azure Active Directory Premium の機能です。 現代の企業では、IT 部門が、組織のメンバーが作業のために使用しているクラウド アプリケーションを部分的にしか認識できていないことがよくあります。 管理者が企業データへの不正アクセスを心配している理由は容易に理解できます。データの漏洩やその他のセキュリティ リスクが発生するおそれがあるからです。 このような認識不足は、セキュリティ リスクに対処するための計画策定が困難であるように思わせる可能性があります。

> [!TIP] 
> [Microsoft Cloud App Security との統合](https://portal.cloudappsecurity.com)によって拡張される、Azure Active Directory (Azure AD) の Cloud App Discovery の機能強化を確認してください。

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


## <a name="next-steps"></a>次のステップ
* [Cloud App Discovery のセキュリティとプライバシーの考慮事項](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)  
* [Cloud App Discovery のカスタム ポートを使用するプロキシ サービス用レジストリ設定](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
* [Cloud App Discovery エージェントの変更ログ ](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)


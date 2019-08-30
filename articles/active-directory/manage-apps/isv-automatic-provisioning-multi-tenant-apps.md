---
title: Azure Active Directory のマルチテナント アプリケーションで自動ユーザー プロビジョニングを有効にする
description: 自動プロビジョニングを有効にするための独立系ソフトウェア ベンダー向けのガイド
services: active-directory
documentationcenter: azure
author: BarbaraSelden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: baselden
ms.reviewer: zhchia
ms.collection: active-directory
ms.openlocfilehash: 11fda31cd06db67e0a11a68a02da8b91a77e04e1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68729189"
---
# <a name="enable-automatic-user-provisioning-for-your-multi-tenant-application"></a>マルチテナント アプリケーションで自動ユーザー プロビジョニングを有効にする

自動ユーザープロビジョニングとは、SaaS アプリケーションなどのターゲット システムでユーザー ID の作成、保守、削除を自動化するプロセスのことです。

## <a name="why-enable-automatic-user-provisioning"></a>自動ユーザー プロビジョニングを有効にする理由

ユーザーが初めてサインインする前にユーザー レコードがアプリケーション内にあることが要求されるアプリケーションでは、ユーザー プロビジョニングが必要です。 サービス プロバイダーと顧客の両方にメリットがあります。

### <a name="benefits-to-you-as-the-service-provider"></a>サービス プロバイダーにとってのメリット

* Microsoft ID プラットフォームを使用することでアプリケーションのセキュリティが強化されます。

* 顧客があなたのアプリケーションを採用するために行う実際の作業と認識されている作業が減ります。

* System for Cross-Domain Identity Management (SCIM) ベースのプロビジョニングを使用することで、自動ユーザー プロビジョニングに対応するための、複数の ID プロバイダー (IdP) との統合のコストが削減されます。

* 顧客がユーザー プロビジョニングの問題をトラブルシューティングするのに役立つ豊富なログを提供することで、サポート コストが削減されます。

* [Azure Active Directory アプリ ギャラリー](https://azuremarketplace.microsoft.com/marketplace/apps)でのあなたのアプリケーションの可視性が向上します。

* アプリのチュートリアル ページで優先順位が付けられた一覧を取得できます。

### <a name="benefits-to-your-customers"></a>顧客にとってのメリット

* ロールを変更するユーザーや組織を去るユーザーの、アプリケーションへのアクセス権を自動的に削除することで、セキュリティが強化されます。

* 手動によるプロビジョニングに関連した人為的エラーや繰り返し作業を回避することで、アプリケーションのユーザー管理が簡素化されます。

* 独自に開発したプロビジョニング ソリューションのホスティングとメンテナンスのコストが削減されます。

## <a name="choose-a-provisioning-method"></a>プロビジョニング方法を選択する

Azure AD には、アプリケーションで自動ユーザー プロビジョニングを有効にするための統合パスがいくつか用意されています。

* [Azure AD プロビジョニング サービス](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)では、Azure AD からアプリケーションへ (外向きのプロビジョニング) とアプリケーションから Azure AD へ (内向きのプロビジョニング) のユーザーのプロビジョニングとプロビジョニング解除が管理されます。 このサービスは、あなたのアプリケーションによって提供される System for Cross-Domain Identity Management (SCIM) ユーザー管理 API エンドポイントに接続します。

* [Microsoft Graph](https://docs.microsoft.com/graph/) を使用しているときに、そのアプリケーションは、Microsoft Graph API のクエリを実行して、Azure AD からそのアプリケーションに対するユーザーとグループの内向きのプロビジョニングと外向きのプロビジョニングを管理します。

* アプリケーションでフェデレーションに SAML が使用されている場合は、Security Assertion Markup Language Just in Time (SAML JIT) のユーザー プロビジョニングを有効にすることができます。 これは、SAML トークンで送信された要求情報を使用してユーザーのプロビジョニングを行います。

アプリケーションで使用する統合オプションを決定するには、比較の概要の表を参照してから、各オプションの詳細情報を確認してください。

| 自動プロビジョニングによって有効化または強化される機能| Azure AD プロビジョニング サービス (SCIM 2.0)| Microsoft Graph API (OData v4.0)| SAML JIT |
|---|---|---|---|
| Azure AD でのユーザーおよびグループの管理| √| √| ユーザーのみ |
| オンプレミスの Active Directory から同期されるユーザーおよびグループを管理する| √*| √*| ユーザーのみ* |
| O365 データ (Teams、SharePoint、電子メール、カレンダー、ドキュメントなど) に Access をプロビジョニングしているときに、ユーザーおよびグループ以外のデータにアクセスする| X+| √| X |
| ビジネス ルールに基づいてユーザーの作成、読み取り、更新を行う| √| √| √ |
| ビジネス ルールに基づいてユーザーを削除する| √| √| X |
| Azure portal からすべてのアプリケーションの自動ユーザー プロビジョニングを管理する| √| X| √ |
| 複数の ID プロバイダーをサポートする| √| X| √ |
| ゲスト アカウント (B2B) をサポートする| √| √| √ |
| エンタープライズ以外のアカウント (B2C) をサポートする| X| √| √ |

<sup>*</sup> – AD から Azure AD にユーザーを同期するには、Azure AD Connect のセットアップが必要です。  
<sup>+</sup >– プロビジョニングに SCIM を使用しても、他の目的のためにアプリケーションを MIcrosoft Graph と統合することが妨げられることはありません。

## <a name="azure-ad-provisioning-service"></a>Azure AD プロビジョニング サービス

Azure AD プロビジョニング サービスでは、多くの ID プロバイダー (IdP) によってサポートされているプロトコルである SCIM が使用されます。 Azure AD に加えて IdP をサポートする場合は、Azure AD プロビジョニング サービスを使用することをお勧めします。これは、SCIM 準拠の IdP は SCIM エンドポイントに接続できるためです。

Azure AD プロビジョニング サービスでの SCIM の使用方法について詳しくは、次を参照してください。 

* [System for Cross-Domain Identity Management (SCIM) を使用して Azure Active Directory からユーザーとグループをアプリケーションに自動的にプロビジョニングする](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Azure AD SCIM の実装について](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

* [Microsoft CLI ライブラリを使用した SCIM エンドポイントの構築](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)

## <a name="microsoft-graph-for-provisioning"></a>プロビジョニングのための Microsoft Graph

プロビジョニングに Microsoft Graph を使用すると、Graph で使用可能なすべてのリッチ ユーザー データにアクセスできます。 ユーザーとグループの詳細情報に加え、ユーザーのロール、マネージャーと直属の部下、所有するデバイスと登録されたデバイス、および [Microsoft Graph](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0) で利用可能なその他の数百のデータなどの追加情報も取得できます。 

1500 万を超える組織、および Fortune 500 企業の 90% が、Office 365、Microsoft Azure、Enterprise Mobility Suite、Microsoft 365 などの Microsoft クラウド サービスをサブスクライブしながら Azure AD を使用しています。 Microsoft Graph を使用して、従業員のオンボーディング (と退職)、プロファイルの保守などの管理ワークフローとアプリを統合できます。 

プロビジョニングでの Microsoft Graph の使用の詳細については、次をご覧ください。

* [Microsoft Graph ホーム ページ](https://developer.microsoft.com/graph)

* [Microsoft Graph の概要](https://docs.microsoft.com/graph/overview)

* [Microsoft Graph 認証の概要](https://docs.microsoft.com/graph/auth/)

* [Microsoft Graph の概要](https://developer.microsoft.com/graph/get-started)

* [Microsoft Graph API を使用する](https://docs.microsoft.com/graph/use-the-api)

* [Microsoft Graph Explorer](https://aka.ms/ge) 

* [Microsoft Graph のアクセス許可のリファレンス](https://docs.microsoft.com/graph/permissions-reference)

* [Microsoft Graph の操作に関するベスト プラクティス](https://docs.microsoft.com/graph/best-practices-concept)

* [Microsoft Graph の主なシナリオ](https://developer.microsoft.com/graph/examples)

## <a name="using-saml-jit-for-provisioning"></a>プロビジョニングに SAML JIT を使用する

アプリケーションへの初めてのサインインのときにのみユーザーをプロビジョニングする必要があり、ユーザーを自動的にプロビジョニング解除する必要はない場合は、SAML JIT を選択できます。 SAML JIT を使用するには、SAML 2.0 がフェデレーション プロトコルとしてアプリケーションでサポートされている必要があります。

SAML JIT は、SAML トークン内の要求情報を使用して、アプリケーションのユーザー情報を作成および更新します。 顧客は、必要に応じて、これらの必要な要求を Azure AD アプリケーションで構成できます。 場合によっては、顧客がこの機能を使用できるようにするために、アプリケーション側から JIT プロビジョニングを有効にする必要があります。 SAML JIT はユーザーの作成と更新には役立ちますが、アプリケーションのユーザーを削除したり非アクティブにしたりすることはできません。

## <a name="next-steps"></a>次の手順

* [アプリケーションのシングル サインオンを有効にする](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-sso-content)

* [アプリケーションの一覧を提出し](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)、Microsoft と連携して Microsoft のサイトでドキュメントを作成します。

* [Microsoft Partner Network に参加し (無料)、市場投入プランを作成します](https://partner.microsoft.com/en-us/explore/commercial)。

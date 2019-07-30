---
title: マルチテナント アプリケーションの SSO を有効にする
description: Azure Active Directory との統合に関する独立系ソフトウェア ベンダー向けガイダンス
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c89a83ade6305579e700afb86f0b9e3aca2695e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795182"
---
# <a name="enable-single-sign-on-for-your-multi-tenant-application"></a>マルチテナント アプリケーションのシングル サインオンを有効にする  

購入またはサブスクリプションを通じて他の会社にアプリケーションを提供する際に、顧客が独自の Azure テナント内でアプリケーションを利用できるようにします。 これは、マルチテナント アプリケーションの作成と呼ばれます。 この概念の概要については、「[Azure 上のマルチテナント アプリケーション](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications)」と「[Azure Active Directory のテナント](../develop/single-and-multi-tenant-apps.md)」を参照してください。

## <a name="what-is-single-sign-on"></a>シングル サインオンとは

シングル サインオン (SSO) によって、ユーザーが Azure Active Directory およびその他の ID を使用してアプリケーションにサインオンするときのセキュリティと利便性が向上します。 アプリケーションで SSO が有効な場合、ユーザーはそのアプリケーションにアクセスするために別の資格情報を入力する必要がありません。 シングル サインオンの詳細については、 「[Azure Active Directory でのアプリケーションへのシングル サインオン](what-is-single-sign-on.md)」を参照してください。

## <a name="why-enable-single-sign-on-in-your-application"></a>アプリケーションでシングル サインオンを有効にする理由

マルチテナント アプリケーションで SSO を有効にする利点はたくさんあります。 アプリケーションの SSO を有効にすると、以下が可能になります。

* アプリケーションを Azure Marketplace に表示できます。ここに表示されるアプリは、Azure Active Directory を使用している数多くの組織から見つやすくなります。
  * 顧客は Azure AD を使用してアプリケーションをすばやく構成できます。

* アプリケーションが、Office.com の Office 365 アプリのギャラリー、Office 365 アプリ起動ツール、および Microsoft Search 内で検出可能になります。

* アプリケーションで Microsoft Graph REST API を使用して、Microsoft Graph から利用できる、ユーザー生産性を高めるデータにアクセスできます。

* 顧客にとって使いやすいものにすることによって、サポート コストを削減できます。
  * 相互の顧客向けにアプリケーション固有のドキュメントを Azure AD チームと共同制作することで、導入しやすくなります。
  * ワンクリック SSO が有効になっている場合、顧客の IT 管理者がアプリケーションの構成方法を知らなくても、アプリケーションを組織内で使用できます。

* 従業員 ID やゲスト ID の認証と承認を完全に管理する機能を顧客に提供できます。

  * すべてアカウント管理とコンプライアンスの責任を顧客の ID 所有者が担います。

  * ビジネス ニーズを満たすように、ID プロバイダーごと、グループごと、またはユーザーごとに SSO を有効/無効にできるようにします。

* 市場性と導入可能性を高めることができます。 多くの大規模組織では、従業員がすべてのアプリケーション間でシームレスな SSO エクスペリエンスを利用できることを必要として (切望して) います。 SSO を容易にすることが重要です。

* エンドユーザーの煩雑さを減らすことで、エンドユーザーの利用と収益を拡大することができます。

## <a name="how-to-enable-single-sign-on-in-your-published-application"></a>発行済みアプリケーションでシングル サインオンを有効にする方法

1. [マルチテナント アプリケーションに合った適切なフェデレーション プロトコルを選択します](isv-choose-multi-tenant-federation.md)。
1. アプリケーションに SSO を実装します
   - [認証パターンに関するガイダンス](../develop/v2-app-types.md)を参照してください
   - OIDC および OAuth プロトコルに対応した [Azure Active Directory のコード サンプル](../develop/sample-v2-code.md)を参照してください
1. [Azure テナントを作成し](isv-tenant-multi-tenant-app.md)、アプリケーションをテストします
1. [SSO のドキュメントを作成し、サイトに発行します](isv-create-sso-documentation.md)。
1. [アプリケーションの一覧を提出し](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx)、Microsoft と連携して Microsoft のサイトでドキュメントを作成します。
1. [Microsoft Partner Network に参加し (無料)、市場投入プランを作成します](https://partner.microsoft.com/en-us/explore/commercial#gtm)。

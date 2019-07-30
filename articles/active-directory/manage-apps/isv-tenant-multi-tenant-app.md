---
title: マルチテナント アプリケーション用に Azure テナントを作成する
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
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659449"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>マルチテナント アプリケーション用に Azure テナントを作成する  

マルチテナント アプリケーションにアクセスできるようにするには、アプリケーションを登録し、顧客の ID のフェデレーションを有効にするために、Azure Active Directory テナントを作成する必要があります。 [マルチテナント アプリケーションのための適切なフェデレーション プロトコルの選択](isv-choose-multi-tenant-federation.md)に関する記事を参照してください。 このテナントにより、顧客の Azure AD 環境に類似する環境でアプリケーションおよびフェデレーションをテストできます。

## <a name="costs-of-hosting-a-multi-tenant-application"></a>マルチテナント アプリケーションをホストするためのコスト

Azure Active Directory は、Free、Basic、および Premium の 3 つの SKU で利用できます。 [詳細な機能の比較](https://azure.microsoft.com/pricing/details/active-directory/)に関する記事を参照してください。

Azure サブスクリプションと Azure Active Directory は無料で作成でき、基本的な機能を使用できます。

## <a name="create-your-tenant"></a>テナントを作成する

1. テナントを作成します。 「[開発環境の設定](../develop/quickstart-create-new-tenant.md)」を参照してください。

2. アプリケーションへのシングル サインオン アクセスを有効にし、テストします。

   a. **OIDC または Oath アプリケーション**の場合、マルチテナント アプリケーションとして[アプリケーションを登録](../develop/quickstart-register-app.md)します。 ‎[サポートされているアカウントの種類] で、[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント] オプションを選択します。

   b. **SAML ベース、および WS-Fed ベースのアプリケーション**の場合、Azure AD で汎用 SAML テンプレートを使用して [SAML ベースのシングル サインオン アプリケーションを構成](configure-single-sign-on-non-gallery-applications.md)します。

必要に応じて、[シングルテナント アプリケーションをマルチテナントに変換](../develop/howto-convert-app-to-be-multi-tenant.md)することもできます。

## <a name="next-steps"></a>次の手順

[アプリケーションで SSO を統合する](isv-sso-content.md)

---
title: Azure Digital Twins API の認証の理解 | Microsoft Docs
description: Azure Digital Twins を使用して API を接続および認証する
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: lyhughes
ms.openlocfilehash: 114edc072524552fab35e9cad6fc85573c4e8d0e
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846542"
---
# <a name="connect-and-authenticate-to-apis"></a>API に接続および認証する

Azure Digital Twins は、Azure Active Directory (Azure AD) を使用してユーザーを認証し、アプリケーションを保護します。 Azure AD では、さまざまな最新アーキテクチャ用の認証がサポートされます。 すべての認証は、業界標準のプロトコルである OAuth 2.0 または OpenID Connect に基づいています。 また、開発者は Azure AD を使用して、シングル テナントと基幹業務 (LOB) アプリケーションを構築することができます。 また開発者は、Azure AD を使用してマルチ テナント アプリケーションを開発することもできます。

Azure AD の概要については、[基礎ページ](https://docs.microsoft.com/azure/active-directory/fundamentals/index)にアクセスしてステップ バイ ステップ ガイド、概念、およびクイック スタートを確認してください。

アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。 詳しい手順とスクリーンショットについては、[こちらのクイック スタート](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)をご覧ください。

Azure AD でサポートされる [5 つの主要なアプリケーション シナリオ](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)は、次のとおりです。

* シングルページ アプリケーション (SPA): ユーザーは、Azure AD によって保護されたシングルページ アプリケーションにサインインする必要があります。
* Web ブラウザー対 Web アプリケーション: ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります。
* ネイティブ アプリケーション対 Web API: 電話、タブレット、または PC 上で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するためにユーザーを認証する必要があります。
* Web アプリケーション対 Web API: Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。
* デーモンまたはサーバー アプリケーション対 Web API: Web UI を備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。

Microsoft Azure 認証ライブラリでは、さまざまな方法で Active Directory トークンを取得できます。 ライブラリとコード サンプルについて詳しくは、[こちらの記事](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)をご覧ください。

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>中間層 Web API から Digital Twins を呼び出す

開発者が Digital Twins ソリューションを設計する際、通常は中間層アプリケーションか API を作成します。 その後、アプリまたは API によって、ダウンストリームの Digital Twins API を呼び出します。 この標準の Web ソリューション アーキテクチャをサポートするには、まずユーザーが次の手順を実行する必要があります。

1. 中間層アプリケーションの認証を受けます

1. 認証中に OAuth 2.0 On-Behalf-Of トークンが取得されます

1. この取得されたトークンは、認証と、On-Behalf-Of フローを使用するさらにダウンストリームである API の呼び出しに使用されます

On-Behalf-Of フローを調整する方法については、[OAuth 2.0 の On-Behalf-Of フロー](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)に関するページを参照してください。 また、コード サンプルについては、[ダウンストリーム Web API の呼び出し](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

OAuth 2.0 の暗黙的な許可フローを使用して Azure Digital Twins を構成およびテストするには、[Postman の構成](./how-to-configure-postman.md)に関するページを参照してください。

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
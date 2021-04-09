---
title: Azure API for FHIR に Azure Active Directory アプリを登録する
description: このチュートリアルでは、Azure API for FHIR および FHIR Server for Azure に登録する必要があるアプリケーションについて説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019101"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Azure API for FHIR に Azure Active Directory アプリを登録する

Azure API for FHIR または FHIR Server for Azure (OSS) を設定する際は、いくつかの構成オプションから選択できます。 オープンソースの場合は、独自のリソース アプリケーションの登録を作成する必要があります。 Azure API for FHIR の場合、このリソース アプリケーションは自動的に作成されます。

## <a name="application-registrations"></a>アプリケーションの登録

アプリケーションは、Azure AD と対話するために、登録する必要があります。 FHIR サーバーのコンテキストでは、次の 2 種類のアプリケーション登録について説明しておく必要があります。

1. リソース アプリケーションの登録。
1. クライアント アプリケーションの登録。

**リソース アプリケーション** は、Azure AD で保護されている API またはリソースを Azure AD で表現したものです。具体的には、Azure API for FHIR です。 Azure API for FHIR のリソース アプリケーションは、サービスをプロビジョニングすると自動的に作成されます。ただし、オープンソース サーバーを使用している場合は、Azure AD に[リソース アプリケーションを登録する](register-resource-azure-ad-client-app.md)必要があります。 このリソース アプリケーションには識別子 URI が設定されます。 この URI は FHIR サーバーの URI と同じにすることをお勧めします。 この URI は、FHIR サーバーの `Audience` として使用する必要があります。 クライアント アプリケーションは、トークンを要求するときに、この FHIR サーバーへのアクセスを要求できます。

"*クライアント アプリケーション*" は、トークンを要求するクライアントの登録です。 OAuth 2.0 では、多くの場合、3 種類以上のアプリケーションが区別されます。

1. **機密クライアント**。Azure AD では Web アプリとも呼ばれます。 機密クライアントとは、[承認コード フロー](../../active-directory/azuread-dev/v1-protocols-oauth-code.md)を使用して、有効な資格情報を提示しているサインイン ユーザーの代わりにトークンを取得するアプリケーションです。 これらが機密クライアントと呼ばれるのは、シークレットを保持でき、認証コードをトークンと交換する際にこのシークレットを Azure AD に提示するためです。 機密クライアントは、クライアント シークレットを使用して自身を認証できるため、パブリック クライアントよりも信頼性が高く、より有効期間が長いトークンを保持することができます。このクライアントには、更新トークンを付与することもできます。 [機密クライアントを登録する](register-confidential-azure-ad-client-app.md)方法の詳細をお読みください。 クライアントが承認コードを受け取るときに応答 URL を登録することが重要であることに注意してください。
1. **パブリック クライアント**。 これらは、シークレットを保持できないクライアントです。 通常、これはモバイル デバイス アプリケーションまたは単一ページの JavaScript アプリケーションであり、クライアント内のシークレットがユーザーによって発見される場合があります。 パブリック クライアントも承認コード フローを使用しますが、トークンの取得時にシークレットを提示することはできません。また、保持できるトークンの有効期間は短くなり、更新トークンを保持できません。 [パブリック クライアントを登録する](register-public-azure-ad-client-app.md)方法の詳細をお読みください。
1. サービス クライアント。 このクライアントは、[クライアント資格情報フロー](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)を使用して、(ユーザーの代わりではなく) 自身の代わりにトークンを取得します。 これらは通常、非インタラクティブな方法で FHIR サーバーにアクセスするアプリケーションを表します。 たとえば、インジェスト プロセスです。 サービス クライアントを使用する場合、`/authorize` エンドポイントへの呼び出しでトークンを取得するプロセスを開始する必要はありません。 サービス クライアントは、直接 `/token` エンドポイントにアクセスし、クライアント ID とクライアント シークレットを提示してトークンを取得できます。 [サービス クライアントを登録する](register-service-azure-ad-client-app.md)方法の詳細をお読みください。

## <a name="next-steps"></a>次のステップ

この概要では、FHIR API の操作に必要となる可能性のあるアプリケーション登録の種類について説明しました。

実際の設定に基づき、アプリケーションを登録するための攻略ガイドを参照してください

* [リソース アプリケーションを登録する](register-resource-azure-ad-client-app.md)
* [機密クライアント アプリケーションを登録する](register-confidential-azure-ad-client-app.md)
* [パブリック クライアント アプリケーションを登録する](register-public-azure-ad-client-app.md)
* [サービス クライアント アプリケーションを登録する](register-service-azure-ad-client-app.md)

アプリケーションの登録が完了したら、Azure API for FHIR をデプロイできます。

>[!div class="nextstepaction"]
>[Azure API for FHIR をデプロイする](fhir-paas-powershell-quickstart.md)
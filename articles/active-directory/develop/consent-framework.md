---
title: Azure Active Directory 同意フレームワーク
description: Azure Active Directory の同意フレームワークと、それを使用してマルチテナントの Web クライアント アプリケーションやネイティブ クライアント アプリケーションを簡単に開発できるしくみについて説明します。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/30/2018
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b9d272c8a01eeed58278a6e7f0cec147b01a10e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482932"
---
# <a name="azure-active-directory-consent-framework"></a>Azure Active Directory 同意フレームワーク

Azure Active Directory (Azure AD) の同意フレームワークを使用すると、マルチテナントの Web クライアント アプリケーションやネイティブ クライアント アプリケーションを簡単に開発できます。 こうしたアプリケーションには、登録されている Azure AD テナントとは異なるテナントのユーザー アカウントを使ってサインインできます。 また、独自の Web API に加えて、Microsoft Graph API (Azure AD、Intune、および Office 365 のサービスへのアクセスに使用する API) をはじめとする Microsoft サービスの API など、各種の Web API にもアクセスが必要になることがあります。

このフレームワークの根底には、ディレクトリのデータにアクセスする可能性があるためにディレクトリへの登録を要求しているアプリケーションに対して同意を与えるユーザーまたは管理者が存在します。 たとえば、Web クライアント アプリケーションで Office 365 ユーザーの予定表情報を読み取る必要があるときは、まずそのユーザーがクライアント アプリケーションに同意を与える必要があります。 同意が与えられると、クライアント アプリケーションがユーザーに代わって Microsoft Graph API を呼び出し、必要に応じて予定表情報を利用できるようになります。 [Microsoft Graph API](https://developer.microsoft.com/graph) は、Office 365 のデータ (Exchange の予定表とメッセージ、SharePoint のサイトとリスト、OneDrive のドキュメント、OneNote のノートブック、Planner のタスク、Excel のブック)、Azure AD のユーザーとグループなど、Microsoft クラウド サービスのさまざまなデータ オブジェクトにアクセスするための API です。

Azure AD の同意フレームワークは、OAuth 2.0 と、public クライアントまたは confidential クライアントを使用した OAuth 2.0 のさまざまなフロー (Authorization Code Grant や Client Credentials Grant など) を基盤としています。 Azure AD では OAuth 2.0 を採用することによって、スマートフォン、タブレット、サーバーなどにインストールされるアプリケーションから Web アプリケーションに至るまで、多種多様なクライアント アプリケーションの作成と、そのアプリケーションが必要なリソースにアクセスすることを可能にしています。

OAuth 2.0 の認可付与を採用している同意フレームワークの使い方の詳細については、[OAuth 2.0 と Azure AD を使用した Web アプリケーションへのアクセスの承認](v1-protocols-oauth-code.md)に関するページと、「[Azure AD の認証シナリオ](authentication-scenarios.md)」を参照してください。 Microsoft Graph を使って Office 365 に対するアクセスの承認を得る方法の詳細については、[Microsoft Graph によるアプリ認証](https://developer.microsoft.com/graph/docs/authorization/auth_overview)に関するページを参照してください。

## <a name="consent-experience---an-example"></a>同意エクスペリエンス - 例

以下の手順は、アプリケーションの開発者とユーザーにとっての同意エクスペリエンスがどのようなものになるかを示したものです。

1. 何らかのリソース/API にアクセスするために一定のアクセス許可を要求する必要がある Web クライアント アプリケーションがあると仮定しましょう。 構成方法については次のセクションで詳しく説明するとして、ここで重要なのは、構成時に Azure Portal を使用してアクセス許可の要求を宣言するという点です。 この設定は他の構成設定と同じく、アプリケーションを Azure AD に登録する作業の一環として行います。

    ![他のアプリケーションに対するアクセス許可](./media/consent-framework/permissions.png)

1. 現在、アプリケーションの更新が終わって、アプリケーションが実行中になり、これからユーザーが初めてアプリケーションを使用するところだとします。 アプリケーションではまず、Azure AD の `/authorize` エンドポイントから認証コードを取得する必要があります。 取得した認証コードは、後でアクセス トークンと更新トークンの取得に使用します。

1. ユーザーの認証がまだであれば、Azure AD の `/authorize` エンドポイントによりユーザーのサインイン画面が表示されます。

    ![ユーザーまたは管理者による Azure AD へのサインイン](./media/quickstart-v1-integrate-apps-with-azure-ad/usersignin.png)

1. ユーザーのサインインが終わると、そのユーザーに対して同意ページを表示する必要があるかどうかが Azure AD により判定されます。 表示の要否の判定基準は、ユーザー (またはそのユーザーが所属する組織の管理者) がアプリケーションに既に同意を与えているかどうかです。 同意がまだであれば、Azure AD からユーザーに対して同意を求めるメッセージと、アプリケーションが機能するうえで必要なアクセス許可が表示されます。 同意ダイアログに表示されるアクセス許可は、Azure portal の **[委任されたアクセス許可]** で選択したものと同じになります。

    ![同意ダイアログに表示されるアクセス許可の例を示します](./media/quickstart-v1-integrate-apps-with-azure-ad/consent.png)

1. ユーザーが同意すると、アプリケーションに認証コードが返されます。アクセス トークンと更新トークンを取得するときには、この認証コードが必要になります。 このフローについて詳しくは、[Web API のアプリの種類](web-api.md)に関する記事をご覧ください。

1. 管理者であれば、テナント内のユーザー全員に代わってアプリケーションの委任されたアクセス許可に同意することもできます。 管理者が同意すると、そのテナントのユーザーには同意ダイアログが表示されなくなります。この同意は、管理者ロールが与えられたユーザーが [Azure portal](https://portal.azure.com) で行うことができます。 委任されたアクセス許可に同意できる管理者ロールについては、「[Azure AD での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

    **委任されたアプリケーションのアクセス許可に同意する**

   1. アプリケーションの **[API アクセス許可]** ページに移動します。
   1. **[管理者の同意の付与]** ボタンをクリックします。

      ![明示的な管理者の同意としてアクセス許可を付与する](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > 現時点では、ADAL.js を使用するシングルページ アプリ (SPA) では、 **[アクセス許可の付与]** ボタンを使用して明示的に同意を付与する必要があります。 そうしないと、アクセス トークンが要求されたときにアプリケーションでエラーが発生します。

## <a name="next-steps"></a>次の手順

* [アプリをマルチテナント アプリに変換する方法](howto-convert-app-to-be-multi-tenant.md)に関する記事をご覧ください。
* 詳細については、[認証コードの付与フロー中に、OAuth 2.0 プロトコル層で同意がどのようにサポートされるか、学習してください。](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

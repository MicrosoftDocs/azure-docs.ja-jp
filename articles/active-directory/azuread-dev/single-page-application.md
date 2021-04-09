---
title: Azure Active Directory のシングルページ アプリケーション
description: シングルページ アプリケーション (SPA) の概要、この種のアプリのプロトコル フロー、登録、およびトークンの有効期限の基本について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154748"
---
# <a name="single-page-applications"></a>シングルページ アプリケーション

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

通常、シングルページ アプリケーション (SPA) は、ブラウザーで実行される JavaScript プレゼンテーション レイヤー (フロント エンド) と、サーバー上で実行され、アプリケーションのビジネス ロジックを実装する Web API バックエンドとして構成されます。暗黙的な承認許可の詳細と、実際のアプリケーションのシナリオに適しているかどうかを判断するために役立つ情報については、「[Azure Active Directory (AD) での OAuth2 の暗黙的な許可フローについて](v1-oauth2-implicit-grant-flow.md)」を参照してください。

このシナリオでは、ユーザーがサインインすると、JavaScript フロントエンドが [Active Directory Authentication Library for JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) と暗黙的な認証付与を使用して、Azure AD から ID トークン (id_token) を取得します。 トークンがキャッシュされ、クライアントは、OWIN ミドルウェアを使用して保護された Web API バックエンドを呼び出すときに、トークンをベアラー トークンとして要求に添付します。

## <a name="diagram"></a>ダイアグラム

![シングルページ アプリケーション ダイアグラム](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>プロトコル フロー

1. ユーザーが Web アプリケーションに移動します。
1. アプリケーションが JavaScript フロントエンド (プレゼンテーション層) をブラウザーに返します。
1. ユーザーが、たとえばサインイン リンクをクリックして、サインインを開始します。 ブラウザーが Azure AD 認証エンドポイントに GET を送信して ID トークンを要求します。 この要求では、クエリ パラメーターにアプリケーション ID と応答 URL が含まれています。
1. Azure AD が、Azure Portal で構成された登録済みの応答 URL と照合して応答 URL を検証します。
1. ユーザーがサインイン ページでサインインします。
1. 認証が成功すると、Azure AD によって ID トークンが作成され、URL フラグメント (#) としてアプリケーションの応答 URL に返されます。 実稼働アプリケーションでは、この応答 URL は HTTPS である必要があります。 返されたトークンには、トークンを検証するためにアプリケーションが必要とする、ユーザーと Azure AD に関する要求が含まれています。
1. ブラウザーで実行されている JavaScript クライアント コードによって、アプリケーションの Web API バックエンドの呼び出しの保護に使用するために、応答からトークンが抽出されます。
1. ブラウザーにより、Authorization ヘッダーの ID トークンを使用して、アプリケーションの Web API バックエンドが呼び出されます。 Azure AD 認証サービスは、リソースがクライアント ID と同じである場合、ベアラー トークンとして使用できる ID トークンを発行します (この場合、Web API がアプリ自身のバックエンドであるため、これが該当します)。

## <a name="code-samples"></a>コード サンプル

[シングルページ アプリケーションのシナリオのコード サンプル](sample-v1-code.md#single-page-applications)を参照してください。 新しいサンプルが頻繁に追加されているので、頻繁に確認してください。

## <a name="app-registration"></a>アプリの登録

* シングル テナント: 自分の組織だけが使用するアプリケーションを構築している場合は、Azure portal を使用して、アプリケーションを会社のディレクトリに登録する必要があります。
* マルチテナント - 自分の組織の外部のユーザーが使用できるアプリケーションを構築している場合は、アプリケーションを会社のディレクトリに登録するだけでなく、そのアプリケーションを使用する各組織のディレクトリにも登録する必要があります。 ディレクトリ内でアプリケーションを使用できるようにするには、ユーザーがアプリケーションに同意できるようにするためのサインアップ プロセスを含めます。 ユーザーがアプリケーションにサインアップするときに、アプリケーションが必要とするアクセス許可と同意オプションを示すダイアログが表示されます。 必要なアクセス許可によっては、他の組織の管理者が同意することが必要な場合があります。 ユーザーまたは管理者が同意すると、アプリケーションがディレクトリに登録されます。

アプリケーションの登録後、OAuth 2.0 暗黙的な許可プロトコルを使用するようにアプリケーションを構成する必要があります。 既定では、このプロトコルはアプリケーションで無効になっています。 アプリケーションで OAuth2 暗黙的な許可プロトコルを有効にするには、Azure portal でそのアプリケーション マニフェストを編集し、"oauth2AllowImplicitFlow" の値を true に設定します。 詳細については、[アプリケーション マニフェスト](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)に関するページを参照してください。

## <a name="token-expiration"></a>トークンの有効期限

ADAL.js を使用すると、以下の場合に役立ちます。

* 期限切れのトークンを更新する
* Web API リソースを呼び出すアクセス トークンを要求する

認証に成功すると、Azure AD はユーザーのブラウザーに Cookie を書き込んでセッションを確立します。 ユーザーと Azure AD の間にセッションが存在することに注意してください (ユーザーと Web アプリケーションの間ではありません)。 トークンの有効期限が切れると、ADAL.js はこのセッションを使用して、ダイアログを表示せずに別のトークンを取得します。 ADAL.js は非表示の iFrame を使用し、OAuth 暗黙的な許可プロトコルを使用して要求を送受信します。 アプリケーションによって呼び出される他の Web API リソースが、クロス オリジン リソース共有 (CORS) をサポートし、ユーザーのディレクトリに登録されており、サインイン時にユーザーから必要な同意を得られていれば、ADAL.js で同じメカニズムを使用して、ダイアログを表示せずに、それらのリソースのアクセス トークンを取得することもできます。

## <a name="next-steps"></a>次のステップ

* その他の[アプリケーションの種類とシナリオ](app-types.md)について学習する
* Azure AD [認証の基本](v1-authentication-scenarios.md)について学習する

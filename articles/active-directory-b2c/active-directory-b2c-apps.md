---
title: Azure AD B2C | Microsoft Docs
description: "Azure Active Directory B2C で構築できるアプリケーションの種類。"
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: 7d582960e615962a3952dd2f58c74ed91e5c450d


---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: アプリケーションの種類
Azure Active Directory (Azure AD) B2C は、さまざまな最新アプリ アーキテクチャの認証をサポートします。 すべての認証は、業界標準のプロトコルである [OAuth 2.0](active-directory-b2c-reference-protocols.md) または [OpenID Connect](active-directory-b2c-reference-protocols.md) に基づいています。 このドキュメントでは、利用する言語やプラットフォームを問わず、作成できるアプリの種類について簡単に説明します。 また、 [アプリケーションの構築を始める](active-directory-b2c-overview.md#get-started)前にシナリオの概要を理解することもできます。

## <a name="the-basics"></a>基本
Azure AD B2C を使用するすべてのアプリは、[Azure Portal](https://portal.azure.com/) で [B2C ディレクトリ](active-directory-b2c-get-started.md)に登録する必要があります。 アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する **アプリケーション ID** 。
* 応答をアプリにリダイレクトして戻すために使用できる **リダイレクト URI** 。
* その他のシナリオに応じた値。 詳細については、 [アプリの登録](active-directory-b2c-app-registration.md)方法を参照してください。

登録の済んだアプリは、Azure AD v2.0 エンドポイントに要求を送ることによって、Azure AD と通信します。

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Azure AD B2C に送信される各要求は、 **ポリシー**を指定します。 ポリシーは、Azure AD の動作を制御します。 これらのエンドポイントを使用して、高度にカスタマイズ可能なユーザー エクスペリエンスのセットを作成することもできます。 一般的なポリシーとしては、サインアップ ポリシー、サインイン ポリシー、プロファイル編集ポリシーなどがあります。 これらのポリシーを詳しく理解していない場合は、先に進む前に Azure AD B2C の「 [拡張可能ポリシー フレームワーク](active-directory-b2c-reference-policies.md) 」を参照する必要があります。

すべてのアプリによる v2.0 エンドポイントとのやり取りは、次のような大まかなパターンに従って行われます。

1. アプリは、 [ポリシー](active-directory-b2c-reference-policies.md)を実行するためにユーザーを v2.0 エンドポイントにリダイレクトします。
2. ユーザーがポリシーの定義に従ってポリシーを完了します。
3. アプリは、v2.0 エンドポイントから何らかの種類のセキュリティ トークンを受け取ります。
4. アプリは、セキュリティ トークンを使って、保護された情報またはリソースにアクセスします。
5. リソース サーバーは、セキュリティ トークンを検証し、アクセスを許可できることを確認します。
6. アプリは、セキュリティ トークンを定期的に更新します。

<!-- TODO: Need a page for libraries to link to -->
これらの手順は、構築しているアプリの種類によりわずかに異なることがあります。 細部は、オープン ソース ライブラリが自動的に対応できます。

## <a name="web-apps"></a>Web Apps
サーバーでホストされ、ブラウザーを通じてアクセスされる Web アプリ (.NET、PHP、Java、Ruby、Python、Node.js など) に対して、Azure AD B2C は、すべてのユーザー エクスペリエンスで [OpenID Connect](active-directory-b2c-reference-protocols.md) をサポートします。 これには、サインイン、サインアップ、およびプロファイル管理が含まれます。 Azure AD B2C の OpenID Connect の実装では、Web アプリは、これらのユーザーエクスペリエンスを Azure AD に認証要求を発行することで開始します。 要求の結果は `id_token`です。 このセキュリティ トークンは、ユーザーの ID を表します。 また、要求の形式でユーザーに関する情報も提供します。

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

アプリで利用できるトークンと要求の種類の詳細については、 [B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)のページを参照してください。

Web アプリでは、 [ポリシー](active-directory-b2c-reference-policies.md) を実行するたびに、次に示す大まかな手順が実行されます。

![Web アプリのスイムレーン イメージ](./media/active-directory-b2c-apps/webapp.png)

ユーザーの ID を確認するには、Azure AD から受け取った公開署名キーを使用して `id_token` を検証するだけで十分です。 これにより、以降のページ要求でユーザーを識別するために使用できるセッション Cookie も設定されます。

このシナリオを実際に確認するには、 [作業開始](active-directory-b2c-overview.md#get-started)に関するセクションのいずれかの Web アプリ サインイン コード サンプルを試してください。

Web サーバー アプリは、サインインを簡単にするだけでなく、他のバックエンド Web サービスにアクセスすることが必要な場合もあります。 このような場合、Web アプリでは少し異なる [OpenID Connect フロー](active-directory-b2c-reference-oidc.md) を実行し、承認コードと更新トークンを使用してトークンを取得することができます。 このシナリオについては、次の「 [Web API](#web-apis)」セクションで説明します。

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Web API
Azure AD B2C を使用して、アプリの RESTful Web API などの Web サービスをセキュリティで保護できます。 Web API では、OAuth 2.0 を使用し、トークンによって受信 HTTP 要求を認証することで、データをセキュリティで保護することができます。 Web API の呼び出し元は、HTTP 要求の承認ヘッダーの中にトークンを追加します。

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Web API はトークンを使用して API の呼び出し元の ID を検証し、トークン内にエンコードされているクレームから呼び出し元に関する情報を抽出します。 アプリで利用できるトークンと要求の種類の詳細については、 [Azure AD B2C トークン リファレンス](active-directory-b2c-reference-tokens.md)のページを参照してください。

> [!NOTE]
> 現在の Azure AD B2C では、Web API の既知のクライアントによってアクセスされる Web API のみをサポートしています。 たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。 このアーキテクチャは完全にサポートされます。 別の iOS アプリなどのパートナー クライアントが同じ Web API にアクセスできるようにすることは、現時点ではサポートされていません。 完成したアプリのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。
>
>

Web API は、Web アプリ、デスクトップ アプリ、モバイル アプリ、シングル ページ アプリ、サーバー サイド デーモン、それ以外の Web API など、多くの種類のクライアントからトークンを受信できます。 ここでは、Web API を呼び出す Web アプリの完全なフローの例を示します。

![Web API のスイムレーン イメージ](./media/active-directory-b2c-apps/webapi.png)

承認コード、更新トークン、およびトークンの取得手順については、 [OAuth 2.0 プロトコル](active-directory-b2c-reference-oauth-code.md)に関するページを参照してください。

Azure AD B2C を使用して Web API をセキュリティ保護する方法の詳細については、Web API チュートリアルの「 [作業開始](active-directory-b2c-overview.md#get-started)」セクションを参照してください。

## <a name="mobile-and-native-apps"></a>モバイル アプリとネイティブ アプリ
モバイル アプリやデスクトップ アプリなどのデバイスにインストールされているアプリは、多くの場合、ユーザーの代わりにバックエンド サービスや Web API にアクセスする必要があります。 カスタマイズされた ID 管理エクスペリエンスをネイティブ アプリに追加し、Azure AD B2C と [OAuth 2.0 の承認コード フロー](active-directory-b2c-reference-oauth-code.md)を使用して、バックエンド サービスを安全に呼び出すことができます。  

このフローでは、アプリが[ポリシー](active-directory-b2c-reference-policies.md)を実行し、ユーザーがポリシーを完了した後、Azure AD から `authorization_code` を受け取ります。 `authorization_code` は、現在サインインしているユーザーに代わってバックエンド サービスを呼び出すためのアプリのアクセス許可を表します。 これにより、アプリはバックグラウンドで `authorization_code` を `id_token` および `refresh_token` と交換できます。  アプリは `id_token` を使用し、HTTP 要求でバックエンド Web API への認証を行うことができます。 また、古い `id_token` の有効期限が切れたときは、`refresh_token` を使用して新しいものを取得できます。

> [!NOTE]
> 現在の Azure AD B2C では、アプリ独自のバックエンド Web サービスにアクセスするために使用されるトークンのみをサポートしています。 たとえば、完成したアプリには、iOS アプリ、Android アプリ、Web API を含めることができます。 このアーキテクチャは完全にサポートされます。 iOS アプリが OAuth 2.0 アクセス トークンを使用してパートナー の Web API にアクセスできるようにすることは、現時点ではサポートされていません。 完成したアプリのすべてのコンポーネントは、1 つのアプリケーション ID を共有する必要があります。
>
>

![ネイティブ アプリのスイムレーン イメージ](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>現時点での制限事項
Azure AD B2C では、現時点で以下の種類のアプリはサポートしていませんが、今後サポート対象に含まれる予定です。 Azure AD B2C に関するその他の制限および制約事項については、 [制限事項と制約事項](active-directory-b2c-limitations.md)に関するページを参照してください。

### <a name="daemonsserver-side-apps"></a>デーモン/サーバー側アプリ
長時間実行されるプロセスを含んだアプリや、ユーザーの介入なしで動作するアプリも、セキュリティで保護されたリソース (Web API など) にアクセスする必要があります。 これらのアプリは、(ユーザーの委任 ID ではなく) アプリの ID と OAuth 2.0 のクライアント資格情報フローを使用して、認証を行い、トークンを取得することができます。

このフローは現在、Azure AD B2C ではサポートされていません。 これらのアプリは、対話型ユーザー フローが行われた後でのみ、トークンを取得できます。

### <a name="web-api-chains-on-behalf-of-flow"></a>Web API チェーン (On-Behalf-Of フロー)
多くのアーキテクチャには別のダウンストリーム Web API を呼び出す必要がある Web API が含まれ、その場合は両方とも Azure AD B2C によってセキュリティ保護されます。 このシナリオは、Web API バックエンドのあるネイティブ クライアントで一般的なものです。 その後は、Azure AD Graph API などの Microsoft オンライン サービスを呼び出します。

このように Web API を連鎖的に呼び出すシナリオは、OAuth 2.0 JWT Bearer Credential Grant (On-Behalf-Of フロー) を使用してサポートできます。  ただし、現時点では、Azure AD B2C に On-Behalf-Of フローは実装されていません。



<!--HONumber=Dec16_HO4-->



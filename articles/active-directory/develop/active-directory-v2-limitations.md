---
title: "Azure Active Directory v2.0 エンドポイントの制限事項および制約事項 | Microsoft Docs"
description: "Azure AD v2.0 エンドポイントに関する制限事項と制約事項のリスト"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: a99289c0-e6ce-410c-94f6-c279387b4f66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 3e0bb32a6c60011d71606c896cc506f430bc3c27
ms.openlocfilehash: 5d1ceabeeee8cef0170b928703488845f70656ef


---
# <a name="should-i-use-the-v20-endpoint"></a>v2.0 エンドポイントの使用が適しているかどうかを判断するには
Azure Active Directory (Azure AD) と統合されるアプリケーションを構築する場合は、v2.0 エンドポイントと認証プロトコルがニーズを満たすかどうかを判断する必要があります。 元の Azure AD エンドポイントは引き続き完全にサポートされ、いくつかの点では v2.0 よりも機能が豊富です。 ただし、v2.0 エンドポイントは、開発者に[大きなメリット](active-directory-v2-compare.md)を提供します。 v2.0 のメリットは、新しいプログラミング モデルを利用する理由になると考えられます。

次に、v2.0 エンドポイントを直ちに使用する場合の推奨事項を示します。

* アプリケーションで個人の Microsoft アカウントをサポートする場合は、v2.0 エンドポイントを使用します。 その準備として、この記事で説明する制限事項を必ず理解してください。特に職場および学校のアカウントに適用される制限事項について理解してください。
* アプリケーションで職場および学校のアカウントのみをサポートする必要がある場合は、[元の Azure AD エンドポイント](active-directory-developers-guide.md)を使用してください。

今後、v2.0 エンドポイントはこの一覧に記載された制限事項をなくすように拡張され、v2.0 エンドポイントのみを使用すればよくなる予定です。 その間も、この記事には、v2.0 エンドポイントがニーズを満たすかを判断する際に役立つ情報が記載されています。 v2.0 エンドポイントの現在の状態を反映するように、この記事を引き続き更新する予定です。 v2.0 の機能に対して、要件を再確認してください。

v2.0 エンドポイントを使用していない既存の Azure AD アプリを使用している場合も、ゼロから始める必要はありません。 将来的に、既存の Azure AD アプリケーションを v2.0 エンドポイントと共に使用する方法が提供される予定です。

## <a name="restrictions-on-app-types"></a>アプリの種類に関する制限事項
現時点では、次の種類のアプリは、v2.0 エンドポイントでサポートされていません。 サポートされているアプリの種類については、[Azure Active Directory v2.0 エンドポイントのアプリの種類](active-directory-v2-flows.md)に関する記事を参照してください。

### <a name="standalone-web-apis"></a>スタンドアロン Web API
v2.0 エンドポイントを使用すると [OAuth 2.0 で保護された Web API をビルド](active-directory-v2-flows.md#web-apis)できます。 ただし、その Web API は、同じアプリケーション ID を持つアプリケーションからしかトークンを受け取ることができません。 別のアプリケーション ID を持つクライアントから Web API にアクセスすることはできません。 クライアントは、Web API へのアクセス許可を要求することも取得することもできません。

同じアプリケーション ID を持つクライアントからのトークンを受け付ける Web API の構築方法については、[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started) セクションに掲載されている v2.0 エンドポイントの Web API サンプルを参照してください。

### <a name="web-api-on-behalf-of-flow"></a>Web API の On-Behalf-Of フロー
v2.0 エンドポイントによって保護された Web API から、同じように保護された別の下流の Web API を呼び出す手法は、多くのアーキテクチャで使われています。 このシナリオは、Web API バック エンドを持つネイティブ クライアントでよく見られます。この Web API から Microsoft Online Services のインスタンスや、Azure AD をサポートしている別のカスタム ビルド Web API が呼び出されます。

このようなシナリオは、OAuth 2.0 JSON Web トークン (JWT) ベアラー資格情報許可 (On-Behalf-Of フロー) を使用して作成できます。 ただし、現時点では、v2.0 エンドポイントは On-Behalf-Of フローをサポートしていません。 一般提供版 Azure AD サービスにおけるこのフローの動作については、[GitHub の On-Behalf-Of コード サンプル](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)を参照してください。

## <a name="restrictions-on-app-registrations"></a>アプリの登録に関する制限事項
現在、v2.0 エンドポイントと統合するアプリごとに、新しい [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)にアプリの登録を作成する必要があります。 既存の Azure AD または Microsoft アカウント アプリは、v2.0 エンドポイントと互換性がありません。 アプリケーション登録ポータル以外のポータルに登録されているアプリは、v2.0 エンドポイントと互換性がありません。 今後、既存のアプリケーションを v2.0 アプリとして使用する方法を提供する予定です。 ただし、現時点では、既存のアプリを移行して v2.0 エンドポイントで機能させる方法はありません。

アプリケーション登録ポータルで登録されたアプリは、元の Azure AD 認証エンドポイントでは機能しません。 ただし、アプリケーション登録ポータルで作成されたアプリを使用して、Microsoft アカウント認証エンドポイント `https://login.live.com` と統合することができます。

また、[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)で作成したアプリケーションの登録には、次の注意点があります。

* *サインオン URL* とも呼ばれる**ホームページ** プロパティは、サポートされていません。 ホームページがない場合、Office MyApps パネルにこれらのアプリケーションは表示されません。
* 現時点で、アプリケーション ID ごとに許可されるアプリ シークレットは 2 種類のみです。
* アプリの登録を表示および管理できるのは、1 人の開発者アカウントのみです。 複数の開発者間で共有することはできません。
* 許可されるリダイレクト URI の形式には、いくつかの制限事項があります。 リダイレクト URI の詳細については、次のセクションを参照してください。

## <a name="restrictions-on-redirect-uris"></a>リダイレクト URI に関する制限事項
現在、アプリケーション登録ポータルで登録されるアプリは、限られたリダイレクト URI 値に限定されています。 Web アプリと Web サービスのリダイレクト URI はスキーム `https` で始める必要があり、すべてのリダイレクト URI 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、次の直接 URI のいずれかを含む Web アプリを登録することはできません。

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

登録システムによって、既存のリダイレクト URI の DNS 名全体と、追加するリダイレクト URI の DNS 名が比較されます。 次のいずれかの条件に当てはまる場合、DNS 名を追加する要求は失敗します。  

* 新しいリダイレクト URI の DNS 名全体が、既存のリダイレクト URI の DNS 名と一致しない場合
* 新しいリダイレクト URI の DNS 名全体が、既存のリダイレクト URI のサブドメインではない場合

たとえば、アプリに次のリダイレクト URI が含まれているとします。

`https://login.contoso.com`

次のように、これに追加できます。

`https://login.contoso.com/new`

この場合、DNS は完全に一致します。 または、次を行うことができます。

`https://new.login.contoso.com`

この場合、login.contoso.com の DNS サブドメインを参照しています。 リダイレクト URI として login-east.contoso.com と login-west.contoso.com を持つアプリが必要な場合は、これらのリダイレクト URI を以下の順番で追加する必要があります。

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

後の 2 つのリダイレクト URI を追加できるのは、それらが 1 つ目の contoso.com というリダイレクト URI のサブドメインであるためです。 この制限は、今後のリリースで解消される予定です。

アプリケーション登録ポータルでアプリを登録する方法については、「[v2.0 エンドポイントを使用してアプリケーションを登録する方法](active-directory-v2-app-registration.md)」を参照してください。

## <a name="restrictions-on-services-and-apis"></a>サービスと API に関する制限事項
現在、v2.0 エンドポイントは、アプリケーション登録ポータルに登録されているすべてのアプリのサインインをサポートしています。これらのアプリは、[サポートされる認証フロー](active-directory-v2-flows.md)のリストに含まれています。 ただし、これらのアプリで取得できるのは、ごく限られたリソースの OAuth 2.0 アクセス トークンだけです。 v2.0 エンドポイントは、次に対してのみアクセス トークンを発行します。

* トークンを要求したアプリ。 論理的に複数の異なるコンポーネントまたは階層で構成されている場合にアプリが取得できるのはそれ自身のアクセス トークンとなります。 その実際のシナリオについては、 [こちら](active-directory-appmodel-v2-overview.md#getting-started) のチュートリアルを参照してください。
* Outlook メール、カレンダー、連絡先の REST API (いずれも https://outlook.office.com に存在)。 これらの API にアクセスするアプリの作成方法については、[Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) に関するチュートリアルを参照してください。
* Microsoft Graph API。 [Microsoft Graph](https://graph.microsoft.io) と利用可能なデータについて詳細を確認できます。

現時点では、上記以外のサービスはサポートされていません。 独自のカスタム ビルド Web API やサービスのサポート以外にも、その他の Microsoft Online Services が将来追加される予定です。

## <a name="restrictions-on-libraries-and-sdks"></a>ライブラリと SDK に関する制限事項
現時点では、v2.0 エンドポイントのライブラリ サポートは制限されています。 運用環境のアプリケーションで v2.0 エンドポイントを使用する場合は、次の選択肢があります。

* Web アプリケーションを構築する場合は、Microsoft が一般提供しているサーバー側のミドルウェアを使用して、サインインとトークンの検証を安全に実行できます。 このようなミドルウェアには、OWIN の ASP.NET 用 Open ID Connect ミドルウェアや Node.js 用 Passport プラグインなどがあります。 Microsoft のミドルウェアを使用するコード サンプルについては、[Getting Started (概要)](active-directory-appmodel-v2-overview.md#getting-started) セクションを参照してください。
* 他のプラットフォームやネイティブ アプリケーションとモバイル アプリケーションでは、アプリケーション コードで直接プロトコル メッセージを送受信することで v2.0 エンドポイントと統合できます。 v2.0 の OpenID Connect プロトコルと OAuth プロトコルについては、このような統合の実行に役立つように、[明確に文書化](active-directory-v2-protocols.md)されています。
* オープン ソースの Open ID Connect および OAuth のライブラリを使用して、v2.0 エンドポイントと統合できます。 v2.0 のプロトコルは通常、大幅な変更を加えなくても、多数のオープンソース プロトコル ライブラリと互換性があります。 これらのライブラリが使用可能かどうかは、言語とプラットフォームによって異なります。 [Open ID Connect](http://openid.net/connect/) および [OAuth 2.0](http://oauth.net/2/) の Web サイトでは、一般的な実装のリストを公開しています。 詳細については、「[Azure Active Directory (AD) v2.0 と認証ライブラリ](active-directory-v2-libraries.md)」、および v2.0 エンドポイントでテスト済みのオープンソース クライアント ライブラリとサンプルの一覧を参照してください。

Microsoft は、.NET 専用の [Microsoft 認証ライブラリ (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) の初期プレビューをリリースしました。 このライブラリは .NET クライアントとサーバー アプリケーションで試用いただけますが、プレビュー ライブラリのため一般公開 (GA) 品質のサポートはありません。

## <a name="restrictions-on-protocols"></a>プロトコルに関する制限事項
v2.0 エンドポイントは、Open ID Connect と OAuth 2.0 のみをサポートしています。 ただし、各プロトコルの一部の機能がまだ v2.0 エンドポイントには組み込まれていません。

次の一般的なプロトコルの機能は、現在 v2.0 エンドポイントでは*利用できません*。

* ユーザー側のセッションの終了をアプリに許可する OpenID Connect `end_session_endpoint` パラメーターは、v2.0 エンドポイントでは利用できません。
* v2.0 エンドポイントで発行される ID トークンには、ユーザーのペアワイズ識別子のみが含まれます。 これは、同一ユーザーに対して異なる 2 つのアプリケーションが異なる ID を受け取ることを意味します。 Microsoft Graph `/me` エンドポイントにクエリを実行すると、アプリケーション間で使用可能なユーザーの相関 ID を取得できます。
* ユーザーから電子メールを表示する許可を取得していても、v2.0 エンドポイントが発行する ID トークンにはユーザーの `email` の要求は含まれません。
* v2.0 エンドポイントに OpenID Connect UserInfo エンドポイントは実装されていません。 ただし、このエンドポイントで受け取る可能性のあるすべてのユーザー プロファイル データは、Microsoft Graph `/me` エンドポイントから使用できます。
* v2.0 エンドポイントで ID トークン内のロールまたはグループの要求の発行はサポートされていません。

v2.0 エンドポイントでサポートされているプロトコル機能の範囲について詳しく理解するには、[OpenID Connect および OAuth 2.0 プロトコルに関するリファレンス](active-directory-v2-protocols.md)を参照してください。

## <a name="restrictions-for-work-and-school-accounts"></a>職場および学校のアカウントの制限
いくつかの Microsoft のエンタープライズ ユーザー向けの機能は、v2.0 エンドポイントでまだサポートされていません。 詳細については、以降のセクションを参照してください。

### <a name="device-based-conditional-access-native-and-mobile-apps-and-microsoft-graph"></a>デバイス ベースの条件付きアクセス、ネイティブとモバイルのアプリ、Microsoft Graph
iOS または Android で実行されるネイティブ アプリのようなモバイル アプリケーションとネイティブ アプリケーションのデバイス認証は、v2.0 エンドポイントではまだサポートされていません。 そのため、一部の組織では、ネイティブ アプリケーションが Microsoft Graph を呼び出せない可能性があります。 管理者がアプリケーションのデバイス ベースの条件付きアクセス ポリシーを設定するときに、デバイス認証が必要となります。 v2.0 のエンドポイントでデバイス ベースの条件付きアクセスを行う可能性が最も高いシナリオは、管理者が Outlook API などの Microsoft Graph のリソースにポリシーを設定する場合です。 管理者がこのポリシーを設定し、ネイティブ アプリケーションが Microsoft Graph にトークンを要求した場合、デバイス認証がまだサポートされていないため、要求は最終的に失敗します。 ただし、デバイス ベースのポリシーが構成された場合、Microsoft Graph にトークンを要求する Web アプリケーションはサポートされます。 Web アプリのシナリオでは、デバイス認証はユーザーの Web ブラウザーを介して実行されます。

開発者は、Microsoft Graph のリソースにポリシーが設定されるタイミングについては、ほとんど制御することができません。 設定されるタイミングを認識できない可能性もあります。 職場または学校のユーザー向けにアプリケーションを構築している場合は、v2.0 エンドポイントでデバイス認証がサポートされるまで、 [元の Azure AD エンドポイント](active-directory-developers-guide.md) を使用する必要があります。 詳細については、[Azure AD でのデバイス ベースの条件付きアクセス](../active-directory-conditional-access.md#device-based-conditional-access)に関するセクションを参照してください。

### <a name="windows-integrated-authentication-for-federated-tenants"></a>フェデレーション テナントの統合 Windows 認証
Windows アプリケーションで (元の Azure AD エンドポイントで) Active Directory 認証ライブラリ (ADAL) を使用している場合は、Security Assertion Markup Language (SAML) アサーションの付与と呼ばれる仕組みを活用している可能性があります。 この付与により、フェデレーション Azure AD テナントのユーザーは資格情報を入力せずに、オンプレミスの Active Directory インスタンスで自動的に認証できます。 現在、SAML アサーションの付与は、v2.0 のエンドポイントではサポートされていません。




<!--HONumber=Jan17_HO3-->



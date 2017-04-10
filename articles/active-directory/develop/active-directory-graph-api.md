---
title: Azure Active Directory Graph API | Microsoft Docs
description: "REST API エンドポイントを介して Azure AD にプログラムでアクセスできる Graph API の概要およびクイック スタート ガイドです。"
services: active-directory
documentationcenter: 
author: PatAltimore
manager: mbaldwin
editor: mbaldwin
ms.assetid: 5471ad74-20b3-44df-a2b5-43cde2c0a045
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/07/2017
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f9a5e7a4e25ce5e7f63fe5a09fafeb6b4a8166ad
ms.lasthandoff: 03/21/2017


---
# <a name="azure-active-directory-graph-api"></a>Azure Active Directory Graph API
> [!IMPORTANT]
> Azure Active Directory リソースにアクセスする場合、Azure AD Graph API ではなく [Microsoft Graph](https://graph.microsoft.io/) を使用することを強くお勧めします。 開発作業は現在 Microsoft Graph に集中しており、Azure AD Graph API の追加の機能強化は予定されていません。 Azure AD Graph API の使用が適切なシナリオの数は非常に限られています。詳しくは、Office デベロッパー センターのブログ投稿「[Microsoft Graph or the Azure AD Graph (Microsoft Graph または Azure AD Graph)](https://dev.office.com/blogs/microsoft-graph-or-azure-ad-graph)」をご覧ください。
> 
> 

Azure Active Directory Graph API を使用すると、REST API エンドポイントを介して Azure AD にプログラムによってアクセスできます。 アプリケーションでは、Graph API を使用して、ディレクトリのデータとオブジェクトに対して、作成、読み取り、更新、および削除 (CRUD) の各操作を実行できます。 たとえば、Graph API では、ユーザー オブジェクトへの次のような一般的な操作がサポートされています。

* ディレクトリに新しいユーザーを作成する
* ユーザーの詳細なプロパティ (所属するグループなど) を取得する
* ユーザーのプロパティ (場所や電話番号など) を更新するか、またはユーザーのパスワードを変更する
* ロールベースのアクセスに対応しているかどうか、ユーザーのグループ メンバーシップを確認する
* ユーザーのアカウントを無効にするか、または完全に削除する

ユーザー オブジェクトに加えて、グループやアプリケーションなどのその他のオブジェクトに対しても同様の操作を実行できます。 ディレクトリ上で Graph API を呼び出すには、アプリケーションを Azure AD に登録し、ディレクトリへのアクセスを許可するように構成する必要があります。 通常、これは、ユーザーまたは管理者の同意フローによって実現されます。

Azure Active Directory Graph API の使用を開始するには、[Graph API クイック スタート ガイド](active-directory-graph-api-quickstart.md)または[対話型の Graph API リファレンス ドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)を参照してください。

## <a name="features"></a>Features (機能)
Graph API には、次の機能が用意されています。

* **REST API エンドポイント**: Graph API は、標準の HTTP 要求を使用してアクセスされるエンドポイントで構成された REST ベースのサービスです。 Graph API では、要求と応答のコンテンツの種類として XML または Javascript Object Notation (JSON) をサポートしています。 詳細については、「 [Azure AD Graph REST API リファレンス](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)」を参照してください。
* **Azure AD による認証**: Graph API に対するすべての要求は、その要求の Authorization ヘッダーに JSON Web トークン (JWT) を追加することによって認証する必要があります。 このトークンを取得するには、Azure AD のトークン エンドポイントに対して要求を送信し、有効な資格情報を提供します。 OAuth 2.0 クライアント資格情報フローまたは承認コード付与フローを使用して、Graph を呼び出すためのトークンを取得することができます。 詳細については、「 [Azure AD での OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx)」を参照してください。
* **ロールベースの承認 (RBAC)**: セキュリティ グループは、Graph API 内で RBAC を実行する際に使用されます。 たとえば、ユーザーが特定のリソースへのアクセス権を持っているかどうかを判断する場合、アプリケーションから[グループ メンバーシップの確認 (推移的)](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#FunctionsandactionsongroupsCheckmembershipinaspecificgrouptransitive) 操作を呼び出すことができます。この操作では、true または false が返されます。
* **差分クエリ**: Graph API に対して頻繁にクエリを実行することなく 2 つの期間の間でディレクトリに変更があるかどうかを確認する場合は、差分クエリ要求を実行することができます。 この種の要求は、前回の差分クエリ要求と現在の要求の間で行われた変更のみを返します。 詳細については、「 [Azure AD Graph API の差分クエリ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-differential-query)」を参照してください。
* **ディレクトリ拡張機能**: ディレクトリ オブジェクトの一意のプロパティの読み取りまたは書き込みを行う必要があるアプリケーションを開発している場合は、Graph API を使用して、拡張機能の値を登録して使用できます。 たとえば、アプリケーションがユーザーごとに Skype ID プロパティを必要とする場合は、ディレクトリに新しいプロパティを登録することができ、そのプロパティはすべてのユーザー オブジェクトで使用できるようになります。 詳細については、「 [Azure AD Graph API ディレクトリ スキーマ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)」を参照してください。
* **アクセス許可スコープによる保護**: AAD Graph API は、AAD データへの保護/承認されたアクセスを有効にし、以下のようなさまざまな種類のクライアント アプリをサポートするアクセス許可スコープを公開しています。
  
  * サインインしたユーザー (代理) から認証を使用してデータへの委任アクセスを付与された、ユーザー インターフェイスを持つアプリ
  * サービス/デーモン クライアント (アプリ ロール) などのアプリケーション定義のロール ベースのアクセス制御を使用するアプリ
    
    委任およびアプリ ロールのアクセス許可スコープは、Graph API によって公開されている権限を表し、 [Azure Portal](https://portal.azure.com) のアプリケーション登録のアクセス許可を通じてクライアント アプリケーションによって要求できます。 クライアントは、委任されたアクセス許可のアクセス トークンで受信したスコープ ("scp") 要求と、アプリ ロールのアクセス許可のロール ("role") 要求を調べることで、自身に付与されているアクセス許可スコープを確認できます。 [Azure AD Graph API のアクセス許可スコープ](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)の詳細については、こちらを参照してください。

## <a name="scenarios"></a>シナリオ
Graph API により、多くのアプリケーション シナリオを実現できます。 最も一般的なシナリオを次に示します。

* **基幹業務 (シングル テナント) アプリケーション**: このシナリオでは、エンタープライズ開発者は、Office 365 サブスクリプションを持つ組織に勤めています。 開発者は、Azure AD を操作してユーザーへのライセンスの割り当てなどのタスクを実行する Web アプリケーションを作成します。 このタスクでは、Graph API へのアクセスが必要となるため、開発者は、Azure AD にこのシングル テナント アプリケーションを登録し、Graph API の読み取りおよび書き込みのアクセス許可を構成します。 その後、アプリケーション独自の資格情報または現在サインインしているユーザーの資格情報を使用して、Graph API を呼び出すトークンを取得するように、アプリケーションは構成されます。
* **サービス アプリケーション (マルチテナント) としてのソフトウェア**: このシナリオでは、独立系ソフトウェア ベンダー (ISV) が、Azure AD を使用する他の組織にユーザー管理機能を提供するホスト型のマルチテナント Web アプリケーションを開発しています。 これらの機能にはディレクトリ オブジェクトへのアクセスが必要になるため、アプリケーションは Graph API を呼び出す必要があります。 開発者は、Azure AD にアプリケーションを登録し、Graph API に対する読み取りと書き込みのアクセス許可を必要とするように構成して、他の組織がディレクトリ内のアプリケーションを使用することに同意できるように外部アクセスを有効にします。 別の組織のユーザーが初めてアプリケーションに対する認証を行うとき、アプリケーションが要求しているアクセス許可に関する同意ダイアログが表示されます。  同意すると、アプリケーションには、ユーザーのディレクトリでの Graph API に対する要求されたアクセス許可が付与されます。 同意フレームワークの詳細については、「 [同意フレームワークの概要](active-directory-integrating-applications.md)」を参照してください。

## <a name="see-also"></a>関連項目
[Azure AD Graph API のクイック スタート ガイド](active-directory-graph-api-quickstart.md)

[AD Graph REST のドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)

[Azure Active Directory 開発者ガイド](active-directory-developers-guide.md)



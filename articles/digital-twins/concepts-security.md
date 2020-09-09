---
title: Azure Digital Twins ソリューションのセキュリティ
titleSuffix: Azure Digital Twins
description: Azure Digital Twins によるセキュリティのベスト プラクティスについて説明します。
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d528ff41d8b7098e313e070dfe734960cdc89eaa
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88827530"
---
# <a name="secure-azure-digital-twins"></a>Azure Digital Twins をセキュリティで保護する

Azure Digital Twins では、セキュリティのために、デプロイ内の特定のデータ、リソース、アクションへのアクセスを正確に制御できます。 この処理には、**ロールベースのアクセス制御 (RBAC)** と呼ばれるきめ細かいロールおよびアクセス許可の管理戦略が使用されています。 Azure の RBAC の一般的な原則については、[こちら](../role-based-access-control/overview.md)を参照してください。

Azure Digital Twins では、保存データの暗号化もサポートされています。

## <a name="granting-permissions-with-rbac"></a>RBAC を使用してアクセス許可を付与する

RBAC は、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) との統合を介して Azure Digital Twins に提供されます。

RBAC を使用して、"*セキュリティ プリンシパル*" (ユーザー、グループ、またはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。 セキュリティ プリンシパルは Azure AD によって認証され、応答として OAuth 2.0 トークンを受け取ります。 このトークンは、Azure Digital Twins インスタンスへのアクセス要求を承認するために使用できます。

### <a name="authentication-and-authorization"></a>認証と権限承認

Azure AD では、アクセスは 2 段階のプロセスです。 セキュリティ プリンシパル (ユーザー、グループ、またはアプリケーション) が Azure Digital Twins にアクセスしようとすると、要求は必ず "*認証*" され、"*承認*" されます。 

1. まず、セキュリティ プリンシパルの ID が "*認証*" され、OAuth 2.0 トークンが返されます。
2. 次に、指定されたリソースへのアクセスを "*承認*" するために、トークンが要求の一部として Azure Digital Twins サービスに渡されます。

この認証手順では、実行時のアプリケーション要求に OAuth 2.0 アクセス トークンが含まれている必要があります。 アプリケーションが Azure エンティティ ([Azure Functions](../azure-functions/functions-overview.md) アプリなど) 内で実行されている場合、**マネージド ID** を使用してリソースにアクセスできます。 マネージド ID の詳細については、次のセクションを参照してください。

承認の手順では、セキュリティ プリンシパルに Azure ロールを割り当てる必要があります。 セキュリティ プリンシパルに割り当てられたロールによって、そのプリンシパルが持つアクセス許可が決定されます。 Azure Digital Twins には、Azure Digital Twins リソースの一連のアクセス許可を含む Azure ロールが用意されています。 これらのロールについては、この記事の後半で説明します。

Azure でサポートされているロールとロールの割り当ての詳細については、Azure RBAC ドキュメントの[*各種ロールの概要*](../role-based-access-control/rbac-and-directory-admin-roles.md)に関するページを参照してください。

#### <a name="authentication-with-managed-identities"></a>マネージド ID による認証

[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) は、アプリケーション コードが実行されるデプロイに関連付けられた、セキュリティで保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。

マネージド ID では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure App Service アプリケーション内で実行される Azure Digital Twins クライアント アプリで、SAS ルールとキー、またはその他のアクセス トークンを処理する必要はありません。 クライアント アプリには、Azure Digital Twins 名前空間のエンドポイント アドレスのみが必要です。 アプリが接続されると、Azure Digital Twins によってマネージド エンティティのコンテキストがクライアントにバインドされます。 マネージド ID に関連付けられると、Azure Digital Twins クライアントは承認済みのすべての操作を実行できます。 次に、マネージド エンティティを Azure Digital Twins Azure ロールに関連付けることにより、承認が付与されます (説明は後述します)。

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Authorization:Azure Digital Twins の Azure ロール

Azure には、Azure Digital Twins リソースへのアクセス承認用に、以下の Azure 組み込みロールが用意されています。
* *Azure Digital Twins Owner (プレビュー)* - Azure Digital Twins リソースに対するフル アクセス権を付与するには、このロールを使用します。
* *Azure Digital Twins Reader (プレビュー)* - Azure Digital Twins リソースへの読み取り専用アクセス権を付与するには、このロールを使用します。

> [!TIP]
> *Azure Digital Twins Reader (プレビュー)* ロールでは、リレーションシップの閲覧もサポートされるようになりました。

組み込みロールの定義方法の詳細については、Azure RBAC のドキュメントの[*ロール定義の概要*](../role-based-access-control/role-definitions.md)に関するページを参照してください。 Azure カスタム ロールの作成については、「[*Azure カスタム ロール*](../role-based-access-control/custom-roles.md)」を参照してください。

ロールを割り当てるには、次の 2 つの方法があります。
* Azure portal で Azure Digital Twins のアクセス制御 (IAM) ペインを使用する ([*Azure RBAC と Azure portal を使用したロールの割り当ての追加または削除*](../role-based-access-control/role-assignments-portal.md)に関するページを参照してください)
* CLI コマンドを使用してロールを追加または削除する

この詳細な実行手順については、[*Azure Digital Twins チュートリアルのエンド ツー エンドのソリューションの接続*](tutorial-end-to-end.md)に関するページを参照してください。

### <a name="permission-scopes"></a>アクセス許可のスコープ

セキュリティ プリンシパルに Azure ロールを割り当てる前に、セキュリティ プリンシパルに必要なアクセスのスコープを決定します。 ベスト プラクティスとしては、できるだけ狭いスコープのみを付与するのが最善の方法です。

次の一覧は、Azure Digital Twins リソースへのアクセスのスコープを指定できるレベルについて説明したものです。
* モデル:このリソースのアクションを使用すると、Azure Digital Twins にアップロードされた[モデル](concepts-models.md)の制御を指示できます。
* Digital Twins グラフのクエリこのリソースのアクションによって、Azure Digital Twins グラフ内のデジタル ツインに対して[クエリ操作](concepts-query-language.md)を実行する機能が決まります。
* デジタル ツイン:このリソースのアクションを使用すると、ツイン グラフ内の[デジタル ツイン](concepts-twins-graph.md)に対する CRUD 操作を制御できます。
* デジタル ツインの関係:このリソースのアクションによって、ツイン グラフ内のデジタル ツイン間の[関係](concepts-twins-graph.md)に対する CRUD 操作の制御が定義されます。
* イベント ルート:このリソースのアクションによって、Azure Digital Twins から [Event Hub](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、または [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) などのエンドポイント サービスに[イベントをルーティング](concepts-route-events.md)するアクセス許可が決まります。

### <a name="troubleshooting-permissions"></a>アクセス許可のトラブルシューティング

ユーザーが自分のロールで許可されていないアクションの実行を試みた場合、`403 (Forbidden)` と書かれた、サービス要求からのエラーが表示されることがあります。 詳細とトラブルシューティングの手順については、"[*Azure Digital Twins 要求が失敗しました: 状態 403 (許可されていません)* ](troubleshoot-error-403.md)" といったエラーのトラブルシューティングに関するページを参照してください。

## <a name="encryption-of-data-at-rest"></a>保存データの暗号化

Azure Digital Twins によって、データ センターに書き込まれる場合の保存データおよび転送中データが暗号化され、アクセス時に復号化されます。 この暗号化は、Microsoft が管理する暗号化キーを使用して行われます。

## <a name="next-steps"></a>次のステップ

* [*インスタンスと認証の設定方法*](how-to-set-up-instance-scripted.md)に関するページで取り上げられている、動作中の概念について確認します。

* クライアント アプリケーション コードからこれらの概念を操作する方法を、[*アプリ認証コードを作成する方法*](how-to-authenticate-client.md)に関するページで確認します。

* 詳細については、「[Azure リソースの RBAC](../role-based-access-control/overview.md)」を参照してください。

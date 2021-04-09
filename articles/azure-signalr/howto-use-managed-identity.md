---
title: Azure SignalR Service のマネージド ID
description: Azure SignalR Service でのマネージド ID のしくみと、サーバーレス シナリオでマネージド ID を使用する方法について説明します。
author: chenyl
ms.service: signalr
ms.topic: article
ms.date: 06/8/2020
ms.author: chenyl
ms.openlocfilehash: dee15977318eda7bcd0b1950286bb33f621221dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731586"
---
# <a name="managed-identities-for-azure-signalr-service"></a>Azure SignalR Service のマネージド ID

この記事では、Azure SignalR Service のマネージド ID を作成する方法と、サーバーレス シナリオでこの ID を使用する方法について説明します。

> [!Important] 
> Azure SignalR Service では、1 つのマネージド ID のみをサポートできます。 つまり、システム割り当て ID とユーザー割り当て ID のどちらかを追加できます。 

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する

Azure portal でマネージド ID を設定するには、まず Azure SignalR Service インスタンスを作成してから、この機能を有効にします。

1. 通常どおり、ポータルで Azure SignalR Service インスタンスを作成します。 ポータルでこのインスタンスを参照します。

2. **[ID]** を選択します。

4. **[システム割り当て済み]** タブで、 **[状態]** を **[オン]** に切り替えます。 **[保存]** を選択します。

    :::image type="content" source="media/signalr-howto-use-managed-identity/system-identity-portal.png" alt-text="ポータルでシステム割り当て ID を追加する":::

## <a name="add-a-user-assigned-identity"></a>ユーザー割り当て ID を追加する

ユーザー割り当て ID を持つ Azure SignalR Service インスタンスを作成するには、ID を作成してから、そのリソース ID をサービスに追加する必要があります。

1. [以下の手順](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)に従って、ユーザー割り当てマネージド ID リソースを作成します。

2. 通常どおり、ポータルで Azure SignalR Service インスタンスを作成します。 ポータルでこのインスタンスを参照します。

3. **[ID]** を選択します。

4. **[ユーザー割り当て済み]** タブで、 **[追加]** を選択します。

5. 先ほど作成した ID を検索して選択します。 **[追加]** を選択します。

    :::image type="content" source="media/signalr-howto-use-managed-identity/user-identity-portal.png" alt-text="ポータルでユーザー割り当て ID を追加する":::

## <a name="use-a-managed-identity-in-serverless-scenarios"></a>サーバーレス シナリオでマネージド ID を使用する

Azure SignalR Service は完全に管理されたサービスであるため、マネージド ID を使用してトークンを手動で取得することはできません。 代わりに、Azure SignalR Service では、アクセス トークンを取得するために設定したマネージド ID が使用されます。 サーバーレス シナリオでは、その後、サービスによってアップストリーム要求の `Authorization` ヘッダーにアクセス トークンが設定されます。

### <a name="enable-managed-identity-authentication-in-upstream-settings"></a>アップストリーム設定でマネージド ID 認証を有効にする

1. システム割り当て ID またはユーザー割り当て ID を追加します。

2. アップストリーム設定を 1 つ追加し、次に示すように、アスタリスクをクリックして詳細ページを表示します。
    :::image type="content" source="media/signalr-howto-use-managed-identity/pre-msi-settings.png" alt-text="pre-msi-setting":::
    
    :::image type="content" source="media/signalr-howto-use-managed-identity/msi-settings.png" alt-text="msi-setting":::

3. マネージド ID 認証設定の **[リソース]** で、ターゲット リソースを指定できます。 リソースは取得したアクセス トークンの `aud` 要求になります。これは、アップストリーム エンドポイントの検証の一部として使用できます。 リソースは次のいずれかになります。
    - Empty
    - サービス プリンシパルのアプリケーション (クライアント) ID
    - サービス プリンシパルのアプリケーション ID URI
    - [Azure サービスのリソース ID](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

    > [!NOTE]
    > サービスでアクセス トークンを自分で検証する場合は、任意のリソース形式を選択できます。 **[認証]** 設定の **[リソース]** の値と検証との間に整合性があることを確認してください。 データ プレーンに対して Azure ロールベースのアクセス制御 (Azure RBAC) を使用する場合は、サービス プロバイダーが要求するリソースを使用する必要があります。

### <a name="validate-access-tokens"></a>アクセス トークンを検証する

`Authorization` ヘッダー内のトークンは、[Microsoft ID プラットフォームのアクセス トークン](../active-directory/develop/access-tokens.md#validating-tokens)です。

アクセス トークンを検証するには、アプリで対象ユーザーと署名トークンも検証する必要があります。 これらの検証は、OpenID 探索ドキュメント内の値に対して行ってください。 たとえば、[テナントに依存しないバージョンのドキュメント](https://login.microsoftonline.com/common/.well-known/openid-configuration)を確認してください。

Azure Active Directory (Azure AD) ミドルウェアには、アクセス トークンを検証するための機能が組み込まれています。 [サンプル](../active-directory/develop/sample-v2-code.md)を参照して、任意の言語で検索することができます。

トークンの検証を処理する方法を示すライブラリとコード サンプルが用意されています。 また、JSON Web Token (JWT) 検証に使用できるオープンソースのパートナー ライブラリもいくつかあります。 ほとんどすべてのプラットフォームと言語に、少なくとも 1 つのオプションがあります。 Azure AD 認証ライブラリとコード サンプルの詳細については、「[Microsoft ID プラットフォームの認証ライブラリ](../active-directory/develop/reference-v2-libraries.md)」を参照してください。

#### <a name="authentication-in-function-app"></a>関数アプリでの認証

関数アプリでのアクセス トークンの検証は、コードを使用せず、簡単かつ効率的に設定できます。

1. **[認証/承認]** ページで、 **[App Service 認証]** を **[オン]** に切り替えます。

2. **[要求が認証されない場合に実行するアクション]** で、 **[Azure Active Directory でのログイン]** を選択します。

3. [認証プロバイダー] で、 **[Azure Active Directory]** をクリックします。

4. 新しいページで、 **[簡易]** を選択し、 **[新しい AD アプリを作成する]** を選択して、 **[OK]** をクリックします :::image type="content" source="media/signalr-howto-use-managed-identity/function-aad.png" alt-text="Function Aad":::

5. SignalR Service に移動し、[手順](howto-use-managed-identity.md#add-a-system-assigned-identity)に従って、システム割り当て ID またはユーザー割り当て ID を追加します。

6. SignalR Service で **[アップストリームの設定]** にアクセスし、 **[マネージド ID の使用]** を選択して、 **[既存のアプリケーションから選択]** を選択します。 以前に作成したアプリケーションを選択します。

これらの設定の後、Function App では、ヘッダーにアクセス トークンがない要求が拒否されるようになります。

## <a name="use-a-managed-identity-for-key-vault-reference"></a>Key Vault 参照にマネージド ID を使用する

SignalR Service では、Key Vault にアクセスし、マネージド ID を使用してシークレットを取得することができます。

1. Azure SignalR Service 用のシステム割り当て ID またはユーザー割り当て ID を追加します。

2. Key Vault のアクセス ポリシーで、マネージド ID に対する読み取りアクセス許可をシークレットに付与します。 [Azure portal を使用した Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)に関する説明を参照してください

現在、この機能は次のシナリオで使用できます。

- [アップストリーム URL パターンでのシークレット参照](./concept-upstream.md#key-vault-secret-reference-in-url-template-settings)


## <a name="next-steps"></a>次のステップ

- [Azure SignalR Service を使用した Azure Functions の開発と構成](signalr-concept-serverless-development-config.md)
---
title: デーモン アプリケーションをセキュリティで保護する方法
titleSuffix: Azure Maps
description: Azure portal を使用して、信頼されたデーモン アプリケーションを構成するための認証を管理します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895649"
---
# <a name="secure-a-daemon-application"></a>デーモン アプリケーションをセキュリティで保護する

以下のガイドは、信頼されており、セキュリティで保護されている環境でホストされるバックグラウンド プロセス、タイマー、ジョブを対象としています。 例としては、Azure Web Jobs、Azure Function App、Windows サービス、およびその他の信頼性の高いバックグラウンド サービスなどがあります。

> [!Tip]
> Microsoft では、運用アプリケーション用に Azure Active Directory (Azure AD) および Azure ロールベースのアクセス制御 (Azure RBAC) を実装することをお勧めします。 概念の概要については、[Azure Maps 認証](./azure-maps-authentication.md)に関するページを参照してください。

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>シナリオ:共有キー認証

Azure Maps アカウントの作成後、主キーと 2 次キーが生成されます。 [共有キー認証を使用して Azure Maps を呼び出す](./azure-maps-authentication.md#shared-key-authentication)場合は、サブスクリプション キーとして主キーを使用することをお勧めします。 2 次キーは、キー変更のローリングなどのシナリオで使用できます。 詳細については、「[Azure Maps による認証](./azure-maps-authentication.md)」をご覧ください。

### <a name="securely-store-shared-key"></a>共有キーを安全に格納する

主および 2 次キーを使用すると、Maps アカウントのすべての API に対して認証を行うことができます。 アプリケーションでは、キーを Azure Key Vault などのセキュリティで保護されたストアに格納する必要があります。 アプリケーションの構成で共有キーをプレーン テキストで格納しないようにするには、アプリケーションで共有キーを Azure Key Vault シークレットとして取得する必要があります。 Azure Key Vault の構成方法については、[Azure Key Vault 開発者ガイド](../key-vault/general/developers-guide.md)に関するページを参照してください。

以下の手順では、このプロセスの概要を示します。

1. Azure Key Vault を作成します。
2. アプリの登録またはマネージド ID を作成して、Azure AD サービス プリンシパルを作成します。作成されたプリンシパルでは、Azure Key Vault へのアクセスを担当します。
3. サービス プリンシパルに、Azure キー シークレット `Get` アクセス許可へのアクセス権を割り当てます。
4. 開発者に対して、シークレット `Set` アクセス許可へのアクセス権を一時的に割り当てます。
5. Key Vault シークレットに共有キーを設定し、デーモン アプリケーション用の構成としてシークレット ID を参照し、シークレット `Set` アクセス許可を削除します。
6. デーモン アプリケーションで Azure AD 認証を実装し、Azure Key Vault から共有キー シークレットを取得します。
7. 共有キーを使用して、Azure Maps REST API 要求を作成します。

> [!Tip]
> アプリが Azure 環境でホストされている場合、マネージド ID を実装し、Azure Key Vault に対して認証するためのシークレットの管理のコストと複雑さを軽減する必要があります。 Azure Key Vault の[マネージド ID を使用して接続するためのチュートリアル](../key-vault/general/tutorial-net-create-vault-azure-web-app.md)に関するページを参照してください。

デーモン アプリケーションでは、セキュリティで保護されたストレージからの共有キーの取得を担当します。 Azure Key Vault を使用する実装では、シークレットにアクセスするために Azure AD による認証が必要です。 代わりに、共有キー認証を使用する場合に増す複雑さと運用要件の結果として、Azure Maps に対する直接の Azure AD 認証をお勧めします。

> [!IMPORTANT]
> キーの再生成を簡素化するために、アプリケーションでは一度に 1 つのキーを使用することをお勧めします。 その後、アプリケーションでは使用されていないキーを再生成し、Azure Key Vault などのセキュリティで保護されたシークレット ストアに再生成された新しいキーをデプロイできます。

## <a name="scenario-azure-ad-role-based-access-control"></a>シナリオ:Azure AD ロールベースのアクセス制御

Azure Maps アカウントが作成されると、Azure portal の認証の詳細ページに Azure Maps の `x-ms-client-id` 値が示されます。 この値は、REST API 要求に使用されるアカウントを表します。 この値は、アプリケーション構成に格納し、HTTP 要求を行う前に取得する必要があります。 このシナリオの目的は、デーモン アプリケーションで Azure AD に対して認証し、Azure Maps REST API を呼び出せるようにすることです。

> [!Tip]
> マネージド ID コンポーネントの利点が得られるように、Azure Virtual Machines、Virtual Machine Scale Sets、または App Services でホストすることをお勧めします。

### <a name="daemon-hosted-on-azure-resources"></a>Azure リソースでホストされているデーモン

Azure リソースで実行する場合は、低コストで最小限の資格情報管理作業を可能にするために Azure マネージド ID を構成します。 

マネージド ID へのアプリケーション アクセスを有効にする場合は、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。

マネージド ID の利点:

* Azure システムで管理される X509 証明書の公開キー暗号化認証。
* クライアント シークレットではなく、X509 証明書を使用する Azure AD セキュリティ。
* Azure によって、マネージド ID リソースに関するすべての証明書が管理および更新される。
* Azure Key Vault のようなセキュリティで保護されたシークレット ストア サービスの必要性をなくすことで簡素化された資格情報の運用管理。 

### <a name="daemon-hosted-on-non-azure-resources"></a>Azure 以外のリソースでホストされているデーモン

Azure 以外の環境で実行する場合、マネージド ID は使用できません。 そのため、デーモン アプリケーションの Azure AD アプリケーション登録を使用して、サービス プリンシパルを構成する必要があります。

1. Azure portal の Azure サービスの一覧で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新規登録]** の順に選択します。  

    > [!div class="mx-imgBorder"]
    > ![アプリの登録](./media/how-to-manage-authentication/app-registration.png)

2. アプリを既に登録している場合は、次の手順に進みます。 アプリを登録していない場合は、 **[名前]** を入力し、 **[Support account type] (サポートされているアカウントの種類)** を選択して、 **[登録]** を選択します。  

    > [!div class="mx-imgBorder"]
    > ![アプリの登録の詳細](./media/how-to-manage-authentication/app-create.png)

3. 委任された API アクセス許可を Azure Maps に割り当てるには、アプリケーションに移動します。 次に、 **[アプリの登録]** で、 **[API のアクセス許可]**  >  **[アクセス許可の追加]** の順に選択します。 **[所属する組織で使用している API]** で、「**Azure Maps**」を検索して選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を追加する](./media/how-to-manage-authentication/app-permissions.png)

4. **[Access Azure Maps] (Azure Maps へのアクセス)** の横にあるチェック ボックスをオンにしてから、 **[アクセス許可の追加]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![アプリの API アクセス許可を選択する](./media/how-to-manage-authentication/select-app-permissions.png)

5. 次の手順を完了し、クライアント シークレットを作成するか、証明書を構成します。

    * お使いのアプリケーション上でサーバー認証またはアプリケーション認証を使用する場合は、アプリの登録ページで、 **[証明書とシークレット]** に移動します。 次に、公開キー証明書をアップロードするか、 **[新しいクライアント シークレット]** を選択してパスワードを作成します。

        > [!div class="mx-imgBorder"]
        > ![クライアント シークレットを作成する](./media/how-to-manage-authentication/app-keys.png)

    * **[追加]** を選択した後、シークレットをコピーし、Azure Key Vault などのサービスに安全に格納します。 証明書またはシークレットを安全に格納する場合は、[Azure Key Vault 開発者ガイド](../key-vault/general/developers-guide.md) に関するページをご確認ください。 Azure AD からトークンを取得するには、このシークレットを使用します。

        > [!div class="mx-imgBorder"]
        > ![クライアント シークレットを追加する](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>デーモン アプリケーションのロールベースのアクセス権を Azure Maps に付与する

"*Azure ロールベースのアクセス制御 (Azure RBAC)* " を付与するには、作成されたマネージ ID またはサービス プリンシパルを、1 つまたは複数の Azure Maps ロールの定義に割り当てます。 Azure Maps に使用できる Azure ロールの定義を表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。 これらの Azure Maps ロールが、アクセス権を付与できるロールです。

> [!div class="mx-imgBorder"]
> ![使用可能なロールを表示する](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure RBAC を使用してアクセス権を付与する](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** タブで、ロールの割り当てを **追加** します。 

    > [!div class="mx-imgBorder"]
    > ![[追加] が選択されているロールの割り当てを示すスクリーンショット。](./media/how-to-manage-authentication/add-role-assignment.png)

3. **[Azure Maps データ閲覧者]** や **[Azure Maps データ共同作成者]** などの組み込みの Azure Maps ロールの定義を選択します。 **[アクセスの割り当て先]** で、 **[Azure AD のユーザー、グループ、またはサービス プリンシパル]** 、または **[ユーザー割り当てマネージド ID]**  /  **[システム割り当てマネージド ID]** のマネージド ID を選択します。 プリンシパルを選択します。 次に、 **[保存]** を選択します。

    > [!div class="mx-imgBorder"]
    > ![ロールの割り当てを追加する方法](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. ロールの割り当てタブで、ロールの割り当てが適用されたことを確認できます。

## <a name="request-token-with-managed-identity"></a>マネージド ID を使用してトークンを要求する

ホスト リソース用にマネージド ID が構成されたら、Azure SDK または REST API を使用して Azure Maps のトークンを取得します。詳細については、[アクセス トークンの取得](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関するページを参照してください。 このガイドに従って、REST API 要求で使用できるアクセス トークンが返されることが想定されます。

## <a name="request-token-with-application-registration"></a>アプリケーションの登録を使用してトークンを要求する

アプリを登録して Azure Maps と関連付けると、アクセス トークンを要求することができます。

* Azure リソース ID `https://atlas.microsoft.com/`
* Azure AD App ID (Azure AD アプリ ID)
* Azure AD Tenant ID (Azure AD テナント ID)
* Azure AD アプリの登録クライアント シークレット

要求:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

応答:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

詳細な例については、[Azure AD の認証シナリオ](../active-directory/develop/authentication-vs-authorization.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure Maps アカウントにおける API 使用状況メトリックを確認します。
> [!div class="nextstepaction"]
> [使用状況メトリックを表示する](how-to-view-api-usage.md)

Azure AD と Azure Maps を統合する方法を示すサンプルを確認します。
> [!div class="nextstepaction"]
> [Azure Maps のサンプル](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
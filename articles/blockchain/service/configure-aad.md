---
title: Azure Active Directory アクセスを構成する - Azure Blockchain Service
description: Azure Active Directory アクセスで Azure Blockchain Service を構成する方法
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85208775"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Azure Blockchain Service 用の Azure Active Directory アクセスを構成する方法

この記事では、Azure Active Directory (Azure AD) のユーザー、グループ、またはアプリケーションの ID を使用して、Azure Blockchain Service へのアクセスと接続を許可する方法について説明します。

Azure AD ではクラウドベースの ID 管理が提供され、企業全体で 1 つの ID を使用して、Azure 内のアプリケーションにアクセスできます。 Azure Blockchain Service は Azure AD と統合されており、ID フェデレーション、シングル サインオン、多要素認証などの利点が提供されます。

## <a name="prerequisites"></a>前提条件

* [Azure portal を使用してブロックチェーン メンバーを作成します](create-member.md)

## <a name="grant-access"></a>アクセス権の付与

メンバー レベルとノード レベルの両方で、アクセスを許可することができます。 メンバー レベルでアクセス権を付与すると、そのメンバーの下にあるすべてのノードへのアクセスが許可されます。

### <a name="grant-member-level-access"></a>メンバー レベルのアクセスを許可する

メンバー レベルでアクセス許可を付与するには。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[アクセス制御 (IAM)] > [追加] > [ロールの割り当ての追加]** に移動します。
1. **[ブロックチェーン メンバー ノード アクセス (プレビュー)]** ロールを選択し、アクセスを許可する Azure AD ID オブジェクトを追加します。 Azure AD ID オブジェクトは次のいずれかです。

    | Azure AD オブジェクト | 例 |
    |-----------------|---------|
    | Azure AD ユーザー   | `kim@contoso.onmicrosoft.com` |
    | Azure AD グループ  | `sales@contoso.onmicrosoft.com` |
    | アプリケーション ID  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![ロールの割り当ての追加](./media/configure-aad/add-role-assignment.png)

1. **[保存]** を選択します。

### <a name="grant-node-level-access"></a>ノード レベルのアクセスを許可する

ノードのセキュリティに移動し、アクセスを許可するノードの名前をクリックすることによって、ノード レベルのアクセスを許可できます。

[ブロックチェーン メンバー ノード アクセス (プレビュー)] ロールを選択し、アクセスを許可する Azure AD ID オブジェクトを追加します。

詳細については、「[Azure Blockchain Service のトランザクション ノードを構成する](configure-transaction-nodes.md#azure-active-directory-access-control)」を参照してください。

## <a name="connect-using-azure-blockchain-connector"></a>Azure Blockchain Connector を使用して接続する

[GitHub から Azure Blockchain Connector](https://github.com/Microsoft/azure-blockchain-connector/) をダウンロードまたは複製します。

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

**readme** のクイック スタート セクションに従って、ソース コードからコネクタをビルドします。

### <a name="connect-using-an-azure-ad-user-account"></a>Azure AD ユーザー アカウントを使用して接続する

1. Azure AD ユーザー アカウントを使用して認証を行うには、次のコマンドを実行します。 \<myAADDirectory\> を Azure AD ドメインに置き換えます。 たとえば、「 `yourdomain.onmicrosoft.com` 」のように入力します。

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD で資格情報の指定が求められます。
1. ユーザー名とパスワードを使用してサインインします。
1. 認証が成功すると、お使いのローカル プロキシがブロックチェーン ノードに接続します。 ローカル エンドポイントに Geth クライアントをアタッチできるようになります。

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>アプリケーション ID を使用して接続する

多くのアプリケーションでは、Azure AD のユーザー アカウントではなく、アプリケーション ID を使用して Azure AD で認証が行われます。

アプリケーション ID を使用してノードに接続するには、**aadauthcode** を **aadclient** に置き換えます。

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| パラメーター | 説明 |
|-----------|-------------|
| tenant-id | Azure AD ドメイン (例: `yourdomain.onmicrosoft.com`)
| client-id | Azure AD に登録されているアプリケーションのクライアント ID
| client-secret | Azure AD に登録されているアプリケーションのクライアント シークレット

Azure AD にアプリケーションを登録する方法について詳しくは、次の記事をご覧ください。[方法: リソースにアクセスできる Azure AD アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>モバイル デバイスまたはテキスト ブラウザーを接続する

Azure AD 認証のポップアップ表示が不可能なモバイル デバイスまたはテキスト ベースのブラウザーでは、Azure AD でワンタイム パスコードが生成されます。 パスコードをコピーし、別の環境で Azure AD 認証を続けることができます。

パスコードを生成するには、**aadauthcode** を **aaddevice** に置き換えます。 \<myAADDirectory\> を Azure AD ドメインに置き換えます。 たとえば、「 `yourdomain.onmicrosoft.com` 」のように入力します。

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>次のステップ

Azure Blockchain Service サービスでのデータのセキュリティの詳細については、「[Azure Blockchain Service のセキュリティ](data-security.md)」を参照してください。
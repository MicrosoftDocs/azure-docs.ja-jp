---
title: Azure Key Vault に対する認証
description: Azure Key Vault に対して認証を行う方法について説明します
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 1ef5b2229aadc4be46361a7319351a1f27b28b63
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378978"
---
# <a name="authenticate-to-azure-key-vault"></a>Azure Key Vault に対する認証

Azure Key Vault を使用すると、セキュリティで保護された一元的なクラウド リポジトリにシークレットを格納して、その配布を制御できます。これにより、アプリケーションで資格情報を保存する必要がなくなります。 アプリケーションでそれらのシークレットにアクセスするためには、実行時に Key Vault で認証を行うことだけが必要です。

## <a name="app-identity-and-service-principals"></a>アプリの ID とサービス プリンシパル

Key Vault による認証は、特定の**セキュリティ プリンシパル**の ID の認証を行う [Azure Active Directory (Azure AD)](/azure/active-directory/fundamentals/active-directory-whatis) と連携して機能します。

セキュリティ プリンシパルは、Azure リソースへのアクセスを要求するユーザー、グループ、サービス、またはアプリケーションを表すオブジェクトです。 すべてのセキュリティ プリンシパルには、Azure によって一意の**オブジェクト ID** が割り当てられます。

* **ユーザー** セキュリティ プリンシパルでは、Azure Active Directory 内にプロファイルを持つ個人が示されます。

* **グループ** セキュリティ プリンシパルでは、Azure Active Directory 内に作成されたユーザーのセットが示されます。 グループに割り当てられたすべてのロールまたはアクセス許可は、グループ内のすべてのユーザーに付与されます。

* **サービス プリンシパル**は、アプリケーションまたはサービス、つまりユーザーまたはグループではなくコードを示すセキュリティ プリンシパルの一種です。 サービス プリンシパルのオブジェクト ID は**クライアント ID** と呼ばれ、ユーザー名のように機能します。 サービス プリンシパルの**クライアント シークレット**は、パスワードのように機能します。

アプリケーションの場合、サービス プリンシパルを取得するには次の 2 つの方法があります。

* 推奨: アプリケーションに対してシステム割り当ての**マネージド ID** を有効にします。

    Azure では、マネージ ID を使用することにより、アプリケーションのサービス プリンシパルが内部的に管理され、他の Azure サービスでのアプリケーションの認証が自動的に行われます。 マネージド ID は、さまざまなサービスにデプロイされたアプリケーションで使用できます。

    詳細については、[マネージド ID の概要](/azure/active-directory/managed-identities-azure-resources/overview)に関する記事を参照してください。 また、[マネージド ID がサポートされている Azure サービス](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)に関する記事も参照してください。それには、特定のサービス (App Service、Azure Functions、Virtual Machines など) でマネージド ID を有効にする方法について説明されている記事へのリンクが含まれます。

* マネージド ID を使用できない場合は、Azure AD テナントにアプリケーションを**登録**します。詳細については、[クイックスタート: Azure ID プラットフォームでのアプリケーションの登録](/azure/active-directory/develop/quickstart-register-app)に関する記事を参照してください。 また、登録によって、すべてのテナントでそのアプリを示す 2 つ目のアプリケーション オブジェクトも作成されます。

## <a name="authorize-a-service-principal-to-access-key-vault"></a>サービス プリンシパルが Key Vault にアクセスすることを承認する

Key Vault は、2 つの異なる承認レベルで動作します。

- **アクセス ポリシー**では、ユーザー、グループ、またはサービス プリンシパルに対して既存の Key Vault リソースの "*内部にある*" シークレット、キー、証明書へのアクセスが承認されるかどうかが制御されます("データ プレーン" 操作とも呼ばれます)。 通常、アクセス ポリシーは、ユーザー、グループ、アプリケーションに対して付与されます。

    アクセス ポリシーを割り当てる方法については、次の記事を参照してください。

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **ロールのアクセス許可**では、ユーザー、グループ、またはサービス プリンシパルに対して Key Vault リソースの作成、削除、その他の管理が承認されるかどうかが制御されます("管理プレーン" 操作とも呼ばれます)。 このようなロールは、ほとんどの場合、管理者にのみ付与されます。
 
    ロールの割り当てと管理については、次の記事を参照してください。

    - [Azure Portal](/azure/role-based-access-control/role-assignments-portal)
    - [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
    - [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)

    現在、Key Vault では、Key Vault リソースに対する管理操作を許可する[共同作成者](/azure/role-based-access-control/built-in-roles#key-vault-contributor)ロールがサポートされています。 他の多くのロールは、現在プレビューの段階にあります。 「[Azure カスタム ロール](/azure/role-based-access-control/custom-roles)」で説明されているように、カスタム ロールを作成することもできます。

    ロールに関する一般的な情報については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](/azure/role-based-access-control/overview)」を参照してください。


> [!IMPORTANT]
> セキュリティを最強にするには、常に最小特権の原則に従い、必要なアクセス ポリシーとロールのうち最も限定的なものだけを付与します。 
    
## <a name="configure-the-key-vault-firewall"></a>Key Vault ファイアウォールを構成する

Key Vault では、パブリック IP アドレスを使用してリソースにアクセスすることが既定で許可されます。 セキュリティを強化するため、特定の IP 範囲、サービス エンドポイント、仮想ネットワーク、またはプライベート エンドポイントにアクセスを制限することもできます。

詳細については、「[ファイアウォールの向こう側にある Azure Key Vault へのアクセス](/azure/key-vault/general/access-behind-firewall)」を参照してください。


## <a name="the-key-vault-authentication-flow"></a>Key Vault の認証フロー

1. サービス プリンシパルによって Azure AD での認証が要求されます。次はその例です。
    * ユーザーが、ユーザー名とパスワードを使用して Azure portal にログインします。
    * アプリケーションが、Azure REST API を呼び出して、クライアント ID、シークレット、またはクライアント証明書を提示します。
    * マネージド ID を持つ仮想マシンなどの Azure リソースが、[Azure Instance Metadata Service (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) の REST エンドポイントに接続してアクセス トークンを取得します。

1. Azure AD に対する認証が成功すると、サービス プリンシパルに OAuth トークンが付与されます。

1. サービス プリンシパルは、Key Vault のエンドポイント (URI) を通して Key Vault REST API を呼び出します。

1. Key Vault ファイアウォールにより、次の条件がチェックされます。 いずれかの条件が満たされた場合、呼び出しは許可されます。 それ以外の場合、呼び出しはブロックされ、不許可の応答が返されます。

    * ファイアウォールは無効になっており、Key Vault のパブリック エンドポイントにパブリック インターネットから到達できます。
    * 呼び出し元は [Key Vault の信頼できるサービス](/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)であり、ファイアウォールをバイパスすることができます。
    * 呼び出し元は、IP アドレス、仮想ネットワーク、またはサービス エンドポイントによってファイアウォールのリストに登録されています。
    * 呼び出し元は、構成されたプライベート リンク接続を通して Key Vault に到達できます。    

1. ファイアウォールで呼び出しが許可された場合、Key Vault によって Azure AD が呼び出されて、サービス プリンシパルのアクセス トークンの検証が行われます。

1. Key Vault により、要求された操作に必要なアクセス ポリシーをサービス プリンシパルが持っているかどうかが確認されます。 そうでない場合、Key Vault からは禁止応答が返されます。

1. Key Vault により、要求された操作が実行されて、結果が返されます。

次の図は、アプリケーションが Key Vault の "シークレットの取得" API を呼び出すプロセスを示したものです。

![Azure Key Vault の認証フロー](../media/authentication/authentication-flow.png)

## <a name="code-examples"></a>コード例

次の表では、アプリケーションのコードで特定の言語の Azure SDK ライブラリを使用して Key Vault を操作する方法が示されているさまざまな記事へのリンクを示します。 Azure CLI や Azure portal などの他のインターフェイスも、便利なように示されています。

| Key Vault シークレット | Key Vault キー | Key Vault 証明書 |
|  --- | --- | --- |
| [Python](/azure/key-vault/secrets/quick-create-python) | [Python](/azure/key-vault/keys/quick-create-python) | [Python](/azure/key-vault/certificates/quick-create-python) | 
| [.NET (SDK v4)](/azure/key-vault/secrets/quick-create-net) | -- | -- |
| [.NET (SDK v3)](/azure/key-vault/secrets/quick-create-net-v3) | -- | -- |
| [Java](/azure/key-vault/secrets/quick-create-java) | -- | -- |
| [JavaScript](/azure/key-vault/secrets/quick-create-node) | -- | -- | 
| | | |
| [Azure Portal](/azure/key-vault/secrets/quick-create-portal) | [Azure Portal](/azure/key-vault/keys/quick-create-portal) | [Azure Portal](/azure/key-vault/certificates/quick-create-portal) |
| [Azure CLI](/azure/key-vault/secrets/quick-create-cli) | [Azure CLI](/azure/key-vault/keys/quick-create-cli) | [Azure CLI](/azure/key-vault/certificates/quick-create-cli) |
| [Azure PowerShell](/azure/key-vault/secrets/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/keys/quick-create-powershell) | [Azure PowerShell](/azure/key-vault/certificates/quick-create-powershell) |
| [ARM テンプレート](/azure/key-vault/secrets/quick-create-net) | -- | -- |

## <a name="next-steps"></a>次の手順

- [Key Vault のアクセス ポリシーのトラブルシューティング](troubleshooting-access-issues.md)
- [Key Vault REST API のエラー コード](rest-error-codes.md)
- [Key Vault 開発者ガイド](developers-guide.md)
- [Azure のロールベースのアクセス制御 (RBAC) とは](/azure/role-based-access-control/overview)

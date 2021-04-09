---
title: Azure Key Vault に対する認証
description: Azure Key Vault に対して認証を行う方法について説明します
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 08/27/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: ed83d52d19df126ef9b0e68f984f88f5dfd40c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492942"
---
# <a name="authenticate-to-azure-key-vault"></a>Azure Key Vault に対する認証

Azure Key Vault を使用すると、セキュリティで保護された一元的なクラウド リポジトリにシークレットを格納して、その配布を制御できます。これにより、アプリケーションで資格情報を保存する必要がなくなります。 アプリケーションでそれらのシークレットにアクセスするためには、実行時に Key Vault で認証を行うことだけが必要です。

## <a name="app-identity-and-security-principals"></a>アプリ ID とセキュリティ プリンシパル

Key Vault による認証は、特定の **セキュリティ プリンシパル** の ID の認証を行う [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) と連携して機能します。

セキュリティ プリンシパルは、Azure リソースへのアクセスを要求するユーザー、グループ、サービス、またはアプリケーションを表すオブジェクトです。 すべてのセキュリティ プリンシパルには、Azure によって一意の **オブジェクト ID** が割り当てられます。

* **ユーザー** セキュリティ プリンシパルでは、Azure Active Directory 内にプロファイルを持つ個人が示されます。

* **グループ** セキュリティ プリンシパルでは、Azure Active Directory 内に作成されたユーザーのセットが示されます。 グループに割り当てられたすべてのロールまたはアクセス許可は、グループ内のすべてのユーザーに付与されます。

* **サービス プリンシパル** は、アプリケーションまたはサービス、つまりユーザーまたはグループではなくコードを示すセキュリティ プリンシパルの一種です。 サービス プリンシパルのオブジェクト ID は **クライアント ID** と呼ばれ、ユーザー名のように機能します。 サービス プリンシパルの **クライアント シークレット** は、パスワードのように機能します。

アプリケーションの場合、サービス プリンシパルを取得するには次の 2 つの方法があります。

* 推奨: アプリケーションに対してシステム割り当ての **マネージド ID** を有効にします。

    Azure では、マネージ ID を使用することにより、アプリケーションのサービス プリンシパルが内部的に管理され、他の Azure サービスでのアプリケーションの認証が自動的に行われます。 マネージド ID は、さまざまなサービスにデプロイされたアプリケーションで使用できます。

    詳細については、[マネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)に関する記事を参照してください。 また、[マネージド ID がサポートされている Azure サービス](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)に関する記事も参照してください。それには、特定のサービス (App Service、Azure Functions、Virtual Machines など) でマネージド ID を有効にする方法について説明されている記事へのリンクが含まれます。

* マネージド ID を使用できない場合は、Azure AD テナントにアプリケーションを **登録** します。詳細については、[クイックスタート: Azure ID プラットフォームでのアプリケーションの登録](../../active-directory/develop/quickstart-register-app.md)に関する記事を参照してください。 また、登録によって、すべてのテナントでそのアプリを示す 2 つ目のアプリケーション オブジェクトも作成されます。

## <a name="authorize-a-security-principal-to-access-key-vault"></a>セキュリティ プリンシパルが Key Vault にアクセスすることを承認する

Key Vault は、2 つの異なる承認レベルで動作します。

- **アクセス ポリシー** では、ユーザー、グループ、またはサービス プリンシパルに対して既存の Key Vault リソースの "*内部にある*" シークレット、キー、証明書へのアクセスが承認されるかどうかが制御されます("データ プレーン" 操作とも呼ばれます)。 通常、アクセス ポリシーは、ユーザー、グループ、アプリケーションに対して付与されます。

    アクセス ポリシーを割り当てる方法については、次の記事を参照してください。

    - [Azure Portal](assign-access-policy-portal.md)
    - [Azure CLI](assign-access-policy-cli.md)
    - [Azure PowerShell](assign-access-policy-portal.md)

- **ロールのアクセス許可** では、ユーザー、グループ、またはサービス プリンシパルに対して Key Vault リソースの作成、削除、その他の管理が承認されるかどうかが制御されます("管理プレーン" 操作とも呼ばれます)。 このようなロールは、ほとんどの場合、管理者にのみ付与されます。
 
    ロールの割り当てと管理については、次の記事を参照してください。

    - [Azure Portal](../../role-based-access-control/role-assignments-portal.md)
    - [Azure CLI](../../role-based-access-control/role-assignments-cli.md)
    - [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

    現在、Key Vault では、Key Vault リソースに対する管理操作を許可する[共同作成者](../../role-based-access-control/built-in-roles.md#key-vault-contributor)ロールがサポートされています。 他の多くのロールは、現在プレビューの段階にあります。 「[Azure カスタム ロール](../../role-based-access-control/custom-roles.md)」で説明されているように、カスタム ロールを作成することもできます。

    ロールに関する一般的な情報については、「[Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)」を参照してください。


> [!IMPORTANT]
> セキュリティを最強にするには、常に最小特権の原則に従い、必要なアクセス ポリシーとロールのうち最も限定的なものだけを付与します。 
    
## <a name="configure-the-key-vault-firewall"></a>Key Vault ファイアウォールを構成する

Key Vault では、パブリック IP アドレスを使用してリソースにアクセスすることが既定で許可されます。 セキュリティを強化するため、特定の IP 範囲、サービス エンドポイント、仮想ネットワーク、またはプライベート エンドポイントにアクセスを制限することもできます。

詳細については、「[ファイアウォールの向こう側にある Azure Key Vault へのアクセス](./access-behind-firewall.md)」を参照してください。


## <a name="the-key-vault-authentication-flow"></a>Key Vault の認証フロー

1. サービス プリンシパルによって Azure AD での認証が要求されます。次はその例です。
    * ユーザーが、ユーザー名とパスワードを使用して Azure portal にログインします。
    * アプリケーションが、Azure REST API を呼び出して、クライアント ID、シークレット、またはクライアント証明書を提示します。
    * マネージド ID を持つ仮想マシンなどの Azure リソースが、[Azure Instance Metadata Service (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) の REST エンドポイントに接続してアクセス トークンを取得します。

1. Azure AD に対する認証が成功すると、サービス プリンシパルに OAuth トークンが付与されます。

1. サービス プリンシパルは、Key Vault のエンドポイント (URI) を通して Key Vault REST API を呼び出します。

1. Key Vault ファイアウォールにより、次の条件がチェックされます。 いずれかの条件が満たされた場合、呼び出しは許可されます。 それ以外の場合、呼び出しはブロックされ、不許可の応答が返されます。

    * ファイアウォールは無効になっており、Key Vault のパブリック エンドポイントにパブリック インターネットから到達できます。
    * 呼び出し元は [Key Vault の信頼できるサービス](./overview-vnet-service-endpoints.md#trusted-services)であり、ファイアウォールをバイパスすることができます。
    * 呼び出し元は、IP アドレス、仮想ネットワーク、またはサービス エンドポイントによってファイアウォールのリストに登録されています。
    * 呼び出し元は、構成されたプライベート リンク接続を通して Key Vault に到達できます。    

1. ファイアウォールで呼び出しが許可された場合、Key Vault によって Azure AD が呼び出されて、サービス プリンシパルのアクセス トークンの検証が行われます。

1. Key Vault により、要求された操作に必要なアクセス ポリシーをサービス プリンシパルが持っているかどうかが確認されます。 そうでない場合、Key Vault からは禁止応答が返されます。

1. Key Vault により、要求された操作が実行されて、結果が返されます。

次の図は、アプリケーションが Key Vault の "シークレットの取得" API を呼び出すプロセスを示したものです。

![Azure Key Vault の認証フロー](../media/authentication/authentication-flow.png)

> [!NOTE]
> シークレット、証明書、キーの Key Vault SDK クライアントは、アクセス トークンなしで Key Vault を追加で呼び出すので、テナント情報を取得する際に 401 応答が返されます。 詳細については、「[認証、要求、応答](authentication-requests-and-responses.md)」を参照してください。

## <a name="code-examples"></a>コード例

次の表では、アプリケーションのコードで特定の言語の Azure SDK ライブラリを使用して Key Vault を操作する方法が示されているさまざまな記事へのリンクを示します。 Azure CLI や Azure portal などの他のインターフェイスも、便利なように示されています。

| Key Vault シークレット | Key Vault キー | Key Vault 証明書 |
|  --- | --- | --- |
| [Python](../secrets/quick-create-python.md) | [Python](../keys/quick-create-python.md) | [Python](../certificates/quick-create-python.md) | 
| [.NET](../secrets/quick-create-net.md) | [.NET](../keys/quick-create-net.md) | [.NET](../certificates/quick-create-net.md) |
| [Java](../secrets/quick-create-java.md) | [Java](../keys/quick-create-java.md) | [Java](../certificates/quick-create-java.md) |
| [JavaScript](../secrets/quick-create-node.md) | [JavaScript](../keys/quick-create-node.md) | [JavaScript](../certificates/quick-create-node.md) | 
| [Azure Portal](../secrets/quick-create-portal.md) | [Azure Portal](../keys/quick-create-portal.md) | [Azure Portal](../certificates/quick-create-portal.md) |
| [Azure CLI](../secrets/quick-create-cli.md) | [Azure CLI](../keys/quick-create-cli.md) | [Azure CLI](../certificates/quick-create-cli.md) |
| [Azure PowerShell](../secrets/quick-create-powershell.md) | [Azure PowerShell](../keys/quick-create-powershell.md) | [Azure PowerShell](../certificates/quick-create-powershell.md) |
| [ARM テンプレート](../secrets/quick-create-net.md) | -- | -- |

## <a name="next-steps"></a>次の手順

- [Key Vault のアクセス ポリシーのトラブルシューティング](troubleshooting-access-issues.md)
- [Key Vault REST API のエラー コード](rest-error-codes.md)
- [Key Vault 開発者ガイド](developers-guide.md)
- [Azure ロールベースのアクセス制御 (Azure RBAC) とは](../../role-based-access-control/overview.md)

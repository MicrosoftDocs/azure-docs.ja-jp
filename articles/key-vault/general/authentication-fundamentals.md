---
title: Azure Key Vault 認証の基礎
description: Key Vault の認証モデルの動作について説明します
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 09/25/2020
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 25f00024fb7371fd08bf6c4ceec3177cfaca029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103572810"
---
# <a name="key-vault-authentication-fundamentals"></a>Key Vault 認証の基礎

Azure Key Vault を使用すると、シークレット、キー、証明書などのアプリケーションの資格情報を、中央のセキュリティ保護されたクラウド リポジトリに安全に格納して管理することができます。 Key Vault を使用すると、アプリケーションに資格情報を保存する必要がなくなります。 アプリケーションで実行時に Key Vault に対して認証を行うことで、資格情報を取得できます。

管理者は、キー コンテナーにアクセスできるユーザーとアプリケーションを厳しく制御し、実行される操作を制限および監査することができます。 このドキュメントでは、Key Vault のアクセス モデルの基本的な概念について説明します。 入門レベルの知識が得られ、キー コンテナーに対してユーザーまたはアプリケーションの認証を行う方法が最初から最後までわかります。

## <a name="required-knowledge"></a>必要な知識

このドキュメントは、次の概念を熟知していることを前提としています。 これらの概念に慣れていない場合は、先に進む前にヘルプのリンクを参照してください。

* Azure Active Directory ([リンク](../../active-directory/fundamentals/active-directory-whatis.md))
* セキュリティ プリンシパル ([リンク](./authentication.md#app-identity-and-security-principals))

## <a name="key-vault-configuration-steps-summary"></a>Key Vault の構成手順の概要

1. ユーザーまたはアプリケーションをセキュリティ プリンシパルとして Azure Active Directory に登録します。
1. Azure Active Directory で、セキュリティ プリンシパルに対するロールの割り当てを構成します。
1. セキュリティ プリンシパルに対して Key Vault のアクセス ポリシーを構成します。
1. キー コンテナーへの Key Vault ファイアウォール アクセスを構成します (省略可能)。
1. セキュリティ プリンシパルが Key Vault にアクセスできることをテストします。

## <a name="register-a-user-or-application-in-azure-active-directory-as-a-security-principal"></a>ユーザーまたはアプリケーションをセキュリティ プリンシパルとして Azure Active Directory に登録する

ユーザーまたはアプリケーションが Key Vault への要求を行うときは、最初に Azure Active Directory で要求の認証を行う必要があります。 これを行うには、ユーザーまたはアプリケーションをセキュリティ プリンシパルとして Azure Active Directory に登録する必要があります。

Azure Active Directory でユーザーまたはアプリケーションを登録する方法については、以下のドキュメントのリンクを参照してください。
**ユーザーを登録する場合はパスワードを作成し、アプリケーションの場合はクライアント シークレットまたはクライアント証明書の資格情報を作成してください。**

* Azure Active Directory でのユーザーの登録 ([リンク](../../active-directory/fundamentals/add-users-azure-active-directory.md))
* Azure Active Directory でのアプリケーションの登録 ([リンク](../../active-directory/develop/quickstart-register-app.md))

## <a name="assign-your-security-principal-a-role"></a>セキュリティ プリンシパルにロールを割り当てる

Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパルにアクセス許可を割り当てることができます。 これらのアクセス許可は、ロールの割り当てと呼ばれます。

Key Vault のコンテキストにおいては、これらのロールの割り当てによって、Key Vault の管理プレーン (コントロール プレーンとも呼ばれます) に対するセキュリティ プリンシパルのアクセス レベルが決まります。 これらのロールの割り当てにより、データ プレーンのシークレットへの直接的なアクセスは提供されませんが、Key Vault のプロパティを管理するためのアクセスは提供されます。 たとえば、**閲覧者ロール** を割り当てられたユーザーまたはアプリケーションは、Key Vault ファイアウォールの設定を変更することはできません。一方、**共同作成者ロール** を割り当てられたユーザーまたはアプリケーションは、変更を行うことができます。 どちらのロールも、Key Vault のデータ プレーンへのアクセス権を割り当てられるまでは、シークレット、キー、証明書の値の作成や取得などの操作を実行するために、直接アクセスすることはできません。 これについては、次のステップで説明します。

>[!IMPORTANT]
> 共同作成者ロールまたは所有者ロールを持つユーザーは、既定では、Key Vault に格納されているシークレットに対する操作を実行するためのアクセス権を持っていませんが、共同作成者および所有者のロールにより、Key Vault に格納されているシークレットに対するアクセス ポリシーを追加または削除するためのアクセス許可は提供されます。 したがって、これらのロールが割り当てられたユーザーは、Key Vault 内のシークレットにアクセスするためのアクセス権を、自分自身に付与することができます。 このため、共同作成者ロールまたは所有者ロールには管理者のみがアクセスできるようにすることをお勧めします。 Key Vault からのシークレットの取得だけが必要なユーザーとアプリケーションには、閲覧者ロールを付与する必要があります。 **詳細については、次のセクションで説明します。**

>[!NOTE]
> Azure Active Directory のテナント レベルでロールの割り当てをユーザーに割り当てると、このアクセス許可のセットは、割り当てのスコープ内にあるすべてのサブスクリプション、リソース グループ、リソースに継承されます。 最小限の特権のプリンシパルに従うには、このロールの割り当てをさらに細かいスコープで行うことができます。 たとえば、1 人のユーザーに、サブスクリプション レベルでは閲覧者ロールを割り当て、1 つのキー コンテナーについては所有者ロールを割り当てることができます。 より細かいスコープでロールの割り当てを行うには、サブスクリプション、リソース グループ、またはキー コンテナーの ID およびアクセス管理 (IAM) の設定にアクセスします。

* Azure ロールの詳細 ([リンク](../../role-based-access-control/built-in-roles.md))
* ロールの割り当ての割り当てまたは削除の詳細 ([リンク](../../role-based-access-control/role-assignments-portal.md))

## <a name="configure-key-vault-access-policies-for-your-security-principal"></a>セキュリティ プリンシパルに対して Key Vault のアクセス ポリシーを構成する

Key Vault にアクセスするためのアクセス権をユーザーやアプリケーションに付与する前に、キー コンテナーで実行できるさまざまな種類の操作を理解しておくことが重要です。 Key Vault の操作には、主に、管理プレーン (コントロール プレーンとも呼ばれます) の操作とデータ プレーンの操作の 2 種類があります。

次の表では、管理プレーンとデータ プレーンによって制御されるさまざまな操作の例をいくつか示します。 キー コンテナーのプロパティを変更する操作は、管理プレーンの操作です。 キー コンテナーに格納されているシークレットの値を変更または取得する操作は、データ プレーンの操作です。

|管理プレーンの操作 (例)|データ プレーンの操作 (例)|
| --- | --- |
| Key Vault の作成 | キー、シークレット、証明書を作成する
| キー コンテナーを削除する | キー、シークレット、証明書を削除する
| キー コンテナーのロールの割り当てを追加または削除する | キー、シークレット、証明書の値を一覧表示して取得する
| キー コンテナーのアクセス ポリシーを追加または削除する | キー、シークレット、証明書をバックアップおよび復元する
| Key Vault ファイアウォールの設定を変更する | キー、シークレット、証明書を更新する
| キー コンテナーの回復の設定を変更する | 論理的に削除されたキー、シークレット、証明書を消去または回復する
| キー コンテナーの診断ログの設定を変更する

### <a name="management-plane-access--azure-active-directory-role-assignments"></a>管理プレーンのアクセスと Azure Active Directory のロールの割り当て

Azure Active Directory のロールの割り当てにより、キー コンテナーに対して管理プレーンの操作を実行するためのアクセス権が付与されます。 このアクセス権は、通常、アプリケーションではなくユーザーに付与されます。 ユーザーのロールの割り当てを変更することにより、ユーザーが実行できる管理プレーンの操作を制限できます。

たとえば、ユーザーに Key Vault 閲覧者ロールを割り当てると、ユーザーはアクセス ポリシーなどのキー コンテナーのプロパティを表示することはできますが、変更することはできません。 所有者ロールを割り当てられたユーザーは、キー コンテナーの管理プレーンの設定を変更するためのフル アクセスを許可されます。

ロールの割り当ては、キー コンテナーの [アクセス制御 (IAM)] ブレードで制御します。 特定のユーザーに複数のキー コンテナー リソースの閲覧者または管理者としてのアクセス権を付与するには、コンテナー、リソース グループ、またはサブスクリプションのレベルでロールの割り当てを作成します。ロールの割り当ては、割り当てのスコープ内のすべてのリソースに追加されます。

データ プレーンのアクセス、つまりキー コンテナーに格納されているキー、シークレット、証明書に対する操作を実行するためのアクセスは、次の 2 つの方法のいずれかで追加できます。

### <a name="data-plane-access-option-1-classic-key-vault-access-policies"></a>データ プレーンのアクセス オプション 1: 従来の Key Vault アクセス ポリシー

Key Vault アクセス ポリシーを使用すると、キー コンテナーに対してデータ プレーン操作を実行するためのアクセス権が、ユーザーとアプリケーションに付与されます。

> [!NOTE]
> このアクセス モデルは、後で説明する Key Vault 用の Azure RBAC (オプション 2) と互換性がありません。 1 つを選択する必要があります。 キー コンテナーの [アクセス ポリシー] タブをクリックして、この選択を行うことができます。

従来のアクセス ポリシーはきめ細かいので、個々のユーザーまたはアプリケーションについて、キー コンテナー内の個々の操作を実行する機能を許可または拒否することができます。 次に例をいくつか示します。

* セキュリティ プリンシパル 1 は、任意のキー操作を実行できますが、シークレットまたは証明書の操作を実行することは許可されていません。
* セキュリティ プリンシパル 2 は、すべてのキー、シークレット、証明書を一覧表示して閲覧することができますが、作成、削除、または更新操作を実行することはできません。
* セキュリティ プリンシパル 3 は、すべてのシークレットのバックアップと復元を行うことができますが、シークレット自体の値を閲覧することはできません。

ただし、従来のアクセス ポリシーを使用すると、オブジェクトごとのレベルでのアクセス許可は許可されず、割り当てられたアクセス許可は、個々のキー コンテナーのスコープに適用されます。 たとえば、"シークレットの取得" アクセス ポリシーのアクセス許可を特定のキー コンテナーのセキュリティ プリンシパルに付与した場合、そのセキュリティ プリンシパルは、その特定のキー コンテナー内のすべてのシークレットを取得することができます。 ただし、この "シークレットの取得" アクセス許可は、他のキー コンテナーに自動的に拡張されることはないので、明示的に割り当てる必要があります。

> [!IMPORTANT]
> 従来の Key Vault のアクセス ポリシーと Azure Active Directory のロールの割り当ては、互いに独立しています。 セキュリティ プリンシパルにサブスクリプション レベルで "共同作成者" ロールを割り当てても、サブスクリプションのスコープ内のすべてのキー コンテナーに対するデータ プレーン操作の実行が、そのセキュリティ プリンシパルに対して自動的に許可されることはありません。 セキュリティ プリンシパルは、データ プレーンの操作を実行するためのアクセス許可を、付与してもらうか、または自分でアクセス ポリシーに付与する必要があります。

### <a name="data-plane-access-option-2--azure-rbac-for-key-vault-preview"></a>データ プレーンのアクセス オプション 2: Key Vault 用の Azure RBAC (プレビュー)

キー コンテナー データ プレーンへのアクセスを許可する新しい方法は、Key Vault 用の Azure ロールベースのアクセス制御 (Azure RBAC) を使用することです。

> [!NOTE]
> このアクセス モデルは、上で示した Key Vault の従来のアクセス ポリシーとは互換性がありません。 1 つを選択する必要があります。 キー コンテナーの [アクセス ポリシー] タブをクリックして、この選択を行うことができます。

Key Vault のロールの割り当ては、キー、シークレット、証明書にアクセスするために使用される一般的なアクセス許可のセットが含まれる、Azure 組み込みロールの割り当てのセットです。 また、このアクセス許可モデルにより、従来の Key Vault アクセス ポリシー モデルでは利用できない追加の機能も有効になります。

* Azure RBAC のアクセス許可は、サブスクリプション、リソース グループ、または個々のキー コンテナーのレベルで、ユーザーにこれらのロールの割り当てを許可することにより、大規模に管理できます。 ユーザーには、Azure RBAC 割り当てのスコープ内のすべてのキー コンテナーに対するデータ プレーン アクセス許可が付与されます。 これにより、キー コンテナーごとに個々のユーザーやアプリケーションに対して、個別のアクセス ポリシーのアクセス許可を割り当てる必要がなくなります。

* Azure RBAC のアクセス許可は、Privileged Identity Management (PIM) と互換性があります。 これにより、Key Vault 管理者などの特権ロールに対して Just-In-Time のアクセスの制御を構成できます。 これは、セキュリティ上のベスト プラクティスであり、キー コンテナーへの継続的なアクセスを排除することで、最小限の特権のプリンシパルに従うことになります。

Key Vault 用の Azure RBAC の詳細については、次のドキュメントをご覧ください。

* Key Vault 用の Azure RBAC ([リンク](./secure-your-key-vault.md#management-plane-and-azure-rbac))
* Key Vault 用の Azure RBAC のロール ([リンク](../../role-based-access-control/built-in-roles.md#key-vault-administrator))

## <a name="configure-key-vault-firewall"></a>Key Vault ファイアウォールを構成する

既定では、パブリック インターネットからのトラフィックがパブリック エンドポイントを介してキー コンテナーに到達することが、Key Vault によって許可されます。 セキュリティをさらに強化するため、キー コンテナーのパブリック エンドポイントへのアクセスを制限するように Azure Key Vault ファイアウォールを構成することができます。

Key Vault ファイアウォールを有効にするには、Key Vault ポータルの [ネットワーク] タブをクリックし、[許可するアクセス元] オプション ボタンで次を選択します: [プライベート エンドポイントと選択されたネットワーク]。 Key Vault ファイアウォールを有効にすることを選択した場合、トラフィックが Key Vault ファイアウォールを通過するのを許可するには、次の方法を使用します。

* Key Vault ファイアウォールの許可リストに IPv4 アドレスを追加します。 このオプションは、静的 IP アドレスを持つアプリケーションに最適です。

* Key Vault ファイアウォールに仮想ネットワークを追加します。 このオプションは、Virtual Machines などの動的 IP アドレスを持つ Azure リソースに最適です。 Azure リソースを仮想ネットワークに追加し、その仮想ネットワークを Key Vault ファイアウォールの許可リストに追加することができます。 このオプションの場合、仮想ネットワーク内のプライベート IP アドレスであるサービス エンドポイントが使用されます。 これにより、追加の保護レイヤーが提供され、Key Vault と仮想ネットワークの間のトラフィックがパブリック インターネット経由でルーティングされることはありません。 サービス エンドポイントの詳細については、次のドキュメントを参照してください。 ([リンク](./network-security.md))

* Key Vault にプライベート リンク接続を追加します。 このオプションを使用すると、仮想ネットワークが Key Vault の特定のインスタンスに直接接続され、Key Vault は実質的に仮想ネットワークの内部に取り込まれます。 Key Vault へのプライベート エンドポイント接続を構成する方法の詳細については、こちらの[リンク](./private-link-service.md)を参照してください

## <a name="test-your-service-principals-ability-to-access-key-vault"></a>サービス プリンシパルが Key Vault にアクセスできることをテストする

上記のすべての手順のとおりにすると、キー コンテナーのシークレットを設定および取得できるようになります。

### <a name="authentication-process-for-users-examples"></a>ユーザーの認証プロセス (例)

* ユーザーは、Azure portal にログインして Key Vault を使用することができます。 [Key Vault ポータルのクイックスタート](./quick-create-portal.md)

* ユーザーは Azure CLI を使用して Key Vault を使用できます。 [Key Vault Azure CLI クイックスタート](./quick-create-cli.md)

* ユーザーは Azure PowerShell を使用して Key Vault を使用できます。 [Key Vault Azure PowerShell クイックスタート](./quick-create-powershell.md)

### <a name="azure-active-directory-authentication-process-for-applications-or-services-examples"></a>アプリケーションまたはサービスの Azure Active Directory 認証プロセス (例)

* Azure Active Directory トークンを取得するため、アプリケーションで関数にクライアント シークレットとクライアント ID を渡します。 

* Azure Active Directory トークンを取得するため、アプリケーションで証明書を提供します。 

* Azure Active Directory トークンを取得するため、Azure リソースで MSI 認証を使用します。 

* MSI 認証について詳しく学習します ([リンク](../../active-directory/managed-identities-azure-resources/overview.md))

### <a name="authentication-process-for-application-python-example"></a>アプリケーションの認証プロセス (Python の例)

次のコード サンプルを使用して、対象のアプリケーションで、構成したサービス プリンシパルを使用してキー コンテナーからシークレットを取得できるかどうかをテストします。

>[!NOTE]
>このサンプルは、デモンストレーションとテストのみを目的としています。 **運用環境ではクライアント シークレット認証を使用しないでください** これは安全な設計手法ではありません。 ベスト プラクティスとして、クライアント証明書または MSI 認証を使用する必要があります。

```python
from azure.identity import ClientSecretCredential
from azure.keyvault.secrets import SecretClient

tenant_id = "{ENTER YOUR TENANT ID HERE}"                          ##ENTER AZURE TENANT ID
vault_url = "https://{ENTER YOUR VAULT NAME}.vault.azure.net/"     ##ENTER THE URL OF YOUR KEY VAULT
client_id = "{ENTER YOUR CLIENT ID HERE}"                          ##ENTER THE CLIENT ID OF YOUR SERVICE PRINCIPAL
cert_path = "{ENTER YOUR CLIENT SECRET HERE}"                      ##ENTER THE CLIENT SECRET OF YOUR SERVICE PRINCIPAL

def main():

    #AUTHENTICATION TO Azure Active Directory USING CLIENT ID AND CLIENT CERTIFICATE (GET Azure Active Directory TOKEN)
    token = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)

    #AUTHENTICATION TO KEY VAULT PRESENTING Azure Active Directory TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    #ENTER NAME OF A SECRET STORED IN KEY VAULT
    secret = client.get_secret('{SECRET_NAME}')

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

## <a name="next-steps"></a>次の手順

Key Vault 認証の詳細については、次のドキュメントを参照してください。 [Key Vault の認証](./authentication.md)

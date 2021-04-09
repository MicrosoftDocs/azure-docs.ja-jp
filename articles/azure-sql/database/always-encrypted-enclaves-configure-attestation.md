---
title: Azure SQL 論理サーバー用に Azure Attestation を構成する
description: Azure SQL Database でセキュリティで保護されたエンクレーブが設定された Always Encrypted に対して Azure Attestation を構成します。
keywords: データの暗号化, sql 暗号化, データベース暗号化, 機密データ, Always Encrypted, セキュリティで保護されたエンクレーブ, SGX, 構成証明
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: fb42a0428f0439053375027481d38977b068e356
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122580"
---
# <a name="configure-azure-attestation-for-your-azure-sql-logical-server"></a>Azure SQL 論理サーバー用に Azure Attestation を構成する

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Azure SQL Database のセキュリティで保護されたエンクレーブが設定された Always Encrypted は、現在、**パブリック プレビュー** 段階にあります。

[Microsoft Azure Attestation](../../attestation/overview.md) は、Intel Software Guard Extensions (Intel SGX) エンクレーブなどの高信頼実行環境 (TEE) を証明するためのソリューションです。 

Azure SQL Database での[セキュリティで保護されたエンクレーブが設定された Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-enclaves) に対して使用される Intel SGX エンクレーブを証明するために Azure Attestation を使用するには、次のことを行う必要があります。

1. [構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)を作成し、推奨される構成証明ポリシーを使用して構成します。

2. Azure SQL 論理サーバーに、構成証明プロバイダーへのアクセスを許可します。

> [!NOTE]
> 構成証明の構成は、構成証明管理者が行います。 「[SGX エンクレーブと構成証明を構成する場合のロールと責任](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation)」を参照してください。

## <a name="requirements"></a>必要条件

Azure SQL 論理サーバーと構成証明プロバイダーは、同じ Azure Active Directory テナントに属している必要があります。 テナント間の相互作用はサポートされていません。 

Azure SQL 論理サーバーには、Azure AD ID が割り当てられている必要があります。 構成証明管理者は、そのサーバーの Azure SQL Database 管理者からサーバーの Azure AD ID を取得する必要があります。 この ID を使用して、サーバーに構成証明プロバイダーへのアクセスを許可します。 

PowerShell と Azure CLI を使用して、ID を持つサーバーを作成するか、既存のサーバーに ID を割り当てる方法については、「[Azure AD ID をサーバーに割り当てる](transparent-data-encryption-byok-configure.md#assign-an-azure-active-directory-azure-ad-identity-to-your-server)」を参照してください。

## <a name="create-and-configure-an-attestation-provider"></a>構成証明プロバイダーを作成して構成する

[構成証明プロバイダー](../../attestation/basic-concepts.md#attestation-provider)は、[構成証明ポリシー](../../attestation/basic-concepts.md#attestation-request)に対して[構成証明要求](../../attestation/basic-concepts.md#attestation-request)を評価し、[構成証明トークン](../../attestation/basic-concepts.md#attestation-token)を発行する Azure Attestation のリソースです。 

構成証明ポリシーは、[要求規則の文法](../../attestation/claim-rule-grammar.md)を使用して指定します。

Azure SQL Database の Always Encrypted に使用される Intel SGX エンクレーブを証明するには、次のポリシーをお勧めします。

```output
version= 1.0;
authorizationrules 
{
       [ type=="x-ms-sgx-is-debuggable", value==false ]
        && [ type=="x-ms-sgx-product-id", value==4639 ]
        && [ type=="x-ms-sgx-svn", value>= 0 ]
        && [ type=="x-ms-sgx-mrsigner", value=="e31c9e505f37a58de09335075fc8591254313eb20bb1a27e5443cc450b6e33e5"] 
    => permit();
};
```

上記のポリシーでは、次のことを確認します。

- Azure SQL Database 内のエンクレーブはデバッグに対応していません。 
  > エンクレーブは、デバッグが無効でも有効でも読み込むことができます。 デバッグ サポートは、開発者がエンクレーブで実行されているコードの問題を解決できるように設計されています。 実稼働システムでは、エンクレーブが提供する保護レベルを下げるようなコンテンツがエンクレーブに含まれていれば、デバッグによって管理者はそれを調べることができます。 悪意のある管理者がエンクレーブ マシンを乗っ取り、デバッグ サポートをオンにしようとした場合、構成証明に失敗するよう、方針としてはデバッグを無効にすることが推奨されます。 
- エンクレーブの製品 ID は、セキュリティで保護されたエンクレーブが設定された Always Encrypted に割り当てられた製品 ID に一致します。
  > エンクレーブにはそれぞれ、他のエンクレーブと区別するための一意の製品 ID が与えられます。 Always Encrypted エンクレーブに割り当てられた製品 ID は 4639 です。 
- ライブラリのセキュリティ バージョン番号 (SVN) は 0 より大きくなります。
  > この SVN によって、Microsoft はエンクレーブ コードで特定された潜在的なセキュリティ バグに対応できます。 セキュリティの問題が見つかり、修正された場合、Microsoft は、新しい SVN が付けられた (1 つ上の番号が付けられた) 新しいバージョンのエンクレーブをデプロイします。 上で推奨されているポリシーは、新しい SVN を反映する目的で更新されます。 推奨されるポリシーに合わせて自分のポリシーを更新することで、悪意のある管理者が古くて安全ではないエンクレーブを読み込もうとしたとき、構成証明を失敗させることができます。
- エンクレーブ内のライブラリが Microsoft 署名キーを使用して署名されている (x-ms-sgx-mrsigner 要求の値が署名キーのハッシュである)。
  > 構成証明の主な目標の 1 つは、エンクレーブ内で実行されるバイナリが、実行されるべきバイナリであることをクライアントに認識させることにあります。 構成証明ポリシーからは、この目的のために 2 つのメカニズムが提供されます。 1 つは **mrenclave** 要求です。これは、エンクレーブで実行されることになっているバイナリのハッシュです。 **mrenclave** の問題は、コードをほんの少し変えただけでもバイナリ ハッシュが変わることにあります。エンクレーブ内で実行されているコードの改訂が難しくなります。 そのため、**mrsigner** の使用を推奨しています。これは、エンクレーブ バイナリの署名に使用されるキーのハッシュです。 Microsoft がエンクレーブを改訂するとき、署名キーが変わらない限り、**mrsigner** も同じままです。 この方法で、顧客のアプリケーションを壊すことなく、更新後のバイナリをデプロイすることが可能になります。 

> [!IMPORTANT]
> 構成証明プロバイダーは、エンクレーブ内で実行されているコードを検証しない、Intel SGX エンクレーブの既定のポリシーを使用して作成されます。 Microsoft では、セキュリティで保護されたエンクレーブが設定された Always Encrypted に既定のポリシーを使用するのではなく、前述の推奨ポリシーを設定することを強くお勧めします。

構成証明プロバイダーを作成して構成証明ポリシーで構成する方法については、以下を参照してください。

- [クイック スタート: Azure portal を使用して Azure Attestation を設定する](../../attestation/quickstart-portal.md)
    > [!IMPORTANT]
    > Azure portal で構成証明ポリシーを構成する場合は、[構成証明の種類] を [`SGX-IntelSDK`] に設定します。
- [クイック スタート:Azure PowerShell を使用して Azure Attestation を設定する](../../attestation/quickstart-powershell.md)
    > [!IMPORTANT]
    > Azure PowerShell で構成証明ポリシーを構成する場合は、`Tee` パラメーターを `SgxEnclave` に設定します。
- [クイックスタート: Azure CLI を使用して Azure Attestation を設定する](../../attestation/quickstart-azure-cli.md)
    > [!IMPORTANT]
    > Azure CLI で構成証明ポリシーを構成する場合は、`attestation-type` パラメーターを `SGX-IntelSDK` に設定します。

## <a name="determine-the-attestation-url-for-your-attestation-policy"></a>構成証明ポリシーの構成証明 URL を確認する

構成証明ポリシーを構成したら、ポリシーを参照する構成証明 URL を、Azure SQL Database でセキュリティで保護されたエンクレーブが設定された Always Encrypted を使用するアプリケーションの管理者と共有する必要があります。 アプリケーション管理者またはアプリケーション ユーザーは、セキュリティで保護されたエンクレーブを使用するステートメントを実行できるように、構成証明 URL を使用してアプリを構成する必要があります。

### <a name="use-powershell-to-determine-the-attestation-url"></a>PowerShell を使用して構成証明の URL を確認する

構成証明の URL を確認するには、次のスクリプトを使用します。

```powershell
$attestationProvider = Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroupName 
$attestationUrl = $attestationProvider.AttestUri + “/attest/SgxEnclave”
Write-Host "Your attestation URL is: " $attestationUrl 
```

### <a name="use-azure-portal-to-determine-the-attestation-url"></a>Azure portal を使用して構成証明 URL を確認する

1. 構成証明プロバイダーの [概要] ペインで、構成証明 URI のプロパティの値をクリップボードにコピーします。 構成証明 URI は次のようになります。`https://MyAttestationProvider.us.attest.azure.net`

2. 構成証明 URI に `/attest/SgxEnclave` を追加します。 

結果として得られる構成証明 URL は次のようになります。`https://MyAttestationProvider.us.attest.azure.net/attest/SgxEnclave`

## <a name="grant-your-azure-sql-logical-server-access-to-your-attestation-provider"></a>Azure SQL 論理サーバーに構成証明プロバイダーへのアクセスを許可する

構成証明のワークフロー中に、データベースを含む Azure SQL 論理サーバーは、構成証明要求を送信するために構成証明プロバイダーを呼び出します。 Azure SQL 論理サーバーが構成証明要求を送信できるようにするには、サーバーは構成証明プロバイダーの `Microsoft.Attestation/attestationProviders/attestation/read` アクションに対するアクセス許可を持っている必要があります。 アクセス許可を付与する方法として推奨される方法は、構成証明プロバイダーの管理者が、構成証明プロバイダーまたはそれに含まれるリソース グループの構成証明リーダー ロールに、サーバーの Azure AD ID を割り当てることです。

### <a name="use-azure-portal-to-assign-permission"></a>Azure portal を使用してアクセス許可を割り当てる

構成証明プロバイダーの構成証明リーダー ロールに Azure SQL サーバーの ID を割り当てるには、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」の一般的な手順に従います。 **[ロールの割り当ての追加]** ペインで、次のようにします。

1. **[Role]\(ロール\)** ドロップダウンで、 **[Attestation Reader]\(構成証明リーダー\)** ロールを選択します。
1. **[選択]** フィールドに、検索する Azure SQL サーバーの名前を入力します。

以下のスクリーンショットの例をご覧ください。

![構成証明リーダー ロールの割り当て](./media/always-encrypted-enclaves/attestation-provider-role-assigment.png)

> [!NOTE]
> **[ロールの割り当ての追加]** ペインにサーバーが表示されるようにするには、サーバーに Azure AD ID が割り当てられている必要があります。「[必要条件](#requirements)」を参照してください。

### <a name="use-powershell-to-assign-permission"></a>PowerShell を使用してアクセス許可を割り当てる

1. Azure SQL 論理サーバーを見つけます。

```powershell
$serverResourceGroupName = "<server resource group name>"
$serverName = "<server name>" 
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName
```
 
2. 構成証明プロバイダーを含むリソース グループの構成証明リーダー ロールに、サーバーを割り当てます。

```powershell
$attestationResourceGroupName = "<attestation provider resource group name>"
New-AzRoleAssignment -ObjectId $server.Identity.PrincipalId -RoleDefinitionName "Attestation Reader" -ResourceGroupName $attestationResourceGroupName
```

詳細については、「[Azure PowerShell を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-powershell.md#assign-role-examples)」を参照してください。

## <a name="next-steps"></a>次の手順

- [セキュリティで保護されたエンクレーブが設定された Always Encrypted のキーを管理する](/sql/relational-databases/security/encryption/always-encrypted-enclaves-manage-keys)

## <a name="see-also"></a>関連項目

- [チュートリアル: Azure SQL Database でのセキュリティで保護されたエンクレーブが設定された Always Encrypted の概要](always-encrypted-enclaves-getting-started.md)

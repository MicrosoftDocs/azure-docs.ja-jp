---
title: Azure Key Vault 開発者ガイド
description: 開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 550084ccbb1df24fe0cbc0a4630efe19e6685e5c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709905"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 開発者ガイド

Key Vault を使用すると、アプリケーション内から機密情報に安全にアクセスできるようになります。

- 自分でコードを記述しなくても、キー、シークレット、証明書が保護され、自分のアプリケーションから簡単に使用できます。
- 顧客自身にキー、シークレット、証明書を所有して管理してもらうことができるため、主要なソフトウェア機能の提供に集中できます。 このようにすると、アプリケーションが顧客のテナント キー、シークレット、証明書に対して義務や潜在的責任を負うことはありません。
- アプリケーションで署名と暗号化にキーを使用しながら、キー管理をアプリケーションの外部で行うことができます。 キーの詳細については、「[キーについて](../keys/about-keys.md)」を参照してください。
- パスワード、アクセス キー、SAS トークンなどの資格情報をシークレットとして Key Vault に格納することにより、管理することができます。[シークレットについて](../secrets/about-secrets.md)のページを参照してください。
- 証明書の管理。 詳細については、[証明書について](../certificates/about-certificates.md)のページを参照してください。

Azure Key Vault の全般的な情報については、「 [Azure Key Vault とは](overview.md)」を参照してください。

## <a name="public-previews"></a>パブリック プレビュー

Key Vault の新機能のパブリック プレビューは定期的にリリースされます。 パブリック プレビュー機能をお試しいただき、azurekeyvault@microsoft.com (フィードバック用のメール アドレス) までご意見をお寄せください。

## <a name="creating-and-managing-key-vaults"></a>Key Vaults の作成と管理

Key Vault の管理は、他の Azure サービスと同様に、Azure Resource Manager サービスを通じて行います。 Azure Resource Manager は、Azure のデプロイおよび管理サービスです。 お使いの Azure アカウント内のリソースを作成、更新、および削除できる管理レイヤーを提供します。 詳細については、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) に関するページを参照してください。

管理レイヤーへのアクセスは、[Azure ロールベースの制御](../../role-based-access-control/overview.md)によって制御されます。 Key Vault においては、管理またはコントロール プレーンとも呼ばれる管理レイヤーを使用して、キー コンテナーとその属性 (アクセス ポリシーを含む) を作成および管理できますが、キー、シークレット、証明書はそれができません (これらはデータ プレーンで管理されます)。 定義済みの `Key Vault Contributor` ロールを使用して、Key Vault への管理アクセス権を付与できます。     

**キー コンテナー管理のための API と SDK:**

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault)<br>[クイックスタート](quick-create-cli.md)|[リファレンス](/powershell/module/az.keyvault)<br>[クイックスタート](quick-create-powershell.md)|[リファレンス](/rest/api/keyvault/)|[リファレンス](/azure/templates/microsoft.keyvault/vaults)<br>[クイックスタート](./vault-create-template.md)|[リファレンス](/dotnet/api/microsoft.azure.management.keyvault)|[リファレンス](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[リファレンス](/java/api/com.microsoft.azure.management.keyvault)|[リファレンス](/javascript/api/@azure/arm-keyvault)|

インストール パッケージとソース コードについては、[クライアント ライブラリ](client-libraries.md)に関するページを参照してください。

Key Vault 管理プレーンの詳細については、[Key Vault 管理プレーン](./secure-your-key-vault.md#management-plane-and-azure-rbac)に関するページを参照してください。

## <a name="authenticate-to-key-vault-in-code"></a>コードでの Key Vault に対する認証

Key Vault には、アクセスを許可するために Azure AD セキュリティ プリンシパルを必要とする Azure AD 認証が使用されています。 Azure AD セキュリティ プリンシパルは、ユーザー、アプリケーション サービス プリンシパル、[Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)、または任意の種類のセキュリティ プリンシパルのグループである場合があります。

### <a name="authentication-best-practices"></a>認証のベスト プラクティス

Azure にデプロイされたアプリケーションにはマネージド ID を使用することをお勧めします。 マネージド ID がサポートされていない Azure サービスを使用する場合、またはアプリケーションをオンプレミスに展開している場合は、他の可能な方法として[証明書を使用したサービス プリンシパル](../../active-directory/develop/howto-create-service-principal-portal.md)があります。 そのシナリオでは、証明書が Key Vault に格納され、頻繁にローテーションされることになります。 シークレットを使用したサービス プリンシパルは、開発およびテスト環境で使用できます。また、ユーザー プリンシパルを使用してローカルまたは Cloud Shell で使用することをお勧めします。

環境ごとに推奨されるセキュリティ プリンシパル:
- **運用環境**:
  - 証明書を使用したマネージド ID またはサービス プリンシパル
- **テストおよび開発環境**:
  - マネージド ID、証明書を使用したサービス プリンシパル、またはシークレットを指定したサービス プリンシパル
- **ローカル開発**:
  - シークレットを指定したユーザー プリンシパルまたはサービス プリンシパル

上記の認証シナリオは、**Azure ID クライアント ライブラリ** でサポートされており、Key Vault SDK と統合されています。 Azure ID ライブラリは、コードを変更することなく、異なる複数の環境やプラットフォームで使用できます。 Azure ID を使用すると、Azure CLI、Visual Studio、Visual Studio Code などによって Azure にログインしたユーザーから、認証トークンを自動的に取得することもできます。 

Azure ID クライアント ライブラリの詳細については、以下を参照してください。

**Azure ID クライアント ライブラリ**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure Identity SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure Identity SDK Python](/python/api/overview/azure/identity-readme)|[Azure Identity SDK Java](/java/api/overview/azure/identity-readme)|[Azure Identity SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> Key Vault .NET SDK バージョン 3 で推奨されていた[アプリ認証ライブラリ](/dotnet/api/overview/azure/service-to-service-authentication)は、現在非推奨になりました。 Key Vault .NET SDK バージョン 4 に移行するには、[AppAuthentication から Azure.Identity への移行ガイダンス](/dotnet/api/overview/azure/app-auth-migration)に関する記事を参照してください。

アプリケーションで Key Vault に対して認証する方法に関するチュートリアルについては、以下を参照してください。
- [VM にホストされているアプリケーションから Key Vault に対して .NET で認証する](./tutorial-net-virtual-machine.md)
- [VM にホストされているアプリケーションから Key Vault に対して Python で認証する](./tutorial-python-virtual-machine.md)
- [App Service を使用して Key Vault に対して認証する](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>キー、証明書、シークレットの管理

キー、シークレット、および証明書へのアクセスは、データ プレーンによって制御されます。 データ プレーンのアクセス制御は、ローカルのコンテナー アクセス ポリシーまたは Azure RBAC (プレビュー) を使用して行うことができます。

**キーの API と SDK**

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/key)<br>[クイックスタート](../keys/quick-create-cli.md)|[リファレンス](/powershell/module/az.keyvault/)<br>[クイックスタート](../keys/quick-create-powershell.md)|[リファレンス](/rest/api/keyvault/#key-operations)|[リファレンス](/azure/templates/microsoft.keyvault/vaults/keys)<br>[クイックスタート](../keys/quick-create-template.md)|[リファレンス](/dotnet/api/azure.security.keyvault.keys)<br>[クイックスタート](../keys/quick-create-net.md)|[リファレンス](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[クイックスタート](../keys/quick-create-python.md)|[リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[クイックスタート](../keys/quick-create-java.md)|[リファレンス](/javascript/api/@azure/keyvault-keys/)<br>[クイックスタート](../keys/quick-create-node.md)|

**証明書の API と SDK**

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/certificate)<br>[クイックスタート](../certificates/quick-create-cli.md)|[リファレンス](/powershell/module/az.keyvault)<br>[クイックスタート](../certificates/quick-create-powershell.md)|[参照](/rest/api/keyvault/#certificate-operations)|該当なし|[リファレンス](/dotnet/api/azure.security.keyvault.certificates)<br>[クイックスタート](../certificates/quick-create-net.md)|[リファレンス](/python/api/overview/azure/keyvault-certificates-readme)<br>[クイックスタート](../certificates/quick-create-python.md)|[リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[クイックスタート](../certificates/quick-create-java.md)|[リファレンス](/javascript/api/@azure/keyvault-certificates/)<br>[クイックスタート](../certificates/quick-create-node.md)|

**シークレットの API と SDK**

| Azure CLI | PowerShell | REST API | リソース マネージャー | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[参照](/cli/azure/keyvault/secret)<br>[クイックスタート](../secrets/quick-create-cli.md)|[リファレンス](/powershell/module/az.keyvault/)<br>[クイックスタート](../secrets/quick-create-powershell.md)|[リファレンス](/rest/api/keyvault/#secret-operations)|[リファレンス](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[クイックスタート](../secrets/quick-create-template.md)|[リファレンス](/dotnet/api/azure.security.keyvault.secrets)<br>[クイックスタート](../secrets/quick-create-net.md)|[リファレンス](/python/api/overview/azure/keyvault-secrets-readme)<br>[クイックスタート](../secrets/quick-create-python.md)|[リファレンス](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[クイックスタート](../secrets/quick-create-java.md)|[リファレンス](/javascript/api/@azure/keyvault-secrets/)<br>[クイックスタート](../secrets/quick-create-node.md)|

インストール パッケージとソース コードについては、[クライアント ライブラリ](client-libraries.md)に関するページを参照してください。

Key Vault データ プレーンのセキュリティの詳細については、[Key Vault データ プレーンとアクセス ポリシー](./secure-your-key-vault.md#data-plane-and-access-policies)および [Key Vault データ プレーンと Azure RBAC (プレビュー)](./secure-your-key-vault.md#data-plane-and-azure-rbac-preview) に関するページをご覧ください

### <a name="code-examples"></a>コード例

アプリケーションで Key Vault を使用する詳細な例については、以下をご覧ください。

- [Azure Key Vault のコード サンプル](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault のコード サンプル。 

## <a name="how-tos"></a>方法

次の記事とシナリオは、Azure Key Vault を操作するためのタスク固有のガイダンスを提供します。

- [ファイアウォールの背後で Key Vault にアクセスする](access-behind-firewall.md) - キー コンテナーにアクセスするには、Key Vault クライアント アプリケーションが、各種の機能のために複数のエンドポイントにアクセスできる必要があります。
- Key Vault から VM に証明書をデプロイする方法 - [Windows](../../virtual-machines/extensions/key-vault-windows.md)、[Linux](../../virtual-machines/extensions/key-vault-linux.md) - Azure の VM で実行するクラウド アプリケーションには証明書が必要です。 今すぐこの VM で証明書を取得する方法を説明します。
- [Key Vault を介して Azure Web アプリ証明書をデプロイする](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)
- アクセス ポリシーを割り当てます ([CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [ポータル](assign-access-policy-portal.md))。 
- 「[CLI で Key Vault の論理的な削除を使用する方法](./key-vault-recovery.md)」では、キー コンテナーの使用方法とライフサイクルおよび論理的な削除が有効な各種キー コンテナー オブジェクトを紹介します。
- [デプロイ時にセキュリティで保護された値 (パスワードなど) を渡す方法](../../azure-resource-manager/templates/key-vault-parameter.md) - デプロイメント時にパラメーターとしてセキュリティで保護された値 (パスワードなど) を渡す必要がある場合、Azure Key Vault にシークレットとしてその値を格納し、他のリソース マネージャー テンプレートで値を参照することができます。

## <a name="integrated-with-key-vault"></a>Key Vault との統合

Key Vault を使用したり、Key Vault と統合したりする他のシナリオとサービスについては、以下の記事で取り上げています。

- [保存データの暗号化](../../security/fundamentals/encryption-atrest.md)によって、データが永続化されるときにエンコード (暗号化) することができます。 データ暗号化キーは多くの場合、さらにアクセスを制限するため、Azure Key Vault のキーの暗号化キーで暗号化されます。
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) では、独自のテナント キーを管理できます。 たとえば、テナント キーの管理をマイクロソフトに任せる (既定値) 代わりに、組織に適用される特定の規則を遵守する独自のテナント キーを管理できます。 独自のテナント キーの管理は、BYOK (Bring Your Own Key: 独自のキーを持ち込むの意) とも呼ばれます。
- [Azure Private Link サービス](private-link-service.md)を使用すると、自分の仮想ネットワーク内のプライベート エンドポイント経由で、Azure サービス (Azure Key Vault、Azure Storage、Azure Cosmos DB など) と、Azure でホストされている顧客またはパートナー サービスにアクセスできます。
- Key Vault と [Event Grid](../../event-grid/event-schema-key-vault.md) の統合により、キー コンテナーに格納されているシークレットの状態が変更されたときにユーザーに通知できます。 アプリケーションに新しいバージョンのシークレットを配布したり、期限切れが近いシークレットをローテーションしたりして、停止を防ぐことができます。
- [Azure Devops](/azure/devops/pipelines/release/azure-key-vault) シークレットを Key Vault で不要なアクセスから保護することができます。
- [DataBricks の Key Vault に格納されているシークレットを使用して Azure Storage に接続する](./integrate-databricks-blob-storage.md)
- Kubernetes 上の[シークレット ストア CSI ドライバー](./key-vault-integrate-kubernetes.md)向けに Azure Key Vault プロバイダーを構成して実行する

## <a name="key-vault-overviews-and-concepts"></a>Key Vault の概要と概念

- [Key Vault の論理的な削除の動作](soft-delete-overview.md)に関するページでは、削除が偶発的なものか意図的なものかにかかわらず、削除されたオブジェクトの回復を可能にする機能について説明します。
- [Key Vault のクライアント調整](overview-throttling.md)に関するページでは、調整の基本的な概念を説明するとともに、アプリへのアプローチも紹介します。
- [Key Vault のセキュリティ ワールド](overview-security-worlds.md)に関するページでは、リージョンとセキュリティ領域の関係について説明します。

## <a name="social"></a>ソーシャル

- [Key Vault Blog](/archive/blogs/kv/)
- [Key Vault Forum](https://aka.ms/kvforum)
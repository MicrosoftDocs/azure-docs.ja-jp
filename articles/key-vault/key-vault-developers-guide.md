---
title: Azure Key Vault 開発者ガイド
description: 開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 1ebf4feb0687d9580bba29cd8213030e542a06da
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268841"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 開発者ガイド

Key Vault を使用すると、アプリケーション内から機密情報に安全にアクセスできるようになります。

- 自分でコードを記述しなくても、キーとシークレットが保護され、アプリケーションから簡単に使用できます。
- 顧客自身にキーを所有して管理してもらうことができるため、主要なソフトウェア機能の提供に集中できます。 この方法では、アプリケーションが顧客のテナント キーとシークレットに対して義務や潜在的責任を負うことはありません。
- アプリケーションで署名と暗号化にキーを使用しながら、キー管理をアプリケーションの外部で行うことができます。これにより、ソリューションは地理的に分散したアプリを対象とすることができます。
- Key Vault の 2016 年 9 月のリリース以降、アプリケーションで Key Vault の証明書を管理できるようになりました。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](/rest/api/keyvault/about-keys--secrets-and-certificates)」を参照してください。

Azure Key Vault の全般的な情報については、「 [Azure Key Vault とは](key-vault-overview.md)」を参照してください。

## <a name="public-previews"></a>パブリック プレビュー

Key Vault の新機能のパブリック プレビューは定期的にリリースされます。 ぜひお試しいただき、azurekeyvault@microsoft.com (フィードバック用のメール アドレス) までご意見をお寄せください。

## <a name="creating-and-managing-key-vaults"></a>Key Vaults の作成と管理

Azure Key Vault は、資格情報およびその他のキーやシークレットを安全に保管する方法を提供しますが、コードは Key Vault に認証してそれらを取得する必要があります。 Azure リソースのマネージド ID は、Azure Active Directory (Azure AD) で自動的に管理されている ID を Azure サービスに付与することで、この問題を簡単に解決します。 この ID を使用して、コードに資格情報が含まれていなくても、Key Vault を含む Azure AD の認証をサポートする任意のサービスに認証することができます。 

Azure リソースのマネージド ID の詳細については、[マネージド ID の概要](../active-directory/managed-identities-azure-resources/overview.md)に関するページを参照してください。 AAD の操作の詳細については、[Azure Active Directory とアプリケーションの統合](../active-directory/develop/active-directory-integrating-applications.md)に関するページを参照してください。

キー コンテナーでキー、シークレット、または証明書を使用する前に、次の記事に従って、CLI、PowerShell、Resource Manager テンプレートまたは REST でキーを作成し、管理します。

- [CLI を使用した Key Vault の管理](key-vault-manage-with-cli2.md)
- [PowerShell を使用した Key Vault の作成と管理](key-vault-overview.md)
- [Azure Resource Manager テンプレートを使用した Key Vault の作成とシークレットの追加](../azure-resource-manager/resource-manager-template-keyvault.md)
- [REST を使用した Key Vault の作成と管理](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Key Vault を使用したコーディング

プログラマー向けの Key Vault 管理システムは、さまざまなインターフェイスで構成されています。 このセクションでは、すべての言語のリンクと、いくつかのコード例を紹介します。 

### <a name="supported-programming-and-scripting-languages"></a>サポートされるプログラミング言語とスクリプト言語

#### <a name="rest"></a>REST

すべてのキー コンテナー リソースは、インターフェイス、コンテナー、キー、シークレットなどの REST インターフェイスからアクセスできます。 

[Key Vault REST API リファレンス](/rest/api/keyvault/)。

#### <a name="net"></a>.NET

[Key Vault の .NET API リファレンス](/dotnet/api/microsoft.azure.keyvault)。

.NET SDK の 2.x バージョンの詳細については、[リリース ノート](key-vault-dotnet2api-release-notes.md)をご覧ください。

#### <a name="java"></a>Java

[Key Vault の Java SDK](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

Node.js では、キー コンテナー管理 API とキー コンテナー オブジェクト API は別々になっています。 次の概要の記事から、両方にアクセスすることができます。 

[Node.js 用 Azure Key Vault モジュール](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Python 用 Azure Key Vault ライブラリ](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI for Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell for Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quick-start-guides"></a>クイック スタート ガイド

- [Key Vault の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Node.js を使用した Key Vault の概要](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>コード例

アプリケーションで Key Vault を使用する詳細な例については、以下をご覧ください。

- [Azure Key Vault のコード サンプル](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault のコード サンプル。 
- [Web アプリケーションからの Azure Key Vault の使用](quick-create-net.md) - このチュートリアルでは、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。 

## <a name="how-tos"></a>方法

次の記事とシナリオは、Azure Key Vault を操作するためのタスク固有のガイダンスを提供します。

- [サブスクリプションの移行後に Key Vault のテナント ID を変更する](key-vault-subscription-move-fix.md) - テナント A からテナント B に Azure サブスクリプションを移行すると、テナント B のプリンシパル (ユーザーとアプリケーション) は既存のキー コンテナーにアクセスできなくなります。この問題を解決するには、次のガイドを使用します。
- [ファイアウォールの背後で Key Vault にアクセスする](key-vault-access-behind-firewall.md) - キー コンテナーにアクセスするには、Key Vault クライアント アプリケーションが、各種の機能のために複数のエンドポイントにアクセスできる必要があります。
- [Azure Key Vault の HSM 保護キーを生成し、転送する方法](key-vault-hsm-protected-keys.md) - この記事は Azure Key Vault と共に使用する独自の HSM 保護キーを計画、生成、転送する際に役立ちます。
- [デプロイ時にセキュリティで保護された値 (パスワードなど) を渡す方法](../azure-resource-manager/resource-manager-keyvault-parameter.md) - デプロイメント時にパラメーターとしてセキュリティで保護された値 (パスワードなど) を渡す必要がある場合、Azure Key Vault にシークレットとしてその値を格納し、他のリソース マネージャー テンプレートで値を参照することができます。
- [Key Vault を使用して SQL Server で拡張キーを管理する方法](https://msdn.microsoft.com/library/dn198405.aspx) - Azure Key Vault 用の SQL Server Connector により、SQL Server と VM 内の SQL で拡張キー管理 (EKM) プロバイダーとして Azure Key Vault サービスを利用して、Transparent Data Encryption (透過的なデータ暗号化、TDE)、バックアップ暗号化、Column Level Encryption (列レベル暗号化、CLE)など、アプリケーション リンクの暗号化キーを保護できます。
- [Key Vault から VM に証明書をデプロイする方法](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Azure の VM で実行するクラウド アプリケーションには証明書が必要です。 今すぐこの VM で証明書を取得する方法を説明します。
- [エンド ツー エンドのキー ローテーションと監査で Key Vault を設定する方法](key-vault-key-rotation-log-monitoring.md): Azure Key Vault を使用して、キー ローテーションと監査を設定する方法について説明します。
- [Key Vault を使用した Azure Web App Certificate のデプロイ]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/): Key Vault に格納されている証明書を、[App Service 証明書](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)の提供の一部としてデプロイするための手順について説明します。
- [Key Vault にアクセスするためのアクセス許可を多数のアプリケーションに付与する](key-vault-group-permissions-for-apps.md): Key Vault のアクセス制御ポリシーでは、最大 1024 のエントリをサポートします。 ただし、Azure Active Directory セキュリティ グループを作成することができます。 関連するすべてのサービス プリンシパルをこのセキュリティ グループに追加し、そのセキュリティ グループに Key Vault へのアクセスを許可してください。
- Key Vault と Azure の統合と使用に関するその他のタスク固有のガイダンスについては、[Ryan Jones による Key Vault 向け Azure Resource Manager テンプレート サンプル](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)をご覧ください。
- 「[CLI で Key Vault の論理的な削除を使用する方法](key-vault-soft-delete-cli.md)」では、キー コンテナーの使用方法とライフサイクルおよび論理的な削除が有効な各種キー コンテナー オブジェクトを紹介します。
- 「[How to use Key Vault soft-delete with PowerShell](key-vault-soft-delete-powershell.md)」(PowerShell で Key Vault の論理的な削除を使用する方法) では、キー コンテナーの使用方法とライフサイクルおよび論理的な削除が有効な各種キー コンテナー オブジェクトを紹介します。

## <a name="integrated-with-key-vault"></a>Key Vault との統合

Key Vault を使用したり、Key Vault と統合したりする他のシナリオとサービスについては、以下の記事で取り上げています。

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、OS とデータ ディスクのボリュームの暗号化を提供します。 ソリューションは Azure Key Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) では、アカウントに格納されているデータを暗号化するオプションを提供します。 キーの管理に関して、Data Lake Store には、Data Lake Store に格納されているすべてのデータを暗号化解除するのに必要なマスター暗号化キー (MEK) を管理するための 2 つのモードが用意されています。 Data Lake Store に MEK の管理を任せることも、Azure Key Vault アカウントを使用して MEK の所有権を保持することもできます。 キー管理のモードは、Data Lake Store アカウントの作成時に指定します。
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) では、独自のテナント キーを管理できます。 たとえば、テナント キーの管理をマイクロソフトに任せる (既定値) 代わりに、組織に適用される特定の規則を遵守する独自のテナント キーを管理できます。 独自のテナント キーの管理は、BYOK (Bring Your Own Key: 独自のキーを持ち込むの意) とも呼ばれます。

## <a name="key-vault-overviews-and-concepts"></a>Key Vault の概要と概念

- [Key Vault の論理的な削除の動作](key-vault-ovw-soft-delete.md)に関するページでは、削除が偶発的なものか意図的なものかにかかわらず、削除されたオブジェクトの回復を可能にする機能について説明します。
- [Key Vault のクライアント調整](key-vault-ovw-throttling.md)に関するページでは、調整の基本的な概念を説明するとともに、アプリへのアプローチも紹介します。
- [Key Vault のストレージ アカウント キーの概要](key-vault-ovw-storage-keys.md)に関するページでは、Key Vault 統合 Azure ストレージ アカウント キーについて説明します。
- [Key Vault のセキュリティ ワールド](key-vault-ovw-security-worlds.md)に関するページでは、リージョンとセキュリティ領域の関係について説明します。

## <a name="social"></a>ソーシャル

- [Key Vault Blog](https://aka.ms/kvblog)
- [Key Vault Forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>対応ライブラリ

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core): 識別子からキーを検索し、キーを使用して操作を実行する、**IKey** および **IKeyResolver** インターフェイスを提供します。
- [Microsoft Azure Key Vault Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) : Azure Key Vault の拡張機能を提供します。

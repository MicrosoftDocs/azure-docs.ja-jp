---
title: "Azure Key Vault 開発者ガイド"
description: "開発者は、Azure Key Vault を使用して、Microsoft Azure 環境内で暗号化キーを管理できます。"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 08/04/2017
ms.author: bruceper
ms.openlocfilehash: fec4769c0bd571edea84dd2f766bb907d8819be5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 開発者ガイド

Key Vault を使用すると、アプリケーション内から機密情報に安全にアクセスできるようになります。

- 自分でコードを記述しなくても、キーとシークレットが保護され、アプリケーションから簡単に使用できます。
- 顧客自身にキーを所有して管理してもらうことができるため、主要なソフトウェア機能の提供に集中できます。 この方法では、アプリケーションが顧客のテナント キーとシークレットに対して義務や潜在的責任を負うことはありません。
- アプリケーションで署名と暗号化にキーを使用しながら、キー管理をアプリケーションの外部で行うことができます。これにより、ソリューションは地理的に分散したアプリを対象とすることができます。
- Key Vault の 2016 年 9 月のリリース以降、アプリケーションで Key Vault の[証明書](https://docs.microsoft.com/rest/api/keyvault/certificate-operations)を使用できるようになりました。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates)」を参照してください。

Azure Key Vault の全般的な情報については、「 [Azure Key Vault とは](key-vault-whatis.md)」を参照してください。

## <a name="public-previews"></a>パブリック プレビュー

Key Vault の新機能のパブリック プレビューは定期的にリリースされます。 ぜひお試しいただき、azurekeyvault@microsoft.com (フィードバック用のメール アドレス) までご意見をお寄せください。

### <a name="storage-account-keys---july-10-2017"></a>ストレージ アカウント キー - 2017 年 7 月 10 日

>[!NOTE]
>Azure Key Vault のこのアップデートでは、**ストレージ アカウント キー**機能のみがプレビュー段階です。

このプレビューには、[.NET/C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/)、[REST](https://docs.microsoft.com/rest/api/keyvault/)、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) の各インターフェイスで使用できる新しいストレージ アカウント キー機能が用意されています。 

新しいストレージ アカウント キー機能について詳しくは、[Azure Key Vault ストレージ アカウント キーの概要](key-vault-ovw-storage-keys.md)に関する記事をご覧ください。

## <a name="videos"></a>ビデオ

このビデオでは、独自の Key Vault を作成して 「Hello Key Vault」 サンプル アプリケーションからそれを使用する方法について説明します。

- [Key Vault 開発者 - クイック スタート ガイド](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

このビデオで説明されているリソース:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault のサンプル コード](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Key Vaults の作成と管理

コード内で Azure Key Vault を使用する前に、次の記事の説明に従って、REST、リソース マネージャー テンプレート、PowerShell、または CLI を使用してコンテナーを作成し、管理します。

- [REST を使用した Key Vault の作成と管理](https://docs.microsoft.com/rest/api/keyvault/)
- [PowerShell を使用した Key Vault の作成と管理](key-vault-get-started.md)
- [CLI を使用した Key Vault の管理](key-vault-manage-with-cli2.md)
- [Azure Resource Manager テンプレートを使用した Key Vault の作成とシークレットの追加](../azure-resource-manager/resource-manager-template-keyvault.md)

> [!NOTE]
> Key Vault に対する操作は、AAD によって認証され、コンテナーごとに定義された Key Vault 独自のアクセス ポリーによって許可されます。

## <a name="coding-with-key-vault"></a>Key Vault を使用したコーディング

プログラマー向けの Key Vault 管理システムは、REST を基盤とするさまざまなインターフェイスで構成されています。 REST インターフェイスを使用すると、すべてのキー コンテナー リソース (キー、シークレット、証明書) にアクセスできます。 [Key Vault REST API リファレンス](https://docs.microsoft.com/rest/api/keyvault/)。 

### <a name="supported-programming-languages"></a>サポートされるプログラミング言語

#### <a name="net"></a>.NET

- [Key Vault の .NET API リファレンス](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

.NET SDK の 2.x バージョンの詳細については、[リリース ノート](key-vault-dotnet2api-release-notes.md)をご覧ください。

#### <a name="java"></a>Java

- [Key Vault の Java SDK](https://docs.microsoft.com/java/api/com.microsoft.azure.keyvault)

#### <a name="nodejs"></a>Node.js

Node.js では、コンテナー管理 API とコンテナー オブジェクト API は別々になっています。 Key Vault Management では、キー コンテナーの作成と更新が行えます。 Key Vault Operations API は、キーやシークレット、証明書といったコンテナー オブジェクトを使用するためのものです。 

- [Key Vault Management の Node.js API リファレンス](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest/)
- [Key Vault Operations の Node.js API リファレンス](http://azure.github.io/azure-sdk-for-node/azure-keyvault/latest/) 

### <a name="quick-start"></a>クイック スタート

- [Key Vault の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Node.js を使用した Key Vault の概要](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>コード例

アプリケーションで Key Vault を使用する詳細な例については、以下をご覧ください。

- [Azure Key Vault コード サンプル](http://www.microsoft.com/download/details.aspx?id=45343) - .NET サンプル アプリケーション *HelloKeyVault* と Azure Web サービスの例。 
- [Web アプリケーションからの Azure Key Vault の使用](key-vault-use-from-web-application.md) - このチュートリアルでは、Azure の Web アプリケーションから Azure Key Vault を使用する方法について学習できます。 

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
- [Key Vault にアクセスするためのアクセス許可を多数のアプリケーションに付与する](key-vault-group-permissions-for-apps.md): Key Vault のアクセス制御ポリシーでは、16 のエントリのみをサポートします。 ただし、Azure Active Directory セキュリティ グループを作成することができます。 関連するすべてのサービス プリンシパルをこのセキュリティ グループに追加し、そのセキュリティ グループに Key Vault へのアクセスを許可してください。
- Key Vault と Azure の統合と使用に関するその他のタスク固有のガイダンスについては、[Ryan Jones による Key Vault 向け Azure Resource Manager テンプレート サンプル](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)をご覧ください。
- 「[CLI で Key Vault の論理的な削除を使用する方法](key-vault-soft-delete-cli.md)」では、キー コンテナーの使用方法とライフサイクルおよび論理的な削除が有効な各種キー コンテナー オブジェクトを紹介します。
- 「[How to use Key Vault soft-delete with PowerShell](key-vault-soft-delete-powershell.md)」(PowerShell で Key Vault の論理的な削除を使用する方法) では、キー コンテナーの使用方法とライフサイクルおよび論理的な削除が有効な各種キー コンテナー オブジェクトを紹介します。

## <a name="integrated-with-key-vault"></a>Key Vault との統合

Key Vault を使用したり、Key Vault と統合したりする他のシナリオとサービスについては、以下の記事で取り上げています。

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) では、Windows の業界標準である [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して、OS とデータ ディスクのボリュームの暗号化を提供します。 ソリューションは Azure Key Vault と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、Azure ストレージで保存中の仮想マシン ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) では、アカウントに格納されているデータを暗号化するオプションを提供します。 キーの管理に関して、Data Lake Store には、Data Lake Store に格納されているすべてのデータを暗号化解除するのに必要なマスター暗号化キー (MEK) を管理するための 2 つのモードが用意されています。 Data Lake Store に MEK の管理を任せることも、Azure Key Vault アカウントを使用して MEK の所有権を保持することもできます。 キー管理のモードは、Data Lake Store アカウントの作成時に指定します。 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) では、独自のテナント キーを管理できます。 たとえば、テナント キーの管理をマイクロソフトに任せる (既定値) 代わりに、組織に適用される特定の規則を遵守する独自のテナント キーを管理できます。 独自のテナント キーの管理は、BYOK (Bring Your Own Key: 独自のキーを持ち込むの意) とも呼ばれます。

## <a name="key-vault-overviews-and-concepts"></a>Key Vault の概要と概念

- [Key Vault の論理的な削除の動作](key-vault-ovw-soft-delete.md)に関するページでは、削除が偶発的なものか意図的なものかにかかわらず、削除されたオブジェクトの回復を可能にする機能について説明します。
- [Key Vault のクライアント調整](key-vault-ovw-throttling.md)に関するページでは、調整の基本的な概念を説明するとともに、アプリへのアプローチも紹介します。
- [Key Vault のストレージ アカウント キーの概要](key-vault-ovw-storage-keys.md)に関するページでは、Key Vault 統合 Azure ストレージ アカウント キーについて説明します。
- [Key Vault のセキュリティ ワールド](key-vault-ovw-security-worlds.md)に関するページでは、リージョンとセキュリティ領域の関係について説明します。

## <a name="social"></a>ソーシャル

- [Key Vault Blog](http://aka.ms/kvblog)
- [Key Vault Forum](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>対応ライブラリ

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core): 識別子からキーを検索し、キーを使用して操作を実行する、**IKey** および **IKeyResolver** インターフェイスを提供します。
- [Microsoft Azure Key Vault Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) : Azure Key Vault の拡張機能を提供します。



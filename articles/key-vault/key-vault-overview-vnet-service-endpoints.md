---
title: Azure Key Vault の仮想ネットワーク サービス エンドポイント - Azure Key Vault | Microsoft Docs
description: Key Vault の仮想ネットワーク サービス エンドポイントの概要
services: key-vault
author: amitbapat
ms.author: ambapat
manager: barbkess
ms.date: 01/02/2019
ms.service: key-vault
ms.topic: conceptual
ms.openlocfilehash: 45499dac3cc50e2b6e79f9ebcb1bc3e7b4330beb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165851"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Azure Key Vault の仮想ネットワーク サービス エンドポイント

Azure Key Vault の仮想ネットワーク サービス エンドポイントを使用すると、指定した仮想ネットワークに対するアクセスを制限できます。 エンドポイントでは、IPv4 (インターネット プロトコル バージョン 4) アドレス範囲のリストへのアクセスを制限することもできます。 このようなソースの外部からお使いのキー コンテナーに接続するユーザーはすべて、アクセスを拒否されます。

この制限には重要な例外が 1 つあります。 ユーザーが信頼できる Microsoft サービスを許可するようにオプトインした場合、このようなサービスからの接続はファイアウォールを介して行われます。 たとえば、Office 365 Exchange Online、Office 365 SharePoint Online、Azure コンピューティング、Azure Resource Manager、Azure Backup などのサービスが含まれます。 このようなユーザーでも有効な Azure Active Directory トークンを提示する必要があり、要求された操作を実行できるアクセス許可 (アクセス ポリシーとして構成) を持っている必要があります。 詳細については、[仮想ネットワーク サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)に関するページを参照してください。

## <a name="usage-scenarios"></a>使用シナリオ

既定で (インターネット トラフィックを含む) すべてのネットワークからのトラフィックに対するアクセスを拒否するように [Key Vault ファイアウォールと仮想ネットワーク](key-vault-network-security.md)を構成することができます。 特定の Azure 仮想ネットワークやパブリック インターネット IP アドレスの範囲からのトラフィックにアクセスを許可できるため、アプリケーションに対してセキュリティで保護されたネットワーク境界を構築することができます。

> [!NOTE]
> Key Vault ファイアウォールと仮想ネットワーク規則は、Key Vault の[データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)にのみ適用されます。 Key Vault のコントロール プレーン操作 (作成、削除、変更操作、アクセス ポリシーの設定、ファイアウォールと仮想ネットワーク規則の設定など) は、ファイアウォールや仮想ネットワーク規則の影響を受けません。

サービス エンドポイントの使用方法の例をいくつか次に示します。

* Key Vault を使用して暗号化キー、アプリケーションのシークレット、証明書を保存しており、パブリック インターネットからキー コンテナーへのアクセスをブロックする場合。
* 自分のアプリケーションまたはリストで指定した少数のホストのみが自分のキー コンテナーに接続できるように、キー コンテナーへのアクセスをロックする場合。
* Azure 仮想ネットワークでアプリケーションを実行していて、この仮想ネットワークはすべての受信および送信トラフィックに対してロックされている。 それでもアプリケーションで、シークレットまたは証明書を取得したり、暗号キーを使用したりするために、キー コンテナーに接続する必要がある場合。

## <a name="configure-key-vault-firewalls-and-virtual-networks"></a>Key Vault のファイアウォールと仮想ネットワークを構成する

ファイアウォールと仮想ネットワークの構成には、次の手順を実行する必要があります。 次の手順は、PowerShell、Azure CLI、Azure portal のどれを使用していても適用されます。

1. 詳細なアクセスログを表示するように [Key Vault のログ記録](key-vault-logging.md)を有効にします。 これは、ファイアウォールと仮想ネットワーク規則によってキー コンテナーにアクセスできない場合の診断に役立ちます。 (このステップは省略可能ですが、強くお勧めします。)
2. ターゲット仮想ネットワークとサブネットの**キー コンテナーのサービス エンドポイント**を有効にします。
3. 特定の仮想ネットワーク、サブネット、および IPv4 アドレス範囲からキー コンテナーへのアクセスを制限するようにキー コンテナーのファイアウォールと仮想ネットワーク規則を設定します。
4. すべての信頼できる Microsoft サービスからこのキー コンテナーにアクセスできるようにする必要がある場合は、**信頼できる Azure サービス**がキー コンテナーに接続できるようにするオプションを有効にします。

詳細については、「[Azure Key Vault のファイアウォールと仮想ネットワークを構成する](key-vault-network-security.md)」を参照してください。

> [!IMPORTANT]
> ファイアウォール ルールを有効にした後は、要求が許可された仮想ネットワークまたは IPv4 アドレス範囲から送信された場合にのみ、ユーザーは Key Vault [データ プレーン](../key-vault/key-vault-secure-your-key-vault.md#data-plane-access-control)の操作を実行できます。 これは、Azure portal から Key Vault にアクセスする場合にも適用されます。 ユーザーは Azure portal からキー コンテナーを参照できますが、クライアント マシンが許可リストに登録されていない場合、キー/シークレット/証明書を一覧表示できない場合があります。 これは、他の Azure サービスによる Key Vault 選択機能にも影響します。 ファイアウォール ルールでクライアント マシンが許可されていない場合、ユーザーはキー コンテナーを一覧表示できても、キーは一覧表示できないことがあります。


> [!NOTE]
> 構成に関する次の制限事項に注意してください。
> * 最大で 127 個の仮想ネットワーク規則と 127 個の IPv4 ルールを指定できます。 
> * "/31" や "/32" のプレフィックス サイズを使用する小さいアドレス範囲はサポートされていません。 これらの範囲を構成するには、個別の IP アドレス ルールを使用します。
> * IP ネットワーク ルールは、パブリック IP アドレスに対してのみ許可されます。 プライベート ネットワーク用に予約されている IP アドレス範囲 (RFC 1918 で定義) は、IP ルールでは許可されません。 プライベート ネットワークには、**10.** 、**172.16-31**、および **192.168.** で始まるアドレスが含まれます。 
> * 現時点でサポートされているのは、IPv4 アドレスのみです。

## <a name="trusted-services"></a>信頼できるサービス

**信頼できるサービスを許可する**オプションが有効な場合にキー コンテナーへのアクセスが許可されている信頼できるサービスの一覧を次に示します。

|信頼できるサービス|使用シナリオ|
| --- | --- |
|Azure Virtual Machines 展開サービス|[ユーザー管理のキー コンテナーから VM に証明書を展開する](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)。|
|Azure Resource Manager テンプレート展開サービス|[デプロイ時にセキュリティで保護された値を渡す](../azure-resource-manager/resource-manager-keyvault-parameter.md)。|
|Azure Disk Encryption ボリューム暗号化サービス|仮想マシンのデプロイ中に、BitLocker キー (Windows VM) または DM パスフレーズ (Linux VM) とキー暗号化キーへのアクセスを許可する。 これにより、[Azure Disk Encryption](../security/azure-security-disk-encryption.md) が有効になります。|
|Azure Backup|[Azure Backup](../backup/backup-introduction-to-azure-backup.md) を使用して、Azure 仮想マシンのバックアップ中に関連するキーとシークレットのバックアップと復元を許可する。|
|Exchange Online と SharePoint Online|[カスタマー キー](https://support.office.com/article/Controlling-your-data-in-Office-365-using-Customer-Key-f2cd475a-e592-46cf-80a3-1bfb0fa17697)を使用する Azure Storage サービス暗号化のためにカスタマー キーへのアクセスを許可する。|
|Azure Information Protection|[Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) のテナント キーへのアクセスを許可する。|
|Azure App Service|[Key Vault を介して Azure Web アプリ証明書をデプロイする](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)。|
|Azure SQL Database|[Azure SQL Database と Data Warehouse のに対する Transparent Data Encryption での Bring Your Own Key のサポート](../sql-database/transparent-data-encryption-byok-azure-sql.md?view=sql-server-2017&viewFallbackFrom=azuresqldb-current)。|
|Azure Storage|[Azure Key Vault で顧客が管理するキーを Storage Service Encryption に使用する](../storage/common/storage-service-encryption-customer-managed-keys.md)。|
|Azure Data Lake Store|顧客が管理するキーによる [Azure Data Lake Store 内のデータの暗号化](../data-lake-store/data-lake-store-encryption.md)。|
|Azure Databricks|[高速で使いやすい、コラボレーション対応の Apache Spark ベースの分析サービス](../azure-databricks/what-is-azure-databricks.md)|
|Azure API Management|[MSI を使用してキー コンテナーからカスタムドメイン用の証明書をデプロイする](../api-management/api-management-howto-use-managed-service-identity.md#use-the-managed-service-identity-to-access-other-resources)|



> [!NOTE]
> 対応するサービスがキー コンテナーにアクセスできるように、関連するキー コンテナー アクセス ポリシーを設定する必要があります。

## <a name="next-steps"></a>次の手順

* [キー コンテナーのセキュリティ保護](key-vault-secure-your-key-vault.md)
* [Azure Key Vault のファイアウォールと仮想ネットワークを構成する](key-vault-network-security.md)

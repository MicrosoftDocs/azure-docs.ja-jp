---
title: キー コンテナーへのアクセスをセキュリティで保護する - Azure Key Vault | Microsoft Docs
description: Azure Key Vault、キー、シークレットのアクセス許可を管理します。 キー コンテナーの認証と承認モデルおよびキー コンテナーをセキュリティで保護する方法について説明します。
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 20c58647b8a6283de4ca2b90c830fe54db927095
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484188"
---
# <a name="secure-access-to-a-key-vault"></a>キー コンテナーへのアクセスをセキュリティで保護する

Azure Key Vault は、暗号化キーとシークレット (証明書、接続文字列、パスワードなど) を保護するクラウド サービスです。 このデータは機密性が高く、ビジネス上重要であるため、承認されたアプリケーションとユーザーだけを許可することで、ご利用のキー コンテナーへのアクセスをセキュリティで保護する必要があります。 この記事では、キー コンテナーのアクセス モデルの概要について説明します。 認証と承認について、およびご利用のキー コンテナーへのアクセスをセキュリティで保護する方法について説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>アクセス モデルの概要

キー コンテナーへのアクセスは、2 つのインターフェイス (**管理プレーン**と**データ プレーン**) を使用して制御します。 管理プレーンでは、Key Vault 自体の管理を行います。 このプレーンでは、キー コンテナーの作成および削除、Key Vault のプロパティの取得、アクセス ポリシーの更新などの操作を行います。 データ プレーンでは、キー コンテナーに格納されているデータを操作します。 キー、シークレット、証明書の追加、削除、および変更を行うことができます。

いずれのプレーン内でもキー コンテナーにアクセスするには、すべての呼び出し元 (ユーザーまたはアプリケーション) が適切な認証と承認を必要とします。 認証では、呼び出し元の ID が確立されます。 承認では、呼び出し元が実行できる操作が決定されます。 

認証については、両方のプレーンで Azure Active Directory (Azure AD) が使用されます。 承認については、管理プレーンではロールベースのアクセス制御 (RBAC) が使用され、データ プレーンでは Key Vault アクセス ポリシーが使用されます。

## <a name="active-directory-authentication"></a>Active Directory 認証

Azure サブスクリプション内でキー コンテナーを作成すると、作成したキー コンテナーは、そのサブスクリプションの Azure AD テナントに自動的に関連付けられます。 両方のプレーンの呼び出し元はすべて、このテナントに登録されている必要があり、キー コンテナーにアクセスするには認証を行う必要があります。 どちらの場合も、アプリケーションは次の 2 つの方法で Key Vault にアクセスできます。

- **ユーザー + アプリケーション アクセス**: サインインしたユーザーの代わりにアプリケーションによって Key Vault にアクセスされます。 この種類のアクセスの例として、Azure PowerShell や Azure portal があります。 ユーザー アクセスは 2 つの方法で許可されます。 ユーザーは任意のアプリケーションから Key Vault にアクセスできます。あるいはユーザーは特定のアプリケーションを使用してアクセスする必要があります (_複合 ID_ と呼ばれます)。
- **アプリケーション専用アクセス**: アプリケーションは、デーモン サービスまたはバックグラウンド ジョブとして実行されます。 アプリケーション ID にはキー コンテナーへのアクセス権が付与されます。

どちらの種類のアクセスの場合も、アプリケーションでは Azure AD を使用して認証が行われます。 アプリケーションでは、アプリケーションの種類に基づいて[サポートされる認証方法](../active-directory/develop/authentication-scenarios.md)が使用されます。 アプリケーションでは、アクセス権を付与するプレーン内のリソース用のトークを取得します。 このリソースは、管理プレーンまたはデータ プレーン内にあるエンドポイントであり、Azure 環境に基づいています。 アプリケーションでは、このトークンを使用して、Key Vault に REST API 要求を送信します。 詳細については、[認証フロー全体](../active-directory/develop/v1-protocols-oauth-code.md)に関するページを確認してください。

1 つのメカニズムで両方のプレーンを認証するモデルには次のようないくつかの利点があります。

- 組織では、組織内のすべてのキー コンテナーへのアクセスを一元的に管理できます。
- 退職したユーザーは、組織内のすべてのキー コンテナーに即座にアクセスできなくなります。
- 組織では、Azure AD のオプションを使用して認証をカスタマイズできます (セキュリティを強化するために多要素認証を有効にするなど)。

## <a name="resource-endpoints"></a>リソースのエンドポイント

アプリケーションによって、エンドポイントを介してプレーンにアクセスされます。 2 つのプレーンに対するアクセス制御は独立して機能します。 キー コンテナー内のキーを使用するためのアクセスをアプリケーションに許可する場合は、Key Vault アクセス ポリシーを使用してデータ プレーンのアクセスを許可します。 ユーザーに対して Key Vault のプロパティおよびタグの読み取りアクセスは許可するが、データ (キー、シークレット、証明書) へのアクセスは許可しない場合は、RBAC を使用して管理プレーンのアクセスを許可します。

次の表に、管理プレーンとデータ プレーンのエンドポイントを示します。

| アクセス&nbsp; プレーン | アクセス エンドポイント | 操作 | アクセス制御メカニズム&nbsp; |
| --- | --- | --- | --- |
| 管理プレーン | **グローバル:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure US Government:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | キー コンテナーの作成、読み取り、更新、削除<br><br>Key Vault アクセス ポリシーの設定<br><br>Key Vault タグの設定 | Azure Resource Manager RBAC |
| データ プレーン | **グローバル:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure US Government:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | キー: 暗号化の解除、暗号化、<br> ラップ解除、ラップ、確認、サイン、<br> 取得、一覧表示、更新、作成、<br> インポート、削除、バックアップ、復元<br><br> シークレット: 取得、一覧表示、設定、削除 | Key Vault アクセス ポリシー |

## <a name="management-plane-and-rbac"></a>管理プレーンと RBAC

管理プレーンでは、RBAC (ロールベースのアクセス制御) を使用して、呼び出し元が実行できる操作を承認します。 RBAC モデルでは、各 Azure サブスクリプションに Azure AD のインスタンスが用意されています。 このディレクトリからユーザー、グループ、アプリケーションにアクセス権を付与します。 Azure サブスクリプションに含まれていて Azure Resource Manager デプロイ モデルを使用しているリソースを管理するためのアクセス権が付与されます。 アクセス権を付与するには、[Azure portal](https://portal.azure.com/)、[Azure CLI](../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs)、または [Azure Resource Manager REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx) を使用します。

リソース グループ内にキー コンテナーを作成し、Azure AD を使用してアクセスを管理します。 リソース グループ内のキー コンテナーを管理する権限をユーザーまたはグループに付与します。 適切な RBAC ロールを割り当てることにより、特定のスコープ レベルでアクセス権を付与します。 キー コンテナーを管理するためのアクセス権をユーザーに付与するには、定義済みの `key vault Contributor` ロールを特定のスコープでそのユーザーに割り当てます。 RBAC ロールには、次のスコープ レベルを割り当てることができます。

- **サブスクリプション**:サブスクリプション レベルで割り当てられた RBAC ロールは、そのサブスクリプション内のすべてのリソース グループとリソースに適用されます。
- **[リソース グループ]**:リソース グループ レベルで割り当てられた RBAC ロールは、そのリソース グループ内のすべてのリソースに適用されます。
- **特定のリソース**: 特定のリソースに対して割り当てられた RBAC ロールは、そのリソースに適用されます。 この場合、リソースは特定のキー コンテナーです。

定義済みのロールが複数あります。 定義済みのロールがニーズに合わない場合は、独自のロールを定義できます。 詳細については、[RBAC: 組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページをご覧ください。

> [!IMPORTANT]
> ユーザーがキー コンテナーの管理プレーンに対する `Contributor` 権限を持っている場合は、そのユーザーは Key Vault アクセス ポリシーを設定することで、データ プレーンへのアクセス権を自分自身に付与できます。 ご利用のキー コンテナーへの `Contributor` ロール アクセス権を持つユーザーの管理は厳密に行う必要があります。 承認されたユーザーだけがコンテナー、キー、シークレット、証明書にアクセスできるようにします。
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>データ プレーンとアクセス ポリシー

データ プレーンのアクセス権を付与するには、キー コンテナーに対して Key Vault アクセス ポリシーを設定します。 このようなアクセス ポリシーを設定するには、ユーザー、グループ、またはアプリケーションにそのキー コンテナーの管理プレーンに対する `Contributor` 権限を与える必要があります。

ユーザー、グループ、またはアプリケーションには、キー コンテナー内のキーやシークレットに対して特定の操作を行うためのアクセス権を付与します。 Key Vault では、1 つのキー コンテナーに対して最大 1,024 個のアクセス ポリシー エントリがサポートされています。 データ プレーンのアクセス権を複数のユーザーに付与するには、Azure AD セキュリティ グループを作成し、そのグループにユーザーを追加します。

<a id="key-vault-access-policies"></a> Key Vault アクセス ポリシーでは、キー、シークレット、および証明書へのアクセス許可が個別に付与されます。 ユーザーに対してはキーへのアクセス権のみ付与でき、シークレットへのアクセス権は付与できません。 キー、シークレット、証明書へのアクセス許可は、コンテナー レベルになります。 Key Vault アクセス ポリシーでは、特定のキー、シークレット、証明書など、細かいオブジェクト レベルのアクセス許可はサポートされていません。 キー コンテナーのアクセス ポリシーを設定するには、[Azure portal](https://portal.azure.com/)、[Azure CLI](../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs)、または [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx) を使用します。

> [!IMPORTANT]
> Key Vault アクセス ポリシーはコンテナー レベルで適用されます。 キーを作成および削除するためのアクセス許可を付与されたユーザーは、そのキー コンテナー内のすべてのキーに対してそれらの操作を実行できます。
>

データ プレーンのアクセスは、[Azure Key Vault の仮想ネットワーク サービス エンドポイント](key-vault-overview-vnet-service-endpoints.md)を使用して制限することができます。 追加のセキュリティ層に対して[ファイアウォールと仮想ネットワークの規則](key-vault-network-security.md)を構成するできます。

## <a name="example"></a>例

この例で開発するアプリケーションでは、SSL 用の証明書が使用され、データの格納に Azure Storage が使用され、署名操作に RSA 2,048 ビット キーが使用されます。 このアプリケーションは、Azure 仮想マシン (VM) (または仮想マシン スケール セット) 内で実行されます。 キー コンテナーを使用してアプリケーション シークレットを格納することができます。 ブートス トラップ証明書を格納することができます。この証明書は、Azure AD に対して認証するアプリケーションによって使用されます。

次に示す格納されたキーとシークレットへのアクセス権が必要です。
- **SSL 証明書**: SSL に使用されます。
- **ストレージ キー**: ストレージ アカウントへのアクセスに使用されます。
- **RSA 2,048 ビット キー**: 署名操作に使用されます。
- **ブートストラップ証明書**:Azure AD による認証に使用されます。 アクセスが許可されると、ストレージ キーをフェッチし、署名に RSA キーを使用できます。

次のロールを定義することで、アプリケーションの管理、デプロイ、および監査を行うことができるユーザーを指定する必要があります。
- **セキュリティ チーム**:CSO (最高セキュリティ責任者) オフィスの IT スタッフまたは同様の共同作成者です。 セキュリティ チームはシークレットの適切な保管を担当します。 シークレットには SSL 証明書、署名に使用される RSA キー、接続文字列、ストレージ アカウント キーなどがあります。
- **開発者と運用者**: アプリケーションを開発して Azure にデプロイするスタッフです。 このチームのメンバーは、セキュリティ スタッフには属していません。 このメンバーには、SSL 証明書や RSA キーなどの機密データへのアクセス権を付与しないでください。 このメンバーがデプロイするアプリケーションのみに機密データへのアクセス権を付与してください。
- **監査者**:このロールは、開発メンバーでも一般的な IT スタッフでもない共同作成者のためのものです。 監査者は、セキュリティ基準に確実に準拠するように証明書、キー、およびシークレットの使用と管理について検証します。 

これ以外に、アプリケーションのスコープ外にあるロールとしてサブスクリプション (またはリソース グループ) 管理者があります。 サブスクリプション管理者は、セキュリティ チームの初期のアクセス許可を設定します。 サブスクリプション管理者は、アプリケーションで必要とされるリソースが属するリソース グループを使用して、セキュリティ チームにアクセス権を付与します。

ロールに対する次の操作を承認する必要があります。

**セキュリティ チーム**
- キー コンテナーを作成する。
- Key Vault のログ記録をオンにする。
- キーとシークレットを追加する。
- ディザスター リカバリーのためにキーのバックアップを作成する。
- 特定の操作に対してアクセス許可をユーザーとアプリケーションに付与するように Key Vault アクセス ポリシーを設定する。
- キーとシークレットが定期的にロールされる。

**開発者と運用者**
- ブートストラップ証明書と SSL 証明書 (拇印)、ストレージ キー (シークレットの URI)、署名用の RSA キー (キーの URI) についてはセキュリティ チームから参照を取得する。
- キーとシークレットにプログラムからアクセスするアプリケーションを開発し、デプロイする。

**監査者**
- Key Vault ログを調べて、キーやシークレットが正しく使用されていること、さらにデータ セキュリティの標準に準拠していることを確認する。

次の表はロールとアプリケーションのアクセス許可をまとめたものです。 

| Role | 管理プレーンのアクセス許可 | データ プレーンのアクセス許可 |
| --- | --- | --- |
| セキュリティ チーム | Key Vault Contributor | キー: バックアップ、作成、削除、取得、インポート、一覧表示、復元<br>シークレット: すべての操作 |
| 開発者と&nbsp;運用者 | Key Vault デプロイ アクセス許可<br><br> **メモ**:このアクセス許可により、デプロイされた VM によりキー コンテナーからシークレットが取り込まれることが許可されます。 | なし |
| 監査者 | なし | キー: 一覧表示<br>シークレット: 一覧表示<br><br> **メモ**:このアクセス許可により、監査者はログに出力されないキーとシークレットの属性 (タグ、ライセンス認証を行った日付、有効期限) を調べることができます。 |
| アプリケーション | なし | キー: 署名<br>シークレット: 取得 |

この 3 つのチーム ロールでは、Key Vault アクセス許可に加えて、他のリソースへのアクセス権も必要です。 VM (または Azure App Service の Web Apps 機能) をデプロイする場合、開発者と運用者はそのような種類のリソースに対する`Contributor`アクセス権が必要になります。 監査者には、Key Vault のログが格納されているストレージ アカウントに対する読み取りアクセス権が必要です。

証明書、アクセス キー、およびシークレットをプログラムでデプロイする方法の詳細については:
- [顧客マネージド キー コンテナーから VM への証明書のデプロイ](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)に関するブログ記事を参照してください。
- [Azure Key Vault クライアント サンプル](https://www.microsoft.com/download/details.aspx?id=45343)をダウンロードしてください。 このコンテンツでは、ブートストラップ証明書を使用して、キー コンテナーにアクセスするための認証を Azure AD に対して行う方法が示されています。

アクセス許可の多くは Azure portal を使用して付与することができます。 きめ細かいアクセス許可を付与するには、Azure PowerShell または Azure CLI を使用します。

このセクションの PowerShell スニペットは、次の前提条件でビルドされています。
- Azure AD 管理者は、3 つのロールを表す次のセキュリティ グループを作成しました: Contoso Security Team、Contoso App DevOps、Contoso App Auditors。 管理者はそれぞれのグループにユーザーを追加しました。
- リソースはすべて、**ContosoAppRG** リソース グループに配置されます。
- Key Vault のログは、**contosologstorage** ストレージ アカウントに格納されます。 
- **ContosoKeyVault** キー コンテナーと **contosologstorage** ストレージ アカウントは、Azure の同じ場所にあります。

サブスクリプション管理者は `key vault Contributor` ロールと `User Access Administrator` ロールをセキュリティ チームに割り当てます。 これらのロールにより、セキュリティ チームはその他のリソースとキー コンテナー (両方とも **ContosoAppRG** リソース グループ内にあります) へのアクセスを管理できます。

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

セキュリティ チームはキー コンテナーを作成し、ログ記録とアクセス許可を設定します。 Key Vault アクセス ポリシーのアクセス許可の詳細については、[Azure Key Vault のキー、シークレット、証明書](about-keys-secrets-and-certificates.md)に関するページをご覧ください。

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

**ContosoAppRG** リソース グループが作成されるサブスクリプションには、定義済みのカスタム ロールのみを割り当てることができます。 他のサブスクリプション内の他のプロジェクトにカスタム ロールを使用するには、そのロールのスコープに該当するサブスクリプションを追加します。

この DevOps スタッフの場合、`deploy/action` アクセス許可に対するカスタム ロールの割り当ての対象はリソース グループです。 **ContosoAppRG** リソース グループに作成された VM のみが、シークレット (SSL 証明書とブートストラップ証明書) へのアクセスを許可されます。 DevOps メンバーによって別のリソース グループに作成された VM は、シークレットの URI が含まれていたとしても、これらのシークレットにアクセスできません。

この例では、シンプルなシナリオを示します。 実際のシナリオはもっと複雑になることがあります。 ニーズに基づいて、ご利用のキー コンテナーに対するアクセス許可を調整できます。 ここでは、アプリケーション内で DevOps スタッフによって使用されるキーとシークレットの参照 (URI と拇印) をセキュリティ チームが提供することを前提としました。 開発者/運用者には、すべてのデータ プレーン アクセスは必要ありません。 ご利用のキー コンテナーをセキュリティで保護する方法に重点を置きました。 [ご利用の VM](https://azure.microsoft.com/services/virtual-machines/security/) や[ストレージ アカウント](../storage/common/storage-security-guide.md)などの Azure リソースをセキュリティで保護する場合も、同じようなことを考慮します。

> [!NOTE]
> この例では、運用環境で Key Vault のアクセスがロックダウンされる仕組みを示しています。 開発者は、アプリケーションを開発する場合、自分のキー コンテナー、VM、ストレージ アカウントを管理するための完全なアクセス許可を持つ独自のサブスクリプションまたはリソース グループを用意する必要があります。

[Key Vault のファイアウォールと仮想ネットワークを構成](key-vault-network-security.md)することで、ご利用のキー コンテナーへのアクセスに追加のセキュリティ保護を設定することをお勧めします。

## <a name="resources"></a>リソース

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC:組み込みのロール](../role-based-access-control/built-in-roles.md)に関するページを参照してください。

* [Resource Manager によるデプロイと従来のデプロイを理解する](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Azure PowerShell を使用して RBAC を管理する](../role-based-access-control/role-assignments-powershell.md) 

* [REST API で RBAC をデプロイする](../role-based-access-control/role-assignments-rest.md)

* [Microsoft Azure 用の RBAC](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    この 2015 Microsoft Ignite カンファレンス ビデオでは、Azure でのアクセス管理とレポート機能について説明されています。 また、Azure AD を使用して Azure サブスクリプションへのアクセスをセキュリティで保護するためのベスト プラクティスについても説明しています。

* [OAuth 2.0 と Azure AD を使用した Web アプリケーションへのアクセスの承認](../active-directory/develop/v1-protocols-oauth-code.md)

* [Key Vault 管理 REST API](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Key Vault アクセス ポリシーの設定など、プログラムでご利用のキー コンテナーを管理するための REST API の参照です。

* [Key Vault REST API](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Key access control (キーのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Secret access control (シークレットのアクセス制御)](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* PowerShell を使用して Key Vault アクセス ポリシーを[設定](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy)および[削除](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy)します。
  
## <a name="next-steps"></a>次の手順

[Key Vault のファイアウォールと仮想ネットワーク](key-vault-network-security.md)を構成します。

管理者用の概要チュートリアルについては、「[Azure Key Vault とは](key-vault-overview.md)」をご覧ください。

Key Vault の使用状況に関するログ記録について詳しくは、「[Azure Key Vault のログ記録](key-vault-logging.md)」をご覧ください。

Azure Key Vault でキーとシークレットを使用する方法の詳細については、[キーとシークレット](https://msdn.microsoft.com/library/azure/dn903623.aspx)に関する記事をご覧ください。

Key Vault に関する質問がある場合は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)にアクセスしてください。

---
title: マネージド HSM へのアクセスをセキュリティで保護する - Azure Key Vault Managed HSM
description: Azure RBAC と Managed HSM ローカル RBAC を使用してマネージド HSM へのアクセスをセキュリティで保護する方法について説明します
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 5a3fcc10f318f2a8065550a48eb2bfb4bbdd4915
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218402"
---
# <a name="secure-access-to-your-managed-hsms"></a>マネージド HSM へのアクセスをセキュリティで保護する

Azure Key Vault Managed HSM は、暗号化キーを保護するクラウド サービスです。 このデータは機密性が高く、ビジネス上重要であるため、承認されたアプリケーションとユーザーからのアクセスのみを許可することで、ご利用のマネージド HSM へのアクセスをセキュリティで保護する必要があります。 この記事では、Managed HSM のアクセス制御モデルの概要について説明します。 認証と承認について説明するほか、ご利用のマネージド HSM へのアクセスをセキュリティで保護する方法について解説します。

このチュートリアルでは、Azure RBAC と Managed HSM ローカル RBAC を使用して職務の分離とアクセス制御を実現する方法を示す簡単な例を紹介します。 Managed HSM アクセス制御モデルの詳細については、「[Managed HSM のアクセス制御](access-control.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この記事の手順を完了するには、次のものが必要です。

* Microsoft Azure サブスクリプション。 サブスクリプションがない場合でも、[無料試用版](https://azure.microsoft.com/pricing/free-trial)にサインアップできます。
* Azure CLI バージョン 2.12.0 以降。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。
* お使いのサブスクリプション内のマネージド HSM。 「[クイック スタート:Azure CLI を使用してマネージド HSM をプロビジョニングしてアクティブにする](quick-create-cli.md)」を参照して、マネージド HSM をプロビジョニングしてアクティブにします。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="example"></a>例

この例では、署名操作に RSA 2048 ビット キーを使用するアプリケーションを開発しています。 このアプリケーションは、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) を備えた Azure 仮想マシン (VM) で実行されます。 署名に使用される RSA キーは、どちらもマネージド HSM に格納されます。

このアプリケーションを管理、デプロイ、監査するロールは次のように指定されています。

- **セキュリティ チーム**:CSO (最高セキュリティ責任者) オフィスの IT スタッフまたは同様の共同作成者です。 セキュリティ チームはキーの適切な保管を担当します。 キーとは、署名のための RSA または EC キー、およびデータ暗号化用の RSA または AES キーです。
- **開発者と運用者**: アプリケーションを開発して Azure にデプロイするスタッフです。 このチームのメンバーは、セキュリティ スタッフには属していません。 このメンバーには、RSA キーなどの機密データへのアクセス権は付与しません。 このメンバーがデプロイするアプリケーションのみに、このような機密データへのアクセス権を付与してください。
- **監査者**:このロールは、開発メンバーでも一般的な IT スタッフでもない共同作成者のためのものです。 監査者は、セキュリティ基準に確実に準拠するように証明書、キー、およびシークレットの使用と管理について検証します。

これ以外に、アプリケーションのスコープ外にあるロールとしてサブスクリプション (またはリソース グループ) 管理者があります。 サブスクリプション管理者は、セキュリティ チームの初期のアクセス許可を設定します。 サブスクリプション管理者は、アプリケーションで必要とされるリソースが属するリソース グループを使用して、セキュリティ チームにアクセス権を付与します。

ロールに対する次の操作を承認する必要があります。

**セキュリティ チーム**
- マネージド HSM を作成する。
- Managed HSM セキュリティ ドメインをダウンロードする (ディザスター リカバリー用)
- ログ記録を有効にします。
- キーを生成またはインポートする。
- ディザスター リカバリー用の Managed HSM バックアップを作成する。
- 特定の操作に対するアクセス許可がユーザーとアプリケーションに付与されるようにローカルの Managed HSM RBAC を設定する。
- キーを定期的に切り替える。

**開発者と運用者**
- 署名に使用される RSA キーの参照 (キーの URI) をセキュリティ チームから取得する。
- キーにプログラムでアクセスするアプリケーションを開発し、デプロイする。

**監査者**
- キーの有効期限を確認し、キーを確実に最新の状態に保つ
- ロールの割り当てを監視して、承認されたユーザーまたはアプリケーションのみがキーにアクセスできるようにする
- マネージド HSM のログを確認して、キーがデータ セキュリティ基準に準拠して正しく使用されていることを確かめる。

次の表は、マネージド HSM にアクセスするためのチームとリソースへのロールの割り当てをまとめたものです。

| Role | 管理プレーン ロール | データ プレーン ロール |
| --- | --- | --- |
| セキュリティ チーム | Managed HSM 共同作成者 | Managed HSM 管理者 |
| 開発者とオペレーター | なし | なし |
| 監査者 | なし | Managed HSM 暗号化監査担当者 |
| アプリケーションによって使用される VM のマネージド ID| なし | Managed HSM 暗号化ユーザー |
| アプリケーションによって使用されるストレージ アカウントのマネージド ID| なし| Managed HSM サービスの暗号化 |

この 3 つのチーム ロールでは、マネージド HSM のアクセス許可に加えて、他のリソースへのアクセス権も必要です。 VM (または Azure App Service の Web Apps 機能) をデプロイする場合、開発者と運用者はそのような種類のリソースに対する`Contributor`アクセス権が必要になります。 監査者には、マネージド HSM のログが格納されているストレージ アカウントに対する読み取りアクセス権が必要です。

管理プレーン ロール (Azure RBAC) を割り当てるには、Azure portal、または Azure CLI や Azure PowerShell などの他の管理インターフェイスを使用できます。 Managed HSM データ プレーン ロールを割り当てるには、Azure CLI を使用する必要があります。

このセクションの Azure CLI スニペットは、次の前提条件で構築されています。

- Azure Active Directory 管理者は、3 つのロールを表すセキュリティ グループを作成しました: Contoso Security Team、Contoso App DevOps、Contoso App Auditors。 管理者はそれぞれのグループにユーザーを追加しました。
- リソースはすべて、**ContosoAppRG** リソース グループに配置されます。
- マネージド HSM のログは、**contosologstorage** ストレージ アカウントに格納されます。
- **ContosoMHSM** マネージド HSM と **contosologstorage** ストレージ アカウントは、Azure の同じ場所にあります。

サブスクリプション管理者は `Managed HSM Contributor` ロールをセキュリティ チームに割り当てます。 このロールにより、セキュリティ チームは既存のマネージド HSM を管理したり、新しいものを作成したりできるようになります。 既存のマネージド HSM がある場合は、それらを管理できるように、"Managed HSM 管理者" ロールを割り当てる必要があります。

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

セキュリティ チームはログを設定し、監査担当者と VM アプリケーションにロールを割り当てます。

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

このチュートリアルでは、ほとんどの部分で、アクセス制御に関連するアクションについてのみ説明しています。 VM へのアプリケーションのデプロイ、ストレージ アカウントのカスタマー マネージド キーによる暗号化の有効化、マネージド HSM の作成に関連するその他のアクションや操作については説明していません。ここでの例では、アクセス制御とロール管理に焦点を絞っています。

この例では、シンプルなシナリオを示します。 実際のシナリオはもっと複雑になることがあります。 ニーズに基づいて、ご利用のキー コンテナーに対するアクセス許可を調整できます。 ここでは、アプリケーション内で DevOps スタッフによって使用されるキーとシークレットの参照 (URI と拇印) をセキュリティ チームが提供することを前提としました。 開発者/運用者には、すべてのデータ プレーン アクセスは必要ありません。 ご利用のキー コンテナーをセキュリティで保護する方法に重点を置きました。 [ご利用の VM](https://azure.microsoft.com/services/virtual-machines/security/) や[ストレージ アカウント](../../storage/blobs/security-recommendations.md)などの Azure リソースをセキュリティで保護する場合も、同じようなことを考慮します。

## <a name="resources"></a>リソース

- [Azure RBAC のドキュメント](../../role-based-access-control/overview.md)
- [Azure RBAC: 組み込みのロール](../../role-based-access-control/built-in-roles.md)に関するページを参照してください。
- [Azure CLI を使用した Azure RBAC の管理](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>次のステップ

管理者用の概要チュートリアルについては、[Managed HSM の概要](overview.md)に関するページをご覧ください。

Managed HSM のログにおける使用状況ログの詳細については、「[Managed HSM のログ](logging.md)」をご覧ください。

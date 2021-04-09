---
title: アカウント暗号化のためのカスタマー マネージド キー
titleSuffix: Azure Storage
description: 独自の暗号化キーを使用して、ストレージ アカウントのデータを保護できます。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、アクセス制御をより柔軟に管理できます。
services: storage
author: tamram
ms.service: storage
ms.date: 03/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 358be21a1f7f13c0c0ce1bf0e57dda53174216b2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517533"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Azure Storage の暗号化のためのカスタマー マネージド キー

独自の暗号化キーを使用して、ストレージ アカウントのデータを保護できます。 カスタマー マネージド キーを指定すると、データを暗号化するキーへのアクセスを保護および制御するために、そのキーが使用されます。 カスタマー マネージド キーを使用すると、アクセス制御をより柔軟に管理できます。

カスタマー マネージド キーを格納するには、Azure Key Vault または Azure Key Vault Managed Hardware Security Module (HSM) (プレビュー) のいずれかを使用する必要があります。 独自のキーを作成してキー コンテナーまたはマネージド HSM に格納することも、Azure Key Vault API を使ってキーを生成することもできます。 ストレージ アカウントとキー コンテナーまたはマネージド HSM は同じリージョンで同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。

Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」をご覧ください。

> [!IMPORTANT]
>
> Azure Key Vault Managed HSM に格納されているカスタマー マネージド キーによる暗号化は、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
>
> Azure Key Vault と Azure Key Vault Managed HSM では、構成用に同じ API と管理インターフェイスがサポートされています。

## <a name="about-customer-managed-keys"></a>カスタマー マネージド キーの概要

次の図は、カスタマー マネージド キーを使用して要求を行うために、Azure Storage で Azure Active Directory とキー コンテナーまたはマネージド HSM をどのように使用するかを示しています。

![Azure Storage でのカスタマー マネージド キーのしくみを示す図](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

次の一覧では、図の番号付きの手順について説明します。

1. Azure Key Vault 管理者が、ストレージ アカウントに関連付けられているマネージド ID に暗号化キーへのアクセス許可を付与します。
2. Azure Storage 管理者が、ストレージ アカウントのカスタマー マネージド キーを使用して暗号化を構成します。
3. Azure Storage は、ストレージ アカウントに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスを認証します。
4. Azure Storage は、Azure Key Vault のカスタマー キーを使用してアカウント暗号化キーをラップします。
5. 読み取り/書き込み操作の場合、Azure Storage は Azure Key Vault に要求を送信して、暗号化と暗号化解除の操作を実行するためにアカウント暗号化キーをラップ解除します。

## <a name="customer-managed-keys-for-queues-and-tables"></a>キューとテーブルのカスタマー マネージド キー

カスタマー マネージド キーがストレージ アカウントに対して有効になっている場合、キューおよびテーブル ストレージに保管されているデータは、カスタマー マネージド キーによって自動的には保護されません。 ストレージ アカウントを作成するときに、必要に応じてこの保護に含めるようにサービスを構成できます。

キューとテーブルのカスタマー マネージド キーがサポートされるストレージ アカウントを作成する方法について詳しくは、「[テーブルとキューのカスタマー マネージド キーがサポートされるアカウントを作成する](account-encryption-key-create.md)」を参照してください。

BLOB ストレージと Azure Files のデータは、カスタマー マネージド キーがストレージ アカウントに対して構成されている場合、カスタマー マネージド キーによって常に保護されます。

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>ストレージ アカウントのカスタマー マネージド キーを有効にする

カスタマー マネージド キーを構成すると、Azure Storage は、関連するキー コンテナーまたはマネージド HSM 内のカスタマー マネージド キーを使用して、アカウントのルート データ暗号化キーをラップします。 カスタマー マネージド キーを有効にしても、パフォーマンスには影響せず、すぐに有効になります。

カスタマー マネージド キーを有効または無効にした場合、あるいはキーまたはキーのバージョンを変更した場合、ルート暗号化キーの保護は変更されますが、Azure Storage アカウントのデータを再暗号化する必要はありません。

カスタマー マネージド キーは、既存のストレージ アカウントでのみ有効にすることができます。 キー コンテナーまたはマネージド HSM は、ストレージ アカウントに関連付けられているマネージド ID にアクセス許可を付与するように構成する必要があります。 マネージド ID は、ストレージ アカウントが作成された後でのみ使用できます。

カスタマー マネージド キーと Microsoft マネージド キーをいつでも切り替えることができます。 Microsoft マネージド キーの詳細については、「[暗号化キーの管理について](storage-service-encryption.md#about-encryption-key-management)」を参照してください。

キー コンテナーにあるカスタマー マネージド キーを使用して Azure Storage 暗号化を構成する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)」を参照してください。 マネージド HSM にあるカスタマー マネージド キーを構成する方法については、「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure AD の 1 つの機能である、Azure リソース用マネージド ID に依存します。 マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID がストレージ アカウントに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはストレージ アカウントを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのストレージ アカウントに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

Azure Storage の暗号化では、2048、3072、および 4096 のサイズの RSA および RSA-HSM キーがサポートされています。 キーの詳細については、「[キーについて](../../key-vault/keys/about-keys.md)」を参照してください。

キー コンテナーまたはマネージド HSM の使用には関連コストがあります。 詳細については、「[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)」を参照してください。

## <a name="update-the-key-version"></a>キーのバージョンを更新する

カスタマー マネージド キーを使用して暗号化を構成する場合、キーのバージョンを更新するには、次の 2 つの方法があります。

- **キーのバージョンを自動的に更新する:** 新しいバージョンが利用可能になった場合にカスタマー マネージド キーを自動的に更新するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を有効にするときに、キーのバージョンを省略します。 キーのバージョンが省略されている場合、Azure Storage によってキー コンテナーまたはマネージド HSM が毎日チェックされ、新しいバージョンのカスタマー マネージド キーがないかどうかが確認されます。 Azure Storage では最新バージョンのキーが自動的に使用されます。
- **キーのバージョンを手動で更新する:** Azure Storage 暗号化に特定のバージョンのキーを使用するには、ストレージ アカウントでカスタマー マネージド キーを使用した暗号化を有効にするときに、そのキー バージョンを指定します。 キーのバージョンを指定した場合、キーのバージョンを手動で更新するまで、Azure Storage ではそのバージョンが暗号化に使用されます。

    キーのバージョンが明示的に指定されている場合は、新しいバージョンを作成するときに、新しいキー バージョンの URI を使用するようにストレージ アカウントを手動で更新する必要があります。 新しいバージョンのキーを使用するようにストレージ アカウントを更新する方法については、「[Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)」または「[Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)」を参照してください。

キーのバージョンを更新すると、ルート暗号化キーの保護が変更されますが、Azure Storage アカウント内のデータは再暗号化されません。 ユーザーがこれ以上操作を行う必要はありません。

> [!NOTE]
> キーを交換するには、コンプライアンス ポリシーに従って、キー コンテナーまたはマネージド HSM に新しいバージョンのキーを作成します。 キーを手動で交換することも、スケジュールに基づいてキーを交換する関数を作成することもできます。

## <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーに対するストレージ アカウントのアクセス権は、いつでも取り消すことができます。 カスタマー マネージド キーに対するアクセス権が取り消された後、あるいはキーが無効化または削除された後、クライアントは BLOB またはそのメタデータに対して読み取りまたは書き込みを行う操作を呼び出すことができません。 次のいずれかの操作を呼び出そうとすると、すべてのユーザーに対してエラー コード 403 (許可されていません) が表示され失敗します。

- [List Blobs](/rest/api/storageservices/list-blobs) (要求 URI で `include=metadata` パラメーターを指定して呼び出す場合)
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob) (`x-ms-meta-name` 要求ヘッダーを使用して呼び出す場合)
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copy Blob From URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Put Block From URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Append Block From URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Put Page From URL](/rest/api/storageservices/put-page-from-url)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)

これらの操作を再び呼び出すには、カスタマー マネージド キーへのアクセス権を復元します。

このセクションに記載されていないすべてのデータ操作は、カスタマー マネージド キーが取り消された後、あるいはキーが無効化または削除された後でも、引き続き実行できます。

カスタマー マネージド キーへのアクセス権を取り消すには、[PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) または [Azure CLI](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) を使用します。

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Azure マネージド ディスク用のカスタマー マネージド キー

カスタマー マネージド キーは、Azure マネージド ディスクの暗号化の管理にも使用できます。 マネージド ディスクに対するカスタマー マネージド キーの動作は、Azure Storage リソースに対する動作とは異なります。 詳細については、Windows の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/disk-encryption.md)」を参照してください。また、Linux の場合は「[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/disk-encryption.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault.md)
- [Azure Key Vault Managed HSM (プレビュー) に格納されているカスタマー マネージド キーによる暗号化を構成する](customer-managed-keys-configure-key-vault-hsm.md)

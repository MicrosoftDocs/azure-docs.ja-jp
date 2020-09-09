---
title: Azure Managed Disks のサーバー側暗号化 - PowerShell
description: Azure Storage では、保存時に暗号化してデータを保護してから、ストレージ クラスターに保存します。 マネージド ディスクの暗号化には Microsoft のマネージド キーを使用できます。また、カスタマー マネージド キーを使用し、独自のキーを使って暗号化を管理できます。
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6174fbeb45c23c0ff04597305c6f65aef05bd26e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815591"
---
# <a name="server-side-encryption-of-azure-disk-storage-for-powershell"></a>PowerShell 用 Azure Disk Storage のサーバー側暗号化

サーバー側暗号化 (SSE) によってデータが保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 SSE では、Azure マネージド ディスク (OS およびデータ ディスク) に格納されているお使いのデータをクラウドに永続化するときに、既定で暗号化します。 

Azure マネージド ディスク内のデータは、利用できる最も強力なブロック暗号の 1 つである 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)を使って透過的に暗号化され、FIPS 140-2 に準拠しています。 Azure マネージド ディスクの基になっている暗号化モジュールについて詳しくは、「[暗号化 API:次世代](/windows/desktop/seccng/cng-portal)」を参照してください。

サーバー側での暗号化は、マネージド ディスクのパフォーマンスには影響しません。また、追加のコストはかかりません。 

> [!NOTE]
> 一時ディスクはマネージド ディスクではなく、ホストで暗号化を有効にしない限り、SSE によって暗号化されません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

プラットフォーム マネージド キーを利用してお使いのマネージド ディスクを暗号化することも、お使いの独自のキーを使用して暗号化を管理することもできます。 独自のキーを使用して暗号化を管理する場合は、マネージド ディスク内のすべてのデータの暗号化と暗号化解除に使用する*カスタマー マネージド キー*を指定できます。 

以降のセクションでは、キー管理の各オプションについてさらに詳しく説明します。

### <a name="platform-managed-keys"></a>プラットフォーム マネージド キー

既定では、マネージド ディスクはプラットフォーム マネージド暗号化キーを使用します。 すべてのマネージド ディスク、スナップショット、イメージ、および既存のマネージド ディスクに書き込まれるデータは、保存時に、プラットフォーム マネージド キーを使用して自動的に暗号化されます。

### <a name="customer-managed-keys"></a>カスタマー マネージド キー

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>制限

現在、カスタマー マネージド キーには次の制限があります。

- この機能がディスクで有効になっている場合、無効にすることはできません。
    これを回避する必要がある場合は、カスタマー マネージド キーを使用していないまったく別のマネージド ディスクに[すべてのデータをコピー](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)する必要があります。
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>ホストでの暗号化 - ご自分の VM データのエンドツーエンド暗号化

エンドツーエンド暗号化は、VM ホスト (お使いの VM が割り当てられている Azure サーバー) から開始します。 お使いの一時ディスク、エフェメラル OS ディスク、永続化された OS およびデータ ディスクのキャッシュ上のデータは、その VM ホストに格納されます。 エンドツーエンド暗号化を有効にした場合、このデータはすべて保存時に暗号化され、暗号化された状態で永続化されているストレージ サービスに送られます。 エンドツーエンド暗号化では、お使いの VM の CPU が使用されないため、お使いの VM のパフォーマンスには影響しません。 

エンドツーエンド暗号化を有効にすると、一時ディスクとエフェメラル OS ディスクは保存時に、プラットフォーム マネージド キーを使用して暗号化されます。 OS とデータ ディスクのキャッシュは、暗号化の種類に応じて、カスタマー マネージド キーまたはプラットフォーム マネージド キーのいずれかを使用して保存時に暗号化されます。 たとえば、ディスクがカスタマー マネージド キーで暗号化されている場合、そのディスクのキャッシュはカスタマー マネージド キーで暗号化されます。ディスクがプラットフォーム マネージド キーで暗号化されている場合、そのディスクのキャッシュはプラットフォーム マネージド キーで暗号化されます。

### <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>保存時の二重暗号化

特定の暗号化アルゴリズム、実装、または侵害されたキーに関連するリスクを懸念しているセキュリティを重視しているお客様が、プラットフォーム マネージド キーを使用した別の暗号化アルゴリズムおよびモードをインフラストラクチャ レイヤーで使用し、追加レイヤーでの暗号化を使用することを選択できるようになりました。 この新しいレイヤーは、二重暗号化を使用して保存時に暗号化される、永続化された OS およびデータ ディスク、スナップショット、イメージのすべてに適用できます。

### <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>サーバー側の暗号化と Azure ディスク暗号化の比較

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) では、Windows の [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。 カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

> [!IMPORTANT]
> カスタマー マネージド キーは、Azure Active Directory (Azure AD) の 1 つの機能である Azure リソース用マネージド ID に依存します。 カスタマー マネージド キーを構成すると、内部でマネージド ID がリソースに自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはマネージド ディスクを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのマネージド ディスクに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure AD ディレクトリ間のサブスクリプションの転送](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [PowerShell](disks-enable-host-based-encryption-powershell.md) または [Azure portal](../disks-enable-host-based-encryption-portal.md) のいずれかで、ホストでの暗号化を使用してエンドツーエンドの暗号化を有効にします。
- [PowerShell](disks-enable-double-encryption-at-rest-powershell.md) または [Azure portal](../disks-enable-double-encryption-at-rest-portal.md) のいずれかを使用して、マネージド ディスクの保存時の二重暗号化を有効にします。
- [PowerShell](disks-enable-customer-managed-keys-powershell.md) または [Azure portal](../disks-enable-customer-managed-keys-portal.md) のいずれかを使用して、マネージド ディスクのカスタマー マネージド キーを有効にします。
- [カスタマー マネージド キーで暗号化されたディスクを作成するための Azure Resource Manager テンプレートを探索する](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault とは](../../key-vault/general/overview.md)

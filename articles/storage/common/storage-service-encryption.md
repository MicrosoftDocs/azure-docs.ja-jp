---
title: Azure Storage Service Encryption for Data at Rest | Microsoft Docs
description: Azure Storage Service Encryption 機能を使用すると、データを格納するときにサービス側で Azure Managed Disks、Azure Blob Storage、Azure Files、Azure Queue Storage、Azure Table Storage を暗号化し、データを取得するときに暗号化を解除することができます。
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 08/01/2018
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: 059091315c378ab6e2bb857e580c02df968b5092
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457175"
---
# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Azure Storage Service Encryption for Data at Rest
Azure Storage Service Encryption for Data at Rest を使用すると、データを保護し、組織のセキュリティとコンプライアンスの要件を満たすことができます。 この機能を使用すると、Azure Storage プラットフォームではデータが Azure Managed Disks、Azure Blob、Queue、Table Storage、または Azure Files に保存される前に自動的に暗号化され、データが取得される前に暗号化が解除されます。 Storage Service Encryption での暗号化、保存時の暗号化、キー管理の処理は、ユーザーにとって透過的に行われます。 Azure Storage プラットフォームに書き込まれるすべてのデータは、現在利用できるブロック暗号化の中でも最強レベルの 256 ビット [AES 暗号化](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)によって暗号化されます。

Storage Service Encryption は新しいストレージ アカウントと既存のストレージ アカウントすべてに対して有効化され、無効にすることはできません。 データは既定で保護されるので、Storage Service Encryption を活用するために、コードまたはアプリケーションを変更する必要はありません。

この機能により次のデータが自動的に暗号化されます。

- Azure Storage サービス:
    - Azure Managed Disks
    - Azure BLOB ストレージ
    - Azure Files
    - Azure Queue Storage
    - Azure Table Storage。  
- パフォーマンス レベル (Standard と Premium の両方)。
- デプロイメント モデル (Azure Resource Manager とクラシックの両方)。

Storage Service Encryption は、Azure Storage サービスのパフォーマンスには影響しません。

Storage Service Encryption には Microsoft が管理する暗号化キーのほか、ユーザー独自の暗号化キーを使うことができます。 独自のキーを使用する方法について詳しくは、「[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](storage-service-encryption-customer-managed-keys.md)」をご覧ください。

## <a name="view-encryption-settings-in-the-azure-portal"></a>暗号化設定を Azure ポータルに表示する
Storage Service Encryption の設定を表示するには、[Azure Portal](https://portal.azure.com) にログインしてストレージ アカウントを選択します。 **[設定]** ウィンドウで **[暗号化]** 設定を選択します。

![暗号化設定が表示されたポータルのスクリーンショット](./media/storage-service-encryption/image1.png)

## <a name="faq-for-storage-service-encryption"></a>Storage Service Encryption のよくあるご質問
**Resource Manager ストレージ アカウントのデータはどのように暗号化すればよいですか?**  
Storage Service Encryption は、すべてのストレージ アカウント (クラシックと Resource Manager) に対して有効になります。暗号化が有効になる前に作成されたストレージ アカウント内の既存のファイルは、バックグラウンド暗号化プロセスによってさかのぼって暗号化されます。

**Storage Service Encryption は、ストレージ アカウントを作成すると、既定で有効になりますか?**  
はい。Storage Service Encryption は、すべてのストレージ アカウントとすべての Microsoft Azure Storage サービスに対して有効です。

**Resource Manager ストレージ アカウントがあります。これに対して Storage Service Encryption を有効にできますか?**  
Storage Service Encryption は、既定で既存のすべての Resource Manager ストレージ アカウントで有効になります。 これは Azure Blob Storage、Azure Files、AzureQueue Storage、Table Storage でサポートされています。 

**ストレージ アカウントで暗号化を無効にできますか?**  
暗号化は既定で有効になっており、ストレージ アカウントの暗号化を無効にするプロビジョニングはありません。 

**Storage Service Encryption を有効にすると、Azure Storage の料金はどれくらい増えますか?**  
追加コストはかかりません。

**独自の暗号化キーを使用できますか?**  
はい、Azure Blob Storage と Azure Files では、独自の暗号化キーを使用できます。 現在、ユーザーが管理するキーは Azure Managed Disks ではサポートされていません。 詳細については、「[ユーザーが管理する Azure Key Vault キーを Storage Service Encryption に使用する](storage-service-encryption-customer-managed-keys.md)」を参照してください。

**暗号化キーへのアクセスを取り消すことはできますか?**  
はい。Azure Key Vault で[独自の暗号化キーを使用している](storage-service-encryption-customer-managed-keys.md)場合は、取り消すことができます。

**Storage Service Encryption と Azure Disk Encryption の違いを教えてください。**  
Azure Disk Encryption は、BitLocker、DM-Crypt、Azure KeyVault などの OS ベースのソリューション間に統合を提供します。 Storage Service Encryption は、Azure Storage プラットフォーム レイヤーの仮想マシンの以下にネイティブで暗号化を提供します。

**クラシック ストレージ アカウントがあります。これに対して Storage Service Encryption を有効にできますか?**  
Storage Service Encryption は、すべてのストレージ アカウント (クラシックと Resource Manager) に対して有効になります。

**クラシック ストレージ アカウントのデータを暗号化するにはどうすればよいですか?**  
既定で有効にされている暗号化を使用して、Azure Storage サービスに保存されているすべてのデータは自動的に暗号化されます。 

**Azure PowerShell および Azure CLI を使用して、Storage Service Encryption を有効にしたストレージ アカウントを作成できますか?**  
Storage Service Encryption は、既定でストレージ アカウント (クラシックまたは Resource Manager) の作成時に有効にされます。 Azure PowerShell と Azure CLI の両方を使用して、アカウントのプロパティを確認できます。

**ストレージ アカウントを、geo 冗長レプリケートされるように設定してあります。Storage Service Encryption では、冗長コピーも暗号化されますか?**  
はい。ストレージ アカウントのすべてのコピーが暗号化されます。 すべての冗長コピー (ローカル冗長ストレージ、ゾーン冗長ストレージ、geo 冗長ストレージ、読み取りアクセス geo 冗長ストレージ) がサポートされます。

**Storage Service Encryption は、特定のリージョンだけで許可されますか?**  
すべてのリージョンで Storage Service Encryption を使用できます。

**Storage Service Encryption は FIPS 140-2 に準拠していますか?**  
はい。Storage Service Encryption は FIPS 140-2 に準拠しています。 Storage Service Encryption の下にある暗号化モジュールの詳細については、「[Cryptography API: Next Generation (暗号化 API: 次世代)](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)」を参照してください。

**問題やフィードバックの連絡先はどこですか?**  
Storage Service Encryption に関する問題またはフィードバックは、[ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) にご連絡ください。

## <a name="next-steps"></a>次の手順
Azure Storage で提供される包括的なセキュリティ機能のセットを利用して、開発者はセキュリティで保護されたアプリケーションを構築できます。 詳しくは、「[Azure Storage セキュリティ ガイド](../storage-security-guide.md)」をご覧ください。

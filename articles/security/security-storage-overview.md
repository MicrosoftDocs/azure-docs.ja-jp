---
title: "Azure Storage で使用できるセキュリティ機能 | Microsoft Docs"
description: " この記事では、Azure Storage で使用できる Azure のコア セキュリティ機能の概要について説明します。. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: b5edb42f7fac6943a72e02a85a4cbc32300b9f38
ms.openlocfilehash: 2db6effdaf2e23d00eefdde82d074dd5d47857d7
ms.lasthandoff: 01/24/2017


---
# <a name="azure-storage-security-overview"></a>Azure Storage のセキュリティの概要
Azure Storage は、持続性、可用性、スケーラビリティで顧客のニーズに応える最新のアプリケーションのためのクラウド ストレージ ソリューションです。 Azure Storage には、包括的な一連のセキュリティ機能が用意されています。

* ストレージ アカウントを、ロールベースのアクセス制御と Azure Active Directory を使用して保護できます。
* アプリケーションと Azure の間で送信されるデータを、クライアント側暗号化、HTTPS、または SMB 3.0 使用して保護できます。
* Storage Service Encryption を使用して Azure Storage に書き込むときに、データが自動的に暗号化されるように設定することができます。
* 仮想マシンに使用する OS とデータ ディスクを、Azure Disk Encryption を使用して暗号化されるように設定できます。
* Shared Access Signature を使用して、Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権を付与できます。
* Storage Analytics を使用して、ユーザーがストレージにアクセスするときに使用した認証方法を追跡できます。

Azure Storage でのセキュリティの詳細については、「 [Azure Storage セキュリティ ガイド](../storage/storage-security-guide.md)」を参照してください。 このガイドには、ストレージ アカウント キー、転送中および保存時のデータ暗号化、Storage Analytics など、Azure Storage のセキュリティ機能が詳しく説明されています。

この記事では、Azure Storage で使用できる Azure のセキュリティ機能の概要について説明します。 各機能の詳細について説明する記事へのリンクが用意されているため、さらに詳しく学習できます。

この記事では、以下の主要機能について扱います。

* ロールベースのアクセス制御
* ストレージ オブジェクトへの委任されたアクセス
* 転送中の暗号化
* 保存時の暗号化/Storage Service Encryption
* Azure Disk Encryption
* Azure Key Vault

## <a name="role-based-access-control-rbac"></a>ロール ベースのアクセス制御 (RBAC)
ロールベースのアクセス制御 (RBAC) を使用して、ストレージ アカウントをセキュリティで保護できます。 データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。 これらのアクセス権は、グループおよびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てることによって付与します。 [組み込み RBAC ロール](../active-directory/role-based-access-built-in-roles.md)(ストレージ アカウントの共同作成者など) を使用して、ユーザーに権限を割り当てることができます。

詳細情報:

* [Azure Active Directory のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>ストレージ オブジェクトへの委任されたアクセス
Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS により、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。 この制限付きアクセス許可を付与するとき、アカウント アクセス キーを共有する必要はありません。 SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。

詳細情報:

* [SAS モデルについて](../storage/storage-dotnet-shared-access-signature-part-1.md)
* [BLOB ストレージでの SAS の作成と使用](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>転送中の暗号化
転送中の暗号化は、ネットワーク間でデータを転送するときにデータを保護するメカニズムです。 Azure Storage では、以下を使用してデータをセキュリティ保護できます。

* [トランスポートレベルの暗号化](../storage/storage-security-guide.md#encryption-in-transit)(Azure Storage の内外にデータを転送する場合の HTTPS など)。
* [ワイヤ暗号化](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)(Azure ファイル共有の SMB 3.0 暗号化など)。
* [クライアント側の暗号化](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)(Storage にデータを転送する前にデータを暗号化し、Storage からデータを転送した後にデータを復号化します)。

クライアント側の暗号化の詳細:

* [Client-Side Encryption for Microsoft Azure Storage (Microsoft Azure Storage のクライアント側の暗号化)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (クラウドのセキュリティ管理シリーズ: 転送中のデータの暗号化)](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>保存時の暗号化
多くの組織にとって、データ プライバシー、コンプライアンス、データ主権を確保するうえで [保存データの暗号化](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) は欠かせません。 Azure には、“保存時の“ データの暗号化を提供する機能が&3; つあります。

* [Storage Service Encryption](../storage/storage-security-guide.md#encryption-at-rest) を使用すると、ストレージ サービスが Azure Storage にデータを書き込むときに自動的に暗号化するように要求できます。
* [クライアント側の暗号化](../storage/storage-security-guide.md#client-side-encryption) には、保存時の暗号化機能もあります。
* [Azure Disk Encryption](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) を使用すると、IaaS 仮想マシンに使用される OS ディスクとデータ ディスクを暗号化できます。

Storage Service Encryption の詳細:

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) は、[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) で使用できます。 他の Azure Storage の種類の詳細については、[File Storage](https://azure.microsoft.com/services/storage/files/)、[ディスク (Premium Storage)](https://azure.microsoft.com/services/storage/premium-storage/)、[Table Storage](https://azure.microsoft.com/services/storage/tables/)、および [Queue Storage](https://azure.microsoft.com/services/storage/queues/) に関するページをそれぞれ参照してください。
* [Azure Storage Service Encryption for Data at Rest](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
仮想マシン (VM) 向けの Azure Disk Encryption は、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で管理するキーとポリシーを使用して VM ディスク (ブート ディスクとデータ ディスクを含む) を暗号化するソリューションです。組織のセキュリティとコンプライアンスの要件に対処する際に大きな効果を発揮します。

Linux および Windows オペレーティング システムに対して機能します。 Key Vault により、ディスク暗号化キーの保護、管理、および監査を行うこともできます。 VM ディスク内のすべてのデータは、Azure Storage アカウントでの保存中に、業界標準の暗号化技術を使用して暗号化されます。 Windows 向けの Disk Encryption ソリューションのベースは [Microsoft BitLocker ドライブ暗号化](https://technet.microsoft.com/library/cc732774.aspx)であり、Linux 向けソリューションは [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) がベースになっています。

詳細情報:

* [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Windows と Linux IaaS Virtual Machines の Azure Disk Encryption)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Disk Encryption では [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を利用してディスク暗号化キーとシークレットを Key Vault サブスクリプションで制御および管理でき、Azure Storage に保存されている仮想マシン ディスク内のすべてのデータを確実に暗号化できます。 Azure Key Vault を使用して、キーとポリシーの使用状況を監査する必要があります。

詳細情報:

* [Azure Key Vault とは](../key-vault/key-vault-whatis.md)
* [Azure Key Vault の概要](../key-vault/key-vault-get-started.md)


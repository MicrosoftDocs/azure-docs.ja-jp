---
title: Azure Storage で使用できるセキュリティ機能 | Microsoft Docs
description: この記事では、Azure Storage で使用できる Azure のコア セキュリティ機能の概要について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2019
ms.author: terrylan
ms.openlocfilehash: ec0e8ae1bf657cda59f3d133db23106436e184e3
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326758"
---
# <a name="azure-storage-security-overview"></a>Azure Storage のセキュリティの概要

Azure Storage は、持続性、可用性、スケーラビリティで顧客のニーズに応える最新のアプリケーションのためのクラウド ストレージ ソリューションです。 Azure Storage には、包括的な一連のセキュリティ機能が用意されています。 次のようにすることができます。

* ストレージ アカウントを、ロールベースのアクセス制御 (RBAC) と Azure Active Directory を使って、セキュリティで保護できます。
* アプリケーションと Azure の間で送信されるデータを、クライアント側暗号化、HTTPS、または SMB 3.0 を使って、セキュリティで保護できます。
* Storage Service Encryption を使って Azure Storage に書き込まれるときに、データが自動的に暗号化されるように設定できます。
* 仮想マシン (VM) によって使われる OS とデータ ディスクを、Azure Disk Encryption を使って暗号化されるように設定できます。
* Shared Access Signature (SAS) を使って、Azure Storage 内のデータ オブジェクトに対する委任されたアクセス権を付与できます。
* 分析を使って、ストレージにアクセスするときにユーザーが使っている認証方法を追跡できます。

Azure Storage でのセキュリティの詳細については、「 [Azure Storage セキュリティ ガイド](../storage/common/storage-security-guide.md)」を参照してください。 このガイドでは、Azure Storage のセキュリティ機能について詳しく説明します。 このような機能としては、ストレージ アカウント キー、転送中と保存中のデータの暗号化、Storage Analytics などがあります。


この記事では、Azure Storage で使用できる Azure のセキュリティ機能の概要について説明します。 各機能の詳細記事へのリンクが用意されているため、さらに詳しく学習できます。

## <a name="role-based-access-control"></a>ロールベースのアクセス制御

ロールベースのアクセス制御を使って、ストレージ アカウントをセキュリティで保護できます。 データ アクセスにセキュリティ ポリシーを適用する組織では、[必知事項](https://en.wikipedia.org/wiki/Need_to_know)と[最小権限](https://en.wikipedia.org/wiki/Principle_of_least_privilege)のセキュリティ原則に基づいてアクセスを制限することが不可欠です。 これらのアクセス権は、グループおよびアプリケーションに適切な RBAC ロールを特定のスコープで割り当てることによって付与します。 [組み込み RBAC ロール](../role-based-access-control/built-in-roles.md)(ストレージ アカウントの共同作成者など) を使用して、ユーザーに権限を割り当てることができます。

詳細情報:

* [Azure Active Directory のロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>ストレージ オブジェクトへの委任されたアクセス

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 SAS により、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。 この制限付きアクセス許可を付与するとき、アカウント アクセス キーを共有する必要はありません。

SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。

詳細情報:

* [SAS モデルについて](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [BLOB ストレージでの SAS の作成と使用](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>転送中の暗号化

転送中の暗号化は、ネットワーク間でデータを転送するときにデータを保護するメカニズムです。 Azure Storage では、以下を使用してデータをセキュリティ保護できます。

* [トランスポートレベルの暗号化](../storage/common/storage-security-guide.md#encryption-in-transit)。Azure Storage の内外にデータを転送する場合の HTTPS など。
* [ワイヤ暗号化](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares)。Azure ファイル共有の SMB 3.0 暗号化など。
* [クライアント側の暗号化](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage)。ストレージにデータを転送する前にデータを暗号化し、ストレージからデータを転送した後にデータを復号化します。

クライアント側の暗号化の詳細:

* [Client-Side Encryption for Microsoft Azure Storage (Microsoft Azure Storage のクライアント側の暗号化)](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Cloud security controls series: Encrypting Data in Transit (クラウドのセキュリティ管理シリーズ: 転送中のデータの暗号化)](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>保存時の暗号化

多くの組織にとって、データ プライバシー、コンプライアンス、データ主権を確保するうえで[保存データの暗号化](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/)は欠かせません。 Azure には、保存時のデータの暗号化を提供する機能が 3 つあります。

* [Storage Service Encryption](../storage/common/storage-security-guide.md#encryption-at-rest) は常に有効化され、ストレージ サービス データが Azure Storage に書き込まれる際には、それらのデータが自動的に暗号化されます。
* [クライアント側の暗号化](../storage/common/storage-security-guide.md#client-side-encryption)には、保存時の暗号化機能もあります。
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) を使用すると、IaaS 仮想マシンに使用される OS ディスクとデータ ディスクを暗号化できます。

Storage Service Encryption の詳細:

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) は、[Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) で使用できます。 他の Azure Storage の種類の詳細については、[Azure Files](https://azure.microsoft.com/services/storage/files/)、[Table Storage](https://azure.microsoft.com/services/storage/tables/)、および [Queue Storage](https://azure.microsoft.com/services/storage/queues/) に関するページをそれぞれ参照してください。
* [Azure Storage Service Encryption for Data at Rest](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

仮想マシン用の Azure Disk Encryption は、組織のセキュリティとコンプライアンスの要件に対応するのに役立ちます。 この機能は、ユーザーが [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で制御できるキーとポリシーを使って、VM ディスク (ブート ディスクとデータ ディスクを含む) を暗号化します。

Linux および Windows オペレーティング システムに対して機能します。 Key Vault により、ディスク暗号化キーの保護、管理、および監査を行うこともできます。 VM ディスク内のすべてのデータは、Azure ストレージ アカウントでの保存中に、業界標準の暗号化技術を使用して暗号化されます。 Windows 向けの Disk Encryption ソリューションのベースは [Microsoft BitLocker ドライブ暗号化](https://technet.microsoft.com/library/cc732774.aspx)であり、Linux 向けソリューションは [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) がベースになっています。

詳細情報

* [Azure Disk Encryption for Windows and Linux IaaS Virtual Machines (Windows と Linux IaaS Virtual Machines の Azure Disk Encryption)](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>ファイアウォールと仮想ネットワーク

Azure Storageでは、ストレージ アカウントのファイアウォール規則を有効にすることができます。 有効にすると、他の Azure サービスからの要求を含めて、データに対する受信要求がブロックされます。 トラフィックを許可する例外を構成できます。 ファイアウォール規則は、既存のストレージ アカウントに対して有効にするか、作成時に有効にできます。

この機能は、許可された一連のネットワークに対してストレージ アカウントをセキュリティで保護するときに使用する必要があります。

Azure Storage ファイアウォールと仮想ネットワークの詳細については、「[Azure Storage ファイアウォールおよび仮想ネットワークを構成する](../storage/common/storage-network-security.md)」を参照してください。

## <a name="azure-data-box"></a>Azure Data Box

ネットワークを利用できない場合は、Data Box、Data Box Disk、Data Box Heavy の各デバイスを使えば、大量のデータを Azure に転送するのに役立ちます。 これらのオフライン データ転送デバイスは、貴社と Azure データセンターとの間で運送業者を介してやり取りされます。 転送中のデータは AES 暗号化を使って保護され、また、アップロード後はサニタイズ処理が適用され、データがデバイスから削除されます。

Data Box Edge と Data Box Gateway は、ネットワーク ストレージのゲートウェイとして機能して自社サイトと Azure の間でデータを管理するオンライン データ転送製品です。 Data Box Edge は、Azure との間でデータを転送する機能と、人工知能 (AI) に対応したエッジ計算によってデータを処理する機能とを備えたオンプレミスのネットワーク デバイスです。 Data Box Gateway は、ストレージ ゲートウェイ機能を備えた仮想アプライアンスです。

詳細情報:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>高度な脅威保護

Azure Storage では Advanced Threat Protection が提供され、ストレージ アカウントに対する通常と異なる潜在的に有害なアクセスの試行すなわちストレージ アカウントの悪用を検出するセキュリティ インテリジェンスが強化されます。 Advanced Threat Protection は、BLOB ストレージに対する不審な読み取り、書込み、または削除要求がないか Azure Storage 診断ログを監視します。

Advanced Threat Protection のアラートは [Azure Security Center](https://azure.microsoft.com/services/security-center/) でご確認いただけます。 Azure Security Center では、検出されたすべての疑わしいアクティビティについての詳細が提供され、潜在的な脅威を調査して修復する方法が推奨されます。

詳細情報:

* [Azure Storage Advanced Threat Protection の概要](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) を使って、キー コンテナー サブスクリプションのディスク暗号化キーとシークレットの制御と管理を助けます。 また、仮想マシン ディスク内のすべてのデータが、Azure Storage での保存時に暗号化されることを保証します。 Azure Key Vault を使用して、キーとポリシーの使用状況を監査する必要があります。

詳細情報

* [Azure Key Vault とは](../key-vault/key-vault-overview.md)

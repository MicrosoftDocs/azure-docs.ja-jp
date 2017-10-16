---
title: "Azure Storage を使用して PaaS アプリケーションをセキュリティで保護する | Microsoft Docs"
description: " PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage のセキュリティ ベスト プラクティスについて説明します。 "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: TomShinder
ms.openlocfilehash: 62c7b6706268e3c6e329f90651125fe299f61d67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Azure Storage を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

[Azure Storage セキュリティ ガイド](../storage/common/storage-security-guide.md)には、Azure Storage とセキュリティに関する詳細な情報が記載されています。  この記事では、セキュリティ ガイドに記載されているいくつかの概念をおおまかに説明し、セキュリティ ガイドへのリンクなど、詳細を確認するためのソースを紹介しています。

## <a name="azure-storage"></a>Azure Storage (Azure Storage)
Azure は、オンプレミスでは簡単に実現できない方法で、ストレージをデプロイして使用することを可能にしています。 Azure Storage を使用すれば、比較的少ない労力で高レベルのスケーラビリティと可用性を実現できます。 Azure Storage は、Windows と Linux の Azure Virtual Machines の基盤となるだけでなく、大規模な分散アプリケーションもサポートしています。

Azure Storage では、4 つのサービスが提供されます。Blob Storage、Table Storage、Queue Storage、および File Storage です。 詳細については、「[Introduction to Microsoft Azure Storage (Microsoft Azure Storage の概要)](../storage/storage-introduction.md)」をご覧ください。

## <a name="best-practices"></a>ベスト プラクティス
この記事では、以下のベスト プラクティスを扱います。

- アクセスの保護:
   - Shared Access Signatures (SAS)
   - 管理ディスク
   - ロール ベースのアクセス制御 (RBAC)

- ストレージ暗号化:
   - 価値の高いデータのクライアント側暗号化
   - 仮想マシン (VM) 用の Azure Disk Encryption
   - Storage Service Encryption

## <a name="access-protection"></a>アクセスの保護
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>ストレージ アカウント キーの代わりに Shared Access Signature を使用する

通常、IaaS ソリューションでは、Windows Server または Linux の仮想マシンの実行中は、アクセス制御メカニズムを使用して漏えいや改ざんの脅威からファイルを保護します。 Windows では[アクセス制御リスト (ACL)](../virtual-network/virtual-networks-acl.md) を使用し、Linux ではおそらく [chmod](https://en.wikipedia.org/wiki/Chmod) を使用することになります。 基本的には、データ センターにあるサーバーのファイルを保護するのであれば、これが現在取り得る適切な手段です。

PaaS の場合は異なります。 Microsoft Azure にファイルを格納する最も一般的な方法の 1 つは、[Azure Blob Storage](../storage/storage-dotnet-how-to-use-blobs.md) を使用することです。 Blob Storage と他のファイル ストレージとの違いは、ファイル I/O と、ファイル I/O を含めた保護方法です。

アクセス制御は非常に重要です。 Azure Storage のアクセス制御のために、[ストレージ アカウント作成](../storage/common/storage-create-storage-account.md)時に、システムは 2 つの 512 ビット ストレージ アカウント キー (SAK) を生成します。 このレベルのキーの冗長性があれば、ルーチン キーの交換中にサービスを中断せずに済みます。

ストレージ アクセス キーは優先度の高い機密情報であり、ストレージ アクセス制御の担当者のみがこれにアクセスできるようにする必要があります。 悪意のあるユーザーがキーにアクセスできてしまうと、ストレージの制御を完全に奪われ、ストレージのファイルの置換、削除、追加が行われる危険があります。 これにはマルウェアや、お客様の組織や顧客を侵害する可能性のある他のタイプのコンテンツが含まれます。

それでも、ストレージ内のオブジェクトにアクセスする手段は必要です。 より詳細なアクセス制御を行う必要がある場合は、[Shared Access Signature](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS) が役立ちます。 SAS を使用すれば、事前に定義した期間に特定の許可を与えて、ストレージ内の特定のオブジェクトを共有できます。 Shared Access Signature では、次のことを定義できます。

- SAS が有効な間隔 (開始時刻と有効期限など)。
- SAS によって付与されるアクセス許可。 たとえば、BLOB 上の SAS では BLOB への読み取りと書き込み許可がユーザーに付与されますが、アクセス許可は削除されません。
- SAS を受け入れる Azure Storage の省略可能な IP アドレスまたは範囲の IP アドレス。 たとえば、組織に属する IP アドレスの範囲を指定できます。 これは、SAS のセキュリティの別の尺度になります。
- SAS を受け入れる Azure Storage のプロトコル。 この省略可能なパラメーターを使用すると、HTTPS を使用したクライアントのアクセスを制限できます。

SAS を使用すれば、ストレージ アカウント キーを知らせることなく、望む方法でコンテンツを共有できます。 アプリケーションで常に SAS を使用すれば、ストレージ アカウント キーを侵害されることなくストレージ リソースを共有できます。

詳細については、「[Using Shared Access Signatures (SAS) (Shared Access Signature (SAS) の使用)](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。 潜在的なリスクと、リスクを軽減するための推奨事項の詳細については、[SAS 使用時のベスト プラクティス](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関する記事をご覧ください。

### <a name="use-managed-disks-for-vms"></a>VM の管理対象ディスクを使用する

[Azure Managed Disks](../storage/storage-managed-disks-overview.md) を選択すると、Azure は、VM ディスクに使用するストレージ アカウントを管理します。 ディスクの種類 (Premium または Standard) とディスク サイズを選択するだけでかまいません。あとは Azure Storage が行います。 他に複数のストレージ アカウントが必要になるような、スケーラビリティの制限については心配する必要はありません。

詳細については、[Premium 管理ディスクと非管理ディスクについてよく寄せられる質問](../storage/storage-faq-for-disks.md)に関する記事をご覧ください。

### <a name="use-role-based-access-control"></a>ロールベースのアクセス制御を使用する

Shared Access Signature (SAS) を使用すれば、ストレージ アカウント キーを知らせずに、ストレージ アカウントのオブジェクトに制限付きでアクセスする権限を他のクライアントに付与できることは既に述べました。 ストレージ アカウントに対する特定の操作に関連するリスクが、SAS の利点より重大である場合もあります。 別の方法でアクセスを管理した方が容易である場合もあります。

アクセスを管理する別の方法として、[Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md) (RBAC) を使用する方法があります。 RBAC を使用すれば、知る必要性と最小権限という 2 つのセキュリティ原則に基づいて、実際に必要となるアクセス許可を従業員に付与することに注力できます。 アクセス許可が多すぎると、アカウントが攻撃者による悪用の対象になりかねません。 アクセス許可が少なすぎると、従業員は業務を効率的に遂行できなくなる可能性があります。 RBAC は、Azure の詳細なアクセス管理によってこの問題を解決します。 これは、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。

Azure の組み込み RBAC ロールを利用して、ユーザーに権限を割り当てることができます。 クラウド事業者は、ストレージ アカウントを管理する必要がある場合はストレージ アカウント共同作業者ロール、従来のストレージ アカウントを管理する場合は従来のストレージ アカウント共同作業者ロールの使用を検討してください。 VM を管理する必要はあるが接続先の仮想ネットワークまたはストレージ アカウントを管理する必要がないというクラウド事業者は、作業者に仮想マシン共同作業者ロールを追加することを検討してください。

RBAC などの機能を利用したデータ アクセス制御を適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 これにより、一部のユーザーが最初の段階でアクセスすべきではないデータにアクセスできてしまい、データのセキュリティ侵害につながる恐れがあります。

RBAC の詳細については、次の記事をご覧ください。

- [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)
- [Azure ロールベースのアクセス制御の組み込みロール](../active-directory/role-based-access-built-in-roles.md)
- RBAC を使用してストレージ アカウントをセキュリティ保護する方法の詳細については、[Azure Storage セキュリティ ガイド](../storage/common/storage-security-guide.md)をご覧ください。

## <a name="storage-encryption"></a>ストレージ暗号化
### <a name="use-client-side-encryption-for-high-value-data"></a>価値の高いデータのクライアント側暗号化を使用する

クライアント側暗号化を使用すると、Azure Storage にアップロードする前に転送中データをプログラムで暗号化でき、ストレージから取得するときにデータをプログラムで複合化できます。  これは転送中のデータの暗号化ですが、保存データの暗号化も可能です。  クライアント側暗号化が最も安全にデータを暗号化できる方法ですが、アプリケーションのプログラムを変更し、キー管理プロセスを組み込む必要があります。

また、クライアント側暗号化では、暗号化キーを制御できる担当者を 1 人に限定できます。  独自の暗号化キーを生成し、管理することができます。  クライアント側暗号化は、エンベロープ手法を使用して、Azure ストレージ クライアント ライブラリでコンテンツ暗号化キー (CEK) を生成し、キーの暗号化キー (KEK) を使用して CEK をラップ (暗号化) します。 KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルで管理することも、[Azure Key Vault](../key-vault/key-vault-whatis.md) に格納することもできます。

クライアント側暗号化は、Java と .NET のストレージ クライアント ライブラリに組み込まれています。  クライアント アプリケーション内のデータの暗号化と、独自の暗号化キーの生成および管理の詳細については、「[Client-Side Encryption and Azure Key Vault for Microsoft Azure Storage (Microsoft Azure Storage のクライアント側暗号化と Azure Key Vault)](../storage/storage-client-side-encryption.md)」をご覧ください。

### <a name="azure-disk-encryption-for-vms"></a>VM の Azure Disk Encryption
Azure Disk Encryption は、Windows と Linux の IaaS 仮想マシン ディスクを暗号化するのに役立つ機能です。 Azure Disk Encryption では、業界標準である Windows の BitLocker 機能と Linux の DM-Crypt 機能を利用して、OS およびデータ ディスクのボリュームの暗号化を提供します。 このソリューションは Azure Key Vault と統合されており、ディスクの暗号化キーとシークレットは Key Vault サブスクリプションで制御および管理できます。 またこのソリューションでは、仮想マシン ディスク上のすべてのデータが、Azure Storage での保存時に暗号化されます。

「[Windows および Linux IaaS VM の Azure ディスク暗号化](azure-security-disk-encryption.md)」をご覧ください。

### <a name="storage-service-encryption"></a>Storage Service Encryption
File Storage の [Storage Service Encryption](../storage/storage-service-encryption.md) を有効にすると、データは自動的に AES-256 暗号化で暗号化されます。 Microsoft がすべての暗号化、復号化、キー管理を処理します。 この機能は、LRS と GRS という種類の冗長性を使用します。

## <a name="next-steps"></a>次のステップ
この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](security-paas-deployments.md)
- [Azure App Services を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](security-paas-applications-using-app-services.md)
- [Azure で Paas データベースをセキュリティ保護する](security-paas-applications-using-sql.md)

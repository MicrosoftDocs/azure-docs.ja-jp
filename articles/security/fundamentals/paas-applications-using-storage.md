---
title: Azure Storage を使用して PaaS アプリケーションをセキュリティで保護する | Microsoft Docs
description: PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage のセキュリティ ベスト プラクティスについて説明します。
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 12cce0eac3827046ae9171f3dd5696ae5905c802
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093984"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Azure Storage を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護するベスト プラクティス
この記事では、PaaS (platform-as-a-service) の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage の一連のセキュリティ ベスト プラクティスについて説明します。 このベスト プラクティスは、Azure に関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

Azure は、オンプレミスでは簡単に実現できない方法で、ストレージをデプロイして使用することを可能にしています。 Azure Storage を使用すれば、比較的少ない労力で高レベルのスケーラビリティと可用性を実現できます。 Azure Storage は、Windows と Linux の Azure Virtual Machines の基盤となるだけでなく、大規模な分散アプリケーションもサポートしています。

Azure Storage では、4 つのサービスが提供されます。Blob Storage、Table Storage、Queue Storage、および File Storage です。 詳細については、「[Introduction to Microsoft Azure Storage (Microsoft Azure Storage の概要)](../../storage/common/storage-introduction.md)」をご覧ください。

[Azure Storage セキュリティ ガイド](../../storage/blobs/security-recommendations.md)には、Azure Storage とセキュリティに関する詳細な情報が記載されています。 このベスト プラクティス記事では、セキュリティ ガイドに記載されているいくつかの概念をおおまかに説明し、セキュリティ ガイドへのリンクなど、詳細を確認するためのソースを紹介しています。

この記事では、以下のベスト プラクティスを扱います。

- Shared Access Signatures (SAS)
- Azure ロールベースのアクセス制御 (Azure RBAC)
- 価値の高いデータのクライアント側暗号化
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>ストレージ アカウント キーの代わりに Shared Access Signature を使用する
アクセス制御は非常に重要です。 Azure Storage のアクセス制御のために、ストレージ アカウント作成時に、2 つの 512 ビット ストレージ アカウント キー (SAK) を生成します。 このレベルのキーの冗長性があれば、ルーチン キーの交換中にサービスを中断せずに済みます。 

ストレージ アクセス キーは優先度の高い機密情報であり、ストレージ アクセス制御の担当者のみがこれにアクセスできるようにする必要があります。 悪意のあるユーザーがキーにアクセスできてしまうと、ストレージの制御を完全に奪われ、ストレージのファイルの置換、削除、追加が行われる危険があります。 これにはマルウェアや、お客様の組織や顧客を侵害する可能性のある他のタイプのコンテンツが含まれます。

それでも、ストレージ内のオブジェクトにアクセスする手段は必要です。 より詳細なアクセス制御を行う必要がある場合は、Shared Access Signature (SAS) が役立ちます。 SAS を使用すれば、事前に定義した期間に特定の許可を与えて、ストレージ内の特定のオブジェクトを共有できます。 Shared Access Signature では、次のことを定義できます。

- SAS が有効な間隔 (開始時刻と有効期限など)。
- SAS によって付与されるアクセス許可。 たとえば、BLOB 上の SAS では BLOB への読み取りと書き込み許可がユーザーに付与されますが、アクセス許可は削除されません。
- SAS を受け入れる Azure Storage の省略可能な IP アドレスまたは範囲の IP アドレス。 たとえば、組織に属する IP アドレスの範囲を指定できます。 これは、SAS のセキュリティの別の尺度になります。
- SAS を受け入れる Azure Storage のプロトコル。 この省略可能なパラメーターを使用すると、HTTPS を使用したクライアントのアクセスを制限できます。

SAS を使用すれば、ストレージ アカウント キーを知らせることなく、望む方法でコンテンツを共有できます。 アプリケーションで常に SAS を使用すれば、ストレージ アカウント キーを侵害されることなくストレージ リソースを共有できます。

Shared Access Signature の詳細については、「[Shared Access Signatures の使用](../../storage/common/storage-sas-overview.md)」を参照してください。 

## <a name="use-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御
アクセスを管理する別の方法として、[Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) を使用します。 Azure RBAC を使用すれば、知る必要性と最小権限という 2 つのセキュリティ原則に基づいて、実際に必要となるアクセス許可を従業員に付与することに注力できます。 アクセス許可が多すぎると、アカウントが攻撃者による悪用の対象になりかねません。 アクセス許可が少なすぎると、従業員は業務を効率的に遂行できなくなる可能性があります。 Azure RBAC は、Azure の詳細なアクセス管理によってこの問題を解決します。 これは、データ アクセスにセキュリティ ポリシーを適用する必要がある組織にとって、絶対に欠かせないものです。

Azure で Azure 組み込みロールを使用して、ユーザーに権限を割り当てることができます。 例えば、クラウド事業者は、ストレージ アカウントを管理する必要がある場合はストレージ アカウント共同作業者ロール、従来のストレージ アカウントを管理する場合は従来のストレージ アカウント共同作業者ロールの使用します。 VM を管理する必要はあるが接続先の仮想ネットワークまたはストレージ アカウントを管理する必要がないというクラウド事業者は、作業者に仮想マシン共同作業者ロールを追加できます。

Azure RBAC などの機能を使用したデータ アクセス制御を適用しない場合、ユーザーに必要以上の権限が付与される可能性があります。 これにより、一部のユーザーが最初の段階でアクセスすべきではないデータにアクセスできてしまい、データのセキュリティ侵害につながる恐れがあります。

Azure RBAC の詳細については、次の記事をご覧ください。

- [Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)
- [Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)
- [Azure Storage セキュリティ ガイド](../../storage/blobs/security-recommendations.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>価値の高いデータのクライアント側暗号化を使用する
クライアント側暗号化を使用すると、Azure Storage にアップロードする前に転送中データをプログラムで暗号化でき、取得するときにデータをプログラムで複合化できます。 これは転送中のデータの暗号化ですが、保存データの暗号化も可能です。 クライアント側暗号化が最も安全にデータを暗号化できる方法ですが、アプリケーションのプログラムを変更し、キー管理プロセスを組み込む必要があります。

また、クライアント側暗号化では、暗号化キーを制御できる担当者を 1 人に限定できます。 独自の暗号化キーを生成し、管理することができます。 エンベロープ手法を使用して、Azure ストレージ クライアント ライブラリでコンテンツ暗号化キー (CEK) を生成し、キーの暗号化キー (KEK) を使用して CEK をラップ (暗号化) します。 KEK は、キー識別子によって識別され、非対称キー ペアまたは対称キーのどちらでもよく、ローカルで管理することも、[Azure Key Vault](../../key-vault/general/overview.md) に格納することもできます。

クライアント側暗号化は、Java と .NET のストレージ クライアント ライブラリに組み込まれています。 クライアント アプリケーション内のデータの暗号化と、独自の暗号化キーの生成および管理の詳細については、「[Client-side encryption and Azure Key Vault for Microsoft Azure Storage (Microsoft Azure Storage のクライアント側暗号化と Azure Key Vault)](../../storage/common/storage-client-side-encryption.md)」をご覧ください。

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>保存データ用の Storage Service Encryption を有効にする
File Storage の [Storage Service Encryption](../../storage/common/storage-service-encryption.md) を有効にすると、データは自動的に AES-256 暗号化で暗号化されます。 Microsoft がすべての暗号化、復号化、キー管理を処理します。 この機能は、LRS と GRS という種類の冗長性を使用します。

## <a name="next-steps"></a>次のステップ

この記事では、PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティ保護するための、Azure Storage の一連のセキュリティ ベスト プラクティスについて説明しました。 PaaS デプロイのセキュリティ保護の詳細については、次のリンク先をご覧ください。

- [PaaS デプロイをセキュリティで保護する](paas-deployments.md)
- [Azure App Services を使用して PaaS の Web アプリケーションとモバイル アプリケーションをセキュリティで保護する](paas-applications-using-app-services.md)
- [Azure で Paas データベースをセキュリティ保護する](paas-applications-using-sql.md)
---
title: ストレージ アカウントの概要
titleSuffix: Azure Storage
description: Azure Storage のさまざまな種類のストレージ アカウントについて説明します。 アカウントの名前付け、パフォーマンス レベル、アクセス層、冗長性、暗号化、エンドポイントなどについて確認します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 24d955b0d1c53f57f5927f9e893b6ecd75fb3ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561893"
---
# <a name="storage-account-overview"></a>ストレージ アカウントの概要

Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル、キュー、テーブル、およびディスク) が含まれます。 ストレージ アカウントでは、世界中のどこからでも HTTP または HTTPS 経由でアクセスできる Azure Storage データ用の一意の名前空間が提供されます。 Azure ストレージ アカウント内のデータは、持続性があり、高可用性で、セキュリティ保護されており、非常にスケーラブルです。

Azure ストレージ アカウントの作成方法については、「[ストレージ アカウントの作成](storage-account-create.md)」を参照してください。

## <a name="types-of-storage-accounts"></a>ストレージ アカウントの種類

Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 アプリケーションに最適な種類を判断するために、ストレージ アカウントを作成する前にこれらの違いを検討してください。 ストレージ アカウントには、次の種類があります。

- **汎用 v2 アカウント**:BLOB、ファイル、キュー、およびテーブル用の基本的なストレージ アカウントの種類。 Azure Storage を使用するほとんどのシナリオにお勧めします。
- **汎用 v1 アカウント**:BLOB、ファイル、キュー、およびテーブル用の従来のアカウントの種類。 可能な場合は汎用 v2 アカウントを代わりに使用してください。
- **BlockBlobStorage アカウント**:ブロック BLOB と追加 BLOB の Premium パフォーマンス特性を持つストレージ アカウント。 トランザクション レートが高く、比較的小さなオブジェクトが使用されるシナリオ、またはストレージ待ち時間が一貫して短いことが要求されるシナリオに推奨されます。
- **FileStorage アカウント**:Premium パフォーマンスの特徴を持つファイル専用ストレージ アカウント。 エンタープライズまたはハイ パフォーマンス スケール アプリケーションにお勧めします。
- **BlobStorage アカウント**:従来の BLOB 専用ストレージ アカウント。 可能な場合は汎用 v2 アカウントを代わりに使用してください。

次の表では、ストレージ アカウントの種類、サポートするサービス、および各アカウントの種類でサポートされているデプロイ モデルについて説明します。

| ストレージ アカウントの種類 | サポートされているサービス | 冗長オプション | デプロイ モデル<sup>1</sup> |
|--|--|--|--|
| 汎用 v2 | BLOB、ファイル、キュー、テーブル、ディスク、および Data Lake Gen2<sup>2</sup> | LRS、GRS、RA-GRS、ZRS、GZRS、RA-GZRS<sup>3</sup> | リソース マネージャー |
| 汎用 v1 | BLOB、ファイル、キュー、テーブル、およびディスク | LRS、GRS、RA-GRS | Resource Manager、クラシック |
| BlockBlobStorage | BLOB (ブロック BLOB と追加 BLOB のみ) | LRS、ZRS<sup>3</sup> | リソース マネージャー |
| FileStorage | ファイルのみ | LRS、ZRS<sup>3</sup> | リソース マネージャー |
| BlobStorage | BLOB (ブロック BLOB と追加 BLOB のみ) | LRS、GRS、RA-GRS | リソース マネージャー |

<sup>1</sup> Azure Resource Manager デプロイ モデルが推奨されます。 一部の場所では、クラシック デプロイ モデルを使用するストレージ アカウントを引き続き作成でき、既存のクラシック アカウントもサポートされます。 詳細については、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../../azure-resource-manager/management/deployment-models.md)」を参照してください。

<sup>2</sup> Azure Data Lake Storage Gen2 は、Azure Blob Storage をベースに構築された、ビッグ データ分析専用の一連の機能です。 Data Lake Storage Gen2 は、階層型名前空間が有効になった汎用の V2 ストレージ アカウントでのみサポートされています。 Data Lake Storage Gen2 の詳細については、「[Azure Data Lake Storage Gen2 の概要](../blobs/data-lake-storage-introduction.md)」を参照してください。

<sup>3</sup> ゾーン冗長ストレージ (ZRS) および geo ゾーン冗長ストレージ (GZRS と RA-GZRS) は、特定のリージョンの Standard 汎用 V2、BlockBlobStorage、FileStorage の各アカウントでのみ使用できます。 Azure Storage の冗長オプションの詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

### <a name="storage-account-redundancy"></a>ストレージ アカウントの冗長性

ストレージ アカウントの冗長オプションには、次のようなものがあります。

- **ローカル冗長ストレージ (LRS)** : シンプルな低コストの冗長戦略。 データは、プライマリ リージョンの 1 つの物理的な場所内で、同期的に 3 回コピーされます。
- **ゾーン冗長ストレージ (ZRS)** : 高可用性を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされます。
- **geo 冗長ストレージ (GRS)** :リージョン障害から保護するためのリージョン間冗長。 データは、プライマリ リージョンで 3 回、同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo 冗長ストレージ (RA-GRS) を有効にします。
- **geo ゾーン冗長ストレージ (GZRS)** :高可用性と最大限の持続性の両方を必要とするシナリオ向けの冗長性。 データは、プライマリ リージョンの 3 つの Azure 可用性ゾーン間で同期的にコピーされた後、セカンダリ リージョンに非同期的にコピーされます。 セカンダリ リージョンのデータへの読み取りアクセスのために、読み取りアクセス geo ゾーン冗長ストレージ (RA-GZRS) を有効にします。

Azure Storage の冗長オプションの詳細については、「[Azure Storage の冗長性](storage-redundancy.md)」を参照してください。

### <a name="general-purpose-v2-accounts"></a>汎用 v2 アカウント

汎用 v2 ストレージ アカウントは、最新の Azure Storage の機能をサポートし、汎用 v1 と BLOB ストレージ アカウントのすべての機能が組み込まれています。 汎用 v2 アカウントは、業界内の他社に引けを取らないトランザクション料金で、Azure Storage に対してギガバイトあたり容量の最低価格を提供しています。 汎用 v2 ストレージ アカウントは、次の Azure Storage サービスをサポートします。

- BLOB (すべての種類: ブロック、追加、ページ)
- Data Lake Gen2
- Files
- ディスク
- キュー
- テーブル

> [!NOTE]
> Microsoft では、ほとんどのシナリオで汎用 v2 ストレージ アカウントを使用することをお勧めしています。 汎用 v1 または BLOB ストレージ アカウントは汎用 v2 アカウントに簡単にアップグレードできます。そのとき、ダウンタイムは発生せず、データをコピーする必要はありません。 ただし、アップグレードを元に戻すことはできません。
>
> 汎用 v2 アカウントへのアップグレードの詳細については、[汎用 v2 ストレージ アカウントへのアップグレード](storage-account-upgrade.md)に関する記事を参照してください。

汎用 v2 ストレージ アカウントには、使用パターンに基づいてデータを格納するための複数のアクセス層があります。 詳細については、「[ブロック BLOB データ用のアクセス層](#access-tiers-for-block-blob-data)」を参照してください。

### <a name="general-purpose-v1-accounts"></a>汎用 v1 アカウント

汎用 v1 ストレージ アカウントでは、すべての Azure Storage サービスにアクセスできますが、最新の機能が利用できない場合があるほか、GB 単価もやや高いことがあります。 汎用 v1 ストレージ アカウントは、次の Azure Storage サービスをサポートします。

- BLOB (すべての種類)
- Files
- ディスク
- キュー
- テーブル

これらのシナリオでは、汎用 v1 アカウントを使用できます：

- アプリケーションで、Azure クラシック デプロイ モデルが必要である。 汎用 v2 アカウントと BLOB ストレージ アカウントは、Azure Resource Manager デプロイ モデルのみをサポートします。

- アプリケーションは、トランザクション集中型であるか、かなり geo レプリケーション帯域幅を使用しますが、大容量は必要ありません。 この場合は、汎用 v1 が最も経済的な選択肢になる可能性があります。

- 2014-02-14 より前の [Storage Services REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) バージョンを使用しているか、4.x より前のクライアント ライブラリ バージョンを使用しています。 アプリケーションをアップグレードできません。

> [!NOTE]
> Microsoft ではほとんどのシナリオで汎用 v2 アカウントを推奨していますが、Microsoft では新規および既存のお客様向けに汎用 v1 アカウントを引き続きサポートしています。 これらのリージョンで Azure Storage を利用できる場合は常に、新しいリージョンに汎用 v1 ストレージ アカウントを作成できます。 Microsoft では、現時点で汎用 v1 アカウントのサポートを廃止する予定はなく、Azure Storage 機能のサポート終了の少なくとも 1 年前に事前通知する予定です。 Microsoft では、汎用 v1 アカウントのセキュリティ更新プログラムを引き続き提供しますが、このアカウントの種類に対して新しい機能の開発は予定されていません。
>
> 2020 年 10 月 1 日の時点で、新しい Azure Storage リージョンでの汎用 v1 アカウントの価格は、これらのリージョンの汎用 v2 アカウントの料金と同じです。 既存の Azure Storage リージョンの価格は変更されていません。 特定のリージョンの汎用 v1 アカウントの価格の詳細については、Azure Storage の価格ページを参照してください。 リージョンを選択し、 **[価格プラン]** の横にある **[その他]** を選択してください。

### <a name="blockblobstorage-accounts"></a>BlockBlobStorage アカウント

BlockBlobStorage アカウントは、Premium パフォーマンス レベルで、非構造化オブジェクト データをブロック BLOB または追加 BLOB として格納することに特化したストレージ アカウントです。 汎用 v2 アカウントと BlobStorage アカウントと比べると、BlockBlobStorage アカウントは待ち時間が一貫して低く、高いトランザクション レートが実現されます。

BlockBlobStorage アカウントは、現在、ホット、クール、またはアーカイブアクセス層への階層化をサポートしていません。 この種類のストレージ アカウントでは、ページ BLOB、テーブル、またはキューはサポートされません。

### <a name="filestorage-accounts"></a>FileStorage アカウント

FileStorage ストレージ アカウントは、Premium ファイル共有の格納と作成に特化したストレージ アカウントです。 この種類のストレージ アカウントでは、ファイルはサポートされますが、ブロック BLOB、追加 BLOB、ページ BLOB、テーブル、およびキューはサポートされません。

FileStorage ストレージ アカウントでは、IOPS バーストなど、固有のパフォーマンスに特化した特性が提供されています。 これらの特性の詳細については、「File プラン ガイド」の「[ファイル共有ストレージ層](../files/storage-files-planning.md#storage-tiers)」セクションをご参照ください。

## <a name="naming-storage-accounts"></a>ストレージ アカウントの名前付け

ストレージ アカウントに名前を付けるときは、次の規則に注意してください。

- ストレージ アカウント名の長さは 3 から 24 文字である必要があり、数字と小文字のみを使用できます。
- ストレージ アカウント名は Azure 内で一意である必要があります。 複数のストレージ アカウントが同じ名前を持つことはできません。

## <a name="performance-tiers"></a>パフォーマンス レベル

作成するストレージ アカウントの種類に応じて、Standard と Premium のパフォーマンス レベルから選択できます。 次の表に、どの種類のストレージ アカウントでどのパフォーマンス レベルが使用できるかをまとめています。

| ストレージ アカウントの種類 | サポートされているパフォーマンス レベル |
|--|--|
| 汎用 v2 | Standard、Premium<sup>1</sup> |
| 汎用 v1 | Standard、Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| FileStorage | Premium |
| BlobStorage | Standard |

<sup>1</sup>汎用 v2 および汎用 v1 アカウント用の Premium パフォーマンスは、ディスクとページ BLOB のみで利用できます。 ブロックまたは BLOB の追加用の Premium パフォーマンスは、BlockBlobStorage アカウントでのみ使用できます。 ファイル用の Premium パフォーマンスは、FileStorage アカウントでのみ使用できます。

### <a name="general-purpose-storage-accounts"></a>汎用ストレージ アカウント

汎用ストレージ アカウントは、次のパフォーマンス レベルのいずれか向けに構成できます。

- BLOB、ファイル、テーブル、キュー、および Azure 仮想マシン ディスクを格納するための Standard パフォーマンス レベル。 Standard ストレージ アカウントのスケーラビリティ ターゲットの詳細については、[Standard ストレージ アカウントのスケーラビリティ ターゲット](scalability-targets-standard-account.md)に関するページを参照してください。
- 仮想マシンのアンマネージド ディスクを格納するための Premium パフォーマンス レベル。 Microsoft は、Azure 仮想マシンでアンマネージド ディスクではなく、マネージド ディスクを使用することをお勧めします。 Premium パフォーマンス レベルのスケーラビリティ ターゲットの詳細については、「[Premium ページ BLOB ストレージ アカウントのスケーラビリティ ターゲット](../blobs/scalability-targets-premium-page-blobs.md)」に関するページを参照してください。

### <a name="blockblobstorage-storage-accounts"></a>BlockBlobStorage ストレージ アカウント

BlockBlobStorage アカウントでは、ブロック BLOB と追加 BLOB を格納するための Premium パフォーマンス レベルが提供されます。 詳細については、「[Premium ブロック BLOB ストレージ アカウントのスケーラビリティ ターゲット](../blobs/scalability-targets-premium-block-blobs.md)」を参照してください。

### <a name="filestorage-storage-accounts"></a>FileStorage ストレージ アカウント

FileStorage ストレージ アカウントは、Azure ファイル共有のための Premium パフォーマンス レベルを提供します。 詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../files/storage-files-scale-targets.md)」を参照してください。

## <a name="access-tiers-for-block-blob-data"></a>ブロック BLOB 用のデータ アクセス層

Azure Storage では、使用パターンに基づいて、ブロック BLOB データにアクセスするためのさまざまなオプションを用意しています。 Azure Storage 内の各アクセス層は、特定のデータの使用パターン用に最適化されています。 ニーズに合った適切なアクセス層を選択することで、最も費用対効果の高い方法でブロック BLOB データを格納できます。

使用可能なアクセス層は次のとおりです。

- **ホット** アクセス層。 この層は、ストレージ アカウント内のオブジェクトへの頻繁なアクセスに対応するように最適化されています。 ホット層でのデータのアクセスは、費用対効果は最も高くなりますが、ストレージ コストは高くなります。 新しいストレージ アカウントは、既定ではホット層に作成されます。
- **クール** アクセス層。 この層は、アクセスされる頻度が低いデータで、少なくとも 30 日以上保管される大量データを格納するために最適化されています。 クール層でのデータの格納は、費用対効果が高くなりますが、データへのアクセスがホット層でのデータへのアクセスよりも高くなる場合があります。
- **アーカイブ** 層。 アーカイブ層は、個々のブロック BLOB 用にのみ使用できます。 このアーカイブ層は、数時間の取得待機時間を許容でき、少なくとも 180 日以上アーカイブ層に保持されるデータ用に最適化されています。 アーカイブ層は、データを格納するための最もコスト効率のよいオプションです。 しかし、ホット層またはクール層のデータにアクセスするよりも、そのデータにアクセスする方がコストが高くなります。

データの使用パターンが変化した場合は、いつでもこれらのアクセス層を切り替えできます。 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](../blobs/storage-blob-storage-tiers.md)」を参照してください。

次の表に、ストレージ アカウントの種類ごとに BLOB で使用できるアクセス層を示します。

| ストレージ アカウントの種類 | サポートされているアクセス層 |
|--|--|
| 汎用 v2 | ホット、クール、アーカイブ<sup>1</sup> |
| 汎用 v1 | 該当なし |
| BlockBlobStorage | 該当なし |
| FileStorage | 該当なし |
| BlobStorage | ホット、クール、アーカイブ<sup>1</sup> |

<sup>1</sup> アーカイブ ストレージと BLOB レベルの階層制御では、ブロック BLOB のみがサポートされます。 アーカイブ層は、個々の BLOB のレベルでのみ利用でき、ストレージ アカウント レベルでは利用できません。 詳細については、「[Azure Blob Storage のアクセス層 - ホット、クール、およびアーカイブ](../blobs/storage-blob-storage-tiers.md)」を参照してください。

> [!IMPORTANT]
> 既存のストレージ アカウントまたは BLOB のアクセス レベルを変更すると、追加料金が発生する場合があります。 詳細については、「[ストレージ アカウントの課金](#storage-account-billing)」を参照してください。

## <a name="encryption"></a>暗号化

ストレージ アカウント内のすべてのデータは、サービス側で暗号化されます。 暗号化の詳細については、「[保存データ向け Azure Storage Service Encryption](storage-service-encryption.md)」をご覧ください。

## <a name="storage-account-endpoints"></a>ストレージ アカウント エンドポイント

ストレージ アカウントは、データ用の一意の名前空間を Azure 内に用意します。 Azure Storage 内に格納されるすべてのオブジェクトには、一意のアカウント名を含むアドレスが割り当てられます。 アカウント名と Azure Storage サービス エンドポイントの組み合わせによって、ストレージ アカウント用のエンドポイントが形成されます。

次の表に、各 Azure Storage サービスのエンドポイントの一覧を示します。

| ストレージ サービス | エンドポイント |
|--|--|
| BLOB ストレージ | `https://<storage-account>.blob.core.windows.net` |
| Azure Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| ストレージ | `https://<storage-account>.queue.core.windows.net` |
| テーブル ストレージ | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> ブロック BLOB および BLOB ストレージ アカウントは、Blob service エンドポイントのみを公開します。

ストレージアカウント内のオブジェクトの場所をエンドポイントに追加して、ストレージアカウント内のオブジェクトにアクセスするためのURLを構築します。 たとえば、BLOB アドレスは、 http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob* のような形式になります。

BLOB 用のカスタム ドメインを使用するようにストレージ アカウントを構成することもできます。 詳細については、「[Azure Storage アカウントのカスタム ドメイン名の構成](../blobs/storage-custom-domain-name.md)」をご覧ください。  

## <a name="control-access-to-account-data"></a>アカウント データへのアクセスの制御

既定では、アカウントのデータはアカウント所有者だけが使用できます。 データにアクセスできるユーザーと、各ユーザーに付与するアクセス許可を制御できます。

ストレージ アカウントに対して行われるすべての要求は、承認される必要があります。 サービスのレベルには、要求には有効な *Authorization* ヘッダーが含まれている必要があります。 具体的には、このヘッダーには、サービスが要求を実行する前に検証するために必要なすべての情報が含まれています。

次の方法のいずれかを使用して、ストレージ アカウント内のデータへのアクセス権を付与できます。

- **Azure Active Directory:** Azure Active Directory (Azure AD) の資格情報を使用して、ユーザー、グループ、またはその他の ID による BLOB データとキュー データへのアクセスを認証します。 ID の認証が成功した場合、Azure AD は、Azure Blob Storage または Queue Storage に対する要求の承認で使用されるトークンを返します。 詳細については、「[Azure Active Directory を使用して Azure Storage へのアクセスを認証する](storage-auth-aad.md)」を参照してください。
- **共有キーによる承認:** ストレージ アカウント アクセス キーを使用して、アプリケーションが実行時に Azure Storage にアクセスするために使用する接続文字列を構成します。 接続文字列内の値を使用して、Azure Storage に渡される "*承認*" ヘッダーが作成されます。 詳細については、「[Azure Storage の接続文字列を構成する](storage-configure-connection-string.md)」を参照してください。
- **共有アクセス署名:** Shared Access Signature (SAS) は、ストレージ アカウント内のリソースへの委任アクセスを可能にするトークンです。 SAS トークンは、URL 上の Azure Storage への要求を認可するために必要なすべての情報をカプセル化します。 SAS の作成時には、SAS がリソースに認めるアクセス許可と、そのアクセス許可の有効期間 SAS がクライアントに付与するアクセス許可を指定できます。 SAS トークンには、Azure AD 資格情報または共有キーを使用して署名できます。 詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](storage-sas-overview.md)」を参照してください。

> [!NOTE]
> Azure AD の資格情報を使用したユーザーまたはアプリケーションの認証は、セキュリティと使いやすさで他の承認手段よりも優れています。 アプリケーションで共有キー承認を引き続き使うことはできますが、Azure AD を使うと、コードでアカウント アクセス キーを保存する必要がなくなります。 Shared Access Signature (SAS) を使ってストレージ アカウント内のリソースに対するきめ細かいアクセスの許可を続けることもできますが、Azure AD は、SAS トークンを管理したり侵害された SAS の取り消しを心配したりする必要なしに、同様の機能を提供します。
>
> Azure Storage の BLOB アプリケーションとキュー アプリケーションでは、できる限り Azure AD 承認を使用することをお勧めします。

## <a name="copying-data-into-a-storage-account"></a>ストレージ アカウントへのデータのコピー

Microsoft では、オンプレミス ストレージ デバイスまたはサード パーティのクラウド ストレージ プロバイダーからデータをインポートするためのユーティリティとライブラリを提供しています。 どのソリューションを使用するかは、転送するデータ量によって決まります。

汎用 v1 アカウントまたは BLOB ストレージ アカウントから汎用 v2 アカウントにアップグレードした場合、データは自動的に移行されます。 アカウントをアップグレードする際は、この経路を使用することをお勧めします。 しかし、汎用 v1 アカウントから Blob ストレージアカウントにデータを移動する場合は、以下で説明するツールとライブラリを使用して、データを手動で移行します。

### <a name="azcopy"></a>AzCopy

AzCopy は、Azure Storage との間で高パフォーマンスのデータ コピーを行うように設計された Windows コマンドライン ユーティリティです。 AzCopy を使用して、既存の汎用ストレージ アカウントから BLOB ストレージ アカウントにデータをコピーできます。またオンプレミス ストレージ デバイスから データをアップロードできます。 詳細については、[AzCopy コマンド ライン ユーティリティを使用したデータの転送](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページをご覧ください。

### <a name="data-movement-library"></a>データ移動ライブラリ

Azure Storage Data Movement Library for .Net は、AzCopy を動作させているコア データ移動フレームワークに基づいています。 ライブラリは、AzCopy と同じように、高パフォーマンスで信頼性が高く簡単なデータ転送操作ができるように設計されています。 データ移動ライブラリを使用して、AzCopy 機能をネイティブに使用することができます。 詳細については、[Azure Storage Data Movement Library for .NET](https://github.com/Azure/azure-storage-net-data-movement) に関するページを参照

### <a name="rest-api-or-client-library"></a>REST API またはクライアント ライブラリ

汎用 v1 ストレージアカウントから Blob ストレージアカウントに、データを移行するためのカスタムアプリケーションを作成できます。 Azure クライアント ライブラリまたは Azure Storage サービス REST API のいずれかを使用します。 Azure Storage には、.NET、Java、C++、Node.js、PHP、Ruby、Python などの複数の言語とプラットフォーム用の豊富なクライアント ライブラリが用意されています。 クライアント ライブラリは、再試行ロジック、ログ、並列アップロードといった高度な機能を提供します。 また、REST API を直接使用して開発することもでき、HTTP/HTTPS 要求を行うどの言語からでも呼び出すことができます。

Azure Storage REST API の詳細については、「[Azure Storage Services REST API Reference](/rest/api/storageservices/)」(Azure Storage Services REST API リファレンス) を参照してください。

> [!IMPORTANT]
> クライアント側の暗号化を使用して暗号化された BLOB には、BLOB と共に暗号化関連メタデータが格納されます。 クライアント側の暗号化で暗号化された BLOB をコピーする場合は、コピー操作の際に BLOB メタデータ、特に暗号化関連のメタデータが保持されるようにしてください。 暗号化メタデータなしで BLOB をコピーした場合、BLOB コンテンツを再度取得することはできません。 暗号化関連メタデータの詳細については、[Azure Storage のクライアント側の暗号化](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

## <a name="storage-account-billing"></a>ストレージ アカウントの課金

Azure Storage では、ストレージ アカウントの使用に基づいて請求されます。 ストレージ アカウント内のすべてのオブジェクトは、グループとしてまとめて課金されます。 ストレージ コストは次の要素に基づいて計算されます。

- **リージョン** とは、自分のアカウントがベースとしている地理的領域を指します。
- **アカウントの種類** とは、使用するストレージ アカウントの種類を指します。
- **アクセス層** とは、汎用 v2 または BLOB ストレージ アカウント用に指定したデータの使用状況パターンを指します。
- **容量** とは、データの保存に使用しているストレージ アカウントの割り当て量を指します。
- **レプリケーション** は、1 回に保持するデータのコピー数とデータの場所を決定します。
- **トランザクション** とは、Azure Storage に対するすべての読み取り操作と書き込み操作を指します。
- **データ転送** とは、Azure リージョンから転送されるデータを指します。 他のリージョンで動作しているアプリケーションがストレージ アカウント内のデータにアクセスした場合は、エグレス データ転送として課金されます  リソース グループを使用してデータとサービスを同じリージョンにグループ化してエグレス料金を制限する方法については、「[Azure リソース グループとは](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)」を参照してください。

アカウントの種類、ストレージ容量、レプリケーション、およびトランザクションに基づく詳細な料金情報については、「 [Azure Storage 料金](https://azure.microsoft.com/pricing/details/storage/) 」ページを参照してください。 送信データ転送の価格の詳細については、「 [データ転送の価格の詳細](https://azure.microsoft.com/pricing/details/data-transfers/) 」を参照してください。 コストの見積には、 [計算ツール](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) をご利用ください。

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>次のステップ

- [ストレージ アカウントの作成](storage-account-create.md)
- [ブロック BLOB ストレージ アカウントの作成](../blobs/storage-blob-create-account-block-blob.md)
- [汎用 v2 ストレージ アカウントにアップグレードする](storage-account-upgrade.md)
- [削除されたストレージ アカウントを復旧します](storage-account-recover.md)
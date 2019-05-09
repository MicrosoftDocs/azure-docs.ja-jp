---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799509"
---
Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 アプリケーションに最適な種類を判断するために、ストレージ アカウントを作成する前にこれらの違いを検討してください。 ストレージ アカウントには、次の種類があります。

- **汎用 v2 アカウント**:BLOB、ファイル、キュー、およびテーブル用の基本的なストレージ アカウントの種類。 Azure Storage を使用するほとんどのシナリオにお勧めします。
- **汎用 v1 アカウント**:BLOB、ファイル、キュー、およびテーブル用の従来のアカウントの種類。 可能な場合は汎用 v2 アカウントを代わりに使用してください。
- **ブロック BLOB ストレージ アカウント**:Premium パフォーマンスの特徴を持つ BLOB 専用ストレージ アカウント。 トランザクション レートが高く、比較的小さなオブジェクトが使用される、またはストレージ待ち時間が一貫して短いことが要求されるシナリオに推奨されます。
- **FileStorage (プレビュー) ストレージ アカウント**:Premium パフォーマンスの特徴を持つファイル専用ストレージ アカウント。 エンタープライズまたはハイ パフォーマンス スケール アプリケーションにお勧めします。
- **BLOB ストレージ アカウント**:BLOB 専用ストレージ アカウント。 可能な場合は汎用 v2 アカウントを代わりに使用してください。

次の表は、ストレージ アカウントの種類とその機能について説明しています。

| ストレージ アカウントの種類 | サポートされているサービス                       | サポートされているパフォーマンス レベル      | サポートされているアクセス層         | レプリケーション オプション               | デプロイ モデル<sup>1</sup> | 暗号化<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 汎用 v2   | BLOB、ファイル、キュー、テーブル、およびディスク       | Standard、Premium<sup>5</sup> | ホット、クール、アーカイブ<sup>3</sup> | LRS、ZRS<sup>4</sup>、GRS、RA-GRS | リソース マネージャー             | 暗号化              |
| 汎用 v1   | BLOB、ファイル、キュー、テーブル、およびディスク       | Standard、Premium<sup>5</sup> | 該当なし                            | LRS、GRS、RA-GRS                  | Resource Manager、クラシック    | 暗号化              |
| ブロック BLOB ストレージ   | BLOB (ブロック BLOB と追加 BLOB のみ) | Premium                       | 該当なし                            | LRS                               | リソース マネージャー             | 暗号化              |
| FileStorage (プレビュー)   | ファイル専用 | Premium                       | 該当なし                            | LRS                               | リソース マネージャー             | 暗号化              |
| BLOB ストレージ         | BLOB (ブロック BLOB と追加 BLOB のみ) | Standard                      | ホット、クール、アーカイブ<sup>3</sup> | LRS、GRS、RA-GRS                  | リソース マネージャー             | 暗号化              |

<sup>1</sup> Azure Resource Manager デプロイ モデルが推奨されます。 一部の場所では、クラシック デプロイ モデルを使用するストレージ アカウントを引き続き作成でき、既存のクラシック アカウントもサポートされます。 詳細については、「[Azure Resource Manager とクラシック デプロイ:デプロイ モデルとリソースの状態について](../articles/azure-resource-manager/resource-manager-deployment-model.md)」を参照してください。

<sup>2</sup> すべてのストレージ アカウントは、保存データ用の Storage Service Encryption (SSE) を使用して暗号化されます。 詳細については、「[保存データ向け Azure Storage Service Encryption](../articles/storage/common/storage-service-encryption.md)」をご覧ください。

<sup>3</sup> アーカイブ 層は、個々の BLOB のレベルでのみ利用でき、ストレージ アカウント レベルでは利用できません。 ブロック BLOB と追加 BLOB のみをアーカイブできます。 詳細については、「[Azure BLOB ストレージ: ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層](../articles/storage/blobs/storage-blob-storage-tiers.md)」をご覧ください。

<sup>4</sup> ゾーン冗長ストレージ (ZRS) は、標準的な汎用 v2 ストレージ アカウントでのみ使用できます。 ZRS に関する詳細については、「[ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション](../articles/storage/common/storage-redundancy-zrs.md)」をご覧ください。 レプリケーション オプションの詳細については、「[Azure Storage のレプリケーション](../articles/storage/common/storage-redundancy.md)」を参照してください。

汎用 v2 アカウントおよび v1 アカウント用の <sup>5</sup> Premium パフォーマンスは、ディスクとページ BLOB のみで利用できます。
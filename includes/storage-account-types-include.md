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
ms.openlocfilehash: 14de5283197778464993b80b312a690662e066ab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443736"
---
Azure Storage では、数種類のストレージ アカウントが提供されています。 各種類は異なる機能をサポートし、独自の価格モデルがあります。 アプリケーションに最適な種類を判断するために、ストレージ アカウントを作成する前にこれらの違いを検討してください。 ストレージ アカウントには、次の種類があります。

- **汎用 v2 アカウント**:BLOB、ファイル、キュー、およびテーブル用の基本的なストレージ アカウントの種類。 Azure Storage を使用するほとんどのシナリオにお勧めします。
- **汎用 v1 アカウント**:BLOB、ファイル、キュー、およびテーブル用の従来のアカウントの種類。 可能な場合は汎用 v2 アカウントを代わりに使用してください。
- **ブロック BLOB ストレージ アカウント**:Premium パフォーマンスの特徴を持つ BLOB 専用ストレージ アカウント。 トランザクション レートが高く、比較的小さなオブジェクトが使用される、またはストレージ待ち時間が一貫して短いことが要求されるシナリオに推奨されます。
- **FileStorage ストレージ アカウント**:Premium パフォーマンスの特徴を持つファイル専用ストレージ アカウント。 エンタープライズまたはハイ パフォーマンス スケール アプリケーションにお勧めします。
- **BLOB ストレージ アカウント**:BLOB 専用ストレージ アカウント。 可能な場合は汎用 v2 アカウントを代わりに使用してください。

次の表は、ストレージ アカウントの種類とその機能について説明しています。

| ストレージ アカウントの種類 | サポートされているサービス                       | サポートされているパフォーマンス レベル      | サポートされているアクセス層         | レプリケーション オプション               | デプロイメント モデル<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 暗号化<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 汎用 v2   | BLOB、ファイル、キュー、テーブル、およびディスク       | Standard、Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | ホット、クール、アーカイブ<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | リソース マネージャー             | 暗号化              |
| 汎用 v1   | BLOB、ファイル、キュー、テーブル、およびディスク       | Standard、Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 該当なし                            | LRS、GRS、RA-GRS                  | Resource Manager、クラシック    | 暗号化              |
| ブロック BLOB ストレージ   | BLOB (ブロック BLOB と追加 BLOB のみ) | Premium                       | 該当なし                            | LRS                               | リソース マネージャー             | 暗号化              |
| FileStorage   | ファイル専用 | Premium                       | 該当なし                            | LRS                               | リソース マネージャー             | 暗号化              |
| BLOB ストレージ         | BLOB (ブロック BLOB と追加 BLOB のみ) | Standard                      | ホット、クール、アーカイブ<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | リソース マネージャー             | 暗号化              |

<div id="deployment-model"><sup>1</sup> Azure Resource Manager デプロイ モデルが推奨されます。 一部の場所では、クラシック デプロイ モデルを使用するストレージ アカウントを引き続き作成でき、既存のクラシック アカウントもサポートされます。 詳細については、「<a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager とクラシック デプロイ:デプロイ モデルとリソースの状態について</a>」を参照してください。</div>

<div id="encryption"><sup>2</sup> すべてのストレージ アカウントは、保存データ用の Storage Service Encryption (SSE) を使用して暗号化されます。 詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">保存データ向け Azure Storage Service Encryption</a>」をご覧ください。</div>

<div id="archive"><sup>3</sup> アーカイブ 層は、個々の BLOB のレベルでのみ利用でき、ストレージ アカウント レベルでは利用できません。 ブロック BLOB と追加 BLOB のみをアーカイブできます。 詳細については、「<a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure BLOB ストレージ: ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層</a>」をご覧ください。</div>

<div id="zone-redundant-storage"><sup>4</sup> ゾーン冗長ストレージ (ZRS) は、標準的な汎用 v2 ストレージ アカウントでのみ使用できます。 ZRS に関する詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">ゾーン冗長ストレージ (ZRS): 高可用 Azure Storage アプリケーション</a>」をご覧ください。 レプリケーション オプションの詳細については、「<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage のレプリケーション</a>」を参照してください。</div>

<div id="premium-performance">汎用 v2 および汎用 V1 アカウント用の <sup>5</sup> Premium パフォーマンスは、ディスクとページ BLOB でのみ使用できます。</div>

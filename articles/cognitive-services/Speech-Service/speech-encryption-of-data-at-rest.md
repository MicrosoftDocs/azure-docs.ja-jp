---
title: Speech サービスによる保存データの暗号化
titleSuffix: Azure Cognitive Services
description: Speech サービスによる保存データの暗号化。
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: egeaney
ms.openlocfilehash: c2e52fbab8d984f7442d8a336e90e9f22c0bf061
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198664"
---
# <a name="speech-service-encryption-of-data-at-rest"></a>Speech サービスによる保存データの暗号化

データは、クラウドに永続化されるときに Speech サービスによって自動的に暗号化されます。 Speech サービスの暗号化によってデータは保護され、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。

## <a name="about-cognitive-services-encryption"></a>Cognitive Services の暗号化について

データは、[FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) に準拠する [256 ビット AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 暗号化を使用して暗号化および暗号化解除されます。 暗号化と暗号化解除は透過的であり、暗号化とアクセスはユーザーによって管理されます。 データは既定でセキュリティ保護され、暗号化を利用するためにコードまたはアプリケーションを変更する必要はありません。

## <a name="about-encryption-key-management"></a>暗号化キーの管理について

Custom Speech とカスタム音声を使用すると、Speech サービスによって次のデータをクラウドに保存できます。  

* 音声トレース データ - カスタム エンドポイントに対してトレースをオンにした場合のみ
* アップロードされたトレーニングとテスト データ

既定では、データは Microsoft のストレージに格納され、サブスクリプションでは Microsoft のマネージド暗号化キーが使用されます。 独自のストレージ アカウントを準備するオプションもあります。 ストアへのアクセスはマネージド ID によって管理され、Speech サービスでは、音声トレース データ、カスタマイズ トレーニング データ、カスタム モデルなど、独自のデータに直接アクセスすることはできません。

詳しくは、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関する記事をご覧ください。

## <a name="bring-your-own-storage-byos-for-customization-and-logging"></a>カスタマイズとログ記録のための独自ストレージの持ち込み (BYOS)

独自ストレージの持ち込みへのアクセスを要求するには、 [Speech サービス - 独自ストレージの持ち込み (BYOS) 要求フォーム](https://aka.ms/cogsvc-cmk)に記入して送信します。 承認されたら、カスタマイズとログ記録に必要なデータを格納するために、独自のストレージ アカウントを作成する必要があります。 ストレージ アカウントを追加すると、Speech サービス リソースによって、システム割り当てのマネージド ID が有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory (AAD) に登録されます。 登録された後、そのストレージ アカウントへのアクセス権がマネージド ID に付与されます。 マネージド ID について詳しくは、こちらをご覧ください。 詳しくは、[マネージド ID の概要](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)に関する記事をご覧ください。

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、ストレージ アカウントへのアクセス権が削除されます。 これにより、ストレージ アカウントへのアクセスを必要とする Speech サービスの部分は動作を停止します。  

Speech サービスでは、現在、カスタマー ロックボックスはサポートされていません。 ただし、BYOS を使用すれば顧客データを格納することができるため、[カスタマー ロックボックス](../../security/fundamentals/customer-lockbox-overview.md)と類似のデータ管理を実現できます。 Speech サービスのデータは保持され、Speech リソースが作成されたリージョンで処理されることに注意してください。 これは、保存データと転送中のデータに適用されます。 Custom Speech や Custom Voice などのカスタマイズ機能を使用する場合、顧客データはすべて、BYOS (使用されている場合) と Speech サービス リソースが存在しているのと同じリージョンで転送、格納、および処理されます。

> [!IMPORTANT]
> Microsoft では、Speech モデルを改善するためにお客様のデータを**使用するということはありません**。 また、エンドポイントのログ記録が無効になっていて、カスタマイズが使用されていない場合、顧客データは格納されません。 

## <a name="next-steps"></a>次のステップ

* [Speech サービス - 独自ストレージの持ち込み (BYOS) 要求フォーム](https://aka.ms/cogsvc-cmk)
* [マネージド ID とは](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

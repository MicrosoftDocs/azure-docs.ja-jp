---
title: カスタマー マネージド キーを使用した Azure Device Provisioning Service 保存データの暗号化 |Microsoft Docs
description: Device Provisioning Service でのカスタマー マネージド キーを使用した保存データの暗号化
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967178"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Device Provisioning Service でのカスタマー マネージド キーを使用した保存データの暗号化

## <a name="overview"></a>概要

Device Provisioning Service (DPS) では、Bring Your Own Key (BYOK) とも呼ばれているカスタマー マネージド キー (CMK) を使用した保存データの暗号化がサポートされています。 DPS によって、データセンターに書き込まれるとき、保存データと転送中データが暗号化され、アクセスするとき、自動的に復号されます。 既定では、DPS は Microsoft マネージド キーを使用して保存データを暗号化します。 CMK を使用すれば、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 管理の key-encryption-key による保存データ暗号化を選択することで、既定のプラットフォーム暗号化に加え、さらに暗号化の層を追加できます。 これによりキーを柔軟に作成、ローテーション、無効化、取り消しできます。 DPS 用に CMK を構成している場合、2 つの保護層で二重の暗号化が有効になっており、データが能動的に保護されます。 

この機能を使用するには、新しい DPS を作成する必要があります。 この機能を試用するには、[Microsoft サポート](https://azure.microsoft.com/support/create-ticket/)経由でお問い合わせください。 Microsoft サポートへのお問い合わせの際は、お客様の会社名とサブスクリプション ID をお知らせください。


## <a name="next-steps"></a>次のステップ

* [Device Provisioning Service の詳細を確認する](./index.yml)

* [Azure Key Vault の詳細を確認する](../key-vault/general/overview.md)
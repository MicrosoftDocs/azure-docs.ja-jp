---
title: カスタマー マネージド キーを使用した Azure IoT Hub 保存データの暗号化 |Microsoft Docs
description: IoT Hub でのカスタマー マネージド キーを使用した保存データの暗号化
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142197"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>IoT Hub でのカスタマー マネージド キーを使用した保存データの暗号化

IoT Hub では、Bring Your Own Key (BYOK) とも呼ばれているカスタマー マネージド キー (CMK) を使用した保存データの暗号化がサポートされています。 Azure IoT Hub によって、データセンターに書き込まれる際の保存データおよび転送中データが暗号化され、アクセス時に復号化されます。 既定では、IoT Hub は Microsoft マネージド キーを使用して保存データを暗号化します。 CMK を使用することで、既定の暗号化に加えて別の暗号化レイヤーを設けることができます。また、[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) で管理されるキー暗号化キーを使用して保存データを暗号化することもできます。 これにより、アクセスの制御を柔軟に作成、ローテーション、無効化、取り消しできます。 BYOK が IoT Hub 用に構成されている場合は、Azure Key Vault で暗号化キーを制御しながら、2 つ目の保護レイヤーを提供する二重暗号化も利用できます。

この機能を利用するには、新しい IoT Hub (Basic または Standard レベル) を作成する必要があります。 この機能を試用するには、[Microsoft サポート](https://azure.microsoft.com/support/create-ticket/)経由でお問い合わせください。 Microsoft サポートへのお問い合わせの際は、お客様の会社名とサブスクリプション ID をお知らせください。


## <a name="next-steps"></a>次のステップ

* [IoT Hub の詳細を確認する](./about-iot-hub.md)

* [Azure Key Vault の詳細を確認する](../key-vault/general/overview.md)
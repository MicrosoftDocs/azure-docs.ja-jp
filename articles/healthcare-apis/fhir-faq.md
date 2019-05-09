---
title: Azure の FHIR サービスについてよく寄せられる質問 (FAQ) - Azure API for FHIR
description: この FAQ の記事では、Azure API for FHIR についてよく寄せられる質問に回答します
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: e3889ed9f758ce2c374eae106674930ba67f7620
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878784"
---
# <a name="frequently-asked-questions-about-azure-api-for-fhir"></a>Azure API for FHIR についてよく寄せられる質問

## <a name="storage-location"></a>保存先

**FHIR&reg; API の背後にあるデータは Azure に格納されますか。** はい。データは Azure のマネージド データベースに格納されます。 Azure API for FHIR は、基になるデータ ストアへの直接アクセスを提供していません。

## <a name="identity-providers"></a>ID プロバイダー

現在当社は、ID プロバイダーとして Microsoft Azure Active Directory をサポートしています。

## <a name="supported-fhir-version"></a>サポートされている FHIR バージョン

現在、バージョン 3.0.1 をサポートしています。 詳細については、「[サポートされる機能](fhir-features-supported.md)」を参照してください。

## <a name="oss-and-azure-api-for-fhir"></a>OSS と Azure API for FHIR

オープン ソースの Microsoft FHIR Server for Azure と Azure API for FHIR の違いは何ですか。 Azure API for FHIR は、Azure の OSS Microsoft FHIR サーバーのホストおよびマネージド バージョンです。 マネージド サービスでは、Microsoft は、すべてのメンテナンス、更新プログラムなどを提供します。OSS FHIR Server for Azure を実行するときに、基になるサービスに直接アクセスできますが、サーバーのメンテナンスや更新、PHI データを格納する場合はすべての必要なコンプライアンス作業も担います。

## <a name="next-steps"></a>次の手順

この記事では、Azure API for FHIR についてよく寄せられる質問のいくつかを確認しました。 Microsoft FHIR Server for Azure のサポートされている API 機能について確認してください。
 
>[!div class="nextstepaction"]
>[サポートされる FHIR 機能](fhir-features-supported.md)
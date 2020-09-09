---
title: Azure API for FHIR でエクスポート設定を構成する
description: この記事では、Azure API for FHIR でエクスポート設定を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/5/2020
ms.author: matjazl
ms.openlocfilehash: 46a55b83b38593a514d40a9f75d99739a1efb8a2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871022"
---
# <a name="configure-export-setting-and-export-the-data-to-a-storage-account"></a>エクスポート設定を構成してデータをストレージ アカウントにエクスポートする

Azure API for FHIR では、Azure API for FHIR アカウントからストレージ アカウントにデータをエクスポートできるようにする $export コマンドがサポートされています。

Azure API for FHIR でエクスポートを実行するには、次の 4 つの手順があります。

1. Azure API for FHIR サービスのマネージド ID を有効にする
2. Azure ストレージ アカウントを作成し (まだ実行していない場合)、Azure API for FHIR へのアクセス許可をストレージ アカウントに割り当てる
3. Azure API for FHIR でストレージ アカウントをエクスポート ストレージ アカウントとして選択する
4. Azure API for FHIR で $export コマンドを呼び出してエクスポートを実行する

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR のマネージド ID の有効化

Azure API for FHIR のエクスポートを構成する最初の手順は、サービスに対するシステム全体のマネージド ID を有効にすることです。 Azure のマネージド ID の詳細については、[こちら](../active-directory/managed-identities-azure-resources/overview.md)で確認できます。

これを行うには、Azure API for FHIR サービスに移動し、[ID] ブレードを選択します。 状態を [オン] に変更すると、Azure API for FHIR サービスでマネージド ID が有効になります。

![マネージド ID の有効化](media/export-data/fhir-mi-enabled.png)

これで、次の手順に進んで、ストレージ アカウントの作成とサービスへのアクセス許可の割り当てを実行できます。

## <a name="adding-permission-to-storage-account"></a>ストレージ アカウントへのアクセス許可の追加

エクスポートの次の手順は、ストレージ アカウントに書き込むためのアクセス許可を Azure API for FHIR サービスに割り当てることです。

ストレージ アカウントを作成したら、ストレージ アカウントの [アクセス制御 (IAM)] ブレードに移動し、[ロールの割り当ての追加] を選択します。

![マネージド ID の有効化](media/export-data/fhir-export-role-assignment.png)

ここでは、ストレージ BLOB データ共同作成者というロールをサービス名に追加します。

![マネージド ID の有効化](media/export-data/fhir-export-role-add.png)

これで、次の手順で、$export の既定のストレージ アカウントとして Azure API for FHIR のストレージ アカウントを選択できるようになりました。

## <a name="selecting-the-storage-account-for-export"></a>$export 用のストレージ アカウントの選択

$Export コマンドを呼び出す前の最後の手順は、Azure API for FHIR によるデータのエクスポート先として使用する Azure ストレージ アカウントを割り当てることです。 これを行うには、Azure portal で Azure API for FHIR サービスの [統合] ブレードに移動し、ストレージ アカウントを選択します。 

![マネージド ID の有効化](media/export-data/fhir-export-storage.png)

その後、$export コマンドを使用してデータをエクスポートできます。

## <a name="exporting-the-data-using-export-command"></a>$export コマンドを使用したデータのエクスポート

エクスポート用に Azure API for FHIR を構成したら、$export コマンドを使用して、指定したストレージ アカウントにサービスからデータをエクスポートできます。 FHIR サーバーで $export コマンドを呼び出す方法については、$export の仕様に関するドキュメント([https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)) を参照してください。

> [!IMPORTANT]
> 現時点では、Azure API for FHIR では、エクスポート仕様 ([https://hl7.org/Fhir/uv/bulkdata/export/index.html](https://hl7.org/Fhir/uv/bulkdata/export/index.html)) に定義されているシステム レベルのエクスポートのみがサポートされていることに注意してください。 また、現時点では、$export でクエリ パラメーターはサポートされていません。

>[!div class="nextstepaction"]
>[[追加設定]](azure-api-for-fhir-additional-settings.md)
---
title: Azure API for FHIR でエクスポート設定を構成する
description: この記事では、Azure API for FHIR でエクスポート設定を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 3/18/2021
ms.author: matjazl
ms.openlocfilehash: ee110420c697afb6ecad857ba823c61d03c6be6c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046993"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>エクスポート設定を構成してストレージ アカウントを設定する

Azure API for FHIR では、Azure API for FHIR アカウントからストレージ アカウントにデータをエクスポートできるようにする $export コマンドがサポートされています。

Azure API for FHIR でエクスポートを構成するには、次の 3 つの手順があります。

1. FHIR サービス用の Azure API でマネージド Id を有効にします。
2. Azure ストレージアカウントを作成し (これまでに実行されていない場合)、FHIR の Azure API へのアクセス許可をストレージアカウントに割り当てます。
3. Azure API for FHIR のストレージアカウントをエクスポートストレージアカウントとして選択します。

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>Azure API for FHIR のマネージド ID の有効化

Azure API for FHIR のエクスポートを構成する最初の手順は、サービスに対するシステム全体のマネージド ID を有効にすることです。 Azure でのマネージド id の詳細については、「 [azure リソースのマネージド Id につい](../../active-directory/managed-identities-azure-resources/overview.md)て」を参照してください。

これを行うには、Azure API for FHIR サービスにアクセスし、[ **id**] を選択します。 状態を **[オン** ] に変更すると、Azure API for FHIR サービスで管理対象 id が有効になります。

![マネージド ID の有効化](media/export-data/fhir-mi-enabled.png)

ここで、ストレージアカウントを作成し、サービスにアクセス許可を割り当てることによって、次の手順に進むことができます。

## <a name="adding-permission-to-storage-account"></a>ストレージ アカウントへのアクセス許可の追加

エクスポートの次の手順では、ストレージアカウントに書き込むための Azure API for FHIR サービスのアクセス許可を割り当てます。

ストレージアカウントを作成したら、ストレージアカウントの **Access Control (IAM)** にアクセスし、[ **ロールの割り当ての追加**] を選択します。

![ロールの割り当てのエクスポート](media/export-data/fhir-export-role-assignment.png)

ここでは、サービス名に role **Storage Blob データ共同作成者** を追加し、[ **保存**] を選択します。

![ロールの追加](media/export-data/fhir-export-role-add.png)

これで、$export の既定のストレージアカウントとして、Azure API for FHIR でストレージアカウントを選択できるようになりました。

## <a name="selecting-the-storage-account-for-export"></a>$export 用のストレージ アカウントの選択

最後の手順は、FHIR 用の Azure API がデータのエクスポートに使用する Azure ストレージアカウントを割り当てることです。 これを行うには、「Azure API for FHIR サービスの **統合** 」にアクセスし、ストレージアカウントを選択します。

![FHIR エクスポート ストレージ](media/export-data/fhir-export-storage.png)

この最後の手順を完了すると、$export コマンドを使用してデータをエクスポートできるようになります。

> [!Note]
> $Export 操作の宛先として登録できるのは、FHIR 用の Azure API の場合と同じサブスクリプション内のストレージアカウントだけです。

データベース設定の構成、アクセス制御、診断ログの有効化、およびカスタムヘッダーを使用した監査ログへのデータの追加の詳細については、以下を参照してください。

>[!div class="nextstepaction"]
>[[追加設定]](azure-api-for-fhir-additional-settings.md)

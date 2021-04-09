---
title: Azure API for FHIR 用に Azure のロールベースのアクセス制御 (Azure RBAC) を構成する
description: この記事では、Azure API for FHIR データ プレーン用に Azure RBAC を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.reviewer: dseven
ms.openlocfilehash: fb57b689b77ec4cc6205c8885c2a2e062c469efb
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018666"
---
# <a name="configure-azure-rbac-for-fhir"></a>FHIR 用に Azure RBAC を構成する 

この記事では、[Azure のロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/index.yml) を使用して、Azure API for FHIR データ プレーンにアクセス権を割り当てる方法について説明します。 データ プレーンのユーザーが Azure サブスクリプションに関連付けられている Azure Active Directory テナントで管理されている場合は、Azure RBAC を使用してデータ プレーンのアクセス権を割り当てることをお勧めします。 外部の Azure Active Directory テナントを使用する場合は、[ローカル RBAC の割り当てのリファレンス](configure-local-rbac.md)に関するページを参照してください。

## <a name="confirm-azure-rbac-mode"></a>Azure RBAC モードの確認

Azure RBAC を使用するには、データ プレーンに Azure サブスクリプション テナントを使用するように Azure API for FHIR を構成し、ID オブジェクト ID が割り当てられていないようにする必要があります。 設定は、Azure API for FHIR の **[認証]** ブレードを調べることで確認できます。

:::image type="content" source="media/rbac/confirm-azure-rbac-mode.png" alt-text="Azure RBAC モードの確認":::

**[機関]** は、お使いのサブスクリプションに関連付けられている Azure Active directory テナントに設定する必要があります。また、 **[許可されたオブジェクト ID]** ボックスには GUID が含まれないようにする必要があります。 また、このボックスは無効になっていて、ラベルにより、データ プレーンのロールを割り当てるために Azure RBAC を使用する必要があることが示されていることがわかります。

## <a name="assign-roles"></a>ロールを割り当てる

ユーザー、サービス プリンシパル、またはグループに FHIR データ プレーンへのアクセス権を付与するには、 **[アクセス制御 (IAM)]** をクリックし、 **[ロールの割り当て]** をクリックし、 **[+ 追加]** をクリックします。

:::image type="content" source="media/rbac/add-azure-rbac-role-assignment.png" alt-text="Azure ロールの割り当ての追加":::

**[ロール]** の選択で、FHIR データ プレーンの組み込みのロールのいずれかを検索します。

:::image type="content" source="media/rbac/built-in-fhir-data-roles.png" alt-text="組み込みの FHIR データ ロール":::

次のいずれかを選択できます。

* FHIR データ リーダー: FHIR データを読み取り (および検索) できます。
* FHIR データ ライター: FHIR データの読み取り、書き込み、および論理的な削除を行うことができます。
* FHIR データ エクスポーター: データの読み取りとエクスポート (`$export` 演算子) を行うことができます。
* FHIR データ共同作成者: すべてのデータ プレーン操作を実行できます。

これらのロールがニーズに十分ではない場合は、[カスタムのロールを作成する](../../role-based-access-control/tutorial-custom-role-powershell.md)こともできます。

**[選択]** ボックスで、ロールを割り当てるユーザー、サービス プリンシパル、またはグループを検索します。

## <a name="caching-behavior"></a>キャッシュ動作

Azure API for FHIR では、決定事項が最大 5 分間キャッシュされます。 許可されたオブジェクト ID の一覧にユーザーを追加することによって FHIR サーバーへのアクセス権を付与する場合、または一覧から削除する場合は、アクセス許可の変更が反映されるまで最大 5 分かかることが予想されます。

## <a name="next-steps"></a>次のステップ

この記事では、FHIR データ プレーンに Azure ロールを割り当てる方法について説明しました。 Azure API for FHIR の追加設定については、以下を参照してください。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)
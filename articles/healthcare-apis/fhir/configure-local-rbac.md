---
title: Azure API for FHIR 用にローカルのロールベースのアクセス制御 (ローカル RBAC) を構成する
description: この記事では、データ プレーンに外部の Azure AD テナントを使用するように Azure API for FHIR を構成する方法について説明します
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019301"
---
# <a name="configure-local-rbac-for-fhir"></a>FHIR 用にローカル RBAC を構成する 

この記事では、データ プレーンのアクセス権を管理するために外部のセカンダリ Azure Active Directory テナントを使用するように、Azure API for FHIR を構成する方法について説明します。 このモードは、お使いのサブスクリプションに関連付けられている Azure Active Directory テナントを使用できない場合にのみ使用してください。

> [!NOTE]
> FHIR サービスのデータ プレーンがお使いのサブスクリプションに関連付けられているプライマリ Azure Active Directory テナントを使用するように構成されている場合は、[Azure RBAC を使用してデータ プレーンのロールを割り当て](configure-azure-rbac.md)てください。

## <a name="add-service-principal"></a>サービス プリンシパルの追加

ローカル RBAC では、FHIR サーバーで外部の Azure Active Directory テナントを使用できます。 ローカル RBAC システムでこのテナントのグループ メンバーシップを確認できるようにするには、Azure API for FHIR でテナント内にサービス プリンシパルが必要です。 このサービス プリンシパルは、Azure API for FHIR をデプロイしたサブスクリプションに関連付けられたテナントに自動的に作成されます。ただし、テナントにサブスクリプションが関連付けられていない場合は、テナント管理者が、次のいずれかのコマンドを使用してこのサービス プリンシパルを作成する必要があります。

`Az` PowerShell モジュールの使用:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

または、`AzureAd` PowerShell モジュールを使用することもできます。

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

または、Azure CLI を使用することもできます。

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>ローカル RBAC を構成する

Azure API for FHIR は、 **[認証]** ブレードで外部またはセカンダリの Azure Active Directory テナントを使用するように構成できます。

![ローカル RBAC の割り当て](media/rbac/local-rbac-guids.png).

機関ボックスに、有効な Azure Active Directory テナントを入力します。 テナントが検証されると、 **[許可されたオブジェクト ID]** ボックスがアクティブになり、ID オブジェクト ID の一覧を入力できるようになります。 これらの ID には、次の ID オブジェクト ID を指定できます。

* Azure Active Directory ユーザー。
* Azure Active Directory サービス プリンシパル。
* Azure Active Directory セキュリティ グループ。

詳細については、[ID オブジェクト ID を検索する](find-identity-object-ids.md)方法に関する記事を参照してください。

必要なオブジェクト ID を入力したら、 **[保存]** をクリックし、変更が保存されるのを待ってから、割り当てられたユーザー、サービス プリンシパル、またはグループを使用してデータ プレーンにアクセスしてみてください。

## <a name="caching-behavior"></a>キャッシュ動作

Azure API for FHIR では、決定事項が最大 5 分間キャッシュされます。 許可されたオブジェクト ID の一覧にユーザーを追加することによって FHIR サーバーへのアクセス権を付与する場合、または一覧から削除する場合は、アクセス許可の変更が反映されるまで最大 5 分かかることが予想されます。

## <a name="next-steps"></a>次のステップ

この記事では、外部 (セカンダリ) の Azure Active Directory テナントを使用して FHIR データ プレーンのアクセス権を割り当てる方法について説明しました。 次に、Azure API for FHIR の追加設定について説明します。
 
>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)

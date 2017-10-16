---
title: "Azure Portal とリソース ポリシー | Microsoft Docs"
description: "Azure Portal を使用して、Resource Manager のポリシーを作成および管理する方法について説明します。 ポリシーは、サブスクリプションまたはリソース グループに適用できます。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
ms.openlocfilehash: 868b2cc1559053057d17b34c03e2e31347f399bf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-portal-to-assign-and-manage-resource-policies"></a>Azure Portal によるリソース ポリシーの割り当てと管理
Azure Portal を使用して、リソース ポリシーをリソース グループやサブスクリプションに割り当てることができます。 割り当てるポリシーを選択したり、そのポリシーにパラメーター値を指定してポリシーの設定をカスタマイズしたりを簡単に行うことができるユーザー インターフェイスが用意されています。 

ポータルを使用してポリシーを割り当てるには、サブスクリプションにポリシー定義があらかじめ存在している必要があります。 サブスクリプションには、リソース グループやサブスクリプションにすぐに割り当てることができる組み込みのポリシー定義が用意されています。 これらの組み込みのポリシーやユーザーが定義したカスタム ポリシーは、ポータルを使用してポリシーを割り当てるときに確認できます。 ポリシーの概要とカスタム ポリシーを定義する方法については、「[リソース ポリシーの概要](resource-manager-policy.md)」をご覧ください。

ポリシーは、すべての子リソースが継承します。 したがって、リソース グループに適用されたポリシーは、そのリソース グループのすべてのリソースに適用されます。 この記事では、**スコープ**という用語はポリシーに割り当てられているリソース グループまたはサブスクリプションのことを指します。 

リソースの作成と更新 (PUT 操作と PATCH 操作) を行うときに、ポリシーが評価されます。

## <a name="assign-a-policy"></a>ポリシーを割り当てる

1. リソース グループまたはサブスクリプションにポリシーを割り当てるには、割り当てる対象のリソース グループまたはサブスクリプションを選択します。 設定で、**[ポリシー]** を選択します。

   ![ポリシーの選択](./media/resource-manager-policy-portal/select-policies.png)

2. このスコープのポリシーの割り当てを作成するには、**[割り当ての追加]** を選択します。

   ![割り当ての追加](./media/resource-manager-policy-portal/add-assignment.png)

3. 割り当てるポリシーを選択します。 サブスクリプションにポリシー定義を追加していない場合でも、割り当て可能な組み込みのポリシーが表示されます。 これらの組み込みのポリシーは、一般的なシナリオの多くを網羅します。

   ![定義の選択](./media/resource-manager-policy-portal/select-definition.png)

4. ポリシーを選択すると、そのポリシーの説明とすべてのパラメーターが表示されます。 たとえば、次の画像は **[Allowed locations (許可されている場所)]** のパラメーターを示します。このパラメーターは、使用可能な場所を制限するポリシーに必要です。

   ![パラメーターの表示](./media/resource-manager-policy-portal/show-parameters.png)

5. ユーザー インターフェイスで、ポリシーのパラメーターに指定する値 (デプロイに使用できる場所など) を選択します。

   ![パラメーターの値を選択する](./media/resource-manager-policy-portal/select-parameters.png)

6. その他のパラメーターの値を指定します。 スコープは、ポリシー割り当てを開始するときに、選択したブレードに基づいて自動的に割り当てられます。 完了したら、 **[OK]** を選択します。

   ![パラメーターを定義する](./media/resource-manager-policy-portal/define-parameters.png)

  指定のスコープにポリシーを割り当てました。

## <a name="view-policy-assignments"></a>ポリシーの割り当てを表示する

ポリシーを割り当てると、そのスコープのポリシーの一覧が表示されます。 **[詳細]** タブには、ポリシー割り当ての概要が表示されます。

![詳細を表示する](./media/resource-manager-policy-portal/show-details.png)

**[割り当てルール]** タブには、ポリシー定義の JSON が表示されます。

![割り当てルールを表示する](./media/resource-manager-policy-portal/show-assignment-rule.png)

## <a name="change-an-existing-policy-assignment"></a>既存のポリシー割り当てを変更する

ポリシーを変更するには、**[割り当ての編集]** または **[削除]** を選択します。

![割り当てを編集または削除する](./media/resource-manager-policy-portal/edit-delete-policy.png)

## <a name="assign-custom-policies"></a>カスタム ポリシーを割り当てる

サブスクリプションでカスタム ポリシーを定義している場合、これらのポリシーはポータルで割り当て可能です。 これらのポリシーは **[カスタム]** で始まります。

![カスタム ポリシー](./media/resource-manager-policy-portal/show-custom-policy.png)

## <a name="next-steps"></a>次のステップ
* ポリシーを定義する JSON 構文について詳しくは、「[リソース ポリシーの概要](resource-manager-policy.md)」をご覧ください。
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。
* [http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json](http://schema.management.azure.com/schemas/2015-10-01-preview/policyDefinition.json) でポリシー スキーマが公開されています。 


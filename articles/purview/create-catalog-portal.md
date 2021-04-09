---
title: 'クイックスタート: Azure portal で Azure Purview アカウントを作成する (プレビュー)'
description: このクイックスタートでは、Azure Purview アカウントを作成し、アクセス許可を構成して、その使用を開始する方法について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 10/23/2020
ms.openlocfilehash: 0346b467bc299b4eb6125df04a4449e94c035e47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666465"
---
# <a name="quickstart-create-an-azure-purview-account-in-the-azure-portal"></a>クイックスタート: Azure portal で Azure Purview アカウントを作成する

> [!IMPORTANT]
> Azure Purview は現在プレビュー段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」に記載されています。

このクイックスタートでは、Azure Purview アカウントを作成します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 所有する [Azure Active Directory テナント](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)。

* ご使用のアカウントには、サブスクリプションにリソースを作成するためのアクセス許可が必要です。

* **ストレージ アカウント** および **EventHub 名前空間** の作成をすべてのアプリケーションに禁止する **Azure Policy** がある場合は、タグを使用してポリシーの例外を作成する必要があります。これは、Purview アカウントを作成する過程で入力できます。 その主な理由は、作成した各 Purview アカウントでマネージド リソース グループを作成し、また、そのリソース グループ内にストレージ アカウントと EventHub 名前空間を作成する必要があるためです。

    > [!important]
    > Azure Policy をお持ちでない場合や、Azure Policy はあるものの、**ストレージ アカウント** と **EventHub 名前空間** の作成が禁止されていない場合、この手順を実行する必要はありません。

    1. Azure portal に移動し、 **[ポリシー]** を検索します。
    1. [カスタム ポリシー定義の作成](../governance/policy/tutorials/create-custom-policy-definition.md)に関するチュートリアルに従うか、`not` 演算子と `resourceBypass` タグを使用して既存のポリシーに 2 つの例外を追加します。

        ```json
        {
          "mode": "All",
          "policyRule": {
            "if": {
              "anyOf": [
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              },
              {
                "allOf": [
                {
                  "field": "type",
                  "equals": "Microsoft.EventHub/namespaces"
                },
                {
                  "not": {
                    "field": "tags['<resourceBypass>']",
                    "exists": true
                  }
                }]
              }]
            },
            "then": {
              "effect": "deny"
            }
          },
          "parameters": {}
        }
        ```
        
        > [!Note]
        > ポリシーでタグを検出できさえすれば、タグは `resourceBypass` 以外のものでもかまいません。後続の手順で Purview を作成する際に、ご自身で値を定義してください。

        :::image type="content" source="./media/create-catalog-portal/policy-definition.png" alt-text="ポリシーの定義の作成方法を示すスクリーンショット。":::

    1. 作成したカスタム ポリシーを使用して、[ポリシーの割り当てを作成](../governance/policy/assign-policy-portal.md)します。

        [ ![ポリシーの割り当ての作成方法を示すスクリーンショット](./media/create-catalog-portal/policy-assignment.png)](./media/create-catalog-portal/policy-assignment.png#lightbox)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="configure-your-subscription"></a>サブスクリプションの構成

ご利用のサブスクリプションで Azure Purview を実行できるよう、必要に応じて、こちらの手順に従ってサブスクリプションを構成します。

   1. Azure portal で、**サブスクリプション** を検索して選択します。

   1. サブスクリプションの一覧から、使用するサブスクリプションを選択します。 サブスクリプションの管理アクセス許可が必要です。

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Azure portal でのサブスクリプションの選択方法を示すスクリーンショット。":::

   1. サブスクリプションについては、**[リソース プロバイダー]** を選択します。 **[リソース プロバイダー]** ペインで、3 つのリソース プロバイダーをすべて検索して登録します。 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      登録されていない場合は、 **[登録]** を選択して登録します。

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Azure portal で Microsoft dot Azure Purview リソース プロバイダーを登録する方法のスクリーンショット。":::

## <a name="create-an-azure-purview-account-instance"></a>Azure Purview アカウント インスタンスの作成

1. Azure portal の **[Purview アカウント]** ページに移動し、 **[追加]** を選択して新しい Azure Purview アカウントを作成します。 または、マーケットプレースに移動して **[Purview アカウント]** を検索し、 **[作成]** を選択することもできます。 追加できる Azure Purview アカウントは一度に 1 つだけであることに注意してください。

   :::image type="content" source="./media/create-catalog-portal/add-purview-instance.png" alt-text="Azure portal で Azure Purview アカウント インスタンスを作成する方法のスクリーンショット。":::

    > [!Note] 
    > Azure Purview では、リージョン間でのアカウントの移動はサポートされていません。 詳細については、[Azure でサポートされるサービスに関するページ](../azure-resource-manager/management/region-move-support.md)をご覧ください。

1. **[基本]** タブで、次の操作を行います。
    1. **リソース グループ** を選択します。
    1. カタログの **Purview アカウント名** を入力します。 スペースと記号は使用できません。
    1. **[場所]** を選択し、 **[次へ: 構成]** を選択します。
1. **[構成]** タブで必要な **[プラットフォーム サイズ]** を選択します。選択できる値は 4 CU (容量ユニット) と 16 CU です。 **タグ** を選択します。
1. **[タグ]** タブで、必要に応じて 1 つまたは複数のタグを追加できます。 これらのタグは Azure portal でのみ使用されます。Azure Purview では使用されません。 

    > [!Note] 
    > **Azure Policy** をご利用の場合で、かつ「**前提条件**」に記載したような例外を追加する必要がある場合は、適切なタグを追加する必要があります。 たとえば、`resourceBypass` タグを追加することができます。:::image type="content" source="./media/create-catalog-portal/add-purview-tag.png" alt-text="Purview アカウントにタグを追加する。":::

1. **[確認および作成]** を選択し、次に **[作成]** を選択します。 作成が完了するまでに数分かかります。 **[Purview アカウント]** ページ上の一覧に、新しく作成した Azure Purview アカウント インスタンスが表示されます。
1. 新しいアカウントのプロビジョニングが完了したら、 **[リソースに移動]** を選択します。

    > [!Note]
    > プロビジョニングが `Conflict` 状態で失敗した場合、**ストレージ アカウント** と **EventHub 名前空間** の作成を Purview に禁止する Azure ポリシーが存在することを意味します。 「**前提条件**」の手順に従って例外を追加する必要があります。
    > :::image type="content" source="./media/create-catalog-portal/purview-conflict-error.png" alt-text="Purview の競合エラー メッセージ":::

1. **[Purview アカウントの起動]** を選択します。

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure Purview アカウント カタログを起動する選択画面のスクリーンショット。":::

## <a name="add-a-security-principal-to-a-data-plane-role"></a>データ プレーン ロールへのセキュリティ プリンシパルの追加

皆さんやそのチームが Azure Purview の使用を開始するには、定義済みのデータ プレーン ロール (**Purview データ閲覧者**、**Purview データ キュレーター**、**Purview データ ソース管理者**) のいずれかに少なくとも 1 つのセキュリティ プリンシパルを追加しておく必要があります。 Azure Purview Data Catalog のアクセス許可について詳しくは、[カタログのアクセス許可](catalog-permissions.md)に関するページを参照してください。

Azure Purview アカウントの **Purview データ キュレーター** データ プレーン ロールにセキュリティ プリンシパルを追加するには:

1. Azure portal の [ **[Purview アカウント]**](https://aka.ms/purviewportal) ページに移動します。

1. 変更する Azure Purview アカウントを選択します。

1. **[Purview アカウント]** ページの **[アクセス制御 (IAM)]** タブを選択します。

1. **[+ 追加]** をクリックします。

[追加] をクリックしたときに、2 つの選択肢が表示され、どちらも無効としてマークされている場合、Azure Purview アカウントのデータ プレーン ロールにユーザーを追加するための適切なアクセス許可がないことを意味します。 Azure Purview アカウントの所有者やユーザー アクセス管理者など、ロールの割り当て権限を持つユーザーを探す必要があります。 適切なユーザーを探すために、 **[ロールの割り当て]** タブを選択し、下へスクロールして [所有者] または [ユーザー アクセス管理者] を見つけ、該当する人物に連絡してください。

1. **[ロールの割り当ての追加]** を選択します。

1. [ロール] には、セキュリティ プリンシパルの使用目的に応じて「**Purview データ キュレーター ロール**」または「**Purview データ ソース管理者ロール**」と入力します (詳細については、「[カタログのアクセス許可](catalog-permissions.md)」および「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](../active-directory/develop/app-objects-and-service-principals.md)」を参照してください)。

1. **[アクセスの割り当て先]** は、既定値の **[User, group, or service principal]\(ユーザー、グループ、またはサービス プリンシパル\)** のままにします。

1. **[選択]** で、割り当てたいサービス プリンシパル、Azure Active Directory グループ、ユーザーの名前を入力し、その名前を結果ペインでクリックします。

1. **[Save]** をクリックします。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この Azure Purview アカウントが不要になったら、次の手順に従って削除してください。

1. Azure portal の **[Purview アカウント]** ページに移動します。

2. このクイックスタートの冒頭で作成した Azure Purview アカウントを選択します。 **[削除]** を選択し、アカウントの名前を入力して、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Purview アカウントを作成する方法について学習しました。

次の記事に進んで、Azure Purview アカウントへのアクセスをユーザーに許可する方法を学習してください。 

> [!div class="nextstepaction"]
> [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
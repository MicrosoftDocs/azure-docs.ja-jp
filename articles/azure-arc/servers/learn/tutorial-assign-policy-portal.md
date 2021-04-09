---
title: チュートリアル - Azure portal を使用した新しいポリシーの割り当て
description: このチュートリアルでは、Azure portal を使用して、Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 9a07e490525ce532f8f843b30b3b83715e65ce3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91826599"
---
# <a name="tutorial-create-a-policy-assignment-to-identify-non-compliant-resources"></a>チュートリアル:準拠していないリソースを識別するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。 Azure Policy では、ゲスト構成ポリシーを使用した、Arc 対応サーバーの状態の監査がサポートされています。 ゲスト構成ポリシーでは、構成は適用されず、マシン内の設定の監査のみが行われます。 このチュートリアルでは、ポリシーの作成と割り当て、およびどの Arc 対応サーバーで Log Analytics エージェントがインストールされていないかの特定のプロセスの手順を説明します。

この手順を終えると、Windows 用または Linux 用の Log Analytics エージェントがインストールされていないマシンが正しく特定されます。 これらはポリシーの割り当てに "_準拠していません_"。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このチュートリアルでは、ポリシー割り当て作成し、" _\[プレビュー]: Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある_" ポリシー定義を割り当てます。

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

   :::image type="content" source="./media/tutorial-assign-policy-portal/search-policy.png" alt-text="[すべてのサービス] で [ポリシー] を検索する" border="false":::

1. Azure Policy ページの左側にある **[割り当て]** を選択します。 割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assignment.png" alt-text="ポリシーの概要ページから [割り当て] ページを選択する" border="false":::

1. **[ポリシー - 割り当て]** ページの上部で **[ポリシーの割り当て]** を選択します。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-assign-policy.png" alt-text="[割り当て] ページでポリシー定義を割り当てる" border="false":::

1. **[ポリシーの割り当て]** ページで、 **[スコープ]** を選択します。その際、省略記号をクリックし、管理グループまたはサブスクリプションを選択します。 任意でリソース グループを選択します。 スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 次に、**[スコープ]** ページの下部にある **[選択]** をクリックします。

   この例では、**Parnell Aerospace** サブスクリプションを使用しています。 お客様によってサブスクリプションは異なります。

1. リソースは **スコープ** に基づいて除外できます。 **除外** は **スコープ** のレベルよりも 1 つ下のレベルで開始されます。 **除外** は省略可能です。ここでは空のまま残してください。

1. **[ポリシー定義]** の省略記号を選択して、使用可能な定義の一覧を開きます。 Azure Policy に組み込まれているポリシー定義を使用できます。 次のようなさまざまな定義を利用できます。

   - タグとその値を強制
   - タグとその値を適用
   - タグが存在しない場合は、リソース グループからタグを継承する

   使用できる組み込みポリシーの部分的な一覧については、[Azure Policy サンプル](../../../governance/policy/samples/index.md)に関する記事をご覧ください。

1. Windows ベースのマシンで Arc 対応サーバー エージェントを有効にしている場合、ポリシー定義の一覧から、" _\[プレビュー]: Log Analytics エージェントは Windows Azure Arc マシンにインストールされる必要がある_" という定義を見つけます。 Linux ベースのマシンの場合、対応するポリシー定義 " _\[プレビュー]: Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある_" を見つけます。 そのポリシーをクリックし、**[選択]** をクリックします。

   :::image type="content" source="./media/tutorial-assign-policy-portal/select-available-definition.png" alt-text="適切なポリシー定義を見つける" border="false":::

1. **[割り当て名]** には選択したポリシー名が自動的に入力されますが、この名前は変更できます。 この例では、選択内容に応じて " _\[プレビュー]: Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある_" または " _\[プレビュー]: Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある_" のままにします。 必要に応じて、**説明** を追加することもできます。 説明では、このポリシーの割り当ての詳細を示します。
   **[割り当て担当者]** には、ログイン ユーザーに基づいて自動的にデータが入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. **[マネージド ID を作成します]** のチェックは外しておいてください。 このボックスは、ポリシーまたはイニシアティブに [deployIfNotExists](../../../governance/policy/concepts/effects.md#deployifnotexists) 効果を含むポリシーが含まれるときにオンにする "_必要があります_"。 このクイック スタートに使用するポリシーにはそれが含まれないため、オフのままにします。 詳しくは、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](../../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. **[割り当て]** をクリックします。

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

ページの左側にある **[コンプライアンス]** を選択します。 次に、作成したポリシーの割り当て " **\[プレビュー]: Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある**" または " **\[プレビュー]: Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある**" を探します。

:::image type="content" source="./media/tutorial-assign-policy-portal/policy-compliance.png" alt-text="ポリシーのコンプライアンスのページのコンプライアンスの詳細" border="false":::

この新しい割り当てに準拠していない既存のリソースがある場合、 **[準拠していないリソース]** の下に表示されます。

既存のリソースに対して条件が評価され、該当した場合、そのリソースはポリシーに準拠していないとしてマークされます。 次の表は、さまざまなポリシーの効果での条件の評価と、その結果であるコンプライアンスの状態を示しています。 Azure portal では評価ロジックは表示されませんが、コンプライアンスの状態の結果は表示されます。 コンプライアンスの状態の結果は、"対応" または "準拠していない" のいずれかです。

| **リソースの状態** | **効果** | **ポリシーの評価** | **コンプライアンスの状態** |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 非準拠 |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 対応 |
| 新規 | Audit、AuditIfNotExist\* | True | 非準拠 |
| 新規 | Audit、AuditIfNotExist\* | False | 対応 |

\* Append、DeployIfNotExist、AuditIfNotExist の各効果では、IF ステートメントが TRUE である必要があります。
また、非準拠となるには、既存の条件が FALSE である必要があります。 TRUE のとき、IF 条件は関連するリソースの既存の条件の評価をトリガーします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次の手順のようにします。

1. [Azure Policy] ページの左側にある **[コンプライアンス]** (または **[割り当て]** ) を選択し、作成したポリシーの割り当て " **\[プレビュー]: Log Analytics エージェントは Windows Azure Arc マシンにインストールする必要がある**" または " **\[プレビュー]: Log Analytics エージェントは Linux Azure Arc マシンにインストールする必要がある**" を探します。

1. ポリシーの割り当てを右クリックし、 **[割り当ての削除]** を選択します。

   :::image type="content" source="./media/tutorial-assign-policy-portal/delete-assignment.png" alt-text="コンプライアンスのページから割り当てを削除する" border="false":::

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ポリシー定義をスコープに割り当て、コンプライアンス レポートを評価しました。 ポリシー定義では、スコープ内のすべてのリソースが準拠していることが検証されて、準拠していないリソースが識別されます。 これで、Azure Monitor for VMs を使用して Azure Arc 対応サーバー マシンを監視することができるようになりました。

ご利用のマシンから、パフォーマンスと実行中のプロセス、およびその依存関係を監視および確認する方法については、引き続き次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [VM 用 Azure Monitor を有効にする](tutorial-enable-vm-insights.md)
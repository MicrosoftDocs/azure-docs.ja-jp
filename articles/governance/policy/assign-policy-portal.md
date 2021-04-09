---
title: ポータルを使用した新しいポリシーの割り当て
description: このクイックスタートでは、Azure portal を使用して、Azure Policy の割り当てを作成し、準拠していないリソースを特定します。
ms.date: 01/29/2021
ms.topic: quickstart
ms.openlocfilehash: e5cbf31e897b5be404327efa254eb90ead990f5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99220889"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources"></a>準拠していないリソースを識別するためのポリシー割り当てを作成する」を参照してください。

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、ポリシーの割り当てを作成して、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシーの割り当てを作成し、"_Managed Disks を使用していない VM の監査_" ポリシー定義を割り当てます。

1. Azure portal で **[すべてのサービス]** を選択し、「**Policy**」を検索して選択することで、Azure Policy サービスを起動します。

   :::image type="content" source="./media/assign-policy-portal/search-policy.png" alt-text="[すべてのサービス] で「Policy」を検索するスクリーンショット。" border="false":::

1. Azure Policy ページの左側にある **[割り当て]** を選択します。 割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。

   :::image type="content" source="./media/assign-policy-portal/select-assignments.png" alt-text="[ポリシーの概要] ページで [割り当て] ページを選択する操作のスクリーンショット。" border="false":::

1. **[ポリシー - 割り当て]** ページの上部で **[ポリシーの割り当て]** を選択します。

   :::image type="content" source="./media/assign-policy-portal/select-assign-policy.png" alt-text="[割り当て] ページで [ポリシーの割り当て] を選択する操作のスクリーンショット。" border="false":::

1. **[ポリシーの割り当て]** ページで、省略記号を選択した後、管理グループまたはサブスクリプションを選択して **[スコープ]** を設定します。 任意でリソース グループを選択します。 スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 次に、**[スコープ]** ページの下部にある **[選択]** ボタンを使用します。

   この例では、**Contoso** サブスクリプションを使用しています。 お客様によってサブスクリプションは異なります。

1. リソースは **スコープ** に基づいて除外できます。 **除外** は **スコープ** のレベルよりも 1 つ下のレベルで開始されます。 **除外** は省略可能です。ここでは空のまま残してください。

1. **[ポリシー定義]** の省略記号を選択して、使用可能な定義の一覧を開きます。 Azure Policy に組み込まれているポリシー定義を使用できます。 次のようなさまざまな定義を利用できます。

   - タグとその値を強制
   - タグとその値を適用
   - タグが存在しない場合は、リソース グループからタグを継承する

   使用できる組み込みポリシーの部分的な一覧については、[Azure Policy サンプル](./samples/index.md)に関する記事をご覧ください。

1. ポリシー定義の一覧で、"_Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)_" 定義を見つけます。 そのポリシーを選択し、**[選択]** ボタンを使用します。

   :::image type="content" source="./media/assign-policy-portal/select-available-definition.png" alt-text="使用可能な定義をフィルター処理するためのスクリーンショット。" border="false":::

1. **[割り当て名]** には選択したポリシー名が自動的に入力されますが、この名前は変更できます。 この例では、"_Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)_" をそのまま使用します。 必要に応じて、**説明** を追加することもできます。 説明では、このポリシーの割り当ての詳細を示します。
   **[割り当て担当者]** には、ログイン ユーザーに基づいて自動的にデータが入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. ポリシーの適用は "_有効_" のままにしておきます。 詳細については、[ポリシー割り当て - 強制モード](./concepts/assignment-structure.md#enforcement-mode)に関するページを参照してください。

1. ページ下部にある **[次へ]** またはページの上部にある **[パラメーター]** タブを選択して、割り当てウィザードの次のセグメントに移動します。

1. **[基本]** タブで選択されたポリシー定義にパラメーターが含まれている場合、このタブで構成します。 _[Managed Disks を使用していない VM の監査]_ にはパラメーターがないので、ページ下部の **[次へ]** を選択するかページ上部の **[修復]** タブを選択して、割り当てウィザードの次のセグメントに移動します。

1. **[マネージド ID を作成します]** のチェックは外しておいてください。 このチェック ボックスは、ポリシーまたはイニシアティブに [deployIfNotExists](./concepts/effects.md#deployifnotexists) または [modify](./concepts/effects.md#modify) 効果を利用したポリシーが含まれる場合に、オンにする "_必要があります_"。 このクイック スタートに使用するポリシーにはそれが含まれないため、オフのままにします。 詳しくは、[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](./how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. ページ下部にある **[次へ]** またはページの上部にある **[Non-compliance messages]\(コンプライアンス違反メッセージ\)** タブを選択して、割り当てウィザードの次のセグメントに移動します。

1. **[Non-compliance message]\(コンプライアンス違反メッセージ\)** を _[Virtual machines should use a managed disk]\(仮想マシンはマネージド ディスクを使用する必要があります\)_ に設定します。 このカスタム メッセージは、リソースが拒否されたときに表示されるほか、コンプライアンス違反のリソースについては、通常の評価時に表示されます。

1. ページ下部にある **[次へ]** またはページの上部にある **[確認と作成]** タブを選択して、割り当てウィザードの次のセグメントに移動します。

1. 選択したオプションを確認してから、ページ下部にある **[作成]** を選択します。

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

ページの左側にある **[コンプライアンス]** を選択します。 次に、作成した "_Managed Disks を使用していない VM の監査_" ポリシー割り当てを見つけます。

:::image type="content" source="./media/assign-policy-portal/policy-compliance.png" alt-text="ポリシーのコンプライアンスのページにあるコンプライアンスの詳細のスクリーンショット。" border="false":::

この新しい割り当てに準拠していない既存のリソースがある場合、**[準拠していないリソース]** の下に表示されます。

既存のリソースに対して条件が評価され、該当した場合、そのリソースはポリシーに準拠していないとしてマークされます。 次の表は、さまざまなポリシーの効果での条件の評価と、その結果であるコンプライアンスの状態を示しています。 Azure portal では評価ロジックは表示されませんが、コンプライアンスの状態の結果は表示されます。 コンプライアンスの状態の結果は、"対応" または "準拠していない" のいずれかです。

| リソースの状態 | 結果 | ポリシーの評価 | コンプライアンスの状態 |
| --- | --- | --- | --- |
| 新機能か更新された機能か | Audit、Modify、AuditIfNotExist | True | 非準拠 |
| 新機能か更新された機能か | Audit、Modify、AuditIfNotExist | False | 対応 |
| Exists | Deny、Audit、Append、Modify、DeployIfNotExist、AuditIfNotExist | True | 非準拠 |
| Exists | Deny、Audit、Append、Modify、DeployIfNotExist、AuditIfNotExist | False | 対応 |

> [!NOTE]
> DeployIfNotExist 効果と AuditIfNotExist 効果が非準拠となるためには、IF ステートメントが TRUE で、存在条件が FALSE である必要があります。 TRUE のとき、IF 条件は関連するリソースの既存の条件の評価をトリガーします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次の手順のようにします。

1. Azure Policy ページの左側の **[コンプライアンス]** (または **[割り当て]**) を選択し、作成した "_Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)_" ポリシーの割り当てを見つけます。

1. "_マネージド ディスクを使用しない VM の監査_" ポリシー割り当てを右クリックし、**[割り当ての削除]** を選択します。

   :::image type="content" source="./media/assign-policy-portal/delete-assignment.png" alt-text="[コンプライアンス] ページからコンテキスト メニューを使用して割り当てを削除するスクリーンショット。" border="false":::

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、ポリシー定義をスコープに割り当て、コンプライアンス レポートを評価しました。
ポリシー定義では、スコープ内のすべてのリソースが準拠していることが検証されて、準拠していないリソースが識別されます。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)
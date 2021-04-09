---
title: チュートリアル:GitHub を使用してコードとしての Azure Policy を実装する
description: このチュートリアルでは、エクスポート、GitHub アクション、および GitHub ワークフローを使用して、コードとしての Azure Policy ワークフローを実装します
ms.date: 10/20/2020
ms.topic: tutorial
ms.openlocfilehash: 76a46adc3fc8efab4f7a2d6e656e83c2537dd037
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92325871"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>チュートリアル:GitHub を使用してコードとしての Azure Policy を実装する

コードとしての Azure Policy ワークフローを使用すると、ポリシーの定義と割り当てをコードとして管理し、それらの定義の更新ライフサイクルを制御し、コンプライアンスの結果の検証を自動化できます。 このチュートリアルでは、Azure Policy 機能と GitHub を使用して、ライフサイクル プロセスを構築する方法について説明します。 これらのタスクは次のとおりです。

> [!div class="checklist"]
> - ポリシー定義と割り当てを GitHub にエクスポートする
> - GitHub で更新されたポリシー オブジェクトを Azure にプッシュする
> - GitHub アクションからコンプライアンス スキャンをトリガーする

既存のリソースの現在のコンプライアンスの状態を識別するためのポリシーを割り当てる方法については、クイックスタートの記事で詳しく説明しています。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
- 「[コードとしての Azure Policy ワークフローの設計](../concepts/policy-as-code.md)」を確認して、このチュートリアルで使用するデザイン パターンを理解してください。

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Azure portal から Azure Policy オブジェクトをエクスポートする

Azure portal からポリシー定義をエクスポートするには、次の手順を実行します。

1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. Azure Policy ページの左側にある **[定義]** を選択します。

1. **[Export definitions]\(定義のエクスポート\)** ボタンを使用するか、ポリシー定義の行の省略記号を選択してから、 **[Export definition]\(定義のエクスポート\)** を選択し ます。

1. **[Sign in with GitHub]\(GitHub でサインイン\)** ボタンを選択します。 リソースをエクスポートする Azure Policy を承認するために GitHub で認証されていない場合は、表示される新しいウィンドウで [GitHub Action](https://github.com/features/actions) に必要なアクセス権を確認し、 **[Authorize AzureGitHubActions]\(AzureGitHubActions を承認する\)** を選択して、エクスポート プロセスを続行します。 完了すると、新しいウィンドウが自動的に閉じます。

1. **[基本]** タブで、次のオプションを設定し、 **[ポリシー]** タブまたは **[Next : Policies]\(次へ: ポリシー\)** ボタンがページの下部にあるので選択します。

   - **Repository filter (リポジトリ フィルター)** :自分が所有しているリポジトリのみを表示するには _[My repositories]\(マイ リポジトリ\)_ に、GitHub アクションによるアクセスを許可したものをすべてを表示するには _[All repositories]\(すべてのリポジトリ\)_ に設定します。
   - **リポジトリ**:Azure Policy リソースをエクスポートするリポジトリを設定します。
   - **ブランチ**:リポジトリにブランチを設定します。 ソース コードにさらにマージする前に更新を検証する場合は、既定以外のブランチを使用することをお勧めします。
   - **ディレクトリ**:Azure Policy リソースをエクスポートする "_ルート レベル フォルダー_"。 このディレクトリの下のサブフォルダーは、エクスポートされるリソースに基づいて作成されます。

1. **[ポリシー]** タブで、省略記号を選択し、管理グループ、サブスクリプション、またはリソース グループの組み合わせを選択して、検索範囲を設定します。
   
1. **[Add policy definition(s)]\(ポリシー定義の追加\)** ボタンを使用して、エクスポートするオブジェクトのスコープを検索します。 開いたサイド ウィンドウ内で、エクスポートする各オブジェクトを選択します。 検索ボックスまたは種類によって選択をフィルター処理します。 エクスポートするオブジェクトをすべて選択したら、ページの下部にある **[追加]** ボタンを使用します。

1. 選択した各オブジェクトについて、 _[Only Definition]\(定義のみ\)_ や _[Definition and Assignment(s)]\(定義と割り当て\)_ など、ポリシー定義の目的のエクスポート オプションを選択し ます。 次に、 **[Review + Export]\(確認 + エクスポート\)** タブまたは **[Next : Review + Export]\(次へ: 確認 + エクスポート\)** ボタンがページ下部にあるので選択します。

   > [!NOTE]
   > _[Definition and Assignment(s)]\(定義と割り当て\)_ オプションを選択した場合、ポリシー定義の追加時にフィルターによって設定されたスコープ内のポリシー割り当てのみがエクスポートされます。

1. **[Review + Export]\(確認 + エクスポート\)** タブで、詳細が一致していることを確認し、ページの下部にある **[エクスポート]** ボタンを使用します。

1. GitHub リポジトリ、ブランチ、"_ルート レベル フォルダー_" を確認して、選択したリソースがソース管理にエクスポートされていることを確認します。

Azure Policy リソースは、選択した GitHub リポジトリと "_ルート レベル フォルダー_" 内の次の構造にエクスポートされます。

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>GitHub で更新されたポリシー オブジェクトを Azure にプッシュする

1. ポリシー オブジェクトがエクスポートされると、作業を開始できるように、`.github/workflows/manage-azure-policy-<randomLetters>.yml` という名前の [GitHub ワークフロー](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) ファイルも作成されます。

   > [!NOTE]
   > GitHub ワークフロー ファイルは、エクスポートを使用するたびに作成されます。 ファイルの各インスタンスは、そのエクスポート アクション時のオプションに固有です。

1. このワークフロー ファイルでは、[Manage Azure Policy](https://github.com/marketplace/actions/manage-azure-policy) アクションを使用して、GitHub リポジトリ内のエクスポートされたポリシー オブジェクトに加えられた変更を Azure Policy にプッシュします。 既定では、このアクションは、Azure に存在するものとは異なるファイルのみを考慮して同期します。 アクションの `assignments` パラメーターを使用して、特定の割り当てファイルに対して行われた変更のみを同期することもできます。 このパラメーターを使用して、特定の環境に対してのみポリシー割り当てを適用できます。 詳細については、[Manage Azure Policy リポジトリの Readme](https://github.com/Azure/manage-azure-policy) を参照してください。

1. 既定では、ワークフローを手動でトリガーする必要があります。 これを行うには、GitHub の **[Actions]\(アクション\)** を使用して、`manage-azure-policy-<randomLetters>` ワークフローを選択し、 **[Run workflow]\(ワークフローの実行\)** の後に、 **[Run workflow]\(ワークフローの実行\)** を再度選択します。

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="GitHub Web インターフェイスの [Actions]\(アクション\) タブ、ワークフロー、および [Run workflow]\(ワークフローの実行\) ボタンのスクリーンショット。":::

   > [!NOTE]
   > ワークフロー ファイルは、既定のブランチ内に存在していないと、検出して手動で実行することはできません。

1. ワークフローにより、ポリシー オブジェクトに対して行われた変更が Azure と同期され、ログ内に状態が記録されます。

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="実行中のワークフローとログに記録された詳細のスクリーンショット。":::

1. また、ワークフローにより、Azure Policy オブジェクトの詳細 `properties.metadata` も追加され、追跡できます。

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="GitHub アクションに固有のメタデータで更新された Azure portal 内の Azure Policy 定義のスクリーンショット。":::

### <a name="trigger-compliance-scans-using-github-action"></a>GitHub アクションを使用してコンプライアンス スキャンをトリガーする

[Azure Policy Compliance Scan アクション](https://github.com/marketplace/actions/azure-policy-compliance-scan)を使用すると、1 つまたは複数のリソース、リソース グループ、またはサブスクリプションに対して [GitHub ワークフロー](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)からオンデマンドのコンプライアンス評価スキャンをトリガーし、それらのリソースのコンプライアンスの状態に基づいてワークフロー パスを変更できます。 また、スケジュールされた時刻に実行するようにワークフローを構成して、最新のコンプライアンスの状態を都合のよいタイミングで取得することもできます。 必要に応じて、この GitHub アクションでは、スキャンされたリソースのコンプライアンスの状態に関するレポートを生成して、詳細な分析やアーカイブを行うこともできます。

次の例では、サブスクリプションに対してコンプライアンス スキャンを実行します。 

```yaml

on:
  schedule:    
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:    
    runs-on: ubuntu-latest
    steps:         
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}} 

    
    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx

```

## <a name="review"></a>確認

このチュートリアルでは、以下のタスクを完了しました。

> [!div class="checklist"]
> - ポリシー定義と割り当てを GitHub にエクスポートする
> - GitHub で更新されたポリシー オブジェクトを Azure にプッシュする
> - GitHub アクションからコンプライアンス スキャンをトリガーする

## <a name="next-steps"></a>次のステップ

ポリシー定義の構造の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Policy の定義の構造](../concepts/definition-structure.md)
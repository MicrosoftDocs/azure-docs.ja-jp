---
title: 作業項目の統合 (プレビュー) - Application Insights
description: Application Insights データが埋め込まれた作業項目を GitHub または Azure DevOps で作成する方法について説明します。
ms.topic: conceptual
ms.date: 02/9/2021
ms.openlocfilehash: ba0a67bad3ba47191414d6b406ab6cb4e6b7da78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731920"
---
# <a name="work-item-integration-preview"></a>作業項目の統合 (プレビュー)

作業項目の統合機能を使用すると、関連する Application Insights データが埋め込まれている作業項目を GitHub または Azure DevOps で簡単に作成できます。

> [!IMPORTANT]
> 現在、作業項目の統合はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-and-configure-a-work-item-template"></a>作業項目テンプレートの作成と構成

1. 作業項目テンプレートを作成するには、Application Insights リソースに移動し、左側の *[構成]* の下にある **[作業項目]** を選択してから、上部にある **[テンプレートの新規作成]** を選択します。

    :::image type="content" source="./media/work-item-integration/create-work-item-template.png" alt-text=" [テンプレートの新規作成] が選択されている [作業項目] タブのスクリーンショット。" lightbox="./media/work-item-integration/create-work-item-template.png":::

    テンプレートが現在存在しない場合は、[エンド ツー エンド トランザクションの詳細] タブから作業項目テンプレートを作成することもできます。 イベントを選択し、右側の **[作業項目を作成する]** 、 **[ブック テンプレートから始める]** の順に選択します。

    :::image type="content" source="./media/work-item-integration/create-template-from-transaction-details.png" alt-text=" [エンド ツー エンド トランザクションの詳細] タブで [作業項目を作成する]、[ブック テンプレートから始める] が選択されたスクリーンショット。" lightbox="./media/work-item-integration/create-template-from-transaction-details.png":::

2. **[テンプレートの新規作成]** を選択した後、追跡システムを選択し、ブックに名前を設定し、選択した追跡システムにリンクして、このテンプレートを格納するリージョンを選択できます (既定は Application Insights リソースがあるリージョンです)。 URL パラメーターは、 `https://github.com/myusername/reponame` や `https://mydevops.visualstudio.com/myproject` など、リポジトリの既定の URL です。

    :::image type="content" source="./media/work-item-integration/create-workbook.png" alt-text=" [作業項目ブック テンプレートの新規作成]のスクリーンショット。":::

## <a name="create-a-work-item"></a>作業項目の作成

 [Performance]\(パフォーマンス\)、[Failures]\(障害\)、[Availability]\(可用性\)、その他のタブからアクセスできる [エンド ツー エンド トランザクションの詳細] で新しいテンプレートにアクセスできます。

1. 作業項目を作成するには、[エンドツーエンドのトランザクションの詳細] に移動してイベントを選択し、 **[作業項目の作成]** を選択して、作業項目テンプレートを選択します。

    :::image type="content" source="./media/work-item-integration/create-work-item.png" alt-text=" [エンド ツー エンド トランザクションの詳細] タブで [作業項目の作成] が選択されたスクリーンショット。" lightbox="./media/work-item-integration/create-work-item.png":::

1. ブラウザーで新しいタブが開き、選択した追跡システムが表示されます。 Azure DevOps では、バグまたはタスクを作成できます。GitHub では、リポジトリに新しい問題を作成できます。 Application Insights によって提供されるコンテキスト情報を使用して、新しい作業項目が自動的に作成されます。

    :::image type="content" source="./media/work-item-integration/github-work-item.png" alt-text=" 自動的に作成された GitHub の問題のスクリーンショット" lightbox="./media/work-item-integration/github-work-item.png":::

    :::image type="content" source="./media/work-item-integration/azure-devops-work-item.png" alt-text=" Azure DevOps で自動的に作成されたバグのスクリーンショット。" lightbox="./media/work-item-integration/azure-devops-work-item.png":::

## <a name="edit-a-template"></a>テンプレートの編集

テンプレートを編集するには、 *[構成]* の下にある **[作業項目]** タブにアクセスし、更新するブックの横にある鉛筆アイコンを選択します。

:::image type="content" source="./media/work-item-integration/edit-template.png" alt-text=" [編集] 鉛筆アイコンが選択されている [作業項目] タブのスクリーンショット。":::

上部にある [編集] ![編集アイコン](./media/work-item-integration/edit-icon.png) を選択して、テンプレートの編集を開始します。 作業項目テンプレートは [Azure Monitor ブック](../visualize/workbooks-overview.md)に基づいています。 作業項目情報は、キーワード クエリ言語を使用して生成されます。 クエリを変更して、チームに必要なコンテキストをさらに追加できます。 編集が完了したら、上部のツールバーにある [保存] アイコン ![保存アイコン](./media/work-item-integration/save-icon.png) 選択してブックを保存します。

:::image type="content" source="./media/work-item-integration/edit-workbook.png" alt-text=" 編集モードの作業項目テンプレート ブックのスクリーンショット。" lightbox="./media/work-item-integration/edit-workbook.png":::

複数の作業項目構成を作成し、各シナリオに合わせてカスタム ブックを作成できます。 また、環境全体で標準の実装を実現する Azure Resource Manager によって、ブックをデプロイすることもできます。
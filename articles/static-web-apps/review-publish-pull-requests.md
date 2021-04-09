---
title: Azure Static Web Apps における実稼働前環境での pull request の確認
description: Azure Static Web Apps で実稼働前環境を使用して pull request の変更を確認する方法について説明します。
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 1cb5f2f9ac941001efb18301f4a54bd0092920ba
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172742"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps-preview"></a>Azure Static Web Apps プレビューにおける実稼働前環境での pull request の確認

この記事では、[Azure Static Web Apps](overview.md) を使用してデプロイされたアプリケーションに対する変更を、実稼働前環境を使用して確認する方法を示します。

実稼働前 (ステージング) 環境は、運用環境で使用できない変更を含む、完全に機能するステージング バージョンのアプリケーションです。

Azure Static Web Apps では、リポジトリに GitHub Actions ワークフローが生成されます。 ワークフローによって監視されるブランチに対して pull request が作成されると、実稼働前環境が構築されます。 実稼働前環境ではアプリがステージされるため、運用環境にプッシュする前にレビューを実行できます。

Azure Static Web Apps を使用する場合は、複数の実稼働前環境を同時に共存させることができます。 監視対象のブランチに対して pull request を作成するたびに、変更を含むステージング バージョンが個別の実稼働前環境にデプロイされます。

実稼働前環境を使用することには多くの利点があります。 たとえば、次のように操作できます。

- 運用とステージングの間の視覚的な変化を確認する。 たとえば、コンテンツやレイアウトの更新内容を表示する。
- 自分のチームに変更内容をデモンストレーションする。
- 異なるバージョンのアプリケーションを比較する。
- 受け入れテストを使用して変更を検証する。
- 運用環境にデプロイする前にサニティ チェックを実行する。

> [!NOTE]
> プレビュー期間中に使用可能な[ステージング環境の数は最大 3 つ](quotas.md)です。

## <a name="prerequisites"></a>前提条件

- Azure Static Web Apps を使用して構成された既存の GitHub リポジトリ。 お持ちでない場合は、[最初の静的アプリの構築](getting-started.md)に関する記事をご覧ください。

## <a name="make-a-change"></a>変更を加える

まず、リポジトリ内で変更を行います。 これは、次の手順に示すように、GitHub 上で直接行うことができます。

1. GitHub 上のプロジェクトのリポジトリに移動し、 **[Branch]\(ブランチ)** ボタンをクリックして新しいブランチを作成します。

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="GitHub インターフェイスを使用して新しいブランチを作成する":::]

    ブランチ名を入力して、 **[Create branch]\(ブランチの作成\)** をクリックします。

1. _app_ フォルダーに移動して、テキストの内容を一部変更します。 たとえば、タイトルや段落を変更できます。 編集するファイルが見つかったら、 **[Edit]\(編集\)** をクリックして変更を加えます。

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="GitHub インターフェイスのファイルの編集ボタン":::

1. 変更が完了したら、 **[Commit changes]\(変更のコミット\)** をクリックして、変更内容をブランチにコミットします。

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="GitHub インターフェイスの [Commit changes]\(変更のコミット\) ボタン":::

## <a name="create-a-pull-request"></a>pull request を作成する

次に、この変更から pull request を作成します。

1. GitHub でプロジェクトの **[Pull request]** タブを開きます。

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="GitHub リポジトリの [Pull request] タブ":::

1. ブランチの **[Compare & pull request]\(比較と pull request\)** ボタンをクリックします。

1. 必要に応じて変更に関する詳細情報を入力し、 **[Create pull request]\(pull request の作成\)** をクリックします。

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="GitHub での pull request の作成":::

必要に応じて、レビュー担当者を割り当てたり、変更内容について説明するコメントを追加したりできます。

> [!NOTE]
> ブランチに新しいコミットをプッシュすることで、複数の変更を行うことができます。 そうすると、pull request は自動的に更新され、すべての変更内容が反映されます。

## <a name="review-changes"></a>変更の確認

pull request が作成されると、[GitHub Actions](https://github.com/features/actions) デプロイ ワークフローが実行され、実稼働前環境に変更がデプロイされます。

ワークフローでアプリのビルドとデプロイが完了すると、GitHub ボットによって実稼働前環境の URL を含むコメントが pull request に追加されます。 このリンクをクリックすると、ステージされた変更を確認できます。

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="実稼働前 URL を含む pull request コメント":::

生成された URL をクリックして、変更内容を確認します。

この URL を詳しく確認すると、次のように構成されていることがわかります: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`。

指定された pull request に対して、この URL は新しい更新をプッシュしても変わりません。 URL が一定であることに加えて、pull request が有効な限り同じ運用前環境が再利用されます。

## <a name="publish-changes"></a>変更を発行する

変更が承認されたら、pull request をマージすることによって、変更内容を運用環境に発行できます。

**[Merge pull request]\(pull request のマージ\)** をクリックします。

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="GitHub インターフェイスの [Merge pull request]\(pull request のマージ\) ボタン":::

マージによって、変更内容が追跡対象のブランチ ("運用" ブランチ) にコピーされます。 その後、追跡対象のブランチでデプロイ ワークフローが開始され、アプリケーションが再構築された後に変更内容が反映されます。

運用環境の変更を確認するには、運用環境の URL を開いて、ライブ バージョンの Web サイトを読み込みます。

## <a name="limitations"></a>制限事項

現在、ステージング バージョンのアプリケーションは、GitHub リポジトリがプライベートであっても、その URL によってパブリックにアクセスできます。

> [!WARNING]
> 実稼働前環境へのアクセスが制限されないため、機密性の高い内容をステージング バージョンに発行する際は注意してください。

Static Web Apps を使用してデプロイした各アプリ用に使用できる実稼働前環境の数は、使用している SKU レベルによって異なります。 たとえば、Free レベルでは、運用環境に加えて 3 つの実稼働前環境を使用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム ドメインを設定する](custom-domain.md)

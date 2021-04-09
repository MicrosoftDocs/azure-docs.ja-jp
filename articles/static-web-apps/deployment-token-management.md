---
title: Azure Static Web Apps のデプロイ トークンをリセットする (プレビュー)
description: Azure Static Web Apps サイトのトークンをリセットする
services: static-web-apps
author: webmaxru
ms.author: masalnik
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 1/31/2021
ms.openlocfilehash: fe1edb2693993d02a705039c18b04c8d1b7b9725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101743772"
---
# <a name="reset-deployment-tokens-in-azure-static-web-apps-preview"></a>Azure Static Web Apps のデプロイ トークンをリセットする (プレビュー)

新しい Azure Static Web Apps サイトを作成するとき、Azure ではデプロイ中にアプリケーションを識別するために使用されるトークンが生成されます。 プロビジョニング時に、このトークンはシークレットとして GitHub リポジトリに格納されます。 この記事では、このトークンの使用と管理の方法について説明します。

通常はデプロイ トークンのことを気にする必要はありませんが、以下のようないくつかの理由で、このトークンを取得またはリセットしなければならないことがあります。

* **トークンの侵害**: トークンが外部パーティに流出した場合は、トークンをリセットします。
* **別の GitHub リポジトリからのデプロイ**: 別の GitHub リポジトリから手動でデプロイする場合は、デプロイ トークンを新しいリポジトリに設定する必要があります。

## <a name="prerequisites"></a>前提条件

- Azure Static Web Apps を使用して構成された既存の GitHub リポジトリ。
- お持ちでない場合は、[最初の静的アプリの構築](getting-started.md)に関する記事をご覧ください。

## <a name="reset-a-deployment-token"></a>デプロイ トークンをリセットする

1. Azure Static Web Apps サイトの _[概要]_ ページで、 **[Manage deployment token]\(デプロイ トークンの管理\)** リンクをクリックします。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token-button.png" alt-text="デプロイ トークンの管理":::

1. **[Reset token]\(トークンのリセット\)** ボタンをクリックします。

    :::image type="content" source="./media/deployment-token-management/manage-deployment-token.png" alt-text="デプロイ トークンのリセット":::

1. _[Deployment token]\(デプロイ トークン\)_ フィールドに新しいトークンを表示した後、 **[クリップボードにコピー]** アイコンをクリックしてトークンをコピーします。


## <a name="update-a-secret-in-the-github-repository"></a>GitHub リポジトリ内のシークレットを更新する

自動デプロイの実行を維持するには、トークンをリセットした後で、対応する GitHub リポジトリ内で新しい値を設定する必要があります。

1. GitHub 上でプロジェクトのリポジトリに移動し、 **[Settings]\(設定\)** タブをクリックします。
1. **[Secrets]\(シークレット\)** メニュー項目をクリックします。 Static Web App のプロビジョニング中に生成された、_AZURE_STATIC_WEB_APPS_API_TOKEN..._ という名前のシークレットが _[Repository secrets]\(リポジトリ シークレット\)_ セクションにあります。

    :::image type="content" source="./media/deployment-token-management/github-repo-secrets.png" alt-text="リポジトリ シークレットの一覧表示":::

    > [!NOTE]
    > このリポジトリの複数のブランチに対して Azure Static Web Apps サイトを作成した場合、この一覧には複数の _AZURE_STATIC_WEB_APPS_API_TOKEN..._ シークレットが表示されます。 Static Web Apps サイトの _[概要]_ タブの _[ワークフローの編集]_ フィールドに表示されているファイル名と突き合わせて、正しいものを選択します。

1. **[Update]\(更新\)** ボタンをクリックして、エディターを開きます。
1. _[Value]\(値\)_ フィールドに、デプロイ トークンの **値を貼り付け** ます。
1. **[Update secret]\(シークレットを更新する\)** をクリックします。

    :::image type="content" source="./media/deployment-token-management/github-update-secret.png" alt-text="リポジトリ シークレットの更新":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [静的サイト ジェネレーターからの発行](publish-gatsby.md)

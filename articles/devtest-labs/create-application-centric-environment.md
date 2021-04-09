---
title: アプリケーション中心の環境を作成 - Azure
description: この記事では、CloudShell Colony と Microsoft Azure を使用して、アプリケーション中心の環境を作成する方法について説明します。
ms.topic: how-to
ms.date: 11/26/2020
ms.openlocfilehash: 88244f268d5ed038e9bb7082d3d5cc1179e5ec4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94918017"
---
# <a name="create-an-application-centric-environment"></a>アプリケーション中心の環境を作成

[Quali の CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) は、Azure や Kubernetes などのクラウド テクノロジーに基づく複雑なアプリケーション中心の環境のための大規模なインフラストラクチャ自動化を実現する SaaS プラットフォームです。 CloudShell Colony は、Azure DevTest Labs を補完して、開発チームが、実稼働までの価値ストリーム全体に複雑なアプリケーションをデプロイできるようにします。

この記事では、CloudShell Colony と Microsoft Azure を使用して、アプリケーション中心の環境を作成する方法について説明します。

## <a name="set-up-the-environment-with-cloudshell-colony-and-microsoft-azure"></a>CloudShell Colony と Microsoft Azure を使用して環境を設定する

1. [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) の無料試用版にサインアップします。

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="無料試用版にサインアップ":::    
1. Azure アカウントをリンクします ([こちらの手順を参照](https://colonysupport.quali.com/hc/articles/360008222234))。

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Colony へようこそ":::     
1. 自分のスペースにユーザーを招待します。
1. YAML ファイルを使用して、初めてのブループリントを作成します ([こちらの手順を参照](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint))。
    1. GitHub または BitBucket のブループリント リポジトリを Colony にリンクします。
    1. Colony のサンプル ブループリントを基盤として使用し、必要に応じて変更します。

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="ストレス テスト":::    
    1. 他のユーザーが使用できるようにブループリントを公開します。
1. Colony を使用して、サンドボックスでアプリケーション環境を起動します。

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Colony を使用して、サンドボックスでアプリケーション環境を起動する":::    

> [!NOTE]
> このブループリントを、Azure DevOps の CI/CD ワークフローの一部として統合することもできます ([こちらの手順を参照](https://colonysupport.quali.com/hc/articles/360008464234))。

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Azure DevOps パイプラインに接続する":::    

## <a name="next-steps"></a>次のステップ

[Colony のデモを依頼する](https://info.quali.com/cloudshell-colony-demo-request)

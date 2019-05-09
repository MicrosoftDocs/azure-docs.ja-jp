---
title: Project Acoustics の Azure Batch アカウントの設定
titlesuffix: Azure Cognitive Services
description: このハウツー記事では、Project Acoustics Unity と Unreal エンジンの統合に使用する Azure Batch アカウントの設定について説明します。
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58309675"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Project Acoustics の Azure Batch アカウントの設定
このハウツー記事では、Project Acoustics Unity と Unreal エンジンの統合に使用する Azure Batch アカウントの設定について説明します。

## <a name="get-an-azure-subscription"></a>Azure サブスクリプションを取得する
Batch および Storage アカウントを設定する前に、[Azure サブスクリプション](https://azure.microsoft.com/free/)が必要です。 初めてサインアップする場合、Azure には、期間限定のいくつかの無料のリソースと 200 ドルのクレジットが用意されています。

## <a name="create-azure-batch-and-storage-accounts"></a>Azure Batch および Storage アカウントを作成する
次に、[次の手順](https://docs.microsoft.com/azure/batch/batch-account-create-portal)に従って、Azure Batch アカウントとそれに関連付けられた Azure Storage アカウントを設定します。

Batch アカウントと Storage アカウントの両方について、既定のオプションを選択します。
  
  ![既定の設定を表示する Azure Batch の新しいアカウントのスクリーンショット](media/new-batch-account-create.png)

  ![既定の設定を表示する Azure Storage の新しいアカウントのスクリーンショット](media/batch-storage-account-create.png)

Azure がこれらのアカウントをデプロイするには数分かかります。 ポータルの右上隅にある完了通知を探してください。
  
  ![Azure アカウントのデプロイ完了通知のスクリーンショット](media/batch-accounts-deploy-notification.png)

アカウントがダッシュボードに表示されるようになります。
  
  ![Batch および Storage アカウントを示す Azure portal ダッシュボードのスクリーンショット](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Azure 資格情報を使用して音響ベイク UI を設定する
ダッシュボードの [Batch アカウント] リンクをクリックしてから、[Batch アカウント] ページの **[キー]** リンクをクリックして資格情報にアクセスします。
  
  ![[キー] ページへのリンクを強調表示した Azure Batch アカウントのスクリーンショット](media/batch-access-keys.png)

  ![アクセス キーを示す Azure Batch アカウントの [キー] ページのスクリーンショット](media/batch-keys-info.png)

ページの **[ストレージ アカウント]** リンクをクリックして Azure Storage アカウントの資格情報にアクセスします。
  
  ![アクセス キーを示す Azure Storage アカウントの [キー] ページのスクリーンショット](media/storage-keys-info.png)

これらの資格情報を [Unity ベイク プラグイン](unity-baking.md)または [Unreal ベイク プラグイン](unreal-baking.md)に入力します。

## <a name="node-types-and-region-support"></a>ノードの種類とリージョンのサポート
Project Acoustics には、すべての Azure リージョンではサポートされない可能性のある、Fsv2 および H シリーズ コンピューティング最適化 Azure VM ノードが必要です。 [この表](https://azure.microsoft.com/global-infrastructure/services)をチェックして、Batch アカウントの正しい場所を選択していることを確認してください。
![リージョン別の Azure Virtual Machines を示すスクリーンショット](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>クォータのアップグレード
Azure Batch アカウントは、アカウント作成時に、20 コンピューティング コアの制限でプロビジョニングされます。 音響ワークロードは、シーン内のプローブ ポイントの数まで多数のノードにまたがって並列化できるため、この制限を増やしてベイク時間を速くすることもできます。 Azure Batch ポータル ページの **[クォータ]** リンクをクリックしてから、**[Request Quota Increase] (クォータの増加を要求する)** をクリックすることによって、クォータの増加を要求できます。

![Azure [クォータ] ページのスクリーンショット](media/azure-quotas.png)

## <a name="next-steps"></a>次の手順
* [Unity](unity-integration.md) または [Unreal](unreal-integration.md) プロジェクトに Project Acoustics プラグインを統合する


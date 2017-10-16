---
title: "Azure サブスクリプションを Azure Cost Management に登録する | Microsoft Docs"
description: "Azure サブスクリプションを使用して、Azure Cost Management by Cloudyn に登録します。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/14/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.openlocfilehash: 64699db16eaa55d6206f7631ced3072e21cd2074
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>個別の Azure サブスクリプションを登録し、コスト データを確認する

Azure サブスクリプションを使用して、Azure Cost Management by Cloudyn に登録します。 登録すると、Cloudyn ポータルにアクセスできます。 このクイック スタートでは、Cloudyn 試用版サブスクリプションの作成に必要な登録プロセスと、Cloudyn ポータルへのサインインについて詳しく説明します。 コスト データの表示を今すぐに開始する方法についても説明します。

## <a name="log-in-to-azure"></a>Azure へのログイン

- Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-trial-registration"></a>試用版の登録を作成する

1. Azure Portal で、サービスの一覧の **[Cost Management + Billing]\(コスト管理 + 課金\)** をクリックします。
2. **[概要]** の **[コスト管理]** をクリックします。  
    ![コスト管理ページ](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. **[コスト管理]** ページで **[Go to Cost Management]\(コスト管理に移動\)** をクリックし、新しいウィンドウで [Cloudyn registration]\(Cloudyn 登録\) ページを開きます。
4. [Cloudyn ポータル試用版の登録] ページで、会社名を入力し、**[Azure Individual Subscription Owner]\(Azure サブスクリプションの個人所有者\)** を選択し、**[次へ]** をクリックします。 お使いのアカウント名とテナント ID が、自動的にフォームに追加されます。  
    ![試用版登録](./media/quick-register-azure-sub/trial-reg-ind.png)
5. お使いのサブスクリプションに関連付けられた **[Offer ID - Name]\(オファー ID - 名前\)** を選択します。 お使いのレート ID とサブスクリプションの対応が不明な場合は、Azure の請求書を確認して**オファー ID** を調べることができます。
6. 使用条件に同意し、情報を検証して **[次へ]** をクリックします。
7. **[Gather additional data]\(追加データを収集する\)** ページで、**[次へ]** をクリックし、Cloudyn を承認して Azure リソース データを収集します。 収集されたデータには、お客様のサブスクリプションの使用状況、パフォーマンス、請求、サブスクリプションからのタグ データなどが含まれています。  
    ![追加データを収集する](./media/quick-register-azure-sub/gather-additional.png)
9. お使いのブラウザーで、Cloudyn のサインイン ページに移動します。 Azure サブスクリプションの資格情報でサインインします。
9. **[Go to Cloudyn]\(Cloudyn に移動\)** をクリックして、Cloudyn ポータルを開くと、**[Accounts Management]\(アカウント管理\)** ページに、Azure サブスクリプション アカウント情報が表示されます。  
    ![アカウントの管理](./media/quick-register-azure-sub/accounts-mgt.png)

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure のサブスクリプション情報を使用して、Cost Management に登録しました。 また、Cloudyn ポータルにサインインし、コスト データの確認を開始しました。 Cloudyn の Azure Cost Management については、Cost Management のチュートリアルで続きを説明します。

> [!div class="nextstepaction"]
> [コスト データの確認](./tutorial-review-usage.md)

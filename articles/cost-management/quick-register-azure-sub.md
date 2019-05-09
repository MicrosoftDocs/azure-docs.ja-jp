---
title: Cloudyn への Azure サブスクリプションの登録 |Microsoft Docs
description: このクイック スタートでは、Cloudyn 試用版サブスクリプションの作成に必要な登録プロセスと、Cloudyn ポータルへのサインインについて詳しく説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management
manager: benshy
ms.openlocfilehash: 8d4772de72f266fbe47158da36a77d5c24450e37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58002532"
---
# <a name="register-an-individual-azure-subscription-and-view-cost-data"></a>個別の Azure サブスクリプションを登録し、コスト データを確認する

Azure サブスクリプションを使用して Cloudyn に登録します｡ この登録によって、Cloudyn ポータルにアクセスできるようになります。 このクイック スタートでは、Cloudyn 試用版サブスクリプションの作成に必要な登録プロセスと、Cloudyn ポータルへのサインインについて詳しく説明します。 コスト データの表示を今すぐに開始する方法についても説明します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

- Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="register-with-cloudyn"></a>Cloudyn に登録する

1. Azure Portal で、サービスの一覧の **[Cost Management + Billing]\(コスト管理 + 課金\)** をクリックします。
2. **[概要]** の **[Cloudyn]** をクリックします｡  
    ![Azure portal に表示される [Cloudyn] ページ](./media/quick-register-azure-sub/cost-mgt-billing-service.png)
3. **[コスト管理]** ページで **[Go to Cloudyn]** をクリックし、新しいウィンドウで [Cloudyn registration (Cloudyn 登録)] ページを開きます。
4. [Cloudyn ポータル試用版の登録] ページで、会社名を入力し、**[Azure Individual Subscription Owner]\(Azure サブスクリプションの個人所有者\)** を選択し、**[次へ]** をクリックします。 お使いのアカウント名とテナント ID が、自動的にフォームに追加されます。  
    ![登録情報を入力する試用版登録ページ](./media/quick-register-azure-sub/trial-reg-ind.png)
5. お使いのサブスクリプションに関連付けられた **[Offer ID - Name]\(オファー ID - 名前\)** を選択します。 お使いのレート ID とサブスクリプションの対応が不明な場合は、Azure の請求書を確認して**オファー ID** を調べることができます。
6. 使用条件に同意し、情報を検証して **[次へ]** をクリックします。
7. **[Gather additional data]\(追加データを収集する\)** ページで、**[次へ]** をクリックし、Cloudyn を承認して Azure リソース データを収集します。 収集されたデータには、お客様のサブスクリプションの使用状況、パフォーマンス、請求、サブスクリプションからのタグ データなどが含まれています。  
    ![Cloudyn を承認する追加データ収集ページ](./media/quick-register-azure-sub/gather-additional.png)
8. お使いのブラウザーで、Cloudyn のサインイン ページに移動します。 Azure サブスクリプションの資格情報でサインインします。
9. **[Go to Cloudyn]\(Cloudyn に移動\)** をクリックして、Cloudyn ポータルを開くと、**[Accounts Management]\(アカウント管理\)** ページに、Azure サブスクリプション アカウント情報が表示されます。  
    ![Azure サブスクリプション情報を表示している [アカウントの管理] ページ](./media/quick-register-azure-sub/accounts-mgt.png)

Azure サブスクリプションの登録に関するチュートリアル ビデオを見るには、[Cloudyn で使用するディレクトリ GUID とレート ID の検索](https://youtu.be/PaRjnyaNGMI)を参照してください。

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure のサブスクリプション情報を使用して Cloudyn に登録しました。 また、Cloudyn ポータルにサインインし、コスト データの確認を開始しました。 Cloudyn に関する詳細は、Cloudyn のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [使用状況とコストを確認する](./tutorial-review-usage.md)

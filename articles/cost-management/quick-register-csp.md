---
title: "CSP パートナー情報を使用して Azure Cost Management に登録する | Microsoft Docs"
description: "CSP パートナー情報を使用して、Cloudyn の Azure Cost Management に登録します。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 09/15/2017
ms.topic: quickstart
ms.custom: mvc
ms.service: cost-management
manager: carmonm
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 77de126d3f4f6c01a6ca574b73ee9c73383151dd
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---


# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>CSP パートナー プログラムに登録してコスト データを確認する

CSP パートナーであれば、Cloudyn の Azure Cost Management に登録できます。 登録すると、Cloudyn ポータルにアクセスできます。 このクイック スタートでは、Cloudyn 試用版サブスクリプションの作成に必要な登録プロセスと、Cloudyn ポータルへのサインインについて詳しく説明します。 コスト データの表示を今すぐに開始する方法についても説明します。

登録を完了するには、パートナー センター API へのアクセスが可能なパートナー プログラム管理者である必要があります。 認証とデータ アクセスには、パートナー センター API の構成が必要です。 詳細については、パートナー センター API への接続に関する記事をご覧ください。

## <a name="log-in-to-azure"></a>Azure へのログイン

- Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-trial-registration"></a>試用版の登録を作成する

1. Azure ポータルで、サービスの一覧の **[Cost Management + Billing]\(コスト管理 + 課金\)** をクリックします。
2. **[概要]** の **[コスト管理]** をクリックします。  
    ![コスト管理ページ](./media/quick-register-csp/cost-mgt-billing-service.png)
3. **[コスト管理]** ページで **[Go to Cost Management]\(コスト管理に移動\)** をクリックし、新しいウィンドウで [Cloudyn registration]\(Cloudyn 登録\) ページを開きます。
4. Cloudyn ポータル試用版登録のページで、会社名を入力し、**[Microsoft CSP Partner Program Administrator]\(Microsoft CSP パートナー プログラム管理者\)** を選択し、**[次へ]** をクリックします。  
5. **[アプリケーション ID]**、**[Commerce ID]\(コマース ID\)**、**[Application Secret key]\(アプリケーションの秘密鍵\)**を入力し、**[Default Pricing Plan]\(既定の料金プラン\)** を選択します。 手元に情報がない場合は、パートナー センター ポータル [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) にプライマリ管理者アカウントでサインインして、次の手順を実行します。
  1. **[ダッシュボード]** > **[アカウント設定]** > **[アプリ管理]** の順に移動します。
  2. 以前に Web アプリを作成したことがある場合は、この手順をスキップします。 作成したことがない場合は、**[Web アプリ]** セクションの **[新規 Web アプリの追加]** をクリックします。
  3. ご自分の Web アプリケーションから、**アプリ ID** の GUID をコピーします。
  4. キーの有効期間は、必要に応じて、1 年または 2 年を選択します。 **[キーの追加]** を選択し、秘密鍵の値をコピーして保存します。  
    ![パートナー センター](./media/quick-register-csp/csp-partner-center.png)
  5. [登録] ページに戻って、情報を貼り付けます。  
      ![CSP アカウントの資格情報](./media/quick-register-csp/csp-reg.png)
6. 使用条件に同意し、情報を検証します。 **[次へ]** をクリックし、Cloudyn を承認して Azure リソース データを収集します。 収集されたデータには、お客様のサブスクリプションの使用状況、パフォーマンス、請求、サブスクリプションからのタグ データなどが含まれています。  
7. **[Invite other stakeholders]\(その他の関係者を招待\)** の下に電子メール アドレスを入力することで、ユーザーを追加できます。 完了したら、**[次へ]** をクリックします。 請求データをすべて Cloudyn に追加するには、約 2 時間かかります。
8. **[Go to Cloudyn]\(Cloudyn に移動\)** をクリックして Cloudyn ポータルを開くと、**[Cloud Accounts Management]\(クラウド アカウント管理\)** ページに、登録された CSP アカウント情報が表示されます。

[!INCLUDE [cost-management-create-account-view-data](../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、CSP 情報を使用して、Cost Management に登録しました。 また、Cloudyn ポータルにサインインし、コスト データの確認を開始しました。Cloudyn の Azure Cost Management については、コスト管理のチュートリアルで続きをご覧ください。

> [!div class="nextstepaction"]
> [コスト データの確認](./tutorial-review-usage.md)


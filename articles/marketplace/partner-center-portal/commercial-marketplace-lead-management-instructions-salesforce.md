---
title: Salesforce でのリード管理 - Microsoft コマーシャル マーケットプレース
description: Salesforce を使用して Microsoft AppSource および Azure Marketplace のリードを構成する方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94489320"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce 用にリード管理を構成する

この記事では、Microsoft AppSource および Azure Marketplace のオファーからリードを処理するように Salesforce システムを設定する方法について説明します。

> [!NOTE]
> Azure Marketplace では、 **[国]** フィールドの値の一覧など、事前設定済みのリストはサポートされていません。 続行する前に、リストが設定されていないことを確認してください。 代わりに、リードを受信するために [HTTPS エンドポイント](./commercial-marketplace-lead-management-instructions-https.md)や [Azure テーブル](./commercial-marketplace-lead-management-instructions-azure-table.md)を構成できます。

## <a name="set-up-your-salesforce-system"></a>Salesforce システムを設定する

1. Salesforce にサインインします。
1. **[Web-to-Lead]\(Web からリード\)** の設定に移動します。 
    
    Salesforce Lighting Experience を使用している場合
    1. Salesforce ホーム ページで **[セットアップ]** を選択します。

       ![Salesforce のセットアップ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. **[セットアップ]** ページで、 **[Platform Tools]\(プラットフォーム ツール\)**  >  **[機能の設定]**  >  **[マーケティング]**  >  **[Web-to-Lead]** にアクセスします。

        ![Salesforce の Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Salesforce Classic Experience を使用している場合

    1. Salesforce ホーム ページで **[セットアップ]** を選択します。

       ![Salesforce Classic のセットアップ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. **[セットアップ]** ページで、 **[ビルド]**  >  **[カスタマイズ]**  >  **[リード]**  >  **[Web-to-Lead]** を選択します。

        ![Salesforce Classic の Web-to-Lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   残りの手順は、両方の Salesforce エクスペリエンスで同じです。

1. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** ページで、 **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** ボタンを選択します。
1. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** で **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** を選択します。

    ![Salesforce の [Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** で、`Include reCAPTCHA in HTML` 設定がクリアされていることを確認し、 **[生成]** を選択します。

    ![Salesforce の [Create Web-to-Lead Form]\(Web-to-Lead ウィンドウの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. 一部の HTML テキストが表示されます。 "oid" というテキストを検索し、HTML テキストから **"oid" 値** (引用符で囲まれたテキストのみ) をコピーして保存します。 この値を発行ポータル上の **[組織 ID]** フィールドに貼り付けます。

    ![Salesforce の HTML "oid" 値を示す [Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. **[完了]** を選択します。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>リードを Salesforce に送信するようにオファーを構成する

発行ポータル内でオファー用にリード管理情報を構成する準備ができたら、次の手順に従います。

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. オファーを選択し、 **[Offer setup]\(オファーのセットアップ\)** タブにアクセスします。

1. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="[潜在顧客]":::

1. **[接続の詳細]** ポップアップ ウィンドウで、 **[リードのターゲット]** に対して **[Salesforce]** を選択し、Web-to-Lead フォームの `oid` 値を **[組織 ID]** フィールドに貼り付けます。

    ![[接続の詳細] ポップアップウィンドウの連絡先の電子メールの [検証] ボックス](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. **[連絡先の電子メール]** で、新しいリードを受信したときに、メール通知を受け取る必要がある社内のユーザーのメール アドレスを入力します。 セミコロンで区切ることで、複数の電子メールを指定できます。

1. **[OK]** を選択します。

リードのターゲットに正常に接続されたことを確認するには、 **[検証]** を選択します。 成功した場合は、リードのターゲットにテスト リードが表示されます。

>[!NOTE]
>オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

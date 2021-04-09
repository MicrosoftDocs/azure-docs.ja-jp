---
title: Marketo でのリード管理 - Microsoft コマーシャル マーケットプレース
description: Marketo CRM システムを使用して、Microsoft AppSource および Azure Marketplace からリードを管理する方法について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: cbd12481312d310add15bf3d41e21e9c416f1c39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94491077"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Marketo を使用してコマーシャル マーケットプレースのリードを管理する

この記事では、Microsoft AppSource および Azure Marketplace のオファーからリードを処理するように Marketo CRM システムを設定する方法について説明します。

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM システムを設定する

1. Marketo にサインインします。

1. **[Design Studio]** を選択します。

    ![Marketo の Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  **[New Form]\(新しいフォーム\)** を選択します。

    ![Marketo の新しいフォーム](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  **[新しいフォーム]** ダイアログ ボックスの必須フィールドを入力して、 **[作成]** を選択します。

    ![Marketo の新しいフォームの作成](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  **[フィールドの詳細]** で、 **[完了]** を選択します。

    ![Marketo の完了フォーム](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  承認して閉じます。

1. **[MarketplaceLeadBackend]** タブで、 **[埋め込みコード]** を選択します。 

    ![Marketo 埋め込みコード](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo の [Embed Code]\(埋め込みコード\) では、次の例のようなコードが表示されます。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. [埋め込みコード] フォームに表示されている以下のフィールドの値をコピーします。 これらの値を使用して、次の手順でリードを受け取るようにオファーを構成します。 Marketo の埋め込みコードの例から必要な ID を取得するには、次の例をガイドとして使用します。

    - Server ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Form ID = **1179**

    これらの値を把握する別の方法を次に示します。

    - サーバー ID は、Marketo インスタンスの URL (`serverID.marketo.com` など) にあります。
    - **[Munchkin アカウント ID]** フィールドで **[管理者]**  >  **[Munchkin]** メニューの順に移動するか、Marketo REST API ホスト サブドメインの最初の部分 (`https://{Munchkin ID}.mktorest.com`) から、ご利用のサブスクリプションの Munchkin ID を取得します。
    - フォーム ID は、マーケットプレースからリードをルーティングするために手順 7 で作成した [埋め込みコード] フォームの ID です。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>リードを Marketo に送信するようにオファーを構成する

発行ポータル内でオファー用にリード管理情報を構成する準備ができたら、次の手順に従います。 

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. オファーを選択し、 **[Offer setup]\(オファーのセットアップ\)** タブにアクセスします。

1. **[潜在顧客]** セクションで、 **[接続]** を選択します。

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="[潜在顧客]":::

1. **[接続の詳細]** ポップアップ ウィンドウで、 **[リードのターゲット]** として **[Marketo]** を選択します。

    ![リードのターゲットを選択する](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. **[サーバー ID]** 、 **[Munchkin アカウント ID]** および **[フォーム ID]** を指定します。

    > [!NOTE]
    > オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。 

1. **[連絡先の電子メール]** で、新しいリードを受信したときに、メール通知を受け取る必要がある社内のユーザーのメール アドレスを入力します。 セミコロンで区切ると、複数のメール アドレスを指定できます。

1. **[OK]** を選択します。

   リードのターゲットに正常に接続されたことを確認するには、 **[検証]** を選択します。 成功した場合は、リードのターゲットにテスト リードが表示されます。

   ![[接続の詳細] ポップアップ ウィンドウ](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)

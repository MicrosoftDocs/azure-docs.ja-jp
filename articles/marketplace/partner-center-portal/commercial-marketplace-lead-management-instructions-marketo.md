---
title: Marketo でリード管理を構成する | Azure Marketplace
description: Azure Marketplace のお客様向けに Marketo でリード管理を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 9470a593f5db564689a0b26601a201874c215aa2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901371"
---
# <a name="configure-lead-management-in-marketo"></a>Marketo でリード管理を構成する

この記事では、マーケットプレース オファーからリードを処理するように Marketo CRM システムをセットアップする方法について説明します。

## <a name="set-up-your-marketo-crm-system"></a>Marketo CRM システムを設定する

1. Marketo にサインインします。
2. **[Design Studio]** を選択します。
    ![Marketo の Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  **[New Form]\(新しいフォーム\)** を選択します。
    ![Marketo の [New Form]\(新しいフォーム\)](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  [New Form]\(新しいフォーム\) の必須フィールドを入力して、 **[Create]\(作成\)** を選択します。
    ![Marketo の新しいフォームの作成](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  [Field Details]\(フィールドの詳細\) で、 **[Finish]\(完了\)** を選択します。
    ![Marketo の完了フォーム](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  承認して閉じます。

7. *[MarketplaceLeadBacked]* タブで、 **[埋め込みコード]** を選択します。 

    ![埋め込みコード](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Marketo の [Embed Code]\(埋め込みコード\) では、次の例のようなコードが表示されます。

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. [埋め込みコード] フォームに表示されている以下のフィールドの値をコピーします。 これらの値を使用して、次の手順でリードを受け取るようにオファーを構成します。 Marketo の [Embed Code]\(埋め込みコード\) の例から必要な ID を取得するには、次の例をガイドとして使用します。

    - Server ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Form ID = **1179**

    **これらの値を把握する別の方法**

    - サーバー ID は、Marketo インスタンスの URL ("`serverID.marketo.com`" など) にあります。
    - [Munchkin アカウント ID] フィールドで [管理者] > [Munchkin] メニューに移動することで、または Marketo REST API ホスト サブドメインの最初の部分 (`https://{Munchkin ID}.mktorest.com`) から、サブスクリプションの Munching ID を取得します。
    - フォーム ID は、マーケットプレースから リード をルーティングするために手順 7. で作成した [埋め込みコード] フォームの ID です。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>リードを Marketo に送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。 

1. オファーの **[オファーのセットアップ]** ページに移動します。
1. [リード管理] セクションで **[接続]** を選択します。 

    ![[リード管理] - [接続]](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. [接続の詳細] ポップアップ ウィンドウで、[リードのターゲット] として **[Marketo]** を選択します。

    ![リードのターゲットを選択する](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. **[サーバー ID]** 、 **[Munching アカウント ID]** および **[フォーム ID]** を指定します。

    >[!Note]
    >オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。 


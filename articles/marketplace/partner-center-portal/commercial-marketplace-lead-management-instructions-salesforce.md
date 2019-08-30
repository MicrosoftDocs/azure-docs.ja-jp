---
title: Salesforce 用にリード管理を構成する | Azure Marketplace
description: Azure Marketplace のお客様向けに Salesforce でリード管理を構成します。
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: d941a7902dd374a931147bdaa8f015c46268ee8a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901339"
---
# <a name="configure-lead-management-for-salesforce"></a>Salesforce 用にリード管理を構成する

この記事では、マーケットプレース オファーからリードを処理するように Salesforce システムをセットアップする方法について説明します。

## <a name="set-up-your-salesforce-system"></a>Salesforce システムを設定する

1. Salesforce にサインインします。
2. Salesforce Lighting Experience を使用している場合。
    1. Salesforce ホーム ページから **[セットアップ]** を選択します。
    ![Salesforce のセットアップ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. [セットアップ] ページで、左側のナビゲーションを使用して **[プラットフォーム ツール] -> [機能の設定] -> [マーケティング] -> [Web-to-Lead]\(Web-to-Lead\)** に移動します。
    ![Salesforce の [Web-to-Lead]\(Web からリード\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Salesforce Classic Experience を使用している場合:
    1. Salesforce ホーム ページから **[セットアップ]** を選択します。
    ![Salesforce Classic の [セットアップ]](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. [セットアップ] ページで、左側のナビゲーションを使用して、 **[ビルド] -> [カスタマイズ] -> [リード] -> [Web-to-Lead]\(Web-to-Lead\)** に移動します。
    ![Salesforce classic の [Web-to-Lead]\(Web-to-Lead\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

残りの手順は、使用している Salesforce エクスペリエンスに関係なく同じです。

4. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** ページで、 **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** ボタンを選択します。
5. **[Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)** で **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** を選択します。
    ![Salesforce - [Web-to-Lead Setup]\(Web-to-Lead のセットアップ\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. **[Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)** で、`the Include reCAPTCHA in HTML` 設定がオフになっていることを確認し、 **[生成]** を選択します。 
    ![Salesforce - [Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. HTML テキストが表示されます。 "oid" というテキストを検索し、HTML テキストから **oid 値** (引用符で囲まれたテキストのみ) をコピーして保存します。 この値を発行ポータル上の **[組織 ID]** フィールドに貼り付けます。
    ![Salesforce - [Create Web-to-Lead Form]\(Web-to-Lead フォームの作成\)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. **[完了]** を選択しました。

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>リードを Salesforce に送信するようにオファーを構成する

発行ポータル内でオファーのリード管理情報を構成する準備ができたら、次の手順に従います。

1. オファーの **[オファーのセットアップ]** ページに移動します。
1. [リード管理] セクションで **[接続]** を選択します。
    ![[リード管理] - [接続]](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. [接続の詳細] ポップアップ ウィンドウで、 **[リードのターゲット]** に対して **[Salesforce]** を選択し、前の手順で作成した Web-to-Lead フォームの `oid` を **[組織 ID]** フィールドに貼り付けます。

1. **[保存]** を選択します。 

    >[!Note]
    >オファーのリードを受け取るには、まずオファーの残りの部分の構成を完了して発行する必要があります。

    ![[接続の詳細] - [リードのターゲット] の選択](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![[接続の詳細] - [リードのターゲット] の選択](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
---
title: Microsoft コマーシャル マーケットプレースでのコンサルティング サービス オファーを作成する方法
description: Microsoft パートナー センターでコマーシャル マーケットプレース プログラムを使用して、Microsoft AppSource または Azure Marketplace 向けの新しいコンサルティング サービス オファーを作成する方法について説明します。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754238"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースでのコンサルティング サービス オファーを作成する方法

この記事では、パートナー センターを使用して Microsoft コマーシャル マーケットプレース向けのコンサルティング サービス オファーを作成する方法について説明します。 

## <a name="before-you-begin"></a>開始する前に

コンサルティング サービス オファーを公開するには、該当する分野における専門知識を示すための特定の資格要件を満たしている必要があります。 まだお読みになっていな場合は、[コマーシャル マーケットプレース向けのコンサルティング サービス オファーの計画](./plan-consulting-service-offer.md)に関する記事をご覧ください。 コンサルティング サービスの前提条件と、パートナー センターでオファーを作成する際に必要となる資産について説明しています。

## <a name="create-a-new-consulting-service-offer"></a>新しいコンサルティング サービス オファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2.  左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3.  [概要] タブで、 **[+ New offer]\(+ 新しいオファー\)**  >  **[Consulting service]\(コンサルティング サービス\)** を選択します。

    ![左側のナビゲーション メニューの画像。](./media/new-offer-consulting-service.png)

4. **[新しいオファー]** ダイアログ ボックスで、 **[オファー ID]** を入力します。 この ID は、コマーシャル マーケットプレース一覧の URL に表示されます。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。

    * アカウント内の各オファーには、一意のオファー ID が必要です。
    * 使用できるのは小文字と数字だけです。 オファー ID にはハイフンとアンダースコアを含めることができますが、スペースは使用できません。また、50 文字に制限されています。
    * **[作成]** を選択した後で、オファー ID を変更することはできません。

5. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。 オンライン ストアには表示されず、顧客に表示されるオファー名とは異なります。
6. オファーを生成して続行するには、 **[作成]** を選択します。

## <a name="configure-lead-management"></a>リード管理の構成

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客がこのコンサルティング サービスに興味を示したときに顧客の連絡先情報を受け取れるようにします。 この接続は、オファーの作成中または作成後にいつでも変更できます。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](./partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

パートナー センターでリード管理を構成するには、次のようにします。

1.  パートナー センターで、 **[オファーのセットアップ]** タブに移動します。
2.  **[潜在顧客]** で、 **[接続]** リンクを選択します。
3.  **[接続の詳細]** ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
4.  表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

    * [リードを Azure テーブルに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
    * [潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [リードを Marketo に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [リードを Salesforce に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  指定した構成を検証するには、 **[検証]** リンクを選択します。
6.  接続の詳細を構成したら、 **[接続]** を選択します。
7.  **[下書きの保存]** を選択します。

パートナー センターでの公開のためにオファーが送信されると、Microsoft で接続を検証して、テスト リードを送信します。 オファーを公開する前のプレビュー中に、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストします。

> [!TIP]
> リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

## <a name="next-steps"></a>次のステップ

* [コンサルティング サービス オファーのプロパティを構成する方法](./create-consulting-service-offer-properties.md)
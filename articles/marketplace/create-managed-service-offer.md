---
title: Microsoft コマーシャル マーケットプレースにマネージド サービス オファーを作成する方法
description: Microsoft パートナー センターでコマーシャル マーケットプレース プログラムを使用して、Azure Marketplace 向けの新しいマネージド サービス オファーを作成する方法について説明します。
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97918035"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>コマーシャル マーケットプレース向けのマネージド サービス オファーを作成する方法

この記事では、パートナー センターを使用して、Microsoft コマーシャル マーケットプレース向けのマネージド サービス オファーを作成する方法について説明します。

マネージド サービス オファーを公開するには、Cloud Platform の Gold または Silver Microsoft コンピテンシーを獲得している必要があります。 まだ獲得していない場合は、[コマーシャル マーケットプレース向けのマネージド サービス オファーの計画](./plan-managed-service-offer.md)に関する記事をご覧ください。 パートナー センターでオファーを作成するときに必要な資産の準備に役立ちます。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
2. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
3. [概要] タブで、 **[+ 新しいオファー]**  >  **[マネージド サービス]** の順に選択します。

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="左側のナビゲーション メニューの画像。":::

4. **[新しいオファー]** ダイアログ ボックスで、 **[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。

    * アカウント内の各オファーには、一意のオファー ID が必要です。
    * 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
    * **[作成]** を選択した後で、オファー ID を変更することはできません。

5. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。 オンライン ストアには表示されず、顧客に表示されるオファー名とは異なります。
6. オファーを生成して続行するには、 **[作成]** を選択します。

## <a name="configure-lead-management"></a>リード管理の構成

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客がこのコンサルティング サービスに興味を示したときに顧客の連絡先情報を受け取れるようにします。 この接続は、オファーの作成中または作成後にいつでも変更できます。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](./partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

パートナー センターでリード管理を構成するには、次のようにします。

1. パートナー センターで、 **[オファーのセットアップ]** タブに移動します。
2. **[潜在顧客]** で、 **[接続]** リンクを選択します。
3. **[接続の詳細]** ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
4. 表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

    * [リードを Azure テーブルに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
    * [潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [リードを Marketo に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [リードを Salesforce に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. 指定した構成を検証するには、 **[検証]** リンクを選択します。
6. 接続の詳細を構成したら、 **[接続]** を選択します。
7. **[下書きの保存]** を選択します。

パートナー センターでの公開のためにオファーが送信されると、Microsoft で接続を検証して、テスト リードを送信します。 オファーを公開する前のプレビュー中に、プレビュー環境でオファーを自分で購入してみることで、リードの接続をテストします。

> [!TIP]
> リードが失われないように、リードの宛先への接続が確実に最新の状態に保たれているようにします。

## <a name="configure-offer-properties"></a>オファーのプロパティを構成する

パートナー センターのオファーの [プロパティ] ページで、オファーに適用されるカテゴリと、法的契約を定義します。 この情報により、マネージド サービスがオンライン ストアに正しく表示され、適切な顧客に提供されるようになります。

### <a name="select-a-category"></a>[カテゴリの選択]

**[カテゴリ]** で、少なくとも 1 つ、最大 5 つのカテゴリを選択して、オファーを適切なコマーシャル マーケットプレース検索領域にグループ化します。

### <a name="provide-terms-and-conditions"></a>使用条件を指定する

**[法的情報]** で、このオファーの使用条件を指定します。 顧客は、オファーを使用する前にそれらに同意するよう求められます。 使用条件を見つけることができる URL を指定することもできます。

続行する前に、 **[下書きの保存]** を選択します。

## <a name="next-step"></a>次のステップ

* [マネージド サービス オファー登録情報を構成する](./create-managed-service-offer-listing.md)
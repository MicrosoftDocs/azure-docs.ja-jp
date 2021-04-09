---
title: コマーシャル マーケットプレースで Azure アプリケーション オファーを作成する方法
description: Microsoft パートナー センターのコマーシャル マーケットプレース ポータルを使用して、Azure Marketplace で、またはクラウド ソリューション プロバイダー (CSP) プログラムを通してリスト登録または販売を行うために、新しい Azure アプリケーション オファーを作成する方法について説明します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94369797"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースで Azure アプリケーション オファーを作成する方法

コマーシャル マーケットプレースの発行元は、潜在顧客がソリューションを購入できるように、Azure アプリケーション オファーを作成することができます。 この記事では、Microsoft コマーシャル マーケットプレース用に Azure アプリケーション オファーを作成するプロセスについて説明します。

まだそうしていない場合は、「[コマーシャル マーケットプレース用の Azure アプリケーション オファーを計画する](plan-azure-application-offer.md)」を参照してください。 リソースが提供され、オファーを作成するときに必要になる情報と資産を収集する助けになります。

## <a name="create-a-new-offer"></a>新しいオファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。

1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。

1. [概要] ページで、 **[+ 新しいオファー]**  >  **[Azure アプリケーション]** の順に選択します。

    ![左側のナビゲーション メニューの画像。](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. **[新しいオファー]** ダイアログ ボックスで、 **[オファー ID]** を入力します。 これは、アカウントのオファーごとに一意の識別子です。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「test-offer-1」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。

     * アカウント内の各オファーには、一意のオファー ID が必要です。
     * 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
     * **[作成]** を選択した後で、オファー ID を変更することはできません。

1. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

     * この名前はパートナー センター内でのみ表示され、顧客に表示されるオファー名やその他の値とは異なります。
     * **[作成]** を選択した後で、オファーの別名を変更することはできません。

1. オファーを生成して続行するには、 **[作成]** を選択します。

## <a name="configure-your-azure-application-offer-setup-details"></a>Azure アプリケーション オファーのセットアップの詳細を構成する

**[オファーのセットアップ]** タブの **[セットアップの詳細]** で、体験版を構成するかどうかを選択します。 また、お使いのカスタマー リレーションシップ マネジメント (CRM) システムを、コマーシャル マーケットプレース オファーと接続します。

### <a name="enable-a-test-drive-optional"></a>体験版を有効にする (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「[体験版](plan-azure-application-offer.md#test-drive)」を参照してください。

#### <a name="to-enable-a-test-drive"></a>体験を有効にするには

- **[体験版]** で、 **[体験版を有効にする]** チェック ボックスをオンにします。

### <a name="customer-lead-management"></a>顧客リードの管理

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客が興味を示したり製品をデプロイしたりしたときに顧客の連絡先情報を受け取ることができるようにします。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>パートナー センターで接続の詳細を構成するには

1. **[潜在顧客]** で、 **[接続]** リンクを選択します。
1. **[接続の詳細]** ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
1. 表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

   - [リードを Azure テーブルに送信するようにオファーを構成する](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
   - [潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [リードを Marketo に送信するようにオファーを構成する](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [リードを Salesforce に送信するようにオファーを構成する](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 指定した構成を検証するには、 **[検証]** リンクを選択します (該当する場合)。
1. ダイアログ ボックスを閉じるには、 **[接続]** を選択します。
1. **[下書きの保存]** を選択してから、次のプロパティ。

> [!NOTE]
> リードが失われないように、リード ターゲットへの接続が最新の状態に保たれていることを確認します。 何かが変更されたときは、必ずこれらの接続を更新してください。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション オファーのプロパティの構成方法](create-new-azure-apps-offer-properties.md)

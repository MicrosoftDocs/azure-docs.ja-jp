---
title: Microsoft コマーシャル マーケットプレースでの SaaS オファーの作成方法
description: Microsoft パートナー センターのコマーシャル マーケットプレース ポータルを使用して、Microsoft AppSource、Azure Marketplace、またはクラウド ソリューション プロバイダー (CSP) プログラムでリスト登録または販売を行うために新しいサービスとしてのソフトウェア (SaaS) オファーを作成する方法について説明します。
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: f689993ce56a1125a1d1de8f65ce05d01f776ea9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130070"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>コマーシャル マーケットプレースでの SaaS オファーの作成方法

コマーシャル マーケットプレースの発行元は、潜在顧客が SaaS ベースの技術ソリューションを購入できるように、サービスとしてのソフトウェア (SaaS) オファーを作成することができます。 この記事では、Microsoft コマーシャル マーケットプレース用の SaaS オファーを作成するプロセスについて説明します。

## <a name="before-you-begin"></a>開始する前に

[コマーシャル マーケットプレースの SaaS オファーを計画する](plan-saas-offer.md)をまだお読みになっていな場合は、お読みください。 SaaS アプリの技術的な要件、およびオファーを作成するときに必要となる情報と資産について説明されています。 コマーシャル マーケットプレースで単純なリスト ( **[問い合わせ]** リスト オプション) を公開する予定がない限り、SaaS アプリケーションは認証に関する技術的な要件を満たしている必要があります。

## <a name="create-a-new-saas-offer"></a>新しい SaaS オファーを作成する

1. [パートナー センター](https://partner.microsoft.com/dashboard/home)にサインインします。
1. 左側のナビゲーション メニューで、 **[商業マーケットプレース]**  >  **[概要]** の順に選択します。
1. **[概要]** タブで、 **[+ 新しいオファー]**  >  **[サービスとしてのソフトウェア]** の順に選択します。

   :::image type="content" source="media/new-offer-saas.png" alt-text="左側のナビゲーション メニューと新しいオファーの一覧を示しています。":::

1. **[新しいオファー]** ダイアログ ボックスで、 **[オファー ID]** を入力します。 この ID は、コマーシャル マーケットプレースの一覧と Azure Resource Manager テンプレートの URL に表示されます (該当する場合)。 たとえば、このボックスに「**test-offer-1**」と入力すると、オファーの Web アドレスは `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` になります。
   + アカウント内の各オファーには、一意のオファー ID が必要です。
   + 使用できるのは小文字と数字だけです。 ハイフンとアンダースコアを含めることができますが、スペースは使用できず、文字数は 50 文字に制限されています。
   + **[作成]** を選択した後で、オファー ID を変更することはできません。

1. **[オファーのエイリアス]** を入力します。 これは、パートナー センター内でオファーに使用される名前です。

   + この名前はコマーシャル マーケットプレースには表示されず、顧客に表示されるオファー名やその他の値とは異なります。
   + **[作成]** を選択した後で、オファーの別名を変更することはできません。
1. オファーを生成して続行するには、 **[作成]** を選択します。

## <a name="configure-your-saas-offer-setup-details"></a>SaaS オファーのセットアップの詳細を構成する

**[オファーのセットアップ]** タブの **[セットアップの詳細]** で、Microsoft を通じてオファーを販売するか、取引を個別に管理するかを選択します。 Microsoft を通じて販売されるオファーは "_取引可能オファー_" と呼ばれます。これは、Microsoft が発行元の代わりにソフトウェア ライセンス費用に対する金銭のやり取りを円滑に進めることを意味します。 これらのオプションの詳細については、「[リスト オプション](plan-saas-offer.md#listing-options)」と「[公開オプションを選定する](determine-your-listing-type.md)」を参照してください。

1. Microsoft を通じて販売し、取引を円滑に進めるには、 **[はい]** を選択します。 「[体験版を有効にする](#enable-a-test-drive-optional)」に進みます。

1. コマーシャル マーケットプレースを通じてオファーを一覧表示し、取引を個別に処理するには、 **[いいえ]** を選択し、次のいずれかの操作を行います。
   + オファーの無料サブスクリプションを提供するには、 **[今すぐ入手 (無料)]** を選択します。 次に、表示される **[オファーの URL]** ボックスに、顧客が [Azure Active Directory (Azure AD) を使用したワンクリック認証](azure-ad-saas.md)で試用版を入手できる (*http* または *https* で始まる) URL を入力します。 たとえば、「 `https://contoso.com/saas-app` 」のように入力します。
   + 30 日間の無料試用版を提供するには、 **[無料試用版]** を選択し、表示される **[試用版 URL]** ボックスに、顧客が [Azure Active Directory (Azure AD) を使用したワンクリック認証](azure-ad-saas.md)で試用版を入手できる (*http* または *https* で始まる) URL を入力します。 たとえば、「 `https://contoso.com/trial/saas-app` 」のように入力します。
   + 潜在顧客からオファーの購入について連絡を受けるには、 **[問い合わせ]** を選択します。

### <a name="enable-a-test-drive-optional"></a>体験版を有効にする (省略可能)

体験版は、構成済み環境に一定時間アクセスできるようにすることで、潜在顧客に対してオファーを紹介するのに最適な方法です。 体験版を提供すると、コンバージョン率が向上し、質の高い潜在顧客を獲得できます。 体験版の詳細については、「[体験版とは](./what-is-test-drive.md)」を参照してください。

> [!TIP]
> 体験版は、無料試用版とは異なります。 体験版または無料試用版のいずれかまたは両方を提供できます。 これらはどちらも、ソリューションを一定期間、顧客に提供します。 ただし、体験版には、実際の実装シナリオの中で製品の主な機能や利点を体験できる実践的なセルフガイド ツアーも含まれます。

**体験を有効にするには**
1.  **[体験版]** で、 **[体験版を有効にする]** チェック ボックスをオンにします。
1.  表示される一覧から、体験版の種類を選択します。

### <a name="configure-lead-management"></a>リード管理の構成

カスタマー リレーションシップ マネジメント (CRM) システムをコマーシャル マーケットプレースのオファーに接続して、顧客が興味を示したり製品をデプロイしたりしたときに顧客の連絡先情報を受け取ることができるようにします。 この接続は、オファーの作成中または作成後にいつでも変更できます。

> [!NOTE]
> Microsoft を通じてオファーを販売している場合、または **[問い合わせ]** リスト オプションを選択した場合は、リード管理を構成する必要があります。 詳細なガイダンスについては、「[コマーシャル マーケットプレース オファーからの潜在顧客](partner-center-portal/commercial-marketplace-get-customer-leads.md)」を参照してください。

#### <a name="to-configure-the-connection-details-in-partner-center"></a>パートナー センターで接続の詳細を構成するには

1.  **[潜在顧客]** で、 **[接続]** リンクを選択します。
1. **[接続の詳細]** ダイアログ ボックスで、一覧から潜在顧客の宛先を選択します。
1. 表示されるフィールドに入力します。 詳細な手順については、次の記事を参照してください。

   - [リードを Azure テーブルに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Dynamics 365 Customer Engagement (旧称 Dynamics CRM Online) にリードを送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)
   - [潜在顧客を HTTPS エンドポイントに送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [リードを Marketo に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [リードを Salesforce に送信するようにオファーを構成する](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 指定した構成を検証するには、 **[検証]** リンクを選択します。
1. ダイアログ ボックスを閉じるには、 **[OK]** を選択します。

## <a name="next-steps"></a>次のステップ

- [SaaS オファーのプロパティを構成する方法](create-new-saas-offer-properties.md)
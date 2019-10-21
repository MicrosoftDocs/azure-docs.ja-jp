---
title: Cloud パートナー ポータルからパートナー センターへのアカウントの移行 - Azure 用商業マーケットプレース
description: アカウントを CPP からパートナー センターに移行する方法。 - Azure 用 Commercial Marketplace
author: ChJenk
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/23/2019
ms.openlocfilehash: 6887430cad06ad33434f0f01e741979014b4558b
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219577"
---
# <a name="account-migration-from-cloud-partner-portal-to-partner-center"></a>Cloud パートナー ポータルからパートナー センターへのアカウントの移行

既存の Cloud パートナー ポータル (CPP) アカウントがある場合は、アカウント設定をパートナー センターに移行する必要があります。

## <a name="account-migration-process"></a>アカウントの移行プロセス

CPP で所有者ロールを持っているユーザーの場合は、[発行元プロファイル] ページに黄色のバナーが表示されます。 次の 2 つのケースのいずれかに該当する可能性があります。

- アカウントは既に移行されており、パートナー センターでアカウントの設定を管理する準備ができている。
- アカウントはパートナー センターに移行されていないため、さらにアクションを実行する必要があります。

### <a name="your-account-has-been-migrated-to-partner-center"></a>アカウントがパートナー センターに移行されている

CPP からパートナー センターへの移行が完了しているすべてのアカウントについては、アカウント管理はパートナー センターで行われます。 ユーザーの追加や削除などの変更は、CPP に同期されます。

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>アカウントがまだパートナー センターに移行されていない

バナーをクリックして、アカウントの移行プロセスを開始します。 次の項目を入力することが期待されています。

1. 仕事用メール アドレス: <br> <br> ほとんどの場合、CPP にサインインするために使用している電子メール アドレスでサインインします。 場合によっては、別の電子メール アドレスを使用する必要があります。

    * Microsoft アカウント:CPP アカウントが Microsoft アカウントの場合は、Microsoft Partner Network (MPN) ID が登録されているテナントに関連付けられている有効な仕事用メール アドレスを入力します。 詳細については、「[Microsoft Partner Network プログラムへのサインアップ](#sign-up-for-microsoft-partner-network-program)」を参照してください。

    * テナントの不一致:仕事用メール アドレスが、CPP アカウント上に存在する Microsoft Partner Network ID に関連付けられているテナントに属していない場合は、エラーが表示されます。 このエラーを解消するには、テナントに関連付けられている電子メール アドレスを入力します。 エラー メッセージに、テナントの名前が示されます。

2. Microsoft Partner Network プログラムへのサインアップ

    CPP アカウントが Microsoft Partner Network ID を持っていないか、無効な ID を持っている場合は、アクティブ化プロセスの一環として、Microsoft Partner Network プログラムにサインアップする必要があります。

## <a name="sign-up-for-microsoft-partner-network-program"></a>Microsoft Partner Network プログラムへのサインアップ

Microsoft との提携を希望される企業は、Microsoft Partner Network (MPN) に参加し、MPN ID を取得する必要があります。 既に Microsoft Partner Network のメンバーであり、MPN ID を持っている場合は、アカウントのアクティブ化プロセスで必要になるため、情報をお手元に用意してください。  

Microsoft Partner Network のメンバーでない場合は、[こちらで参加](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/en-us/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new)して MPN ID を取得できます。 アカウントのアクティブ化プロセスで入力する必要があるため、MPN ID をメモしておきます。

Microsoft Partner Network の詳細については、パートナー Web サイト「[Microsoft Partner Network に参加する](https://partner.microsoft.com/en-US/membership)」を参照してください。 Microsoft Partner Network の ISV 特典の詳細については、「[ISV リソース ハブ](https://partner.microsoft.com/isv-resource-hub)」を参照してください。  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Dynamics 365 と PowerApps のオファーをパートナー センターに移行する

Dynamics 365 Customer Engagement、PowerApps、Dynamics 365 Operations のアカウントとオファーの管理を効率化するために、オファーは[パートナー センター](https://partner.microsoft.com/)に移行されました。 この移行により、パブリックと販売者の両方のカタログで同じコンテンツを使用できるようになります。

Dynamics 365 Customer Engagement、PowerApps、Dynamics 365 Operations のオファーに関して **2019 年 10 月 15 日**までに実行する必要のあることについては、次の手順に従ってください。

> [!NOTE]
> これは、Dynamics 365 Business Central オファーには適用されません。  

1. MPN メンバーシップ アカウントが最初にパートナー メンバーシップ センター (PMC) で作成された場合は、[パートナー センター](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile)にサインインしてそのアカウントが移行されていることを確認してください。 MPN ID を含むプロファイル画面が表示されている場合は、処理を続行する準備ができています。 そうでない場合は、[パートナー メンバーシップ センター](https://partners.microsoft.com/partnerprogram/Welcome.aspx)のプロンプトに従って、アカウントの移行を開始する必要があります。 ヘルプが必要な場合は、[サポート](https://partner.microsoft.com/support?issueid=100-0077)を参照してください。
2. [パートナー センターの商業マーケットプレースの概要ページ](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)に移動します。 左側のナビゲーション ウィンドウに "商業マーケットプレース" が表示されている場合は、登録済みであり、次の手順に進んでください。 そうでない場合は、ここで[商業マーケットプレースに登録](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/azureisv)します。
3. [プランを検索](https://appsource.microsoft.com/)して、プランが AppSource にあることを確認します。 オファーが AppSource に既に存在する場合は、次の手順に進みます。 AppSource にないオファーの場合は、[新しい Dynamics 365 for Customer Engagement オファー](create-new-customer-engagement-offer.md)または[新しい Dynamics 365 for Operations オファー](create-new-operations-offer.md)を作成します。
4. パートナー センターの[契約ページ](https://partner.microsoft.com/dashboard/account/agreements)で「**Business アプリケーション ISV 補遺**」を確認し、同意していることを確認します。
5. パートナー センターの [[アカウント設定]](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile) で、課金情報が入力されていることを確認します。
6. オファーが以前に認定されている場合でも、認定資格とパブリケーションのために新規と既存の各オファーを送信します。
    * 認定資格のためのアプリの指定や、マーケティング情報など、情報画面に入力します。 **2019 年 10 月 15 日**までに **[送信]** (画面の右上隅) を選択します。 オファーの可用性に影響を与えないようにするには、これらの手順を完了する必要があります。
    * 資格がある場合は、このプロセス中に Premium レベルへの参加を要求することができます。
    * 認定または再認定を行うには、アプリが最新バージョンの Business Applications プラットフォームをサポートしている必要があります。
    * アプリが承認されると、オファーに戻るためのメールが送信されます。[起動] を選択すると、Microsoft AppSource でオファーを開始することができます。

## <a name="additional-resources"></a>その他のリソース

サポートと更新プログラムについては、週単位の [Dynamics ISV コミュニティ コール](https://aka.ms/DynamicsISV-CommunityCall)に参加してください。

マーケットプレースのオファーの公開、認定、または管理に関するサポートが必要な場合は、[サポート チケットを提出](https://aka.ms/MarketplacePublisherSupport)してください。

## <a name="next-steps"></a>次の手順

- [パートナー センターで Commercial Marketplace アカウントを管理する](./manage-account.md)

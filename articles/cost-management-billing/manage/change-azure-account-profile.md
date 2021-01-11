---
title: Azure アカウントの連絡先情報を変更する
description: Azure 管理アカウントの連絡先情報を変更する方法について説明します
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/03/2020
ms.author: banders
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6045afba230fa204dd5f93adc11b67ff0e3e209f
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684900"
---
# <a name="change-the-contact-information-for-your-azure-account"></a>Azure アカウントの連絡先情報を変更する

この記事は、Azure portal でアカウントの連絡先情報を更新するために役立ちます。 連絡先情報を更新する手順は、課金アカウントの種類によって異なります。 課金アカウントの詳細を確認し、ご自分の課金アカウントの種類を特定するには、[Azure portal での課金アカウントの表示](view-all-accounts.md)に関するページを参照してください。

*販売先住所* - 販売先住所は、課金アカウントの責任者である組織または個人の住所および連絡先情報です。 課金アカウントに対して生成されるすべての請求書に表示されます。

*請求先住所* - 請求先住所は、課金アカウントに対して生成された請求書の責任者である組織または個人の住所および連絡先情報です。 Microsoft Online Service Program (MOSP) の課金アカウントの場合、アカウントに対して生成されたすべての請求書に表示される 1 つの請求先住所があります。 Microsoft 顧客契約 (MCA) の課金アカウントの場合、課金プロファイルごとに請求先住所があり、課金プロファイルに対して生成された請求書に表示されます。

*サービスおよびマーケティング メール用の連絡先メール アドレス* - サインインに使用するメール アドレスとは異なるメール アドレスを指定して、ご利用の Azure アカウントに関しての請求、サービス、推奨量に関連した重要な通知を受け取ることができます。 アカウントで使用されているサービスに対する緊急のセキュリティ イシュー、価格の変更、破壊的変更などのサービス通知メールは、常にサインインアドレスに送信されます。

## <a name="update-an-mosp-billing-account-address"></a>MOSP 課金アカウントの住所を更新する

1. アカウントの管理者アクセス許可を持つメール アドレスを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/change-azure-account-profile/search-cmb.png)
1. 左側で **[プロパティ]** を選択します。  
    ![住所更新ページのスクリーンショット](./media/change-azure-account-profile/update-contact-information-select-properties.png)
1. **[Update billing address]\(請求先住所の更新\)** を選択してから、販売先および請求先の住所を更新します。 新しい住所を入力して **[保存]** を選択します。  
    ![住所更新ページのスクリーンショット](./media/change-azure-account-profile/update-contact-information-mosp.png)

## <a name="update-an-mca-billing-account-sold-to-address"></a>MCA 課金アカウントの販売先住所を更新する

1. Microsoft 顧客契約の課金アカウントの所有者または共同作成者のロールを持つメール アドレスを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/change-azure-account-profile/search-cmb.png)
1. 左側の **[プロパティ]** を選択してから、 **[Update sold-to]\(販売先の更新\)** を選択します。  
    ![販売先の更新を選択したスクリーンショット](./media/change-azure-account-profile/update-sold-to-list-properties-mca.png)
1. 新しい住所を入力し、 **[保存]** を選択します。  
    ![住所の更新を示すスクリーンショット](./media/change-azure-account-profile/update-sold-to-save-mca.png)

    > [!IMPORTANT]
    > 一部のアカウントでは、販売先を更新する前に追加認証が必要です。 アカウントで手動の承認が必要な場合、Azure サポートに連絡するよう求められます。

## <a name="update-an-mca-billing-account-address"></a>MCA 課金アカウントの住所を更新する

1. MCA の課金アカウントまたは課金プロファイルの所有者または共同作成者のロールを持つメール アドレスを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. "**コスト管理 + 請求**" を検索します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/change-azure-account-profile/search-cmb.png)
1. 左側の **[課金プロファイル]** を選択します。
1. 請求先住所を更新するには、課金プロファイルを選択します。  
    ![ポータルでのコストの管理と請求の検索を示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-list-profiles-mca.png)
1. 左側で **[プロパティ]** を選択します。
1. **[住所の更新]** を選択します。  
    ![ポータルでコスト管理と請求を検索しているところを示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-list-properties-mca.png)
1. 新しい住所を入力して **[保存]** を選択します。  
    ![住所の更新を示すスクリーンショット](./media/change-azure-account-profile/update-bill-to-save-mca.png)

## <a name="service-and-marketing-emails"></a>サービスおよびマーケティングのメール

[Azure portal](https://portal.azure.com) では、90 日ごとにメール アドレスの確認または更新を求められます。 Microsoft では、次の Azure アカウント関連情報のメールを、このメール アドレスに送信します。

- サービスの通知
- セキュリティのアラート
- 請求の目的
- サポート
- マーケティングの通信
- Azure の使用状況に基づくベスト プラクティスのレコメンデーション

アカウントに関する通信を受け取るメール アドレスを入力します。 メール アドレスを入力することにより、Microsoft からの通信の受信にオプトインします。

![連絡先情報更新のプロンプトの例](./media/change-azure-account-profile/update-contact-information.png)

### <a name="change-your-contact-email-address"></a>連絡先のメール アドレスを変更する

次のいずれかの方法を使って、連絡先のメール アドレスを変更できます。 連絡先メール アドレスを更新しても、サインインに使用するメール アドレスは更新されません。

* MOSP アカウントのアカウント管理者の場合は、「[MOSP 課金アカウントの住所を更新する](#update-an-mosp-billing-account-address)」の指示に従い、最後の手順で **[Update contact info]\(連絡先情報の更新\)** を選択します。 次に、新しいメール アドレスを入力します。

* Azure portal の [[連絡先情報]](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) 領域に移動して、新しいメール アドレスを入力します。 

* [Azure portal](https://portal.azure.com/#blade/HubsExtension/ContactInfoBlade) で、自分のイニシャルの付いたアイコンまたは画像を選択します。 次に、コンテキスト メニュー **[...]** を選択します。次に、メニューから **[My Contact Information]\(自分の連絡先情報\)** を選択し、新しいメール アドレスを入力します。

![Azure でのメール アドレスの更新の例](./media/change-azure-account-profile/azure-contact-information.png)

### <a name="opt-out-of-marketing-emails"></a>マーケティング メールをオプトアウトする

マーケティング メールの受信からオプトアウトするには:

1. [要求フォーム](https://account.microsoft.com/profile/permissions-link-request)に移動し、自分のプロファイルのメール アドレスを使って要求を送信します。 個人設定を更新するためのリンクをメールで受け取ります。
1. そのリンクを選択して、 **[Manage communication permissions]\(通信のアクセス許可の管理\)** ページを開きます。 このページには、メール アドレスがオプトインされているマーケティング通信の種類が表示されます。 オプトアウトする選択をオフにして、 **[保存]** を選択します。  
    ![通信のアクセス許可を管理するためのページの例](./media/change-azure-account-profile/manage-communication-permissions.png)

マーケティング通信をオプトアウトしても、アカウントに基づくサービスの通知はまだ受け取ります。

## <a name="update-the-email-address-that-you-sign-in-with"></a>サインインに使用するメール アドレスを更新する

アカウントへのアクセスに使用するメール アドレスは更新できません。 ただし、MOSP の課金アカウントがある場合は、新しいメール アドレスを使用して別のアカウントにサインアップし、サブスクリプションの所有権を次のアカウントに譲渡することができます。 MCA 課金アカウントの場合は、[新しいメール アドレスのアクセス許可をアカウントに付与できます](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)。

## <a name="update-your-credit-card"></a>クレジット カードを更新する

クレジット カードを更新する方法については、[Azure サブスクリプションの支払いに使用するクレジット カードの変更](change-credit-card.md)に関するページをご覧ください。

## <a name="update-your-country-or-region"></a>国または地域を更新する

既存のアカウントの国または地域の変更はサポートされていません。 ただし、別の国または地域で新しいアカウントを作成してから、Azure サポートに連絡してサブスクリプションを新しいアカウントに譲渡することができます。

## <a name="change-the-subscription-name"></a>サブスクリプション名を変更する

1. [Azure portal](https://portal.azure.com) にサインインし、左側のウィンドウから **[サブスクリプション]** を選択して、名前を変更するサブスクリプションを選択します。
1. **[概要]** を選択し、コマンド バーから **[名前の変更]** を選択します。  
    ![Azure サブスクリプションの名前変更の例](./media/change-azure-account-profile/rename-sub.png)
1. 名前を変更したら、 **[保存]** を選択します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [請求先アカウントを表示する](view-all-accounts.md)

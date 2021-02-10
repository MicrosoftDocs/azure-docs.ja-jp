---
title: Azure サブスクリプションの請求書による支払い
description: Azure サブスクリプションを請求書で支払う方法について説明します。 詳細については、よく寄せられる質問を参照してください。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 0afaa9a577432741da44317a9d13e288cdea0d0d
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052483"
---
# <a name="pay-for-your-azure-subscription-by-invoice"></a>Azure サブスクリプションの請求書による支払い

この記事は、Azure Web サイトから Azure にサインアップした Microsoft 顧客契約 (MCA) のお客様に適用されます。 [Microsoft Customer Agreement にアクセスできるかどうかを確認](#check-access-to-a-microsoft-customer-agreement)してください。 Microsoft 担当者を通じて Azure にサインアップした場合、既定の支払い方法があらかじめ "*小切手または電信送金*" に設定されています。

請求書による支払いに切り替えた場合、請求日から 30 日以内に小切手/電信送金で請求金額を支払うことになります。 Azure サブスクリプションの支払いを請求書で行う対象となるには、Azure サポートに要求を送信します。 要求が承認されると、Azure portal で請求書支払い (小切手/電信送金) に切り替えられます。

> [!IMPORTANT]
> * 請求書支払い (小切手/電信送金) は、会社を代表して Azure を使用しているお客様にのみ提供されます。
> * 請求書支払いに切り替える前に、すべての未払いの料金を支払います。
> * 現在、中国のグローバル Azure では、請求書支払いはサポートされていません。

## <a name="request-to-pay-by-invoice"></a>請求書による支払いを要求する

1. Azure portal にサインインして、サポート リクエストを送信します。 **[ヘルプとサポート]** を検索して選択します。  
    ![Microsoft Azure portal で [ヘルプとサポート] を検索する](./media/pay-by-invoice/search-for-help-and-support.png)
1. **[新しいサポート リクエスト]** を選択します。  
    ![[新しいサポート リクエスト] リンク、[ヘルプとサポート] 画面、Microsoft Azure portal](./media/pay-by-invoice/help-and-support.png)
1. **[問題の種類]** で **[課金]** を選択します。 *[問題の種類]* は、サポート要求のカテゴリです。 請求書で支払うサブスクリプションを選択します。サポート プランを選択し、 **[次へ]** を選択します。
1. **[お支払]** を **[問題の種類]** として選択します。 *[問題の種類]* は、サポート要求のサブカテゴリです。
1. **[Switch to Pay by Invoice]\(請求書による支払いに切り替える\)** を **[問題のサブタイプ]** として選択します。
1. **[詳細]** ボックスに次の情報を入力し、 **[次へ]** を選択します。
    - 新規または既存のお客様:
    - 既存のお客様の場合は現在のお支払い方法:
    - 注文 ID (請求書オプションを要求する):
    - アカウント管理者の Live ID (または組織 ID) (会社のドメインである必要があります):
    - コマース アカウント ID:
    - 会社の名前 (VAT または政府機関の Web サイトで登録されているもの):
    - 会社の住所 (VAT または政府機関の Web サイトで登録されているもの):
    - 会社の Web サイト:
    - 国:
    - 税 ID/ VAT ID:
    - 会社の設立日 (年):
    - これまでの Microsoft とのお取引:
    - 連絡先の名前:
    - 連絡先の電話番号:
    - 連絡先の電子メール:
    - クレジット カードではなく請求書オプションを希望する正当な理由:
    - コアのを増加する場合は、次の追加情報を入力してください。
        - (これまでのクォータ) 既存のコア:
        - (新しいクォータ) 要求したコア:
        - サブスクリプションの具体的なリージョンおよびシリーズ:
    - **[会社名]** と **[会社の住所]** は、Azure アカウントに指定した情報と一致させる必要があります。 情報を表示または更新するには、「[Azure アカウントのプロファイル情報を変更する](change-azure-account-profile.md)」を参照してください。
    - 与信限度額を承認する前に、Azure portal で請求連絡先情報を追加します。 連絡先の詳細を会社の買掛金または財務部門に関連付ける必要があります。
1. 連絡先情報と優先連絡方法を確認し、 **[作成]** を選択します。

必要とするクレジット総額によっては与信審査が必要になることがあります。この場合は、カスタマー サポートから与信審査申込書をお送りします。 会社の監査済み財務諸表の提出をお願いする場合があります。 財務情報が提供されていない場合や、必要な与信限度額を裏付ける十分な情報がない場合は、与信審査要求を承認するために、保証金またはスタンドバイ信用状を求める場合があります。

## <a name="switch-to-invoice-pay-checkwire-transfer"></a>請求書支払い (小切手/電信送金) に切り替える

請求書による支払いが承認されると、Azure portal で請求書支払い (小切手/電信送金) に切り替えることができます。

Microsoft Online Services Program アカウントの場合は、Azure サブスクリプションを小切手/電信送金に切り替えることができます。 Microsoft 顧客契約を結んでいる場合は、課金プロファイルを小切手/電信送金に切り替えることができます。

### <a name="switch-azure-subscription-to-checkwire-transfer"></a>Azure サブスクリプションを小切手/電信送金に切り替える

Azure サブスクリプションを請求書支払い (小切手/電信送金) に切り替えるには、次の手順に従います。 "*請求書支払い (小切手または電信送金) に切り替えた後にクレジット カードに戻すことはできません*"。

1. Azure portal にアクセスし、アカウント管理者としてサインインします。 **[コストの管理と請求]** を検索して選択します。  
    ![Azure portal での [コストの管理と請求] の検索を示すスクリーンショット。](./media/pay-by-invoice/search.png)
1. 請求書支払いに切り替えるサブスクリプションを選択します。
1. **[支払い方法]** を選択します。
1. コマンド バーで、 **[請求書による支払い]** ボタンを選択します。  
    ![[請求書による支払い] ボタン、[支払い方法]、Microsoft Azure portal](./media/pay-by-invoice/pay-by-invoice.png)

### <a name="switch-billing-profile-to-checkwire-transfer"></a>課金プロファイルを小切手/電信送金に切り替える

課金プロファイルを小切手/電信送金に切り替えるには、次の手順に従います。 課金プロファイルの既定の支払い方法を変更できるのは、Azure にサインアップしたユーザーのみです。

1. Azure portal にアクセスして、課金情報を表示します。 **[コストの管理と請求]** を検索して選択します。
1. メニューから **[課金プロファイル]** を選択します。  
    ![[課金プロファイル] メニュー項目、[コストの管理と請求]、Microsoft Azure portal](./media/pay-by-invoice/billing-profile.png)
1. 課金プロファイルを選択します。
1. **[課金プロファイル]** メニューで **[支払い方法]** を選択します。  
   ![[支払い方法] メニュー項目、[課金プロファイル]、[コスト管理]、Microsoft Azure portal](./media/pay-by-invoice/billing-profile-payment-methods.png)
1. 小切手または電信送金で支払う資格があることを示すバナーを選択します。  
    ![小切手または電信送金に切り替えるためのバナー、[支払い方法]、Microsoft Azure portal](./media/pay-by-invoice/customer-led-switch-to-invoice.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft 顧客契約にアクセスできるかどうかを確認する
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="frequently-asked-questions"></a>よく寄せられる質問

*法的文書の要求が届いたのはなぜですか?*

提供された情報が不完全または検証できない場合、Microsoft は法的文書を必要とすることがあります。 例には以下が含まれます。

* アカウント名と会社名の間での名前の相違
* 名前の変更

## <a name="next-steps"></a>次のステップ

* 必要に応じて、[Azure portal](https://portal.azure.com) で請求先連絡先情報を更新します。
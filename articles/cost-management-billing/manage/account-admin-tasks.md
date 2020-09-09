---
title: Azure portal でのアカウント管理者のタスク
description: Azure portal で支払い操作を実行する方法について説明します
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: ad39b9980562c345aff87f4e0771f265011886b3
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683302"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure portal でのアカウント管理者のタスク

この記事では、Azure portal で次のタスクを実行する方法について説明します。
- サブスクリプションの支払い方法を管理する
- サブスクリプションの使用制限を削除する
- Azure イン オープン プラン サブスクリプションにクレジットを追加する

これらのすべてのタスクを実行するには、アカウント管理者である必要があります。

## <a name="navigate-to-your-subscriptions-payment-methods"></a>サブスクリプションの支払い方法に移動する

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。

1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、クレジット カードを追加するサブスクリプションを選択します。

   ![[概要] の [個人用サブスクリプション] グリッドを示すスクリーンショット](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. **[支払い方法]** を選択します。

    ![[支払方法] ブレードを示すスクリーンショット。](./media/account-admin-tasks/subscription-payment-methods-blade.png)

ここでは、新しいクレジット カードの追加、アクティブな支払い方法の変更、クレジット カードの詳細の編集、およびクレジット カードの削除を行えます。

### <a name="change-active-payment-method"></a>アクティブな支払い方法を変更する

アクティブな支払い方法を変更するには、新しいクレジット カードを追加するか、既に保存されているものを選択します。 アクティブな支払い方法を新しいクレジット カードに変更するには:

1. 左上隅にある [+] を選択して、クレジット カードを追加します。

    ![[+] 記号を示すスクリーンショット](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. 右側のフォームにクレジット カードの詳細を入力します。

    ![クレジット カードの追加フォームを示すスクリーンショット。](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. このカードをアクティブな支払い方法にするには、フォームの上にある **[これをアクティブな支払い方法にする]** の横のチェック ボックスをオンにします。 選択したサブスクリプションと同じカードを使用しているすべてのサブスクリプションで、このカードがアクティブな支払い方法になります。

    ![カードをアクティブな支払い方法にするためのチェック ボックスを示すスクリーンショット。](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. **[次へ]** を選択します。

アクティブな支払い方法を、既に保存されているクレジットカードに変更するには:

1. アクティブな支払い方法にするカードの横にあるチェック ボックスを選択します。

    ![クレジット カードの横のオンになっているボックスを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. コマンド バーで、 **[アクティブに設定]** をクリックします。

    ![[アクティブに設定] ボタンを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>クレジット カードの詳細を編集する

有効期限日や住所などのクレジット カードの詳細を編集するには、編集するクレジット カードをクリックします。 クレジット カードのフォームが右側に表示されます。

![選択されたクレジット カードを示すスクリーンショット](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

クレジット カードの詳細を更新し、 **[保存]** をクリックします。

### <a name="remove-a-credit-card-from-the-account"></a>クレジット カードをアカウントから削除する

1. 削除するカードの横にあるチェック ボックスをオンにします。

    ![クレジット カードの横のオンになっているボックスを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. コマンド バーの **[削除]** をクリックします。

    ![[削除] ボタンを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

クレジット カードがいずれかの Microsoft サブスクリプションのアクティブな支払い方法である場合は、Azure アカウントから削除できません。 このクレジット カードにリンクされているすべてのサブスクリプションのアクティブな支払い方法を変更し、再度お試しください。

### <a name="switch-to-invoice-payment"></a>請求書支払いに切り替える

請求書による支払い (小切手または電信送金) の資格がある場合は、Azure portal でサブスクリプションを請求書支払い (小切手または電信送金) に切り替えることができます。

1. コマンド バーで **[Pay by invoice]\(請求書による支払い\)** を選択します。

    ![[支払方法] ブレードを示すスクリーンショット。](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. 請求書による支払い方法用に、住所を入力します。
1. **[次へ]** をクリックします。

請求書による支払いの承認を得るには、[請求書による支払い方法](pay-by-invoice.md)に関するページをご覧ください。

### <a name="edit-invoice-payment-address"></a>請求書支払いの住所を編集する

請求書支払い方法の住所を編集するには、サブスクリプションの支払い方法の一覧で **[請求書]** をクリックします。 住所のフォームが右側に表示されます。

## <a name="remove-spending-limit"></a>支出制限を削除する

Azure の使用制限により、クレジット額を超過する支出が防止されます。 Azure サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除できます。 複数月にわたるクレジットを含むサブスクリプション タイプ (Visual Studio Enterprise、Visual Studio Professional など) の場合、翌請求期間の開始時に使用制限を再度有効にできます。

使用制限は、コミットメント プランや従量課金制価格を使用したサブスクリプションでは利用できません。 [Azure のサブスクリプションの種類と使用制限の利用の可否を網羅した一覧](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、Visual Studio Enterprise サブスクリプションを選択します。

   ![[概要] の [個人用サブスクリプション] グリッドを示すスクリーンショット](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. サブスクリプションの概要で、オレンジ色のバナーをクリックして使用制限を削除します。

    ![使用制限の削除バナーを示すスクリーンショット](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. 使用制限を永久に削除するか、現在の請求期間に対してのみ削除するかを選択します。

   ![[使用制限の削除] ブレードを示すスクリーンショット](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. **[支払い方法の選択]** をクリックして、サブスクリプションの支払い方法を選択します。 これが、サブスクリプションのアクティブな支払い方法になります。

1. **[完了]** をクリックします。

## <a name="add-credits-to-azure-in-open-subscription"></a>Azure イン オープン プラン サブスクリプションにクレジットを追加する

Azure イン オープン プランのライセンス サブスクリプションがある場合は、プロダクト キーを使用するか、クレジット カードでクレジットを購入することによって、Azure portal でサブスクリプションにクレジットを追加できます。

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、Azure イン オープン プラン サブスクリプションを選択します。

    ![[概要] の [個人用サブスクリプション] グリッドを示すスクリーンショット](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > サブスクリプションがここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 サブスクリプションのディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. **[クレジットの履歴]** を選択します。

    ![クレジットの履歴を示すスクリーンショット](./media/account-admin-tasks/aio-credit-history-blade.png)

1. 左上隅にある [+] を選択して、さらにカードを追加します。

    ![[クレジットの追加] ボタンを示すスクリーンショット](./media/account-admin-tasks/aio-credit-history-plus.png)

1. ドロップダウンで支払い方法の種類を選択します。 プロダクト キーを追加するか、クレジット カードを使用してクレジットを購入できます。

    ![[クレジットの追加] ブレードの [支払方法] ドロップダウンを示すスクリーンショット](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. プロダクト キーを選択する場合:
    - プロダクト キーを入力します
    - **[検証]** をクリックします

1. クレジット カードを選択する場合:
    - **[支払い方法の選択]** をクリックして、クレジット カードを追加するか既存のものを選択します。
    - 追加するクレジットの金額を指定します。

1. **[適用]** をクリックします

## <a name="troubleshooting"></a>トラブルシューティング
バーチャル カードやプリペイド カードはサポートされません。 有効なクレジット カードを追加または更新するときにエラーが発生する場合は、ブラウザーをプライベート モードで開いてみてください。

## <a name="next-steps"></a>次のステップ
- [Azure portal での予想外のコストの分析と防止](getting-started.md)に関する詳細を確認する

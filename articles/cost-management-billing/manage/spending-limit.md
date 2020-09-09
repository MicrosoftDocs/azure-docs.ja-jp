---
title: Azure の使用制限
description: この記事では、Azure の使用制限のしくみと削除方法を説明します。
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: df5c5ed74a5e0a63727962fa86d261cfb588a384
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690563"
---
# <a name="azure-spending-limit"></a>Azure の使用制限

Azure の使用制限により、クレジット額を超過する支出が防止されます。 Azure 無料アカウント、または複数月にわたるクレジットが含まれるサブスクリプション タイプにサインアップする新規のお客様に対してはすべて、既定で使用制限が有効化されます。 使用制限はクレジットの金額と等価であり、変更できません。 たとえば Azure 無料アカウントにサインアップした場合、使用制限は 200 ドルであり、500 ドルに変更することはできません。 ただし、使用制限の削除はできます。 したがって選択肢は、制限なしとするか、またはクレジットと同額の制限を設けるかのどちらかとなります。 ほとんどの種類の支出は、これで防止されます。 使用制限は、コミットメント プランや従量課金制価格を使用したサブスクリプションでは利用できません。 [Azure のサブスクリプションの種類と使用制限の利用の可否を網羅した一覧](https://azure.microsoft.com/support/legal/offer-details/)をご覧ください。

## <a name="reaching-a-spending-limit"></a>使用制限への到達

使用量が、使用制限を使い果たす額に到達した場合、デプロイしたサービスは、請求の残りの期間、無効になります。

たとえば、Azure 無料アカウントに含まれているすべてのクレジットを使用した場合は、デプロイした Azure リソースは運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 ストレージ アカウントのデータは、読み取り専用として使用できます。

ご利用の種類のサブスクリプションに複数月のクレジットが含まれる場合、翌請求期間の開始時点でサブスクリプションは自動的に再び有効化されます。 この後、Azure リソースを再デプロイし、ストレージ アカウントおよびデータベースに完全にアクセスできるようになります。

使用制限に達した場合は、Azure により電子メール通知が送信されます。 [Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) にサインインして、使用制限に到達したサブスクリプションに関する通知を確認します。

Azure 無料アカウントにサインアップしたユーザーが使用制限に達した場合、[従量課金制](upgrade-azure-subscription.md)価格にアップグレードすることで、使用制限を解除し、サブスクリプションを自動的に再有効化できます。

## <a name="remove-the-spending-limit-in-azure-portal"></a>Azure portal での使用制限の解除

Azure サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除できます。 複数月にわたるクレジットを含むサブスクリプション タイプ (Visual Studio Enterprise、Visual Studio Professional など) の場合、無期限に、または現在の請求期間について使用制限を削除することができます。 現在の請求期間のみを選択した場合、使用制限は次の請求期間の開始時に自動的に有効になります。

Azure の無料アカウントをお持ちの場合、使用制限を削除するには、[Azure サブスクリプションのアップグレード](upgrade-azure-subscription.md)に関するページをご覧ください。 それ以外の場合は、次の手順に従って使用制限を削除します。

<a id="remove"></a>

1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/spending-limit/search-bar.png)

1. **[個人用サブスクリプション]** の一覧で、ご使用のサブスクリプションを選択します。 たとえば、"*Visual Studio Enterprise*" など。

   ![[概要] の [個人用サブスクリプション] グリッドを示すスクリーンショット](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. サブスクリプションの概要で、オレンジ色のバナーをクリックして使用制限を削除します。

    ![使用制限の削除バナーを示すスクリーンショット](./media/spending-limit/msdn-remove-spending-limit-banner-x.png)

1. 使用制限を永久に削除するか、現在の請求期間に対してのみ削除するかを選択します。

      ![[使用制限の削除] ブレードを示すスクリーンショット](./media/spending-limit/remove-spending-limit-blade-x.png)

      | オプション | 結果 |
      | --- | --- |
      | 使用制限を無期限に削除 | 使用制限は、次の請求期間の開始時に自動的に再び有効になるわけではありません。 ただし、いつでも自分で再び有効にすることができます。 |
      | 現在の請求期間の使用制限を削除 | 使用制限は、次の請求期間の開始時に自動的に再び有効になります。 |


1. **[支払い方法の選択]** をクリックして、サブスクリプションの支払い方法を選択します。 これが、サブスクリプションのアクティブな支払い方法になります。

1. **[完了]** をクリックします。


## <a name="why-you-might-want-to-remove-the-spending-limit"></a>使用制限を削除しようと考えるときの理由は何ですか

使用制限により、特定のサードパーティおよび Microsoft サービスのデプロイまたは使用ができなくなる場合があります。 サブスクリプションの使用制限を削除する必要がある状況を次に示します。

-  Oracle のようなサードパーティ イメージや Azure DevOps Services などのサービスのデプロイを計画しています。 この状況は、ほぼ即座に使用制限に到達し、サブスクリプションが無効化される原因となります。
- 中断したくないサービスがあります。 使用制限に達した場合、デプロイ済みの Azure リソースは運用環境から削除され、Azure 仮想マシンは停止し、割り当てが解除されます。 中断したくないサービスがある場合は、使用制限を解除してください。
- 仮想 IP アドレスなど、中断することのできない設定があるサービスおよびリソースを利用しています。 これらの設定は、使用制限に到達してサービスおよびリソースの割り当てが解除されると、失われます。

## <a name="turn-on-the-spending-limit-after-removing"></a>削除後に使用制限を有効にする

この機能を利用できるのは、複数月にわたるクレジットを含む種類のサブスクリプションで、使用制限が無期限に解除されている場合だけです。 この機能を使用すると、次の請求期間の開始時に使用制限を自動的に有効にできます。


1. [Azure Portal](https://portal.azure.com) にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/spending-limit/search-bar.png)

1. **[個人用サブスクリプション]** の一覧で、ご使用のサブスクリプションを選択します。 たとえば、"*Visual Studio Enterprise*" など。

   ![[概要] の [個人用サブスクリプション] グリッドを示すスクリーンショット](./media/spending-limit/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. サブスクリプションの概要で、ページの上部にあるバナーをクリックして、使用制限を再び有効にします。

## <a name="custom-spending-limit"></a>カスタムの使用制限

カスタムの使用制限は使用できません。

## <a name="a-spending-limit-doesnt-prevent-all-charges"></a>すべての料金が使用制限で防止されるわけではない

[Azure Marketplace で公開されている一部の外部サービス](../understand/understand-azure-marketplace-charges.md)には、サブスクリプション クレジットを使用できません。また、使用制限が設定されている場合でも、別に料金を請求される場合があります。 Visual Studio のライセンス、Azure Active Directory Premium、サポート プランとほとんどのサード パーティ ブランド サービスなどがその例として挙げられます。 新しい外部サービスをプロビジョニングすると、警告が表示され、サービス料が別に請求されることをお知らせします。

![Marketplace 購入警告](./media/spending-limit/marketplace-warning01.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ
- [従量課金制](upgrade-azure-subscription.md)料金のプランにアップグレードする。

---
title: "別のアカウントに Azure サブスクリプションの所有権を譲渡する | Microsoft Docs"
description: "Azure サブスクリプションを別のユーザーに譲渡する方法と、そのプロセスに関してよく寄せられる質問 (FAQ) について説明します"
keywords: "Azure サブスクリプションの譲渡,譲渡、Azure サブスクリプション,Azure サブスクリプションを別のアカウントに移動する,Azure サブスクリプション所有者の変更,譲渡、Azure サブスクリプションを別のアカウントに"
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing,top-support-issue
ms.assetid: c8ecdc1e-c9c5-468c-a024-94ae41e64702
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: genli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0ec56111847300833ba92cc9149e8a276ba2c6c1
ms.openlocfilehash: af7cabf3d5286dc3cd368db191bc5218cd9c5893
ms.lasthandoff: 03/02/2017

---
# <a name="transfer-ownership-of-an-azure-subscription-to-another-account"></a>Azure サブスクリプションの所有権を別のアカウントに譲渡する

従量課金、Visual Studio、Action Pack、または BizSpark のサブスクリプションについては、アカウント センターでサブスクリプションを別のユーザーに譲渡できます。 これらのサブスクリプションに向けた Azure 外部サービスについても、譲渡がサポートされます。 

次のような場合に、Azure サブスクリプションの所有権を譲渡できます。

* Azure サブスクリプションの所有権を別のユーザーに譲渡する必要がある。
* Azure へのサインアップに使用したアカウントを変更する必要がある。 おそらく、Microsoft アカウントを使用していたが、代わりに職場または学校アカウントを使用する必要がある。
* Azure サブスクリプションをあるディレクトリから別のディレクトリに移動する必要がある。
* 異なるテナントにある Azure と Office 365 を統合する必要がある。

サブスクリプションの別のプランへの変更については、「[別のプランへの Azure サブスクリプションの切り替え](billing-how-to-switch-azure-offer.md)」で詳細を参照してください。 

## <a name="transfer-ownership-of-an-azure-subscription"></a>Azure サブスクリプションの所有権を譲渡する
> [!VIDEO https://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/Transfer-an-Azure-subscription/player]
>
>

1. <https://account.windowsazure.com/Subscriptions> でサインインします。 所有権の譲渡を実行するには、アカウント管理者である必要があります。 サブスクリプションのアカウント管理者を確認する方法については、[よく寄せられる質問](#faq)を参照してください。

2. 譲渡するサブスクリプションを選択します。

3. **[サブスクリプションの譲渡]** オプションをクリックします。 このボタンが表示されない場合は、[FAQ](#no-button)を参照してください。

   ![Azure account subscriptions tab](./media/billing-subscription-transfer/image1.png)
4. 譲渡先を指定します。

   ![Transfer Subscription dialog box](./media/billing-subscription-transfer/image2.PNG)
5. 譲渡先には、承認用のリンクが記載された電子メールが自動的に送信されます。

   ![Subscription transfer email to recipient](./media/billing-subscription-transfer/image3.png)
6. 譲渡先のユーザーは、リンクをクリックして指示に従います (支払情報の入力など)。

   ![First subscription transfer web page](./media/billing-subscription-transfer/image4.png)

   ![Second subscription transfer web page](./media/billing-subscription-transfer/image5.png)
7. 成功です。 サブスクリプションが譲渡されました。

## <a name="transfer-subscription-ownership-for-enterprise-agreement-ea-customers"></a>エンタープライズ契約 (EA) のお客様の、サブスクリプションの所有権の譲渡
エンタープライズ管理者は、加入契約内でのサブスクリプションの所有権を譲渡できます。 作業を開始するには、EA ポータルの「[アカウントの所有権の譲渡](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)」を参照してください。

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>サブスクリプションの所有権を受け取った後の次のステップ
1. アカウント管理者になったら、 サービス管理者と共同管理者を見直して更新します。 管理者の管理は、[Azure クラシック ポータルで](https://manage.windowsazure.com) [設定] に移動して実行します。 [管理者の役割の詳細についてはこちらを参照してください](billing-add-change-azure-subscription-administrator.md)。

2. サブスクリプションとサービスに対して、ロールベースのアクセス制御 (RBAC) を使用することもできます。 [Azure Portal](https://portal.azure.com) にアクセスします。 [RBAC の詳細についてはこちらを参照してください](../active-directory/role-based-access-control-configure.md)。

3. このサブスクリプションのサービスに関連付けられている以下の資格情報を更新します。
   
   * サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「 [Create and upload a management certificate for Azure](../cloud-services/cloud-services-certs-create.md)
   
   * Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。
   
   * Azure Virtual Machines などのサービス用のリモート アクセス資格情報。 

4. [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)で、[このサブスクリプション用の課金アラートを更新します](billing-set-up-alerts.md)。 

5. パートナーがいる場合は、このサブスクリプションのパートナー ID を更新することを検討します。 パートナー ID は、[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)で更新できます。

<a id="faq"></a>

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)
* <a name="whoisaa"></a>**サブスクリプションのアカウント管理者は誰ですか。**

  アカウント管理者は、Azure サブスクリプションのサインアップまたは購入を行ったユーザーです。 [アカウント センター](https://account.windowsazure.com/Home/Index)にアクセスし、サブスクリプションの作成、サブスクリプションの取り消し、サブスクリプションの課金の変更、サービス管理者の変更などのさまざまな管理タスクを実行する権限を持ちます。 サブスクリプションのアカウント管理者が不明な場合は、次の手順で確認します。

  1. [Azure ポータル](https://portal.azure.com)にサインインします。
  2. ハブ メニューで、 **[サブスクリプション]**を選択します。
  3. 確認するサブスクリプションを選択し、**[設定]** を調べます。
  4. **[プロパティ]**を選択します。 サブスクリプションのアカウント管理者が、 **[アカウント管理者]** ボックスに表示されます。  

* **あらゆるものが譲渡されるのでしょうか。リソース グループ、VM、ディスク、他の実行中のサービスは含まれますか?**

  はい。VM、ディスク、Web サイトなどのすべてのリソースが、新しい所有者に譲渡されます。 ただし、自分で設定した[管理者の役割](billing-add-change-azure-subscription-administrator.md)と[ロールベースのアクセス制御 (RBAC)](../active-directory/role-based-access-control-configure.md) ポリシーは譲渡されません。 

* <a id="no-button"></a>**[サブスクリプションの譲渡] ボタンが表示されないのはなぜですか?**

  **[サブスクリプションの譲渡]** ボタンが表示されない場合、お使いのプランについてサブスクリプションの譲渡はサポートされていません。 [サポートにお問い合わせください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

* **サブスクリプションの譲渡により、サービスのダウンタイムは発生しますか。**

  サービスへの影響はありません。 サブスクリプションを譲渡すると、現在のアカウント管理者からサブスクリプションが取り消され、譲渡先のアカウントにサブスクリプションが作成されます。 新しいサブスクリプションは、基になる Azure サービスに関連付けられます。 サブスクリプション ID は変わりません。

* **このプロセスを使用してサブスクリプションのディレクトリを変更する方法を教えてください。**

  Azure サブスクリプションは、アカウント管理者が属するディレクトリに作成されます。 ディレクトリを変更するためには、サブスクリプションを譲渡先ディレクトリ内のユーザー アカウントに譲渡します。 譲渡先のユーザーが譲渡を受け入れるための手順を完了すると、サブスクリプションは自動的に譲渡先ディレクトリに移動します。

* **サブスクリプションの所有権を別の組織から引き継ぐ場合、その組織のユーザーが引き続きこのリソースにアクセスすることはできますか。**

  サブスクリプションが別のテナントに譲渡されると、以前のテナントに関連付けられているユーザーはそのサブスクリプションにアクセスできなくなります。 サービス管理者または共同管理者ではなくなったユーザーでも、下記のような他のセキュリティ メカニズムを介して、まだサブスクリプションにアクセスできることがあります。

  * サブスクリプションのリソースに対する管理者権限をユーザーに付与する管理証明書。 詳細については、「[Azure Cloud Services の証明書の概要](../cloud-services/cloud-services-certs-create.md)」を参照してください。
  * Storage などのサービス用のアクセス キー。 詳細については、「[Azure ストレージ アカウントについて](../storage/storage-create-storage-account.md)」を参照してください。
  * Azure Virtual Machines などのサービス用のリモート アクセス資格情報。

 譲渡先でリソースへのアクセスを制限する必要がある場合、サービスに関連付けられているすべてのシークレットの更新を検討する必要があります。 ほとんどのリソースは、次の手順を使って更新できます。

    1. [Azure ポータル](https://portal.azure.com)にアクセスします。
    2. ハブ メニューで、**[すべてのリソース]** を選択します。
    3. リソースを選択します。 
    4. リソースのブレードで **[設定]**をクリックします。 ここで、既存のシークレットを表示して更新できます。

* **請求サイクルの途中でサブスクリプションを譲渡した場合、譲渡先が請求サイクル全体の料金を支払うことになりますか。**

  譲渡元は、譲渡が完了した時点までに報告されたすべての使用量に対して支払う責任があります。 譲渡先は、譲渡した時点以降に報告された使用量に対して責任を負います。 譲渡する前に発生したにもかかわらず、譲渡後に報告される使用量もあります。 この使用量は、譲渡先の請求書に含まれます。

* **譲渡先は、使用履歴と請求履歴にアクセスできますか。**

  譲渡先に示される情報は、最後の請求書の金額 (最初の請求書が生成される前にサブスクリプションが譲渡された場合は現在の残高) のみです。 使用履歴と請求履歴の他の部分は、サブスクリプションと共に譲渡されることはありません。

* **譲渡時にプランを変更できますか。**

  プランはそのままにしておく必要があります。 プランの変更については、「[別のプランへの Azure サブスクリプションの切り替え](billing-how-to-switch-azure-offer.md)」を参照してください。

* **他の国のユーザー アカウントにサブスクリプションを譲渡できますか。**

  いいえ。他の国のユーザー アカウントにサブスクリプションを譲渡することはできません。 譲渡先のユーザー アカウントは、同じ国に属している必要があります。

* **譲渡先で別の支払い方法を使用できますか。**

  はい。 ただし、サブスクリプションの請求履歴は&2; つのアカウントに分かれます。  

* **Azure サブスクリプションを譲渡した後に、支払い方法に対する影響はありますか。**

  サブスクリプションの譲渡を受け入れるには、サブスクリプションの支払いに使用するクレジット カードまたは類似の支払い方法を指定する必要があります。 たとえば、Bob が Jane にサブスクリプションを譲渡し、Jane が譲渡を受け入れる場合、Jane はサブスクリプションの支払いに使用する支払い方法を指定する必要があります。 譲渡が完了した後は、Bob ではなく Jane にサブスクリプションの請求が行われます。

* **Azure サブスクリプションのデータとサービスを新しいサブスクリプションに移行するにはどうすればよいですか。**

  サブスクリプションの所有権を譲渡できない場合、リソースを手動で移行することもできます。 「[Move resources to new resource group or subscription (新しいリソース グループまたはサブスクリプションへのリソースの移動)](../azure-resource-manager/resource-group-move-resources.md)」を参照してください。



## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 




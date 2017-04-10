---
title: "発行者アカウントの作成と登録 | Microsoft Docs"
description: "承認のうえで、Azure Marketplace でさまざまなプランの種類を販売できるように Microsoft 開発者アカウントを作成する手順。"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 5a2fe68d-2967-463f-8af6-42bed07e3eaa
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 125d4d4bc916883e3bb53b9b3b4bf46bbb4bf139
ms.openlocfilehash: 642e4a2d11ef5a92f5ab46bc4872414966b04c0d
ms.lasthandoff: 01/06/2017


---
# <a name="create-a-microsoft-developer-account"></a>Microsoft 開発者アカウントの作成
この記事では、Azure Marketplace の承認済みの Microsoft 開発者になるために必要なアカウントの作成と登録のプロセスについて説明します。

## <a name="1-create-a-microsoft-account"></a>1.Microsoft アカウントの作成
発行プロセスを開始するには、まず Microsoft アカウントを作成する必要があります。 このアカウントは、**Microsoft デベロッパー センター**と **Azure 発行ポータル**の両方に対する登録に使用します。 複数の Azure Marketplace サービスに対して&1; つの Microsoft アカウントを使用してください。 各サービスおよびプランに対し、個別のアカウントを作成する必要はありません。

ユーザー名が含まれたアドレスは、貴社のドメインか IT チームで管理する必要があります。 発行関連のアクティビティはすべて、このアカウントを通じて配信されます。

> [!WARNING]
> Microsoft アカウントの登録では、"**Azure**" や "**Microsoft**" などの単語は使用できません。 アカウントの作成および登録プロセスを完了するには、これらの単語を使用しないでください。
>
>

### <a name="guidelines-for-company-accounts"></a>会社のアカウントに関するガイドライン
会社のアカウントを作成する場合、そのアカウントを開いた Microsoft アカウントを使用して複数のユーザーがログインし、そのアカウントにアクセスする必要があるのであれば、次のガイドラインに従ってください。

> [!Important]
> 重要: デベロッパー センター アカウントへのアクセスを複数のユーザーに許可する場合は、各ユーザー (各自の Azure AD 資格情報を使用してサインインし、アカウントにアクセスできるユーザー) に対し、Azure Active Directory を使用してロールを割り当てることをお勧めします。 詳しくは、「[アカウント ユーザーの管理](https://msdn.microsoft.com/en-us/windows/uwp/publish/manage-account-users)」をご覧ください。

* 個人ではなく、会社のドメインに属する電子メール アドレスを使用して Microsoft アカウントを作成してください (たとえば、windowsapps@fabrikam.com)。
* この Microsoft アカウントへのアクセスは、最小限の数の開発者に制限してください。
* 開発者アカウントにアクセスする必要があるユーザーを登録した電子メール配布リストを会社で設定し、セキュリティ情報にこの電子メール アドレスを追加してください。 これにより、リストに登録されたすべての従業員が、必要なときにセキュリティ コードを受信し、Microsoft アカウントのセキュリティ情報を管理できるようになります。 配布リストを設定できない場合は、プロンプトが表示されたとき (アカウントに新しいセキュリティ情報が追加されたときや、新しいデバイスからアクセスする必要があるとき) に、各電子メール アカウントの所有者がセキュリティ コードにアクセスし、セキュリティ コードを共有できるようにする必要があります。
* 会社の電話番号を追加する際には、内線番号なしで主要なチーム メンバーに連絡できる番号を使用してください。
* 通常、開発者が会社の開発者アカウントにログインする際には、信頼済みデバイスを使用するようにします。 主要チーム メンバーの全員が、これらの信頼済みデバイスにアクセスできるようにする必要があります。 これにより、アカウントへのアクセス時にセキュリティ コードを送信する必要性を減らせます。
* 信頼されていない PC からアカウントにアクセスできるようにする必要がある場合は、アクセス権を付与する開発者を最大&5; 人に制限してください。 これらの開発者はなるべく、地理的位置やネットワーク上の場所が同じであるマシンからアカウントにアクセスするようにしてください。
* [https://account.live.com/proofs/Manage](https://account.live.com/proofs/Manage) で、会社のセキュリティ情報がすべて最新であることを頻繁に確認するようにしてください。

開発者アカウントには、主に信頼済み PC からアクセスする必要があります。 アカウントごとに生成されるコードの数 (1 週間あたり) には制限があるため、このことは非常に重要です。 またこれは、サインインをシームレスするという意味でも重要でます。

開発者アカウントに関するその他のガイドラインやセキュリティについて詳しくは、[こちら](https://msdn.microsoft.com/en-us/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)をクリックしてください。

### <a name="instructions"></a>このサンプルについての指示
1. 新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、既存のアカウントにサインインしていないことを確認します。
2. [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx) を使用して、上記のガイドラインに従った電子メール (たとえば、windowsapp@fabrikam.com)) を Microsoft アカウントとして登録します。 次の手順に従ってください。

   1. アカウントを Microsoft アカウントとして登録するときに、有効な電話番号を指定する必要があります。この電話番号には、アカウント検証コードが、テキスト メッセージまたは自動通話として送信されます。
   2. アカウントを Microsoft アカウントとして登録するときに、アカウント検証用の自動電子メールを受信できるように、有効な電子メール ID を指定する必要があります。
3. DL に送信された電子メール アドレスを確認します。
4. これで、Microsoft デベロッパー センターで新しい Microsoft アカウントを使用する準備ができました。

## <a name="2-register-your-account-in-microsoft-developer-center"></a>2.Microsoft デベロッパー センターでのアカウント登録
Microsoft デベロッパー センターは、会社情報を登録するために&1; 回だけ使用します。 登録者は、合法的な企業の代表者である必要があります。また、ID を検証する方法として、個人情報を提供する必要があります。 登録時には、会社用に共有されている Microsoft アカウントを使用する必要があり、**Azure 発行ポータルでも同じアカウントを使用する必要があります**。 作成する前に、会社がまだ Microsoft デベロッパー センターのアカウントを所有していないことを確認してください。 作成中は、会社の住所情報、銀行口座情報、および税金に関する情報が収集されます。 通常、これらは財務またはビジネス用連絡先から取得できます。

> [!IMPORTANT]
> プランの作成とデプロイメントのさまざまなフェーズを進めるために、次のような開発者プロファイル コンポーネントを完成させる必要があります。
>
>

| 開発者プロファイル | ドラフトを開始するには | ステージング | 無料とソリューション テンプレートの発行 | 商用プランの発行 |
| --- | --- | --- | --- | --- |
| 会社登録 |必須 |必須 |必須 |必須 |
| 税プロファイル ID |省略可能 |省略可能 |省略可能 |必須 |
| 銀行口座 |省略可能 |省略可能 |省略可能 |必須 |

> [!NOTE]
> ライセンス持ち込み (BYOL) は、仮想マシンのみでサポートされており、 **無料** サービスと見なされます。
>
>

### <a name="register-your-company-account"></a>会社アカウントの登録
1. 新しい Internet Explorer InPrivate または Chrome Incognito のブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2. [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure) に移動して、デベロッパー センターでご自身を販売者として登録します。 作業を進める前に、次の重要な注意事項をお読みください。

   > [!IMPORTANT]
   > デベロッパー センターで登録に使用する電子メール ID または配布リスト (個人から依存関係を削除するときは配布リストを推奨) が、最初に Microsoft アカウントとして登録されていることを確認してください。 登録されていない場合は、こちらの [リンク](https://signup.live.com/signup?uaid=e479342fe2824efeb0c3d92c8f961fd3&lic=1)を使用して登録してください。 また、デベロッパー センター登録に、**Microsoft 社のドメイン、つまり @microsoft.com の電子メール ID を使用することはできません**。
   >
   >

    ![図][img-signin]
3. [アカウントの保護にご協力ください] ウィザードを完了します。これにより、電話番号または電子メール アドレスから ID が検証されます。

    ![図][img-verify]
4. [登録] の [アカウント情報] セクションで、ドロップダウン メニューから **[アカウントの国/地域]** を選択します。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_04.png)

   > [!WARNING]
   > **"販売元" の国:** Azure Marketplace でサービスを販売するには、登録済みエンティティが、承認されたいずれかの "販売元" の国に所在している必要があります。 この制限は、支払いおよび課税上の理由から生じます。 「販売元」の国の範囲は今後拡大される予定です。 詳細については、「[Microsoft Azure Marketplace 参加ポリシー](http://go.microsoft.com/fwlink/?LinkID=526833)」を参照してください。
   >
   >
5. [アカウントの種類] として、**[会社]** を選択し、**[次へ]** をクリックします。

   > [!IMPORTANT]
   > アカウントの種類と、自社に最適な選択について理解を深めるには、「 [アカウントの種類、場所、料金](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx)
   >
   >

    ![図](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_05.png)
6. **発行者表示名**を入力します。通常は、会社名を使用します。

   > [!TIP]
   > プランが表示されたら、デベロッパー センターに入力した発行者表示名は Azure Marketplace に表示されませんが、 登録プロセスを完了するために入力する必要があります。
   >
   >
7. アカウント確認用の **連絡先情報** を入力します。

   > [!IMPORTANT]
   > デベロッパー センターで会社を承認するための検証プロセスで使用されるため、連絡先情報は正確に入力してください。
   >
   >
8. **会社の承認者**の連絡先情報を入力します。 会社の承認者とは、デベロッパー センターでアカウントを作成する権限が、組織の代表者として付与されていることを証明できる社内の担当者です。 完了したら、**[次へ]** をクリックし、**[支払い] セクション**に移動します。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_08.png)
9. アカウントの料金を支払うための支払い情報を入力します。 登録コストが免除されるプロモーション コードがある場合は、ここで入力できます。 ない場合は、クレジット カード情報 (またはサポートされている市場であれば PayPal 情報) を入力します。 完了したら、**[次へ]** をクリックし、**確認画面**に移動します。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgRegisterCo_09.png)
10. アカウント情報を見直し、すべて正しいことを確認します。 次に、 [Microsoft Azure Marketplace 発行者契約](http://go.microsoft.com/fwlink/?LinkID=699560)の契約条件を読んで同意します。 チェック ボックスをオンにすると、これらの条件を読んで同意したと見なされます。
11. **[完了]** をクリックし、登録内容を確認します。 確認メッセージが電子メール アドレスに送信されます。
12. 無料プランの発行のみを計画している場合は、**[Azure Marketplace 発行ポータルに移動する]** をクリックします。また、このドキュメントのセクション 3「[発行ポータルでのアカウントの登録](#3-register-your-account-in-the-publishing-portal)」にスキップできます。

商用プラン (時間単位の課金モデルが指定された Virtual Machine プランなど) の発行を計画している場合は、**[アカウント情報を更新してください]** をクリックします。そこで、デベロッパー センター アカウントに税金と銀行口座の情報を入力する必要があります。

税金と銀行口座の情報を後で更新する場合は、次のセクション、つまり、このドキュメントのセクション 3「 [発行ポータルでのアカウントの登録](#3-register-your-account-in-the-publishing-portal)」に進み、後から発行ポータル内のリンクを使用して、この手順に戻ることができます。

> [!IMPORTANT]
> 商用プランの場合、税金および銀行口座の情報の入力を完了することなく、プランを運用環境に移行することはできません。
>
>

税金と銀行口座の情報を後で更新する場合は、セクション 3「 [発行ポータルでのアカウントの登録](#3-register-your-account-in-the-publishing-portal)」に進み、後から発行ポータル内のリンクを使用して、この手順に戻ることができます。

### <a name="add-tax-and-banking-information"></a>税金と銀行取引情報の追加
 購入向けに商用プランを発行する場合は、支払い情報と税金情報を追加し、デベロッパー センターでの検証のために送信する必要があります。 無料プラン (または、BYOL プラン) のみを発行する場合は、この情報を追加する必要はありません。 後ほど追加することはできますが、税金情報の検証に時間がかかります。 購入向けに商用プランを提供することが確定している場合は、これらの情報を可能な限り早く入力することをお勧めします。

**銀行口座情報**

1. Microsoft アカウントを使用して、 [Microsoft デベロッパー センター](http://dev.windows.com/registration?accountprogram=azure) にサインインします。
2. 左側の **[支払いアカウント]** をクリックし、**[支払方法の選択]** で **[銀行口座]** または **[PayPal]** をクリックします。

   > [!IMPORTANT]
   > Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。
   >
   >
3. 支払い情報を入力し、問題がないことを確認して **[保存]** をクリックします。

   > [!IMPORTANT]
   > 支払いアカウントを更新または変更する必要がある場合は、上記と同じ手順に従い、現在の情報の代わりに新しい情報を使用します。 支払いアカウントを変更すると、最大で支払い期間&1; 回分の遅延が支払いに発生する可能性があります。 この遅延は、支払いアカウントが初めて設定された場合と同様に、アカウント変更の確認が必要になるために発生します。 アカウントが確認された後も、お支払いは引き続き一括払いになります。期日が現在の支払い期間内にある支払いは、次の支払い期間に追加されます。
   >
   >
4. **[次へ]**をクリックします。

**税金情報**

1. 必要に応じて、Microsoft アカウントを使用して [Microsoft デベロッパー センター](http://dev.windows.com/registration?accountprogram=azure) にサインインします。
2. 左側のメニューの **[税プロファイル]** をクリックします。
3. **[税フォームの設定]** ページで、永続権を持つ国または地域を選択し、第一市民権を保持する国または地域を選択します。 **[次へ]**をクリックします。
4. 税金の詳細を入力し、 **[次へ]**をクリックします。

> [!WARNING]
> Microsoft デベロッパー センター アカウントで税金と銀行口座の情報の入力を完了することなく、商用プランを運用環境に移行することはできません。
>
>

デベロッパー センター登録で問題が発生した場合は、次の手順でサポート チケットを記録してください

1. サポート リンク [https://developer.microsoft.com/windows/support](https://developer.microsoft.com/windows/support)
2. **[お問い合わせ]** セクションで、**[インシデントを提出]** をクリックします (次のスクリーンショットを参照)

    ![図](media/marketplace-publishing-accounts-creation-registration/imgAddTax_02.png)
3. **[問題の種類]** で [Dev Center のヘルプ] を、**[カテゴリ]** で [アプリの発行と管理] を選択します。 その後、[メールでのお問い合わせ] をクリックします。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgAddTax_03.png)
4. ログイン ページが表示されます。 Microsoft アカウントのサインインを使用します。 Microsoft アカウントがない場合は、こちらの [リンク](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)を使用して作成します。
5. 問題の詳細を入力し、 **[送信]** をクリックしてチケットを送信します。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgAddTax_05.png)

## <a name="3-register-your-account-in-the-publishing-portal"></a>3.発行ポータルでのアカウントの登録
[発行ポータル](http://publish.windowsazure.com) は、プランの発行と管理に使用されます。

1. 新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2. [http://publish.windowsazure.com](http://publish.windowsazure.com)に移動します。
3. 新しいユーザーが発行ポータルに初めてサインインする場合は、デベロッパー センター アカウントの登録に使用した電子メール ID でサインインする必要があります。 これにより、デベロッパー センター アカウントと発行ポータル アカウントが相互に関連付けられます。 アプリケーションで作業している会社の他のメンバーは、次の手順に従って、発行ポータルで共同管理者として後で追加できます。

発行ポータルで共同管理者として追加されている場合は、共同管理者アカウントでサインインできます。

> [!TIP]
> 参加ポリシーについては、 [Azure Web サイト](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)を参照してください。
>
>

## <a name="4-steps-to-add-a-co-admin-in-the-publishing-portal"></a>4.発行ポータルで共同管理者を追加する手順
**管理者である必要があります** 。

> [!NOTE]
> **新規ユーザーの場合** 、発行ポータルで共同管理者を追加する前に、発行ポータルで少なくとも&1; つのアプリケーションが作成されていることを確認します。 **[パブリッシャー]** タブは、発行ポータルでアプリケーションを作成しないと表示されないからです。
>
>

1. 共同管理者の電子メール ID が Microsoft アカウント (MSA) であることを確認します。 MSA でない場合は、こちらの [リンク](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)を使用して、MSA として登録します。
2. 共同管理者の追加を試みる前に、管理者アカウントのアプリケーションが少なくとも&1; つ存在していることを確認します。
3. 上記の手順が完了したら、共同管理者の電子メール ID で発行ポータルにログインし、その後、ログアウトします。
4. ここで、管理者の電子メール ID で発行ポータルにログインします。
5. [パブリッシャー] に移動してアカウントを選択し、[管理者] に移動して共同管理者を追加します (以下のスクリーンショットを参照)

   ![図](media/marketplace-publishing-accounts-creation-registration/imgAddAdmin_05.png)

## <a name="5-steps-to-delete-a-co-admin-in-the-publishing-portal"></a>5.発行ポータルで共同管理者を削除する手順
**管理者である必要があります** 。

1. 管理者の電子メール ID で発行ポータルにログインします。
2. **[パブリッシャー]** > 自分のアカウントを選択 > **[管理者]**  ->  **[Co-Admins (共同管理者)]** の順に移動します。
3. 削除する共同管理者の横にある **[X]** ボタンをクリックします (以下のスクリーンショットを参照)。

    ![図](media/marketplace-publishing-accounts-creation-registration/imgDeleteAdmin_03.png)

## <a name="next-steps"></a>次のステップ
以上でアカウントの作成と登録は完了です。プランを発行するためには、技術面以外の前提条件を満たす必要があります。[技術面以外の前提条件](marketplace-publishing-pre-requisites.md)に関するページをご覧ください。

## <a name="see-also"></a>関連項目
* [Microsoft Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)

[img-msalive]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]:media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]:media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]:marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]:marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]:marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]:marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]:marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]:marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]:marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]:marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]:marketplace-publishing-push-to-staging.md


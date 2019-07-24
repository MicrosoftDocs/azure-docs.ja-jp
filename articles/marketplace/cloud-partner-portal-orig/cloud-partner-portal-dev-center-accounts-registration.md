---
title: Microsoft 開発者アカウントの作成 | Microsoft Docs
description: Microsoft 開発者アカウントの作成の要件と手順を説明します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: adfe0af646fae79bc9a954cd5d654626880915d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894089"
---
<a name="create-a-microsoft-developer-account"></a>Microsoft 開発者アカウントの作成
====================================

この記事では、Azure Marketplace で発行するために承認済みの Microsoft 開発者になる方法について説明します。

## <a name="create-a-microsoft-account"></a>Microsoft アカウントの作成

発行プロセスを始めるには、**Microsoft デベロッパー センター**で登録を完了する必要があります。 同じ登録済みアカウントを使用して、**[Cloud パートナー ポータル](https://cloudpartner.azure.com/)** で発行プロセスを開始します。

### <a name="general-account-guidelines"></a>一般アカウントのガイドライン

複数の Azure Marketplace サービスに対して 1 つの Microsoft アカウントを使用することをお勧めします。 このアカウントは、サービスまたはオファーに固有のものにしないでください。

ユーザー名が含まれたアドレスは、貴社のドメインか IT チームで管理する必要があります。 発行関連のアクティビティはすべて、このアカウントを通じて配信されます。

>[!WARNING]
>Microsoft アカウントの登録では、"Azure" や "Microsoft" などの単語は使用できません。 アカウントの作成および登録プロセスを完了するには、これらの単語を使用しないでください。

### <a name="company-account-guidelines"></a>会社アカウントのガイドライン

アカウントを開いた Microsoft アカウントを使用して複数のユーザーがログインし、そのアカウントにアクセスする必要がある場合は、次のガイドラインに従ってください。

>[!IMPORTANT]
>複数のユーザーがデベロッパー センター アカウントにアクセスできるように、Azure Active Directory を使用して個々のユーザーにロールを割り当てることをお勧めします。 これらのユーザーは、個人の Azure AD 資格情報を使ってサインインすることで、アカウントにアクセスできます。 詳しくは、「[アカウント ユーザーの管理](https://docs.microsoft.com/windows/uwp/publish/manage-account-users)」をご覧ください。

-   個人ではなく、会社のドメインに属するメール アドレスを使用して Microsoft アカウントを作成してください。 たとえば、windowsapps\@fabrikam.com などです。
-   この Microsoft アカウントへのアクセスは、最小限の数の開発者に制限してください。
-   開発者アカウントにアクセスする必要があるユーザーを登録した電子メール配布リストを会社で設定し、セキュリティ情報にこの電子メール アドレスを追加してください。 これにより、リストに登録されたすべての従業員が、必要なときにセキュリティ コードを受信し、Microsoft アカウントのセキュリティ情報を管理できるようになります。 配布リストを設定できない場合は、プロンプトが表示されたとき (アカウントに新しいセキュリティ情報が追加されたときや、新しいデバイスからアクセスする必要があるとき) に、各電子メール アカウントの所有者がセキュリティ コードにアクセスし、セキュリティ コードを共有できるようにする必要があります。
-   会社の電話番号を追加する際には、内線番号なしで主要なチーム メンバーに連絡できる番号を使用してください。
-   通常、開発者が会社の開発者アカウントにログインする際には、信頼済みデバイスを使用するようにします。 主要チーム メンバーの全員が、これらの信頼済みデバイスにアクセスできるようにする必要があります。 これにより、アカウントへのアクセス時にセキュリティ コードを送信する必要性を減らせます。
-   信頼されていない PC からアカウントにアクセスできるようにする必要がある場合は、アクセス権を付与する開発者を最大 5 人に制限してください。 これらの開発者はなるべく、地理的位置やネットワーク上の場所が同じであるマシンからアカウントにアクセスするようにしてください。
-   [会社のセキュリティ情報](https://account.live.com/proofs/Manage)が最新であることを頻繁に確認するようにしてください。

>[!IMPORTANT]
>開発者アカウントには、主に信頼済み PC からアクセスする必要があります。 アカウントごとに生成されるコードの数 (1 週間あたり) には制限があるため、このことは非常に重要です。 またこれは、サインインをシームレスするという意味でも重要でます。
>
>詳しくは、[開発者アカウントに関するその他のガイドラインやセキュリティ](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts)に関するページをご覧ください。

### <a name="to-create-a-microsoft-account"></a>Microsoft アカウントを作成するには

1.  新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、既存のアカウントにサインインしていないことを確認します。
2.  この[リンク](https://signup.live.com/signup.aspx)を使用し、上記のガイドラインに従って、メール アドレスを Microsoft アカウントとして登録します。 次のサインアップ手順を完了します。

    - アカウントを Microsoft アカウントとして登録するときに、有効な電話番号を指定する必要があります。この電話番号には、アカウント検証コードが、テキスト メッセージまたは自動通話として送信されます。
    - アカウントを Microsoft アカウントとして登録するときに、アカウント検証用の自動電子メールを受信できるように、有効な電子メール ID を指定する必要があります。
    - DL に送信された電子メール アドレスを確認します。

    これで、Microsoft デベロッパー センターで新しい Microsoft アカウントを使用する準備ができました。

## <a name="register-your-account-in-microsoft-developer-center"></a>Microsoft デベロッパー センターでのアカウント登録

Microsoft デベロッパー センターは、会社情報を登録するために 1 回だけ使用します。 登録者は、合法的な企業の代表者である必要があります。また、ID を検証する方法として、個人情報を提供する必要があります。 登録時には、会社用に共有されている Microsoft アカウントを使用する必要があり、**クラウド パートナー ポータルでも同じアカウントを使用する必要があります。** 作成する前に、会社がまだ Microsoft デベロッパー センターのアカウントを所有していないことを確認してください。 作成中は、会社の住所情報、銀行口座情報、および税金に関する情報が収集されます。 通常、これらは財務またはビジネス用連絡先から取得できます。

>[!IMPORTANT]
>プランの作成とデプロイメントのさまざまなフェーズを進めるために、次のような開発者プロファイル コンポーネントを完成させる必要があります。

| 開発者プロファイル     | ドラフトを開始するには    | ステージング       | 無料発行とソリューション テンプレート   | 商用の発行   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| 会社登録  | 必須         | 必須     | 必須                             | 必須             |
| 税プロファイル ID        | 省略可能          | 省略可能      | 省略可能                              | 必須             |
| 銀行口座          | 省略可能          | 省略可能      | 省略可能                              | 必須             |

>[!NOTE]
>ライセンス持ち込み (BYOL) は、仮想マシンのみでサポートされており、無料サービスと見なされます。

### <a name="register-your-company-account"></a>会社アカウントの登録

1. 新しい Internet Explorer InPrivate または Chrome Incognito のブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。

2. [Windows デベロッパー センター](https://dev.windows.com/registration?accountprogram=azure)に移動し、自分を販売者として登録します。 作業を進める前に、次の重要な注意事項をお読みください。

   ![Microsoft アカウントの確認](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >デベロッパー センターで登録に使用する電子メール ID または配布リスト (個人から依存関係を削除するときは配布リストを推奨) が、最初に Microsoft アカウントとして登録されていることを確認してください。 登録されていない場合は、こちらのリンクを使用して登録してください。 また、デベロッパー センターの登録に、Microsoft 社のドメインのメール ID を使うことはできません。

   ![デベロッパー センターのサインイン](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. [アカウントの保護にご協力ください] ウィザードを実行し、電話番号またはメール アドレスを使用して ID を検証します。

4. [登録] の [アカウント情報] で、ドロップダウン リストから **[アカウントの国/地域]** を選択し、**[次へ]** を選択します。

   ![国/リージョンの選択](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"販売元" の国:Azure Marketplace でサービスを販売するには、登録済みエンティティが、ドロップダウン リストに表示される承認されたいずれかの "販売元" の国に所在している必要があります。 この制限は、支払いおよび課税上の理由から生じます。 詳細については、Marketplace 参加ポリシーに関するページをご覧ください。

5. [アカウントの種類] として **[会社]** を選択し、**[次へ]** を選択します。

    >[!IMPORTANT]
    >アカウントの種類を理解し、最適な種類を決定するには、次のスクリーン キャプチャで示されているアカウントの種類、場所、料金のページをご覧ください。

    ![販売者のアカウントの種類](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. **[発行者表示名]** を入力します。 これは通常、会社の名前です。

    >[!NOTE]
    >プランが表示されたら、デベロッパー センターに入力した発行者表示名は Azure Marketplace に表示されません。 ただし、この情報は登録プロセスを完了するために必要です。

7. アカウント確認用の **連絡先情報** を入力します。

    >[!IMPORTANT]
    >デベロッパー センターで会社を承認するための検証プロセスで使用されるため、連絡先情報は正確に入力してください。

8. **会社の承認者**の連絡先情報を入力します。 会社の承認者とは、デベロッパー センターでアカウントを作成する権限が、組織の代表者として付与されていることを証明できる社内の担当者です。 この情報を提供した後、**[次へ]** を選択して**支払いセクション**に移動します。

    ![会社の承認者を識別する](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. アカウントの支払い情報を入力します。 登録コストが免除されるプロモーション コードがある場合は、ここで入力できます。 ない場合は、クレジット カード情報 (またはサポートされている市場であれば PayPal 情報) を入力します。 **[次へ]** を選択して最後の**確認**に進みます。

   ![支払いの登録](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. アカウント情報を見直し、すべて正しいことを確認します。 [Microsoft Azure Marketplace 発行者契約](https://go.microsoft.com/fwlink/?LinkID=699560)の契約条件を読んで同意します。 チェック ボックスをオンにすると、これらの条件を読んで同意したと見なされます。

11. **[完了]** をクリックし、登録内容を確認します。 確認メッセージがユーザーのメール アドレスに送信されます。

12. 無料オファーのみを公開する予定の場合は、こちらの[Cloud パートナー ポータルに移動](https://cloudpartner.azure.com/)を選択し、この記事の「クラウド パートナー ポータルでのアカウントの登録」に進みます。

### <a name="commercial-offers"></a>商用オファー

時間単位の課金モデルを使用する仮想マシン プランなど、商用オファーを発行する場合は、税金と銀行取引情報を提供する必要があります。 そのためには、デベロッパー センター アカウントにサインインし、**[Update your account information]\(アカウント情報を更新する\)** を選択します。 次のセクション「銀行情報と税金情報を追加する」の指示に従います。

>[!IMPORTANT]
>銀行口座および税の情報を提供することなく、運用環境に商用オファーをプッシュすることはできません。

銀行情報と税金情報を後で更新する場合は、この記事の「クラウド パートナー ポータルでのアカウントの登録」に進んでもかまいません。

>[!NOTE]
>税金情報の検証に時間がかかるために、できるだけ早く銀行口座と税金の情報を提供することをお勧めします。

### <a name="add-banking-and-tax-information"></a>銀行情報と税金情報を追加する

購入向けに商用オファーを発行する場合は、支払い情報と税金情報を追加し、デベロッパー センターでの検証のために送信する必要があります。

**銀行に関する情報を提供するには**

1.  Microsoft アカウントを使用して、 [Microsoft デベロッパー センター](https://dev.windows.com/registration?accountprogram=azure) にサインインします。
2.  左側の **[支払いアカウント]** を選択し、**[支払方法の選択]** で **[銀行口座]** または **[PayPal]** を選択します。

    >[!NOTE]
    >Marketplace で顧客が購入した商用オファーがある場合、このアカウントで購入の支払いを受け取ることになります。
3.  支払い情報を入力し、**[保存]** を選択します。

    >[!IMPORTANT]
    >支払いアカウントを更新または変更する必要がある場合は、上記の手順に従い、現在の情報の代わりに新しい情報を使用します。
    >
    >支払いアカウントを変更すると、最大で支払い期間 1 回分の遅延が支払いに発生する可能性があります。 この遅延は、支払いアカウントが初めて設定された場合と同様に、アカウント変更の確認が必要になるために発生します。 アカウントが確認された後も、お支払いは引き続き一括払いになります。期日が現在の支払い期間内にある支払いは、次の支払い期間に追加されます。

4.  **[次へ]** を選択します。

**税に関する情報を追加するには**

1.  必要に応じて、Microsoft アカウントを使用して [Microsoft デベロッパー センター](https://dev.windows.com/registration?accountprogram=azure) にサインインします。
2.  左側のメニューで **[税プロファイル]** を選択します。
3.  **[税フォームの設定]** ページで:
    - 永住権を持つ国または地域を選択します。
    - 基本的な市民権を保持している国または地域を選択します。
    - **[次へ]** を選択します。
4.  税金の詳細を入力し、**[次へ]** をクリックします。

>[!WARNING]
>Microsoft デベロッパー センター アカウントで銀行口座と税金の情報の入力を完了することなく、商用オファーを運用環境に移行することはできません。

### <a name="developer-center-registration-issues"></a>デベロッパー センターの登録に関する問題

デベロッパー センター登録で問題が発生した場合は、次の手順でサポート チケットを開いてください。

1.  [サポート リンク](https://developer.microsoft.com/windows/support)に移動します。
2.  **[お問い合わせ]** で、**[インシデントの送信]** を選択します。
    ![チケットを開く](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  **[問題の種類]** で [Dev Center のヘルプ] を、**[カテゴリ]** で [アプリの発行と管理] を選択します。 **[Start email]\(メールの開始\)** を選択します。

    ![問題の種類を示す](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  サインイン ページが表示されます。 Microsoft アカウントを使用してサインインします。 Microsoft アカウントを持っていない場合は、[作成します](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1)。

5.  問題に関する詳細情報を提供し、**[送信]** を選択してチケットを送信します。

    ![チケットを送信する](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>クラウド パートナー ポータルでのアカウントの登録

[Cloud パートナー ポータル](https://cloudpartner.azure.com/)は、プランの発行と管理に使用します。

1.  新しい Chrome Incognito または Internet Explorer InPrivate ブラウズ セッションを開き、個人用アカウントにサインインしていないことを確認します。
2.  [クラウド パートナー ポータル](https://cloudpartner.azure.com/)に移動します。
3.  新しいユーザーが初めて [Cloud パートナー ポータル](https://cloudpartner.azure.com/)にサインインする場合は、デベロッパー センター アカウントの登録に使用したのと同じメール ID でサインインする必要があります。 これにより、デベロッパー センター アカウントと Cloud パートナー ポータル アカウントが相互に関連付けられます。

後で、アプリケーションで作業している会社の他のメンバーを追加できます。 次のセクションの手順に従って、Cloud パートナー ポータルで共同作成者または所有者として追加できます。

クラウド パートナー ポータル ポータルで共同作成者/所有者として追加された場合は、自分のアカウントでサインインすることができます。

>[!TIP]
>参加ポリシーについては、Azure Web サイトを参照してください。

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>クラウド パートナー ポータルで所有者または共同作成者としてのユーザーを管理する

[クラウド パートナー ポータルでユーザーを管理する手順](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>次の手順

これでアカウントの作成と登録が行われたので、Azure Marketplace での発行プロセスを開始できます。

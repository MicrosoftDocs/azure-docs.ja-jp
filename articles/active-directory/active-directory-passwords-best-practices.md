---
title: "ベスト プラクティス: Azure AD Password Management | Microsoft Docs"
description: "Azure Active Directory での Password Management のデプロイと使用のベスト プラクティス、サンプル エンド ユーザー マニュアル、およびトレーニング ガイドです。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: f8cd7e68-2c8e-4f30-b326-b22b16de9787
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 629fa1cc52963a844ee3bd7f8031ef2e5c340ae5
ms.lasthandoff: 03/28/2017


---
# <a name="deploying-password-management-and-training-users-to-use-it"></a>Password Management のデプロイとユーザー トレーニング
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
>
>

パスワードのリセットを有効にした後、次に実行する必要がある手順は、組織内のユーザーにサービスを使用してもらうことです。 そのためには、ユーザーがサービスを使用できるように適切に構成されていることを確認すると共に、ユーザーが自分のパスワードを問題なく管理するために必要なトレーニングを受けてもらう必要があります。 この記事では、次の概念を説明します。

* [**ユーザーに Password Management を構成してもらう方法**](#how-to-get-users-configured-for-password-reset)
  * [アカウントがパスワード リセット用に構成されている状態とは](#what-makes-an-account-configured)
  * [認証データを管理者が設定する方法](#ways-to-populate-authentication-data)
* [**パスワード リセットを組織内に展開する最善の方法**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [電子メール ベースの展開 + サンプル メール通信](#email-based-rollout)
  * [ユーザー用に独自のカスタム パスワード管理ポータルを作成する](#creating-your-own-password-portal)
  * [ユーザーのサインイン時に登録を強制する強制登録の使用方法](#using-enforced-registration)
  * [ユーザー アカウントの認証データをアップロードする方法](#uploading-data-yourself)
* [**サンプル ユーザーとサポート トレーニング資料 (準備中)**](#sample-training-materials)

## <a name="how-to-get-users-configured-for-password-reset"></a>ユーザーにパスワード リセットを構成してもらう方法
このセクションでは、組織内のすべてのユーザーが、自分のパスワードを忘れた場合にセルフ サービス パスワード リセットを効果的に使用できるようにするさまざまなメソッドについて説明します。

### <a name="what-makes-an-account-configured"></a>アカウントが構成された状態とは
ユーザーは、パスワード リセットを使用する前に、次の条件を **すべて** 満たす必要があります。

1. ディレクトリでパスワードのリセットが有効であること。  パスワード リセットの有効化については、「[ユーザーによる Azure AD パスワードのリセットを有効にする](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords)」または「[ユーザーによる AD パスワードのリセットまたは変更を有効にする](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords)」を参照してください。
2. ユーザーがライセンスを取得していること。
   * クラウド ユーザーの場合は、**有料の Office 365 ライセンス**を持っているか、**AAD Basic** または **AAD Premium ライセンス**が割り当てられていること。
   * オンプレミスのユーザー (フェデレーション ユーザーまたはハッシュ同期ユーザー) の場合は、 **AAD Premium ライセンスが割り当てられていること**。
3. ユーザーが現在のパスワード リセット ポリシーに従って、 **認証データの最小セットを定義していること** 。
   * 認証データは、ディレクトリ内の対応するフィールドに適切な形式のデータが含まれている場合に定義されたとみなされます。
   * 認証データの最小セットは、ワン ゲート ポリシーが構成される場合は有効な認証オプションの**少なくとも 1 つ**が、ツー ゲート ポリシーが構成される場合は有効な認証オプションの**少なくとも 2 つ**が構成されたときに定義されます。
4. ユーザーがオンプレミスのアカウントを使用している場合は、 [Password Writeback](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) が有効化され、オンになっていること。

### <a name="ways-to-populate-authentication-data"></a>認証データを設定する方法
組織内のユーザーがパスワードのリセットに使用するデータを指定する方法はいくつかあります。

* [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)または [Office 365 管理ポータル](https://portal.microsoftonline.com)でユーザーを編集する
* Azure AD Sync を使用して、ユーザー プロパティをオンプレミスの Active Directory ドメインから Azure AD に同期する
* [この手順に従って](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)、Windows PowerShell を使用してユーザー プロパティを編集します。
* ユーザーに登録ポータル ( [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)
* パスワードのリセットのために、Azure AD アカウントへのサインイン時に登録を必須にするには、[**[サインイン時にユーザーに登録を求めますか?]**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) 構成オプションを **[はい]** に設定します。

システムでパスワード リセットを機能させるためにユーザーが登録を行う必要はありません。  たとえば、ローカル ディレクトリに既存の携帯電話または会社の電話番号がある場合は、Azure AD に同期することで、パスワード リセットで自動的に使用できます。

詳細については、[パスワードのリセットのデータの使用方法](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset)と [PowerShell で各認証フィールドを設定する方法](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users)に関するセクションを参照してください。

## <a name="what-is-the-best-way-to-roll-out-password-reset-for-users"></a>ユーザーにパスワード リセットを展開する最善の方法
パスワード リセットの一般的な展開手順を次に示します。

1. [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)で、**[構成]** タブに移動し、**[パスワードのリセットが有効になっているユーザー]** オプションで **[はい]** を選択することで、ディレクトリでパスワード リセットを有効にします。
2. **Microsoft Azure 管理ポータル** で、 [[ライセンス]](https://manage.windowsazure.com)タブに移動し、パスワード リセットを許可するユーザーに適切なライセンスを割り当てます。
3. 必要に応じて、**[パスワード リセットへのアクセスの制限]** オプションを **[はい]** に設定し、パスワード リセットを有効にするセキュリティ グループを選択することで、パスワード リセットをユーザー グループに制限して、この機能を時間をかけて展開します (これらのユーザーにはライセンスが割り当てられている必要があります)。
4. 登録手順を説明した電子メールをユーザーに送信するかアクセス パネルでの強制登録を有効にすることで、ユーザーにパスワード リセットを使用するように指示します。または、DirSync、PowerShell、[Microsoft Azure 管理ポータル](https://manage.windowsazure.com)を使用して、ユーザー用の適切な認証データを管理者がアップロードします。  詳細は後述します。
5. 時間が経過したら、[レポート] タブに移動し、[**[パスワード リセット登録アクティビティ]**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity-in-the-classic-portal) レポートを表示して、ユーザーの登録状況を確認します。
6. 十分な数のユーザーが登録されたら、[レポート] タブに移動し、[**[パスワード リセット アクティビティ]**](active-directory-passwords-get-insights.md#view-password-reset-activity-in-the-classic-portal) レポートを表示して、ユーザーによるパスワード リセットの使用状況を確認します。

組織内のユーザーにパスワード リセットの登録とパスワード リセットの使用を実行できることを通知する方法はいくつかあります。  これらを次に説明します。

### <a name="email-based-rollout"></a>電子メール ベースの展開
パスワード リセットの登録とパスワード リセットの使用についてユーザーに通知する最も簡単な方法は、手順の説明を記載した電子メールを送信することです。  この方法で使用できるテンプレートを次に示します。  色やロゴを自由に置き換えて、要件に合わせてカスタマイズしてください。

  ![][001]

電子メール テンプレートを [ここ](https://1drv.ms/f/s!AharQMeRmrWggcNnPlk_7xHInYJzow)からダウンロードできます。

### <a name="creating-your-own-password-portal"></a>独自のパスワードのポータルを作成する
パスワード管理機能をデプロイする多くの顧客にとって有効な 1 つの手法は、1 つの場所でパスワードに関係するすべてのことを管理するのに使用できる単一の「パスワード ポータル」を作成することです。  

最大規模のお客様の多くは、Azure AD パスワード リセット ポータル、パスワード リセット登録ポータル、およびパスワード変更ページへのリンクを使用して、https://passwords.contoso.com のようなルート DNS エントリを作成する方法を選択します。  この方法では、ユーザーがサービスの使用を開始するまでに少し時間がある場合にアクセス可能な覚えやすい URL を 1 つ、送信する任意の電子メールまたは広告に含めることができます。

ここでは、最新の応答性の高い UI 設計パラダイムを使用する単純なページを作成してあります。これは、すべてのブラウザーおよびモバイル デバイスで動作します。

  ![][007]

[ここで Web サイト テンプレートをダウンロードする](https://github.com/kenhoff/password-reset-page)ことができます。  組織のニーズに合わせてロゴおよび色をカスタマイズすることをお勧めします。

### <a name="using-enforced-registration"></a>強制登録の使用
ユーザー自身にパスワード リセットの登録を行ってもらう場合は、ユーザーがアクセス パネル ( [http://myapps.microsoft.com](http://myapps.microsoft.com)) にサインインしたときに、登録を強制することもできます。  このオプションは、ディレクトリの **[構成]** タブで **[ユーザーが初めてアクセス パネルにサインインするときに登録を要求しますか?]** オプションを有効にすることで、有効にできます。  

必要に応じて、 **[ユーザーによる連絡先データの確認が必要になるまでの日数]** オプションを 0 以外の値に変更することで、構成可能な期間の後でユーザーに再登録を求めるかどうかを定義することもできます。 詳細については、「 [User Password Management の動作のカスタマイズ](active-directory-passwords-customize.md#password-management-behavior) 」を参照してください。

  ![][002]

このオプションを有効にした後、ユーザーがアクセス パネルにサインインすると、管理者がユーザーの連絡先情報を確認することを求めていることを通知するポップアップが表示されます。 ユーザーが自分のアカウントにアクセスできなくなっている場合は、ポップアップからパスワードをリセットできます。

  ![][003]

**[今すぐ確認する]** をクリックすると、**パスワード リセット登録ポータル** ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) に移動し、登録を求められます。  この方法による登録は、 **[キャンセル]** ボタンをクリックするかウィンドウを閉じることでキャンセルできますが、ユーザーが登録を行っていない場合は、サインインするたびに登録を求められます。

  ![][004]

### <a name="uploading-data-yourself"></a>管理者によるデータのアップロード
管理者が認証データをアップロードする場合、パスワードをリセットする前にユーザーがパスワード リセットの登録を行う必要はありません。  ユーザーは、定義されたパスワード リセット ポリシーに従って認証データをアカウントに定義している限り、自分のパスワードをリセットできます。

AAD Connect または Windows PowerShell を使用して設定できるプロパティについては、「 [パスワードのリセットで使用されるデータ](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) を参照してください。

認証データは、次の手順に従って、 [Microsoft Azure 管理ポータル](https://manage.windowsazure.com) でアップロードできます。

1. **Microsoft Azure 管理ポータル** の [[Active Directory 拡張機能]](https://manage.windowsazure.com)で、ディレクトリに移動します。
2. **[ユーザー]** タブをクリックします。
3. 対象のユーザーを一覧から選択します。
4. 最初のタブに、パスワードのリセットを有効にするためのプロパティとして使用できる **[連絡用メール アドレス]**が表示されます。

   ![][005]
5. **[勤務先の情報]** タブをクリックします。
6. ページに、**[会社電話]**、**[携帯電話]**、**[認証用電話]**、および **[認証用メール]** が表示されます。  これらのプロパティを設定して、ユーザーがパスワードをリセットできるようにすることも可能です。

   ![][006]

これらのプロパティの使用方法については、「 [パスワードのリセットで使用されるデータ](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) 」を参照してください。

PowerShell でこのデータの参照と設定を行う方法については、 [PowerShell からユーザーのパスワード リセット データにアクセスする方法](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) に関するセクションを参照してください。

## <a name="sample-training-materials"></a>サンプル トレーニング資料
パスワード リセットのデプロイと使用を IT 組織とユーザーに短時間で浸透させるサンプル トレーニング資料を準備しています。  しばらくお待ちください。

<br/>
<br/>
<br/>

## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。

[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"


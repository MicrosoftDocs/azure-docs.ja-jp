---
title: "しくみ: Azure AD でのパスワード管理 | Microsoft Docs"
description: "ユーザーによるパスワードの登録、リセット、および変更、そして管理者によるオンプレミスの Active Directory パスワードの構成、レポート作成、および管理を含む、Azure AD でのパスワード管理について学習します。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 24c94f80afdb3d3330a835fae2f77ecb213a9ae5
ms.lasthandoff: 03/28/2017


---
# <a name="how-password-management-works-in-azure-active-directory"></a>Azure Active Directory でのパスワード管理のしくみ
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。

Azure Active Directory (Azure AD) でのパスワード管理は、次の論理コンポーネントで構成されます。

* **パスワード管理の構成ポータル** – [Azure Portal](https://manage.windowsazure.com) にある自分のディレクトリの **[構成]** タブから、テナント内のパスワード管理方法のさまざまな側面を制御できます。
* **ユーザー登録ポータル** – ユーザーはこの Web ポータルから、パスワード リセットを自分で登録できます。
* **ユーザー パスワードのリセット ポータル** – ユーザーは、管理者の定めるパスワード リセット ポリシーに従ってさまざまなチャレンジをクリアすることにより、自分のパスワードをリセットできます。
* **ユーザー パスワードの変更ポータル** – ユーザーはこの Web ポータルを使用すると、古いパスワードを入力し、新しいパスワードを選択することにより、自分のパスワードをいつでも変更できます。
* **パスワード管理レポート** – [Azure Portal](https://manage.windowsazure.com) にある自分のディレクトリの **[レポート]** タブの **[活動レポート]** セクションから、テナント内のパスワードのリセットと登録のアクティビティを参照および分析できます。
* **Password Writeback Component of Azure AD Connect** – Azure AD Connect のインストール時に、必要に応じてパスワード ライトバック機能を有効にし、クラウドからのフェデレーション ユーザーまたはパスワード同期ユーザーのパスワード管理を有効化できます。

## <a name="password-management-configuration-portal"></a>パスワード管理の構成ポータル
特定のディレクトリについて、[Azure Portal](https://manage.windowsazure.com) を使用してパスワード管理ポリシーを構成するには、ディレクトリの **[構成]** タブにある **[ユーザー パスワードのリセット ポリシー]** セクションに移動します。 この構成ページでは、パスワードが組織内でどのように管理されるかを、以下をはじめとするさまざまな側面から制御できます。

* ディレクトリのユーザー全員に対してパスワードのリセットの有効と無効を切り替える。
* パスワードをリセットする前にユーザーがクリアする必要があるチャレンジの数 (1 つまたは 2 つ) を設定する。
* 組織内のユーザーに対して有効にするチャレンジを次の中から選択して設定する。
  * 携帯電話 (テキストによる確認コードまたは音声通話)
  * 会社電話 (音声通話)
  * 代替電子メール (電子メールによる確認コード)
  * セキュリティの質問 (ナレッジ ベースの認証)
* セキュリティの質問の認証方法 (セキュリティの質問が有効な場合のみ表示される) を使用するために、登録する必要がある質問の数を設定する。
* セキュリティの質問の認証方法 (セキュリティの質問が有効な場合のみ表示される) を使用するために、リセット時に指定する必要がある質問の数を設定する。
* ユーザーがパスワード リセットに登録する際に使用を選択できる事前に準備されたローカライズ済みのセキュリティに関する質問を使用する (セキュリティの質問が有効になっている場合にのみ表示される)。
* ユーザーがパスワード リセットに登録する際に使用を選択できるカスタムのセキュリティに関する質問を定義する (セキュリティの質問が有効になっている場合にのみ表示される)。
* アプリケーションの[アクセス パネル](http://myapps.microsoft.com)へのアクセス時に、パスワード リセットの登録をユーザーに求める。
* 設定可能な日数が経過した後、以前登録したデータを再確認することをユーザーに求める (適用されている登録が有効な場合のみ表示される)。
* パスワードのリセットに際して問題が発生した場合に表示される、カスタムのヘルプデスクの電子メール アドレスまたは URL を指定する。
* パスワード ライトバック機能を有効化または無効化する (Azure AD Connect を使用してパスワード ライトバックがデプロイされている場合)。
* パスワード ライトバック エージェントの状態を表示する (Azure AD Connect を使用してパスワード ライトバックがデプロイされている場合)。
* パスワードがリセットされたときのユーザーへの電子メール通知を有効化する ([Azure Portal](https://manage.windowsazure.com) の **[通知]** セクションで確認できる)。
* 管理者のパスワードを他の管理者がリセットしたときの管理者への電子メール通知を有効化する ([Azure Portal](https://manage.windowsazure.com) の **[通知]** セクションで確認できる)。
* テナント ブランド化のカスタマイズ機能 ([Azure Portal](https://manage.windowsazure.com) の **[ディレクトリのプロパティ]** セクション) を使用して、ユーザー パスワードのリセット ポータルとパスワード リセットの電子メールを組織のロゴおよび名前によりブランド化する。

組織内のパスワード管理を構成する方法について詳しくは、[Azure AD でのパスワード管理の概要](active-directory-passwords-getting-started.md)に関する記事をご覧ください。

## <a name="user-registration-portal"></a>ユーザー登録ポータル
ユーザーがパスワードをリセットできるようにするには、パスワードのリセットに対して管理者が設定した数のチャレンジをクリアできるように、クラウドのユーザー アカウントを正しい認証データに更新する必要があります。 Azure Web ポータルまたは Office Web ポータル、DirSync または Azure AD Connect、あるいは Windows PowerShell を使用して、ユーザーの代わりにこの認証情報を定義することもできます。

ただし、ユーザーが自分でデータを登録する方が好ましい場合は、ユーザーがアクセスして情報を追加できる Web ページも用意されています。 このページでユーザーは、それぞれの組織で有効になっているパスワード リセット ポリシーに従って認証情報を指定できます。 このデータが確認されると、クラウドのユーザー アカウントに保存され、後にアカウントを復旧するときに使用されます。

登録ポータルの外観を次に示します。

  ![][001]

詳しくは、[Azure AD でのパスワード管理の概要](active-directory-passwords-getting-started.md)および [Azure AD でのパスワード管理のベスト プラクティス](active-directory-passwords-best-practices.md)に関する記事をご覧ください。

## <a name="user-password-reset-portal"></a>ユーザー パスワード リセット ポータル
セルフサービスのパスワード リセットを有効にして、セルフサービスのパスワード リセットについて組織のポリシーを設定し、ディレクトリに正しいユーザーの連絡先データが含まれることを確認できたら、企業または学校のアカウントを使用してサインインする Web ページ ([portal.microsoftonline.com](https://portal.microsoftonline.com)など) であればどこからでも、組織内のユーザーがパスワードを自動的にリセットできるようになります。 サインインに組織のアカウントを使用するページでは、**[アカウントにアクセスできない場合]** というリンクが表示されます。

  ![][002]

このリンクをクリックすると、セルフサービスのパスワード リセット ポータルが開きます。

  ![][003]

ユーザーが自分のパスワードをリセットする方法について詳しくは、[Azure AD でのパスワード管理の概要](active-directory-passwords-getting-started.md)に関する記事をご覧ください。

## <a name="user-password-change-portal"></a>ユーザー パスワード変更ポータル
ユーザーが自分のパスワードを変更したい場合は、パスワード変更ポータルを使用すればいつでも変更できます。 パスワード変更ポータルへは、アクセス パネルのプロファイル ページからアクセスできるほか、Office 365 アプリケーション内の **[パスワードを変更する]** リンクをクリックしてもアクセスできます。 パスワードの有効期限が切れた場合は、ユーザーがサインインすると自動的にパスワードの変更を求められます。

  ![][004]

いずれの場合も、パスワード ライトバックが有効で、ユーザーがフェデレーションであるかパスワードが同期されているかのいずれかであれば、これらの変更されたパスワードはオンプレミスの Active Directory にライトバックされます。

パスワード変更ポータルの外観を次に示します。

  ![][005]

ユーザーが自分のオンプレミスの Active Directory パスワードを変更する方法について詳しくは、[Azure AD でのパスワード管理の概要](active-directory-passwords-getting-started.md)に関する記事をご覧ください。

## <a name="password-management-reports"></a>パスワード管理レポート
**[レポート]** タブに移動すると、**[アクティビティ ログ]** セクションに、**[パスワード リセット アクティビティ]** および **[パスワード リセット登録アクティビティ]** の 2 つのパスワード管理レポートが表示されます。 これら 2 つのレポートを使用すると、組織内でパスワード リセットに登録しているユーザー、およびパスワード リセットを使用しているユーザーのビューを参照できます。

[Azure Portal](https://manage.windowsazure.com) でのこれらのレポートの外観を示します。

  ![][006]

詳しくは、[Azure AD でのパスワード管理のレポートの詳細](active-directory-passwords-get-insights.md)に関する記事をご覧ください。

## <a name="password-writeback-component-of-azure-ad-connect"></a>Azure AD Connect のパスワード ライトバック コンポーネント
組織内のユーザーのパスワードがオンプレミスの環境 (フェデレーションまたはパスワード同期のいずれか) から発生している場合、Azure AD Connect の最新バージョンをインストールすると、クラウドから直接パスワードを更新できます。 これは、ユーザーが Azure AD のパスワードを忘れた場合や変更を希望する場合、Web から直接更新可能だということです。 次に、Azure AD Connect インストール ウィザードでパスワード ライトバックを選択できる場所を示します。

  ![][007]

Azure AD Connect について詳しくは、[Azure AD Connect の概要](active-directory-aadconnect.md)に関する記事をご覧ください。 パスワード ライトバックについて詳しくは、[Azure AD でのパスワード管理の概要](active-directory-passwords-getting-started.md)に関する記事をご覧ください。


## <a name="next-steps"></a>次のステップ

Azure AD のパスワードのリセットについて詳しくは、次のページをご覧ください。

* **サインインに問題がありますか?** 問題がある場合は、[自分のパスワードを変更してリセットする方法](active-directory-passwords-update-your-own-password.md#reset-your-password)を確認してください。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。

[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"


---
title: アカウントの 2 要素認証に関する一般的な問題 - Azure AD
description: 2 要素認証と職場または学校アカウントの一般的な問題のソリューション。
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8f3aef42-7f66-4656-a7cd-d25a971cb9eb
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 08/20/2020
ms.author: curtand
ms.reviewer: kexia
metadata ms.custom: contperfq1
ms.openlocfilehash: 61ba06054e86d544ae4ab1edbdef8d86667ef3b2
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796580"
---
# <a name="common-problems-with-two-factor-verification-and-your-work-or-school-account"></a>2 要素認証と職場または学校アカウントの一般的な問題

Azure Active Directory (Azure AD) 組織では、2 要素認証 (2FV) を有効にできます。 2FV には、だれもが願うよりも頻繁に発生するように思われる一般的な問題がいくつか存在します。 この記事に、最も一般的な問題の修正方法をまとめました。

2FV がオンになっている場合にアカウントにサインインするには、以下のデータの組み合わせが必要です。

- ユーザー名
- お使いのパスワード
- モバイル デバイスまたは電話

2FV では、_知っている_何かに加えて、_持っている_何か必要とされるため、2FV の安全性はパスワードだけの場合よりも高くなります。 ユーザーの物理的な電話を持っているハッカーはいないのです。

>[!Important]
>管理者の方は、 Azure AD 環境の設定と管理方法の詳細については、「[Azure AD のドキュメント](../index.yml)」をご参照ください。

このコンテンツは、職場または学校アカウント、つまり勤務先から支給されるアカウント (dritan@contoso.com など) での使用をサポートすることを目的としています。 ご自身のために自分で設定するアカウント (danielle@outlook.com など) である個人用 Microsoft アカウントでの 2 要素認証に関して問題がある場合は、[Microsoft アカウントの 2 要素認証を有効または無効にする方法](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)に関するページをご参照ください。

## <a name="i-dont-have-my-mobile-device-with-me"></a>自分のモバイル デバイスを持っていません

よくあることです。 家にモバイル デバイスを置いてきたため、電話を使って自分の身元を確認できなくなっています。 以前に、会社の電話など、アカウントにサインインするための代わりの方法を追加したかも知れません。 その場合は、ここでこの代わりの方法を利用できます。 代わりの検証方法を追加したことがない場合は、所属する組織のヘルプ デスクに支援を依頼することができます。

### <a name="to-sign-in-to-your-work-or-school-account-using-another-verification-method"></a>別の検証方法を使用して職場または学校アカウントにサインインするには

1. アカウントにサインインし、**2 要素認証**ページで **[別の方法でサインイン]** リンクを選択します。

    ![サインインの検証方法を変更する](./media/multi-factor-authentication-end-user-troubleshoot/two-factor-auth-signin-another-way.png)

    **[Sign in another way]\(別の方法でサインイン\)** リンクが表示されない場合は、他の検証方法を設定していないことを意味します。 アカウントにサインインするには、管理者に連絡してサポートを受ける必要があります。

2. 他の認証方法を選択し、2 要素認証を続行します。

## <a name="i-lost-my-mobile-device-or-it-was-stolen"></a>モバイル デバイスを紛失したか、または盗難にあいました

モバイル デバイスを紛失した場合やモバイル デバイスが盗まれた場合は、次のいずれかのアクションを行うことができます。

- 別の方法を使用してサインインする。
- 所属する組織のヘルプ デスクに設定をクリアするよう依頼する。

携帯電話の紛失や盗難があった場合は、所属する組織のヘルプ デスクに知らせることを強くお勧めします。 ヘルプ デスクでは、アカウントに対して適切な更新を行うことができます。 設定が消去されると、次回のサインイン時に [2 要素認証の登録](multi-factor-authentication-end-user-first-time.md)を求めるメッセージが表示されます。

## <a name="im-not-receiving-the-verification-code-sent-to-my-mobile-device"></a>モバイル デバイスに送信された確認コードを受信していません

確認コードを受信しないことは一般的な問題です。 この問題は、通常、モバイル デバイスとその設定に関係があります。 試すことができるいくつかのアクションを示します。

これを試します | ガイダンス情報
--------- | ------------
モバイル デバイスを再起動します | 場合によっては、デバイスの更新だけで済むことがあります。 デバイスを再起動すると、すべてのバックグラウンド プロセスとサービスが終了されます。 再起動により、デバイスのコア コンポーネントもシャットダウンされます。 どのサービスやコンポーネントも、デバイスを再起動すると更新されます。
セキュリティ情報が正しいことを確認します | セキュリティ検証方法の情報が正確であること (特に電話番号) を確認します。 間違った電話番号を入力すると、すべてのアラートがその正しくない番号に送られます。 幸い、そのユーザーはアラートに対して何もできませんが、アカウントへのサインインにも役立ちません。 情報が正しいことを確認するには、「[2 要素認証方法の設定を管理する](multi-factor-authentication-end-user-manage-settings.md)」の記事の手順を参照してください。
通知が有効になっていることを確認します | モバイル デバイスで通知が有効になっていることを確認します。 以下の通知モードが許可されるようにします。 <br/><br/> &bull; 音声通話 <br/> &bull; 認証アプリ <br/> &bull; テキスト メッセージング アプリ <br/><br/> お使いのデバイスに_表示_されるアラートが、これらのモードによって作成されることを確認します。
デバイス信号とインターネット接続があることを確認します | 通話とテキスト メッセージがモバイル デバイスに送信されていることを確認します。 友人に頼んで、電話での呼び出しとテキスト メッセージの送信を行ってもらい、両方とも受信することを確認するします。 通話やテキストを受信しない場合は、最初にモバイル デバイスの電源が入っていることを確認します。 デバイスの電源がオンであっても通話やテキストをまだ受信していない場合は、おそらくネットワークに問題があります。 お使いのプロバイダーに連絡する必要があります。 信号関連の問題が頻繁に発生する場合は、モバイル デバイスに [Microsoft Authenticator アプリ](user-help-auth-app-download-install.md)をインストールして使用することをお勧めします。 Authenticator アプリを使用すると、電波またはインターネット接続を必要とせずに、サインイン用のランダムなセキュリティ コードを生成できます。
応答不可をオフにします | モバイル デバイスの**応答不可**機能が有効になっていないことを確認します。 この機能が有効になっていると、モバイル デバイスで通知を受け取ることができません。 この機能を無効にする方法については、モバイル デバイスのマニュアルを参照してください。
電話番号をブロック解除します | 米国の場合、Microsoft からの音声通話は、+1 (866) 539 4191、+1 (855) 330 8653、+1 (877) 668 6536 から発信されます。
バッテリに関連する設定を確認します | これは一見、少し奇妙に思えます。 しかし、バッテリの最適化を設定し、使用頻度の低いアプリがバックグラウンドでアクティブのままにならないようにしている場合は、通知システムが高い確率で影響を受けています。 この問題を解決するには、認証アプリとメッセージング アプリの両方に対してバッテリ最適化を無効にします。 その後、お使いのアカウントに再度サインインしてみてください。
サードパーティ製のセキュリティ アプリを無効にします | 一部の電話セキュリティ アプリでは、いらいらさせる不明な発信元からのテキスト メッセージや通話がブロックされます。 このようなアプリを使用すると、電話での確認コードの受信が妨げられる可能性があります。 お使いの電話でサードパーティ製のセキュリティ アプリを無効にしてから、別の確認コードを送信するよう依頼してください。

## <a name="im-not-being-prompted-for-my-second-verification-information"></a>2 番目の検証情報の入力を求めるメッセージが表示されていません

自分のユーザー名とパスワードを使って、職場または学校用のアカウントにサインインします。 次に、追加のセキュリティ検証情報の入力を求めるメッセージが表示されるはずです。 メッセージが表示されない場合は、お使いのデバイスをまだ設定していない可能性があります。 特定の追加のセキュリティ検証方法を使用するように、モバイル デバイスを設定する必要があります。

モバイル デバイスの電源がオンになっていて使用可能なことを確認するには、[2 要素認証方法の設定の管理](multi-factor-authentication-end-user-manage-settings.md)に関する記事を参照してください。 デバイスまたはアカウントを設定していないことがわかっている場合は、[2 段階認証のアカウントの設定](multi-factor-authentication-end-user-first-time.md)に関する記事の手順に従って、これを行うことができます。

## <a name="i-have-a-new-phone-number-and-i-want-to-add-it"></a>新しい電話番号があるのでそれを追加したい

新しい電話番号がある場合は、セキュリティ検証方法の詳細を更新する必要があります。 これにより、検証のプロンプトが正しい場所に表示されるようにすることができます。 検証方法を更新するには、[2 要素認証方法の設定の管理](multi-factor-authentication-end-user-manage-settings.md#add-or-change-your-phone-number)に関する記事の、「**電話番号を追加または変更する**」の手順に従います。

## <a name="i-have-a-new-mobile-device-and-i-want-to-add-it"></a>新しいモバイル デバイスがあるのでそれを追加したい

新しいモバイル デバイスがある場合は、2 要素認証で動作するようにデバイスを設定する必要があります。 これはマルチステップのソリューションです。

1. [2 段階認証のためのアカウントの設定](multi-factor-authentication-end-user-first-time.md)に関する記事の手順に従って、お使いのアカウントで動作するようにデバイスを設定します。

1. **[追加のセキュリティ確認]** ページでアカウントとデバイスの情報を更新します。 古いデバイスを削除し、新しいデバイスを追加することで、更新を実行します。 詳しくは、「[2 要素認証方法の設定を管理する](multi-factor-authentication-end-user-manage-settings.md)」を参照してください。

省略可能な手順:

- モバイル デバイスに Microsoft Authenticator アプリをインストールしてセットアップします。 記事「[Microsoft Authenticator アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」の手順に従ってインストールします。

- 信頼されたデバイスで 2 要素認証 (2FV) を有効にします。 [2 要素認証方法の設定の管理](multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device)に関する記事の、「**信頼されたデバイスで 2 要素認証のプロンプトを有効にする**」セクションの手順に従って 2FV を有効にします。

## <a name="im-having-problems-signing-in-on-my-mobile-device-while-traveling"></a>旅行中にモバイル デバイスにサインインするときに問題が発生します

外国にいるときは、テキスト メッセージングなどのモバイル デバイス関連の検証方法を使用するのが難しい場合があります。 また、モバイル デバイスでローミング料金が発生する可能性もあります。 このような状況では、Wi-Fi ホットスポット接続オプションを有効にして Microsoft Authenticator アプリを使用することをお勧めします。 モバイル デバイスに Microsoft Authenticator アプリをセットアップする方法の詳細については、「[Microsoft Authenticator アプリのダウンロードとインストール](user-help-auth-app-download-install.md)」を参照してください。

## <a name="i-cant-get-my-app-passwords-to-work"></a>アプリ パスワードを動作させることができません

2 要素認証がサポートされていない古いバージョンのデスクトップ アプリケーションでは、アプリ パスワードによって通常のパスワードが置き換えられます。 まず、パスワードを正しく入力したことを確認してください。 それでも問題が解決されない場合は、アプリのために新しいアプリ パスワードを作成してみてください。 これは、記事「[2 段階認証のアプリ パスワードを管理する](multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page)」の、**MyApps ポータルを使用したアプリ パスワードの作成と削除**に関するセクションの手順に従って行います。

## <a name="i-cant-turn-off-two-factor-verification"></a>2 要素認証を無効にすることができません

職場または学校のアカウント (alain@contoso.com など) で 2 要素認証を使用している場合、追加したこのセキュリティ機能を使用しなければならないと組織が決定している可能性があります。 この機能を使用しなければならないことは組織の決定であるため、個人でこれを無効にすることはできません。 ただし、alain@outlook.com のような個人アカウントで 2 要素認証を使用している場合は、この機能の有効/無効を切り替えることができます。 個人のアカウントに対する 2 要素認証を制御する方法については、[Microsoft アカウントの 2 段階認証を有効または無効にする](https://support.microsoft.com/help/4028586/microsoft-account-turning-two-step-verification-on-or-off)に関するページをご覧ください。

2 要素認証を無効にできない場合は、組織レベルで適用されているセキュリティの既定値群が原因である可能性もあります。 セキュリティの既定値の詳細については、[セキュリティの既定値](../fundamentals/concept-fundamentals-security-defaults.md)に関する記事を参照してください。

## <a name="i-didnt-find-an-answer-to-my-problem"></a>問題に対する回答が見つからなかった場合

これらの手順を試してもまだ問題が発生する場合は、自分の組織のヘルプ デスクに連絡してサポートを受けてください。

## <a name="related-articles"></a>関連記事

- [2 要素認証方法の設定を管理する](multi-factor-authentication-end-user-manage-settings.md)

- [アカウントへの 2 段階認証の設定](multi-factor-authentication-end-user-first-time.md)

- [Microsoft Authenticator アプリに関する FAQ](user-help-auth-app-faq.md)
---
title: "カスタマイズ: Azure Active Directory のパスワード管理 | Microsoft Docs"
description: "ニーズに合わせて Azure AD でパスワード管理の外観、動作、および通知をカスタマイズする方法。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: 2cddd150-8747-447a-a7cf-1d7d5775c0b3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 06d94b4f0f2c5cc979fe7ecb061fee7d8dde8972
ms.lasthandoff: 03/28/2017


---
# <a name="customizing-password-management-to-fit-your-organizations-needs"></a>組織ニーズに合わせたパスワード管理のカスタマイズ
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
>
>

ユーザーに最良のエクスペリエンスを与えるため、パスワード管理の使用可能なすべての構成オプションを調べて試してみることをお勧めします。 **Azure クラシック ポータル** で、 [[Active Directory 拡張機能]](https://manage.windowsazure.com)の [構成] タブに移動することで、今すぐ調査を開始できます。 このトピックでは、**Azure クラシック ポータル**の中で、ディレクトリの [[構成]](https://manage.windowsazure.com) タブから管理者として実行できる各種パスワード管理のカスタマイズについて説明します。

## <a name="what-customization-options-are-available"></a>使用可能なカスタマイズ オプション
以下の表は、Azure Active Directory のパスワード リセットで使用可能なすべてのカスタマイズ オプションの概要を示します。

| トピック | 設定 | 必要なライセンス |
| --- | --- | --- |
| パスワードのリセットを有効または無効にする方法 |[設定: パスワードのリセットが有効になっているユーザー](#users-enabled-for-password-reset) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| パスワードのリセットを一連の特定のユーザーに適用する方法 |[パスワードのリセットを特定のユーザーに制限する](#restrict-access-to-password-reset) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| サポートされる認証方法を変更する方法 |[設定: ユーザーが使用できる認証方法](#authentication-methods-available-to-users) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| 必要な認証方法の数を変更する方法 |[設定: 必要な認証方法の数](#number-of-authentication-methods-required) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| カスタムの秘密の質問を設定する方法 |[設定: カスタムの秘密の質問](#custom-security-questions) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| 既定のローカライズされた秘密の質問を設定する方法 |[設定: ナレッジ ベースの秘密の質問](#knowledge-based-security-questions) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| 必要な秘密の質問の数を変更する方法 |[設定: 登録またはリセット用の秘密の質問の数](#number-of-questions-required-to-register) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| サインイン時にユーザーに登録を強制する方法 |[パスワード リセットの登録ベースのロールアウトの適用](#require-users-to-register-when-signing-in) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| 登録内容の定期的な再確認をユーザーに強制する方法 |[設定: ユーザーに認証データの再確認を強制するまでの日数](#number-of-days-before-users-must-confirm-their-contact-data) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| ユーザーが管理者に連絡する方法をカスタマイズする方法 |[設定: "管理者に問い合わせてください" リンクをカスタマイズする](#customize-the-contact-your-administrator-link) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| クラウドの管理エクスペリエンスからパスワード ライトバックを有効および無効にする方法 |[設定: パスワード ライトバックを有効または無効にする](#write-back-passwords-to-on-premises-directory) | <ul><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| パスワードをリセットせずにオンプレミスの AD アカウントのロックを解除することをユーザーに許可する方法 |[設定: ユーザーがパスワードをリセットせずに AD アカウントのロックを解除できるようにする](#allow-users-to-unlock-accounts-without-resetting-their-password) | <ul><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| ユーザーのパスワード リセット通知を有効にする方法 |[設定: パスワードがリセットされたときにユーザーに通知する](#notify-users-and-admins-when-their-own-password-has-been-reset) |  <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| 管理者のパスワード リセット通知を有効にする方法 |[設定: 管理者が自分のパスワードをリセットしたときに他の管理者に通知する](#notify-admins-when-other-admins-reset-their-own-passwords) | <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |
| パスワード リセットの外観をカスタマイズする方法 |[設定: 会社名、ブランド、ロゴ ](#password-management-look-and-feel) |  <ul><li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li><li>Azure AD Basic [クラウド ユーザーのみ]</li><li>Azure AD Premium P1 または P2 [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Mobility Suite [クラウドまたはオンプレミスのユーザー]</li><li>Enterprise Cloud Suite [クラウドまたはオンプレミスのユーザー]</li></ul> |

## <a name="password-management-look-and-feel"></a>Password Managment の外観
次の表は、各コントロールが、パスワード リセットの登録とリセットを行うユーザーのエクスペリエンスに対して、どのように影響するかを説明しています。  これらのオプションは、[Azure の管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[ディレクトリのプロパティ]** セクションで構成できます。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="directory-name">
                  <p>ディレクトリ名</p>
                </div>
              </td>
              <td>
                <p>パスワード リセット メール通信で、ユーザーまたは管理者に対して表示される組織名を決定します。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>"管理者に問い合わせてください" メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
送信元のフレンドリー名を決定します。例: “Microsoft (<strong>Wingtip Toys</strong> の代理として)”<br><br></li>
                  <li class="unordered">
メールの件名を決定します。例: "<strong>Wingtip Toys</strong> アカウント メール確認コード"<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
送信元のフレンドリー名を決定します。例: “Microsoft (<strong>Wingtip Toys</strong> の代理として)”<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="sign-in-and-access-panel-page-appearance">
                  <p>サインイン ページとアクセス パネル ページの表示</p>
                </div>
              </td>
              <td>
                <p>パスワード リセット ページにアクセスするユーザーに表示するロゴ (マイクロソフトのロゴまたは独自のカスタム ロゴ) を決定します。  この構成項目は、アクセス パネル ページとサインイン ページにブランド化も追加します。</p>
                <p>テナントのブランド化とカスタマイズ機能については、「<a href="https://technet.microsoft.com/library/dn532270.aspx">サインイン ページとアクセス パネル ページに会社のブランド化を追加</a>」を参照してください。</p>
                                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
既定のマイクロソフトのロゴの代わりに、カスタム ロゴをパスワード リセット ポータルの上部に表示するかどうかを決定します。<br><br>
                    <strong>注:</strong> パスワード リセット ページに直接アクセスした場合、パスワード リセット ポータルの最初のページにカスタム ロゴが表示されないことがあります。 ユーザーが自分のユーザー名を入力して [次へ] をクリックすると、カスタム ロゴが表示されます。<br><br>
<code><a href="https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com">https://passwordreset.microsoftonline.com?whr=wingtiptoysonline.com</a></code> のようにパスワード リセットのページで <code>whr</code> パラメーターを渡すことで、カスタム ロゴをページの読み込み時に強制的に表示できます。<br><br>
<code>username</code> パラメーターを渡すことで、ユーザー名フィールドが事前に入力されたリンクを生成できます。 これにより、構成されている場合は組織のロゴ (<code><a href="https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com">https://passwordreset.microsoftonline.com?username=user%40wingtiptoysonline.com</a></code>) も表示されます。</li>
                </ul>
                <p>
                  <strong>"管理者に問い合わせてください" メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーがパスワード リセット UI の [管理者に問い合わせてください] リンクをクリックすることで問い合わせを行うことを選択したときに、管理者に送信されるメールの下部にカスタム ロゴを表示するかどうかを決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット メール:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーがパスワードをリセットしたときに、ユーザーに送信されるメールの下部にカスタム ロゴを表示するかどうかを決定します。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-behavior"></a>パスワード管理の動作
次の表は、各コントロールが、パスワード リセットの登録とリセットを行うユーザーのエクスペリエンスに対して、どのように影響するかを説明しています。  これらのオプションは、[Azure の管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[ユーザー パスワードのリセット ポリシー]** セクションで構成できます。

> [!NOTE]
> これらのポリシー コントロールを表示するには、使用している管理者アカウントに AAD Premium ライセンスが割り当てられている必要があります。<br><br>これらのポリシー コントロールは、パスワードをリセットするエンド ユーザーにのみ適用されます。管理者には適用されません。  **管理者には、Microsoft が管理者のために指定した連絡用メールと携帯電話の既定のポリシーがあり、これらは変更できません。**
>
>

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="users-enabled-for-password-reset">
                  <p>パスワードのリセットが有効になっているユーザー</p>
                </div>
              </td>
              <td>
                <p>このディレクトリのユーザーがパスワードをリセットできるかどうかを決定します。 </p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] を設定した場合、ユーザーは独自のチャレンジ データを登録できません。<br><br></li>
                  <li class="unordered">
[はい] を設定した場合、ディレクトリ内のすべてのユーザーは登録ポータル (<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a>) に移動して、チャレンジ データを登録できます。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーは、パスワード リセットの登録を行う前に、Azure AD Premium または Basic ライセンスが割り当てられている必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] を設定した場合、ユーザーがパスワードをリセットするには管理者に問い合わせる必要があることを示すメッセージが表示されます。<br><br></li>
                  <li class="unordered">
[はい] を設定した場合、ユーザーは、<a href="http://passwordreset.microsoftonline.com">http://passwordreset.microsoftonline.com</a> に移動するか、組織 ID サインイン ページの <strong>[アカウントにアクセスできない場合]</strong> リンクをクリックすることで、パスワードを自動的にリセットできます。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーは、パスワードをリセットする前に、Azure AD Premium または Basic ライセンスが割り当てられている必要があります。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="restrict-access-to-password-reset">
                  <p>パスワード リセットへのアクセスの制限</p>
                </div>
              </td>
              <td>
                <p>特定のユーザー グループのみがパスワードのリセットを使用できるかどうかを決定します  (<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
この設定は、パスワード リセット登録ポータルへのユーザー アクセスには影響しません。 <strong>[パスワードのリセットが有効になっているユーザー]</strong> に <strong>[はい]</strong> を設定した場合、ディレクトリのすべてのエンド ユーザーが、<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> でパスワード リセットの登録を実行できます。
                  </li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] を設定した場合、ディレクトリのすべてのエンド ユーザーがパスワードを変更できます。<br><br></li>
                  <li class="unordered">
[はい] を設定した場合、<strong>[パスワードをリセットできるグループ]</strong> コントロールに指定されたエンド ユーザーだけがパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="group-that-can-perform-password-reset">
                  <p>パスワードをリセットできるグループ</p>
                </div>
              </td>
              <td>
                <p>パスワードをリセットできるエンド ユーザーのグループを決定します  </p>
                <p>(<strong>[パスワード リセットへのアクセスの制限]</strong> が <strong>[はい]</strong> に設定されている場合のみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
グループを指定しないで <strong>[保存]</strong>をクリックすると、<strong>SSPRSecurityGroupUsers</strong> という名前の空のグループが作成されます。<br><br></li>
                  <li class="unordered">
独自のグループを指定する場合は、独自の表示名を指定できます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
この設定は、パスワード リセット登録ポータルへのユーザー アクセスには影響しません。 <strong>[パスワードのリセットが有効になっているユーザー]</strong> に <strong>[はい]</strong> を設定した場合、ディレクトリのすべてのエンド ユーザーが、<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> でパスワード リセットの登録を実行できます。
                  </li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
<strong>[パスワード リセットへのアクセスの制限]</strong> が <strong>[はい]</strong> に設定されている場合、このグループのエンド ユーザーだけがパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="authentication-methods-available-to-users">
                  <p>ユーザーが使用できる認証方法</p>
                </div>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするために使用できるチャレンジを決定します </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
少なくとも 1 つのオプションを選択する必要があります。<br><br></li>
                  <li class="unordered">
ユーザーがパスワードを柔軟にリセットできるように、少なくとも 2 つのオプションを有効にすることを強くお勧めします。<br><br></li>
                  <li class="unordered">
セキュリティの質問を使用する場合、セキュリティの質問は電話またはメール ベースのパスワードのリセット方法よりも安全性が低いため、別の認証方法と併せて使用することを強くお勧めします。<br><br></li>
                </ul>
                <p>
                  <strong>使用されるディレクトリ フィールド</strong>
                </p>
                <ul>
                  <li class="unordered">
[会社電話] は、ディレクトリ内のユーザー オブジェクトの <strong>[会社電話]</strong> 属性に対応します。<br><br></li>
                  <li class="unordered">
[携帯電話] は、ディレクトリ内のユーザー オブジェクトの <strong>[認証用モバイル]</strong> 属性 (パブリックに表示されません)、または<strong>[携帯電話]</strong> 属性 (パブリックに表示されます) のいずれかに対応します。  <strong>[認証用電話]</strong> のデータが先にチェックされ、それが存在しない場合は <strong>[携帯電話]</strong> 属性がチェックされます。<br><br></li>
                  <li class="unordered">
[連絡用メール アドレス] は、ディレクトリ内のユーザー オブジェクトの <strong>[認証用電子メール]</strong> 属性 (パブリックに表示されません) または <strong>[連絡用電子メール]</strong> のいずれかに対応します。  <strong>[認証用電子メール]</strong> のデータが先にチェックされ、それが存在しない場合は <strong>[連絡用電子メール]</strong> 属性がチェックされます。<br><br></li>
                  <li class="unordered">
セキュリティの質問は、ディレクトリ内のユーザー オブジェクトに非公開かつ安全に保存され、登録時にユーザーだけが回答できます。  安全保護のため、現時点では、管理者がこれらの回答を編集または表示する方法はありません。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>既定では、クラウド属性の [会社電話] と [携帯電話] だけが、オンプレミス ディレクトリからクラウド ディレクトリに同期されます。  クラウドに同期されるオンプレミス属性の詳細については、「<a href="https://msdn.microsoft.com/library/azure/dn764938.aspx">Azure Active Directory に同期される属性</a>」を参照してください。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーが登録を行うときに表示される認証メソッドに影響します。  特定の認証方法を有効にしない場合、ユーザーはその認証メソッドでは自己登録できなくなります。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>ユーザーは、現時点では、会社の電話番号を登録することはできません。その認証方法は管理者が定義する必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
特定の確認手順でユーザーがチャレンジとして使用できる認証方法を決定します。  たとえば、Azure Active Directory で、ユーザーの <strong>[会社電話]</strong> フィールドと <strong>[認証用電話]</strong> フィールドの両方にデータがある場合は、これらの認証方法のいずれかを使用してパスワードを回復できます。<br><br></li>
                  <li class="unordered">
                    <strong>注: </strong>管理者が有効にした認証メソッドにデータがある場合にのみ、ユーザーはパスワードをリセットできます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-authentication-methods-required">
                  <p>必要な認証方法の数</p>
                </div>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするために通過する必要がある認証方法の最小数を決定します </p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
必要な認証方法を 1 つまたは 2 つ設定できます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
登録を完了する前に、ユーザーが登録する必要がある認証方法の最小数を決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーがパスワードをリセットする前に通過する必要がある確認手順の数に影響します。  確認手順は、ユーザーが認証情報の 1 つを使用してアカウントを確認するように定義されます (会社電話番号への電話または連絡用電子メールへのメールなど)。<br><br></li>
                  <li class="unordered">
                    <strong>注:</strong> ユーザーのアカウントに、設定されたポリシーに従ってパスワードを問題なくリセットするために必要な認証情報が定義されていない場合は、管理者にパスワードのリセットを依頼することを指示するエラー ページが表示されます。  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-register">
                  <p>登録するために必要な質問の数</p>
                </div>
              </td>
              <td>
                <p>[セキュリティの質問] オプションを登録するときに、ユーザーが回答する必要がある質問の最小数を決定します </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
登録するために必要な 3 ～ 5 個の質問を設定できます。<br><br></li>
                  <li class="unordered">
登録するために必要な質問の数は、リセットするために必要な質問の数以上にする必要があります。<br><br></li>
                  <li class="unordered">
ユーザーが柔軟にパスワードをリセットできるように、登録するために必要な質問の数は、リセットするために必要な質問の数よりも大きい数を設定することをお勧めします。  これは、ユーザーに回答を求める質問を、ユーザーが登録したすべての質問の中からランダムに選択できるため、より安全な構成でもあります。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーがパスワードをリセットするための登録が完了したとみなされる前に、ユーザーが回答する必要がある質問の最小数を決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-questions-required-to-reset">
                  <p>リセットするために必要な質問の数 </p>
                </div>
              </td>
              <td>
                <p>ユーザーがパスワードをリセットするときに回答する必要がある質問の最小数を決定します </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
リセットするために必要な 3 ～ 5 個の質問を設定できます。<br><br></li>
                  <li class="unordered">
リセットするために必要な質問の数は、登録するために必要な質問の数未満にする必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
ユーザーがパスワードをリセットする前に回答する必要がある質問の最小数を決定します。<br><br></li>
                  <li class="unordered">
パスワードのリセット時に、この数の質問が、ユーザーが登録したすべての質問の一覧からランダムに選択されます。  たとえば、ユーザーが 5 つの質問を登録している場合、ユーザーがパスワードをリセットするときに、これら 5 つの質問の中から、3 つの質問がランダムに選択されます。  ユーザーは、パスワードをリセットする前に、すべての質問に正しく答える必要があります。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="knowledge-based-security-questions">
                  <p>ナレッジ ベースのセキュリティに関する質問</p>
                </div>
              </td>
              <td>
                <p>パスワード リセットの登録時とパスワードのリセット時に、ユーザーが選択できる、事前に用意されたセキュリティの質問を定義します。</p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
ナレッジ ベースの質問はすべて、ユーザーのブラウザーのロケールに基づいて O365 言語の完全なセットにローカライズされます。<br><br></li>
                  <li class="unordered">
最大で合計 20 の質問を定義できます (カスタムおよびナレッジ ベースの質問の合計)。<br><br></li>
                 <li class="unordered">
回答の最小文字数は 3 文字です。<br><br></li>
                  <li class="unordered">
回答の最大文字数は 40 文字です。<br><br></li>
                  <li class="unordered">
ユーザーは、同じ質問に 2 度回答することはできません。<br><br></li>
                  <li class="unordered">
ユーザーは、2 つの異なる質問に同じ回答をすることはできません。<br><br></li>
                  <li class="unordered">
Unicode 文字を含む任意の文字セットを使用して回答を定義できます。<br><br></li>
                  <li class="unordered">
定義する質問の数は、登録するために必要な質問の数以上にする必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
パスワード リセットの登録時にユーザーが回答できる質問を決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
パスワードをリセットするためにユーザーが使用できる質問を決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-security-questions">
                  <p>カスタムのセキュリティに関する質問</p>
                </div>
              </td>
              <td>
                <p>パスワード リセットの登録時とパスワードのリセット時に、ユーザーが選択できるセキュリティの質問を定義します </p>
                <p>(<strong>[セキュリティの質問]</strong> チェック ボックスがオンの場合のみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
最大で合計 20 の質問を定義できます (カスタムおよびナレッジ ベースの質問の合計)。<br><br></li>
                  <li class="unordered">
質問の最大文字数は 200 文字です。<br><br></li>
                  <li class="unordered">
回答の最小文字数は 3 文字です。<br><br></li>
                  <li class="unordered">
回答の最大文字数は 40 文字です。<br><br></li>
                  <li class="unordered">
ユーザーは、同じ質問に 2 度回答することはできません。<br><br></li>
                  <li class="unordered">
ユーザーは、2 つの異なる質問に同じ回答をすることはできません。<br><br></li>
                  <li class="unordered">
Unicode 文字を含む任意の文字セットを使用して、質問と回答を定義できます。<br><br></li>
                  <li class="unordered">
定義する質問の数は、登録するために必要な質問の数以上にする必要があります。<br><br></li>
                  <li class="unordered">
カスタムの質問については、ロケールごとに異なる質問を定義することはサポートされていません。  カスタムの質問はすべて、管理用の UI に入力したときの言語で表示されます。ユーザーのブラウザーのロケールが異なる場合でもそのように表示されます。  これらの質問をローカライズ対象とする必要がある場合は、"ナレッジ ベース" の質問を使用してください。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
パスワード リセットの登録時にユーザーが回答できる質問を決定します。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
パスワードをリセットするためにユーザーが使用できる質問を決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="require-users-to-register-when-signing-in">
                  <p>サインイン時にユーザーに登録を求めますか?</p>
                </div>
              </td>
              <td>
                <p>ユーザーが次回サインインしたときに、パスワードをリセットするための連絡先情報の登録をユーザーに要求するかどうかを決定します。  
                </p>
                <p>この機能は、職場または学校のアカウントを使用するサインイン ページで機能します。  このようなページには、Office 365、Microsoft Azure 管理ポータル、アクセス パネル、Azure AD を使用してサインインするフェデレーション アプリケーションまたはカスタム開発されたアプリケーションがすべて含まれます。
                </p>
                <p>強制登録は、パスワードのリセットが有効になっているユーザーにのみ適用されます。そのため、[パスワード リセットへのアクセスの制限] 機能を使用し、パスワード リセットを特定のユーザー グループに適用している場合、そのグループのユーザーだけが、サインイン時にパスワード リセットのための登録を求められます。</p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
この機能を無効にした場合は、ユーザーを手動で<a href="http://aka.ms/ssprsetup">http://aka.ms/ssprsetup</a> に送って、連絡先データを登録することもできます。  <br><br></li>
                  <li class="unordered">
ユーザーは、アクセス パネルの [プロファイル] タブにある <strong>[パスワード リセットの登録]</strong> リンクをクリックすることで登録ポータルにアクセスすることもできます。<br><br></li>
                  <li class="unordered">
この方法による登録は、[キャンセル] ボタンをクリックするかウィンドウを閉じることでキャンセルできますが、ユーザーが登録していない場合は、サインインするたびに登録を求められます。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
この設定は、登録ポータルの動作には影響しません。ユーザーがアクセス パネルにサインインしたときに登録ポータルが表示されるかどうかを決定します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="number-of-days-before-users-must-confirm-their-contact-data">
                  <p>ユーザーによる連絡先データの確認が必要になるまでの日数</p>
                </div>
              </td>
              <td>
                <p><strong>[ユーザーに登録を要求しますか?]</strong> がオンになっている場合、この設定は、ユーザーがデータを再確認するまでの経過時間を決定します。 </p>
                <p>(<strong>[ユーザーが初めてアクセス パネルにサインインするときに登録を要求しますか?]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
0 ～ 730 日の値を指定できます。0 日は、ユーザーが連絡先データの再確認を求められることはないことを意味します。<br><br></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
この設定は、登録ポータルの動作には影響しません。ユーザーの連絡先情報を再確認する必要があるときに登録ポータルが表示されるかどうかを決定します。 <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="customize-the-contact-your-administrator-link">
                  <p>[管理者に問い合わせてください] リンクをカスタマイズしますか?</p>
                </div>
              </td>
              <td>
                <p>エラーが発生したとき、またはユーザーの操作が完了しないときに、パスワード リセット ポータルの左側に表示される [管理者に問い合わせてください] リンクが、カスタム URL またはメール アドレスをポイントするかどうかを制御します。</p>
                <p>(<strong>[パスワードのリセットが有効になっているユーザー]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
この設定を有効にした場合は、この設定のすぐ下にある <strong>[カスタム電子メール アドレスまたは URL]</strong> フィールドに入力して、カスタム URL またはメール アドレスを選択する必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] に設定した場合、ユーザーが強調表示されているリンクをクリックすると、すべてのテナント管理者のプライマリ メール アドレスに、ユーザーのパスワードをリセットすることを要求するメールが送信されます。  このメールは、以下のロジックに従って送信されます。<br><br></li>
                  <li class="unordered">
                    <ul>
                      <li class="unordered">
パスワード管理者がいる場合は、すべてのパスワード管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                      <li class="unordered">
パスワード管理者がいない場合は、すべてのユーザー管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                      <li class="unordered">
ユーザー管理者がいない場合は、すべてのグローバル管理者 (最大 100 人の受信者) にメールを送信します。<br><br></li>
                    </ul>
                  </li>
                  <li class="unordered">
[はい] を設定した場合、この設定は、強調表示されたリンクの動作をカスタマイズして、ユーザーがパスワードのリセットに関するヘルプを得るために移動できるカスタム URL またはメール アドレスを指定します。<br><br></li>
                  <li class="unordered">
URL を指定した場合は新しいタブで開きます。<br><br></li>
                  <li class="unordered">
メール アドレスを指定した場合は、そのメール アドレスへの mailto リンクが作成されます。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="custom-email-address-or-URL">
                  <p>カスタム電子メール アドレスまたは URL</p>
                </div>
              </td>
              <td>
                <p><strong>[管理者に問い合わせてください]</strong> リンクがポイントするメール アドレスまたは URL を制御します  </p>
                <p>(<strong>[" 管理者に連絡" リンクをカスタマイズしますか?]</strong> が <strong>[はい]</strong> に設定されている場合にのみ表示されます)。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
有効なイントラネットまたはエクストラネットの URL またはメール アドレスにする必要があります。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
<strong>[管理者に問い合わせてください]</strong> リンクがポイントする場所を変更します。<br><br></li>
                  <li class="unordered">
メール アドレスを指定した場合、リンクはそのメール アドレスへの “mailto” リンクになります。<br><br></li>
                  <li class="unordered">
URL を指定した場合、リンクは、その URL を新しいタブで開く標準的な href になります。  <br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="write-back-passwords-to-on-premises-directory">
                  <p>オンプレミス ディレクトリへのパスワードの書き戻し</p>
                </div>
              </td>
              <td>
                <p>このディレクトリで Password Writeback が有効になっているかどうかを制御します。書き戻しがオンの場合は、オンプレミスの書き戻しサービスの状態を示します。</p>
                <p>この設定は、Azure AD Connect を再構成せずにサービスを一時的に無効にする場合に便利です。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
このコントロールは、Azure AD Connect の最新バージョンをダウンロードしてパスワード ライトバックをインストールし、<strong>[オプション機能]</strong> 選択画面で <strong>[パスワード ライトバック]</strong> オプションを有効にした場合にのみ表示されます。<br><br></li>
                  <li class="unordered">
パスワード ライトバックを有効にしたときに、サービスに構成の問題があると感じる場合は、このタブにアクセスして、<strong>[パスワードの書き戻しサービスの状態]</strong> ラベルを見ることで、問題があるかどうかを確認できます。<br><br></li>
                  <li class="unordered">
表示できる状態は、次のとおりです。<br><br><ul><li class="unordered"><strong>構成済み</strong> – すべてが期待どおりに動作しています<br><br></li><li class="unordered"><strong>未構成</strong> – 書き戻しがインストールされていますが、そのサービスにアクセスできません。443 への発信接続がブロックされていないことを確認し、問題が解決しない場合はサービスを再インストールします。<br><br></li></ul></li>
                </ul>
                <p>
                  <strong>登録ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
書き戻しがデプロイされ、構成されているときに、このスイッチを <strong>[いいえ]</strong> に設定すると、書き戻しは無効になり、フェデレーション ユーザーとパスワード ハッシュ同期ユーザーは、パスワード リセットの登録を行うことはできません。<br><br></li>
                  <li class="unordered">
スイッチが <strong>[はい]</strong> に設定されている場合、書き戻しは有効になり、フェデレーション ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできます。<br><br></li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
書き戻しがデプロイされ、構成されているときに、このスイッチを <strong>[いいえ]</strong> に設定すると、書き戻しは無効になり、フェデレーション ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできません。<br><br></li>
                  <li class="unordered">
スイッチが <strong>[はい]</strong> に設定されている場合、書き戻しは有効になり、フェデレーション ユーザーとパスワード ハッシュ同期ユーザーは、パスワードをリセットできます。<br><br></li>
                </ul>
              </td
            </tr>
             <tr>
              <td>
                <div id="allow-users-to-unlock-accounts-without-resetting-their-password">
                  <p>パスワードをリセットせずにオンプレミスの Active Directory アカウントのロックを解除することをユーザーに許可する</p>
                </div>
              </td>
              <td>
              <p>パスワード リセット ポータルにアクセスするユーザーに、パスワードをリセットせずにオンプレミスの Active Directory アカウントのロックを解除するオプションを表示するかどうかを指定します。 既定では、パスワード リセットを実行するときに、Azure AD によりアカウントのロックが常に解除されます。この設定により、次の 2 つの操作を分離することができます。</p>
              <p>[はい] に設定すると、ユーザーはパスワードをリセットし、アカウントのロックを解除するか、パスワードをリセットせずにロックを解除するかを選択できます。 </p>
              <p>[いいえ] に設定すると、ユーザーはパスワードのリセットとアカウントのロック解除を組み合わせた操作しか実行できなくなります。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>注:</strong>
                </p>
                <ul>
                  <li class="unordered">
この機能を使用するには、2015 年 8 月以降のリリースの Azure AD Connect (バージョン  1.0.8667.0 以上) をインストールする必要があります。<br><br><a href="http://www.microsoft.com/download/details.aspx?id=47594">ここをクリックすると、Azure AD Connect の最新バージョンをダウンロードできます。</a></li>
                  <li class="unordered">
                    <strong>注:</strong> この機能をテストするには、パスワード ライトバックを有効にし、(フェデレーション ユーザーやパスワード同期ユーザーなどの) オンプレミスのロックされたアカウントを使用する必要があります。  オンプレミスではなく、ロックされたアカウントを使用していないユーザーには、アカウントのロックを解除するオプションは表示されません。</li>
                </ul>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
このオプションを有効にすると、ロックされたオンプレミスのアカウントを使用するユーザーがパスワード リセット ポータルにアクセスしたときに、パスワードをリセットせずにアカウントのロックを解除するオプションが表示されます。<br><br>パスワード ライトバックを使用している場合、パスワードをリセットするときに、既にアカウントのロックが自動的に解除されているので、このオプションはこれらの操作を分離するだけのものになります。<br><br>特に、アカウントのロック解除要求のために、ヘルプデスクへの多数の問い合わせが発生することがわかっている場合、このオプションを有効にすると役立ちます。</li>
                </ul>
              </td>
            </tr>
          </tbody></table>

## <a name="password-management-notifications"></a>パスワード管理の通知
次の表は、各コントロールが、パスワード リセット通知を受信するユーザーと管理者のエクスペリエンスに対して、どのように影響するかを説明しています。  これらのオプションは、[Azure の管理ポータル](https://manage.windowsazure.com)の中で、ディレクトリの **[構成]** タブにある **[通知]** セクションで構成できます。

<table>
            <tbody><tr>
              <td>
                <p>
                  <strong>ポリシー コントロール</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>説明</strong>
                </p>
              </td>
              <td>
                <p>
                  <strong>影響</strong>
                </p>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-admins-when-other-admins-reset-their-own-passwords">
                  <p>管理者が自分のパスワードをリセットしたときに、他の管理者に通知します。</p>
                </div>
              </td>
              <td>
                <p>別の種類の管理者がパスワードをリセットしたときに、すべてのグローバル管理者にプライマリ メール アドレスを使用してメールで通知するかどうかを決定します。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] を設定した場合、通知は送信されません。<br><br></li>
                  <li class="unordered">
[はい] を設定した場合は、1 人の管理者が自分のパスワードをリセットしたときに、本人以外のすべてのグローバル管理者に通知されます。<br><br></li>
                  <li class="unordered">
この通知は、組織内の本人以外のすべてのグローバル管理者のプライマリ電子メール アドレスにメールで送信されます。<br><br></li>
                </ul>
                <p>
                  <strong>例:</strong>
                </p>
                <ul>
                  <li class="unordered">
このオプションが有効になっている場合、管理者 A が自分のパスワードをリセットしたときに、B、C、および D という 3 人の管理者がいると、管理者 B、C、および D は、管理者 A がパスワードをリセットしたことを示す電子メールを受信します。<br><br></li>
                </ul>
              </td>
            </tr>
            <tr>
              <td>
                <div id="notify-users-and-admins-when-their-own-password-has-been-reset">
                  <p>パスワードがリセットされたときにユーザーおよび管理者に通知する</p>
                </div>
              </td>
              <td>
                <p>パスワードをリセットしたエンド ユーザーまたは管理者が、パスワードがリセットされたことを知らせるメールを受信するかどうかを決定します。</p>
                <br>
                <p><b><u>次のいずれかのライセンスが必要 <a href="https://docs.microsoft.com/azure/active-directory/active-directory-passwords#pricing-and-availability">詳細</a></u></b></p>
                 <ul>
                   <li>O365 (有料の SKU すべて) [クラウド ユーザーのみ]</li>
                   <li>Azure AD Basic [クラウド ユーザーのみ]</li>
                   <li>Azure AD Premium P1 または P2 [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Mobility Suite [クラウドおよびオンプレミスのユーザー]</li>
                   <li>Enterprise Cloud Suite [クラウドおよびオンプレミスのユーザー]</li>
                 </ul>
              </td>
              <td>
                <p>
                  <strong>パスワード リセット ポータル:</strong>
                </p>
                <ul>
                  <li class="unordered">
[いいえ] を設定した場合、通知は送信されません。<br><br></li>
                  <li class="unordered">
[はい] を設定した場合、ユーザーまたは管理者は、自分のパスワードをリセットするたびに、パスワードがリセットされたことを示す通知を受信します。<br><br></li>
                  <li class="unordered">
この通知は、パスワードをリセットしたユーザーのユーザー プリンシパル名と連絡用 (または認証用) のメール アドレスにメールで送信されます。<br><br></li>
                </ul>
              </td>
            </tr>
          </tbody></table>


<br/>
<br/>
<br/>

## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。
* [**詳細情報**](active-directory-passwords-learn-more.md) - サービスの機能の技術的な詳細を掘り下げます。

[001]: ./media/active-directory-passwords-customize/001.jpg "Image_001.jpg"


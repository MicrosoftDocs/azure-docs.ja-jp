---
title: "Azure Active Directory B2C: ユーザー インターフェイス (UI) のカスタマイズ | Microsoft Docs"
description: "Azure Active Directory B2C のユーザー インターフェイス (UI) カスタマイズ機能についてのトピック"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 99f5a391-5328-471d-a15c-a2fafafe233d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2017
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 74b077f6f09d53c9232e5b209a5dd811364ee3f5
ms.openlocfilehash: c995e0de46c67c5c5d243739b2d36266267bdade


---
# <a name="azure-active-directory-b2c-customize-the-azure-ad-b2c-user-interface-ui"></a>Azure Active Directory B2C: Azure AD B2C ユーザー インターフェイス (UI) のカスタマイズ
コンシューマー向けのアプリケーションでは、ユーザー エクスペリエンスが最も重要です。 ユーザー エクスペリエンスは、単に悪くないだけのアプリケーションと優れたアプリケーションを分けるものであると同時に、コンシューマーがアクティブ ユーザーにとどまるか、それともコンシューマーから真のエンゲージメントを獲得できるかを分けるものでもあるからです。 Azure Active Directory (Azure AD) B2C では、コンシューマーのサインアップ ページ、サインイン ページ (*下記の注参照*)、プロファイルの編集ページ、およびパスワード リセット ページをピクセル単位で正確に制御してカスタマイズすることができます。

> [!NOTE]
> ローカル アカウントのサインイン ページ、それに付随するパスワード リセット ページ、および確認メールのカスタマイズには、現在、[会社のブランド化機能](../active-directory/active-directory-add-company-branding.md)のみ使用できます。この記事で説明するメカニズムは使用できません。
> 
> 

この記事では、次のことについて説明します。

* ページのユーザー インターフェイス (UI) カスタマイズ機能
* サンプル コンテンツを使用してページの UI カスタマイズ機能をテストするためのツール。
* ページの種類ごとの基本的な UI 要素。
* この機能を実行するときのベスト プラクティス。

## <a name="the-page-ui-customization-feature"></a>ページ UI カスタマイズ機能
ページ UI カスタマイズ機能を使用すると、コンシューマーのサインアップ ページ、サインイン ページ、パスワードのリセット ページ、およびプロファイルの編集ページの外観を、( [ポリシー](active-directory-b2c-reference-policies.md)を構成することによって) カスタマイズできます。 Azure AD B2C で提供されるページとアプリケーションの間を移動する際に、コンシューマーにシームレスなユーザー エクスペリエンスを実現します。

その他のサービスでは、UI オプションは制限されていたり、API 経由での利用に限られていたりするのに対し、Azure AD B2C では、ページの UI のカスタマイズに最新の (わかりやすい) 手法を使用しています。

しくみは次のとおりです。Azure AD B2C はコンシューマーのブラウザーでコードを実行すると共に、[クロス オリジン リソース共有 (CORS)](http://www.w3.org/TR/cors/) と呼ばれる最新の手法を使用して、ポリシーで指定した URL からコンテンツをロードします。 URL はページごとに別々に指定することができます。 Azure AD B2C の UI 要素と URL からロードしたコンテンツをマージし、コンシューマーにページを表示する作業は、コードが担当します。 必要なのは、以下の手順を実行することだけです。

1. 適切な形式の HTML5 コンテンツを作成し、`<body>` 内に `<div id="api"></div>` 要素 (空の要素である必要があります) を配置します。 この要素は、Azure AD B2C コンテンツを挿入する位置をマークします。
2. (CORS が許可されている) HTTPS エンドポイントでコンテンツをホストします。 CORS を構成するときに、GET と OPTIONS の両方の要求メソッドを有効にする必要があります。
3. Azure AD B2C によって挿入された UI 要素のスタイルを設定します。

## <a name="test-out-the-ui-customization-feature"></a>UI カスタマイズ機能を試す
サンプルの HTML と CSS のコンテンツを使用して UI カスタマイズ機能を試すことができるように、[単純なヘルパー ツール](active-directory-b2c-reference-ui-customization-helper-tool.md)が用意されています。そのツールを使用して、Azure Blob Storage 上にサンプル コンテンツをアップロードして構成します。

> [!NOTE]
> Web サーバー、CDN、AWS S3、ファイル共有システムなど、任意の場所で UI コンテンツをホストすることができます。(CORS が許可されている) 公開 HTTPS エンドポイントでコンテンツがホストされている限り、問題ありません。 ここでは、説明のためだけに、Azure Blob Storage を使用しています。
> 
> 

### <a name="the-most-basic-html-content-for-testing"></a>テスト用の最も基本的な HTML コンテンツ
この機能のテストに使用できる最も基本的な HTML コンテンツを以下に示します。 既に説明したのと同じヘルパー ツールを使用して、このコンテンツを Azure Blob Storage にアップロードして構成します。 その後、各ページの基本的な非定型ボタンとフォーム フィールドが表示されて機能していることを確認できます。

```HTML

<!DOCTYPE html>
<html>
    <head>
        <title>!Add your title here!</title>
    </head>
    <body>
        <div id="api"></div>    <!-- IMP: This element is intentionally empty; don't enter anything here -->
    </body>
</html>

```

## <a name="the-core-ui-elements-in-each-type-of-page"></a>ページの種類ごとの基本的な UI 要素
以降のセクションで、Azure AD B2C が、コンテンツ内に配置された `<div id="api"></div>` 要素にマージする HTML5 フラグメントの例を示します。 **これらのフラグメントは HTML5 のコンテンツに挿入しないでください。** Azure AD B2C サービスによって、実行時に挿入されます。 独自のスタイル シートをデザインするには、次の例を使用してください。

### <a name="azure-ad-b2c-content-inserted-into-the-identity-provider-selection-page"></a>"ID プロバイダーの選択ページ" に挿入される Azure AD B2C コンテンツ
このページには、サインアップ時やサインイン時にユーザーが選択できる ID プロバイダーの一覧が含まれます。 この一覧には、Facebook や Google+ などのソーシャル ID プロバイダーとローカル アカウント (電子メール アドレスやユーザー名を使用するもの) の両方が含まれます。

```HTML

<div id="api" data-name="IdpSelections">
    <div class="intro">
         <p>Sign up</p>
    </div>

    <div>
        <ul>
            <li>
                <button class="accountButton" id="FacebookExchange">Facebook</button>
            </li>
            <li>
                <button class="accountButton" id="GoogleExchange">Google+</button>
            </li>
            <li>
                <button class="accountButton" id="SignUpWithLogonEmailExchange">Email</button>
            </li>
        </ul>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-local-account-sign-up-page"></a>"ローカル アカウントのサインアップ ページ" に挿入される Azure AD B2C コンテンツ
このページには、ユーザーが電子メールアドレスやユーザー名を使ったローカル アカウントにサインアップするときに入力が必要になるサインアップ フォームが含まれます。 このフォームには、テキスト入力ボックス、パスワード入力ボックス、ラジオ ボタン、単一選択ドロップダウン ボックス、複数選択チェック ボックスなどのさまざまな入力コントロールを含めることができます。

```HTML

<div id="api" data-name="SelfAsserted">
    <div class="intro">
        <p>Create your account by providing the following details</p>
    </div>

    <div id="attributeVerification">
        <div class="errorText" id="passwordEntryMismatch" style="display: none;">The password entry fields do not match. Please enter the same password in both fields and try again.</div>
        <div class="errorText" id="requiredFieldMissing" style="display: none;">A required field is missing. Please fill out all required fields and try again.</div>
        <div class="errorText" id="fieldIncorrect" style="display: none;">One or more fields are filled out incorrectly. Please check your entries and try again.</div>
        <div class="errorText" id="claimVerificationServerError" style="display: none;"></div>
        <div class="attr" id="attributeList">
            <ul>
                <li>
                    <div class="attrEntry validate">
                        <div>
                            <div class="verificationInfoText" id="email_intro" style="display: inline;">Verification is necessary. Please click Send button.</div>
                            <div class="verificationInfoText" id="email_info" style="display:none">Verification code has been sent to your inbox. Please copy it to the input box below.</div>
                            <div class="verificationSuccessText" id="email_success" style="display:none">E-mail address verified. You can now continue.</div>
                            <div class="verificationErrorText" id="email_fail_retry" style="display:none">Incorrect code, try again.</div>
                            <div class="verificationErrorText" id="email_fail_no_retry" style="display:none">Exceeded number of retries you need to send new code.</div>
                            <div class="verificationErrorText" id="email_fail_server" style="display:none">Server error, please try again</div>
                            <div class="verificationErrorText" id="email_incorrect_format" style="display:none">Incorect format.</div>
                        </div>

                    <div class="helpText show">This information is required</div>
                        <label>Email</label>
                        <input id="email" class="textInput" type="text" placeholder="Email" required="" autofocus=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Email address that can be used to contact you.');" class="tiny">What is this?</a>

                    <div class="buttons verify" claim_id="email">
                        <div id="email_ver_wait" class="working" style="display: none;"></div>
                            <label id="email_ver_input_label" for="email_ver_input" style="display: none;">Verification code</label>
                            <input id="email_ver_input" type="text" placeholder="Verification code" style="display:none">
                            <button id="email_ver_but_send" class="sendButton" type="button" style="display: inline;">Send verification code</button>
                            <button id="email_ver_but_verify" class="verifyButton" type="button" style="display:none">Verify code</button>
                            <button id="email_ver_but_resend" class="sendButton" type="button" style="display:none">Send new code</button>
                            <button id="email_ver_but_edit" class="editButton" type="button" style="display:none">Change e-mail</button>
                            <button id="email_ver_but_default" class="defaultButton" type="button" style="display:none">Default</button>
                        </div>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .This information is required</div>
                        <label>Enter password</label>
                        <input id="password" class="textInput" type="password" placeholder="Enter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title="8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ &quot; ( ) ; ." required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Enter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"> This information is required</div>
                        <label>Reenter password</label>
                        <input id="reenterPassword" class="textInput" type="password" placeholder="Reenter password" pattern="^((?=.*[a-z])(?=.*[A-Z])(?=.*\d)|(?=.*[a-z])(?=.*[A-Z])(?=.*[^A-Za-z0-9])|(?=.*[a-z])(?=.*\d)(?=.*[^A-Za-z0-9])|(?=.*[A-Z])(?=.*\d)(?=.*[^A-Za-z0-9]))([A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~&quot;();!]|\.(?!@)){8,16}$" title=" " required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Reenter password');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Name</label>
                        <input id="displayName" class="textInput" type="text" placeholder="Name" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your display name.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Gender</label>
                        <input id="extension_Gender_F" name="extension_Gender" type="radio" value="F" autofocus="">
                        <label for="extension_Gender_F">Female</label>
                        <input id="extension_Gender_M" name="extension_Gender" type="radio" value="M">
                        <label for="extension_Gender_M">Male</label>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>Loyalty number</label>
                        <input id="extension_MemNum" class="textInput" type="text" placeholder="Loyalty number"><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Membership number');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText"></div>
                        <label>State</label>
                        <select class="dropdown_single" id="state">
                            <option style="display:none" disabled="disabled" value="placeholder" selected="">State</option>
                            <option value="WA">Washington</option>
                            <option value="NY">New York</option>
                            <option value="CA">California</option>
                        </select>
                        <a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('Your residential state or province.');" class="tiny">What is this?</a>
                    </div>
                </li>
                <li>
                    <div class="attrEntry">
                        <div class="helpText">This information is required</div>
                        <label>Zip code</label>
                        <input id="postalCode" class="textInput" type="text" placeholder="Zip code" required=""><a href="javascript:void(0)" onclick="selfAssertedClient.showHelp('The postal code of your address.');" class="tiny">What is this?</a>
                    </div>
                </li>
            </ul>
        </div>
        <div class="buttons"> <button id="continue" disabled="">Create</button> <button id="cancel">Cancel</button></div>
    </div>
    <div class="verifying-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="verifying_blurb"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-social-account-sign-up-page"></a>"ソーシャル アカウントのサインアップ ページ" に挿入される Azure AD B2C コンテンツ
このページには、Facebook や Google+ などのソーシャル ID プロバイダーから提供された既存のアカウントを使用してサインアップするときに、コンシューマーが入力する必要のあるサインアップ フォームが含まれます。 このページは、パスワード入力フィールドを除いて、ローカル アカウントのサインアップ ページ (前のセクションを参照) とほぼ同じです。

### <a name="azure-ad-b2c-content-inserted-into-the-unified-sign-up-or-sign-in-page"></a>"統合されたサインアップまたはサインイン ページ" に挿入される Azure AD B2C コンテンツ
このページは、コンシューマーのサインアップとサインインの両方を処理します。コンシューマーは、Facebook や Google+ などのソーシャル ID プロバイダーを使用することも、ローカル アカウントを使用することもできます。

```HTML

<div id="api" data-name="Unified">
        <div class="social" role="form">
               <div class="intro">
                       <h2>Sign in with your social account</h2>
               </div>
               <div class="options">
                       <div><button class="accountButton firstButton" id="MicrosoftAccountExchange" tabindex="1">msa</button></div>
                       <div><button class="accountButton" id="FacebookExchange" tabindex="1">fb</button></div>
               </div>
        </div>
        <div class="divider">
               <h2>OR</h2>
        </div>
        <div class="localAccount" role="form">
               <div class="intro">
                       <h2>Sign in with your existing account</h2>
               </div>
               <div class="error pageLevel" aria-hidden="true" style="display: none;">
                       <p role="alert"></p>
               </div>
               <div class="entry">
                       <div class="entry-item">
                               <label for="logonIdentifier">Email Address</label> 
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="email" id="logonIdentifier" name="LogonIdentifier" pattern="^[a-zA-Z0-9.!#$%&amp;’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$" placeholder="LogonIdentifier" value="" tabindex="1">
                       </div>
                       <div class="entry-item">
                               <div class="password-label"> <label for="password">Password</label><a id="forgotPassword" tabindex="2">Forgot your password?</a></div>
                               <div class="error itemLevel" aria-hidden="true" style="display: none;">
                                      <p role="alert"></p>
                               </div>
                               <input type="password" id="password" name="Password" placeholder="Password" tabindex="1">
                       </div>
                       <div class="working"></div>
                       <div class="buttons"> <button id="next" tabindex="1">Sign in</button> </div>
               </div>
               <div class="divider">
                       <h2>OR</h2>
               </div>
               <div class="create">
                       <p>Don't have an account?<a id="createAccount" tabindex="1">Sign up now</a> </p>
               </div>
        </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-multi-factor-authentication-page"></a>"多要素認証ページ" に挿入される Azure AD B2C コンテンツ
このページでは、ユーザーがサインアップやサインインをするときに、電話番号を (文字や音声を使用して) 確認することができます。

```HTML

<div id="api" data-name="Phonefactor">
    <div id="phonefactor_initial">
        <div class="intro">
            <p>Enter a number below that we can send a code via SMS or phone to authenticate you.</p>
        </div>
        <div class="errorText" id="errorMessage" style="display:none"></div>
        <div class="phoneEntry" id="phoneEntry">
            <div class="phoneNumber">
                <select id="countryCode" style="display:inline-block">
                    <option value="+93">Afghanistan (+93)</option>
                    <!-- Not all country codes listed -->
                    <option value="+44">United Kingdom (+44)</option>
                    <option value="+1" selected="">United States (+1)</option>
                    <!-- Not all country codes listed -->
                </select>
            </div>
            <div class="phoneNumber">
                <input type="text" id="localNumber" style="display:inline-block" placeholder="Phone number">
            </div>
        </div>
        <div id="codeVerification" style="display:none">
            <div>
                <p>Enter your verification code below, or <button id="retryCode" class="textButton">send a new code</button></p>
                <input type="text" id="verificationCode" placeholder="Verification code">
            </div>
        </div>
        <div class="buttons">
            <button id="verifyCode" class="sendInitialCodeButton">Send Code</button>
            <button id="verifyPhone" style="display:inline-block">Call Me</button>
            <button id="cancel" style="display:inline-block">Cancel</button>
        </div>
    </div>
    <div class="dialing-modal">
        <div class="preloader"> <img src="https://login.microsoftonline.com/static/img/win8loader.gif" alt="Please wait"></div>
        <div id="dialing_blurb"></div><div id="dialing_number"></div>
    </div>
</div>

```

### <a name="azure-ad-b2c-content-inserted-into-the-error-page"></a>"エラー ページ" に挿入される Azure AD B2C コンテンツ
```HTML

<div id="api" class="error-page-content" data-name="GlobalException">
    <h2>Sorry, but we're having trouble signing you in.</h2>
    <div class="error-page-help">We track these errors automatically, but if the problem persists feel free to contact us. In the meantime, please try again.</div>
    <div class="error-page-messagedetails">Your administrator hasn't provided any contact details.</div>
    <div class="error-page-messagedetails">
        <div class="error-page-correlationid">Correlation ID:1c4f0397-c6e4-4afe-bf74-42f488f2f15f</div>
        <div>Timestamp:2015-09-14 23:22:35Z</div>
        <div class="error-page-detail">AADB2C90065: A B2C client-side error 'Access is denied.' has occurred requesting the remote resource.</div>
    </div>
</div>

```

## <a name="things-to-remember-when-building-your-own-content"></a>独自のコンテンツを構築する際の注意点
ページの UI カスタマイズ機能を使用する場合には、以下のベスト プラクティスを確認してください。

* Azure AD B2C の既定のコンテンツをコピーしたり、変更したりしないでください。 HTML5 コンテンツは最初から構築し、既定のコンテンツはあくまで参考としての利用にとどめることをお勧めします。
* サインイン、サインアップ、およびプロファイルの編集ポリシーによって処理されるすべてのページ (エラー ページを除く) では、指定したスタイル シートが、これらのページの <head> フラグメントに追加される既定のスタイル シートより優先される必要があります。 サインアップまたはサインイン ポリシーとパスワードのリセット ポリシーによって処理されるすべてのページと、すべてのポリシーのエラー ページでは、すべてのスタイルを自分で指定する必要があります。
* セキュリティ上の理由から、コンテンツに JavaScript を含めることはできません。 必要なものの多くは既に用意されているはずです。 必要なものがない場合は、 [ユーザーの声](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) を使用して新しい機能を要望してください。
* サポートされているブラウザーのバージョン:
  * Internet Explorer 11、10、Edge
  * Internet Explorer 9、8 の制限付きサポート
  * Google Chrome 42.0 以降
  * Mozilla Firefox 38.0 以降



<!--HONumber=Feb17_HO2-->



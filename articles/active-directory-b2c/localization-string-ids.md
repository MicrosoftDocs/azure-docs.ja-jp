---
title: ローカライズ文字列 ID - Azure Active Directory B2C |Microsoft Docs
description: Azure Active Directory B2C のカスタム ポリシーに api.signuporsignin の ID を持つコンテンツ定義の ID を指定します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 41a72013f1538b0a857c76bc949a7109e1cd54b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510922"
---
# <a name="localization-string-ids"></a>ローカライズ文字列 ID

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**ローカリゼーション**要素を使用すると、ユーザー体験に関するポリシーで複数のロケールまたは言語をサポートできます。 この記事では、ポリシーで使用できるローカリゼーション ID の一覧を示します。 UI のローカリゼーションに精通するには、[ローカリゼーション](localization.md)を参照してください。

## <a name="sign-up-or-sign-in-page-elements"></a>サインアップ ページまたはサインイン ページの要素

次の ID は、`api.signuporsignin`の ID を持つコンテンツ定義に使用されます。

| ID | Default value |
| -- | ------------- |
| **local_intro_email** | 自分の既存のアカウントでサインイン |
| **logonIdentifier_email** | 電子メール アドレス |
| **requiredField_email** | メール アドレスを入力してください |
| **invalid_email** | 有効な電子メール アドレスを入力してください |
| **email_pattern** | ^[a-zA-Z0-9.!#$%&’' *+/=?^_\`{\|}~-]+@[a-zA-Z0-9-]+(?:\\.[a-zA-Z0-9-]+)* $ |
| **local_intro_username** | 自分のユーザー名でサインイン |
| **logonIdentifier_username** | ユーザー名 |
| **requiredField_username** | ユーザー名を入力してください |
| **password** | パスワード |
| **requiredField_password** | パスワードを入力してください |
| **invalid_password** | 入力したパスワードは、予期された形式ではありません。 |
| **forgotpassword_link** | パスワードを忘れた場合 |
| **createaccount_intro** | アカウントをお持ちでない場合は、 |
| **createaccount_link** | 今すぐサインアップ |
| **divider_title** | または |
| **cancel_message** | ユーザーがパスワードを忘れました |
| **button_signin** | サインイン |
| **social_intro** | 自分のソーシャル アカウントでサインイン |
  **remember_me** |サインインしたままにする|
| **unknown_error** | サインインで問題が発生しました。 後でもう一度やり直してください。 |

次の例は、サインアップまたはサインインページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![サインアップまたはサインイン ページの UX 要素](./media/localization-string-ids/localization-susi.png)

ID プロバイダーの ID は、ユーザー体験  **ClaimsExchange** 要素で構成されます。 ID プロバイダーのタイトルをローカライズするには、 **ElementType** は`ClaimsProvider`に設定され、**StringId** は`ClaimsExchange`の ID に設定されます。

```XML
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

次の例では、Facebook の ID プロバイダーをアラビア語にローカライズしています。

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>サインアップまたはサインインのエラー メッセージ

| ID | Default value |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | パスワードが正しくありません。 |
| **UserMessageIfClaimsPrincipalDoesNotExist** | ご利用のアカウントが見つからないようです。 |
| **UserMessageIfOldPasswordUsed** | 古いパスワードが使用された可能性があります。 |  
| **DefaultMessage** | ユーザー名またはパスワードが無効です。 |  
| **UserMessageIfUserAccountDisabled** | ご使用のアカウントはロックされています。 サポート担当者に連絡してロックを解除してから、もう一度お試しください。 |  
| **UserMessageIfUserAccountLocked** | ご使用のアカウントは、不正使用を防ぐために一時的にロックされています。 後でもう一度やり直してください。 |  
| **AADRequestsThrottled** | この時点でリクエストが多すぎます。 しばらく待って、もう一度やり直してください。 |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>サインアップとセルフ アサート ページのユーザー インターフェイス要素

以下は、`api.localaccountsignup`の ID を持つコンテンツ定義、または`api.selfasserted.profileupdate`や`api.localaccountpasswordreset`など、`api.selfasserted`で始まるコンテンツ定義の ID です。

| ID | Default value |
| -- | ------------- |
| **ver_sent** | 次の場所に確認コードが送信されました: |
| **ver_but_default** | 既定値 |
| **cancel_message** | セルフ アサート情報の入力がユーザーによって取り消されました |
| **preloader_alt** | お待ちください |
| **ver_but_send** | 確認コードを送信する |
| **alert_yes** | はい |
| **error_fieldIncorrect** | 1 つ以上のフィールドが正しく入力されていません。 入力を確認してから、やり直してください。 |
| **year** | 年 |
| **verifying_blurb** | 情報の処理が終わるまでお待ちください。 |
| **button_cancel** | Cancel |
| **ver_fail_no_retry** | 多くの正しくない試行を行いました。 後でもう一度やり直してください。 |
| **month** | 月 |
| **ver_success_msg** | 電子メールアドレスが確認されました。 続行することができるようになりました。 |
| **months** | 1 月、2 月、3 月、4 月、5 月、6 月、7 月、8 月、9 月、10 月、11 月、12 月 |
| **ver_fail_server** | ご使用のメールアドレスの確認に問題があります。 有効なメール アドレスを入力して、もう一度やり直してください。 |
| **error_requiredFieldMissing** | 必要なフィールドがありません。 すべての必須フィールドに入力してから、やり直してください。 |
| **initial_intro** | 以下の詳細を指定してください。 |
| **ver_but_resend** | 新しいコードを送信します |
| **button_continue** | Create |
| **error_passwordEntryMismatch** | パスワードの入力フィールドが一致しません。 両方のフィールドに同じパスワードを入力して、もう一度やり直してください。 |
| **ver_incorrect_format** | 形式が正しくありません |
| **ver_but_edit** | 電子メールを変更します |
| **ver_but_verify** | コードの確認 |
| **alert_no** | No |
| **ver_info_msg** | 確認コードが受信トレイに送信されました。 次の入力ボックスにコピーしてください。 |
| **day** | 日 |
| **ver_fail_throttled** | このメール アドレスを確認するリクエストが多すぎます。 しばらく待って、再試行してください。 |
| **helplink_text** | これは何ですか。 |
| **ver_fail_retry** | そのコードが正しくありません。 もう一度実行してください。 |
| **alert_title** | 詳細情報の入力をキャンセルします。 |
| **required_field** | この情報は必須です。 |
| **alert_message** | 詳細情報の入力をキャンセルしますか。 |
| **ver_intro_msg** | 確認が必要です。 [送信] ボタンをクリックしてください。 |
| **ver_input** | 確認コード |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>サインアップとセルフ アサート ページ エラー メッセージ

| ID | Default value |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | 指定した ID を持つユーザーは既に存在します。 別の ID を選択してください。 |
| **UserMessageIfClaimNotVerified** | 要求が確認されていません: {0} |
| **UserMessageIfIncorrectPattern** | {0} のパターンが正しくありません: |
| **UserMessageIfMissingRequiredElement** | 必要な要素が見つかりません: {0} |
| **UserMessageIfValidationError** | {0} による検証中にエラーが発生しました |
| **UserMessageIfInvalidInput** | {0}に無効な入力があります。 |
| **ServiceThrottled** | この時点でリクエストが多すぎます。 しばらく待って、もう一度やり直してください。 |

次の例では、サインアップページにユーザーインターフェイス要素の一部を使用しています。

![サインアップページの UX 要素](./media/localization-string-ids/localization-sign-up.png)

次の例は、ユーザーが [認証コードを送信する] ボタンをクリックした後に、サインアップ ページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>電話ファクター認証ページのユーザー インタフェース要素

以下は、`api.phonefactor`の ID を持つコンテンツ定義用の ID です。 

| ID | Default value |
| -- | ------------- |
| **button_verify** | 電話する |
| **country_code_label** | 国番号 |
| **cancel_message** | ユーザーが多要素認証を取り消しました |
| **text_button_send_second_code** | 新しいコードを送信する |
| **code_pattern** | \\d{6} |
| **intro_mixed** | レコードに次の番号があります。 ユーザーを認証する電話または SMS を使用してコードを送信できます。 |
| **intro_mixed_p** | レコードに、次の番号があります。 ユーザーに電話できる番号を選択するか、認証するために SMS 経由でコードを送信します。 |
| **button_verify_code** | コードの確認 |
| **requiredField_code** | 受信した確認コードを入力してください |
| **invalid_code** | 受信した 6 桁のコードを入力してください |
| **button_cancel** | Cancel |
| **local_number_input_placeholder_text** | 電話番号 |
| **button_retry** | Retry |
| **alternative_text** | 自分の電話を持っていません |
| **intro_phone_p** | レコードに、次の番号があります。 電話で認証できる番号を選択してください。 |
| **intro_phone** | レコードに次の番号があります。 認証するためにお電話します。 |
| **enter_code_text_intro** | 以下に確認コードを入力するか、  |
| **intro_entry_phone** | 認証用に電話を受けることを希望する番号を以下に入力してください。 |
| **intro_entry_sms** | 認証用に SMS でコードを受け取ることを希望する番号を以下に入力してください。 |
| **button_send_code** | コードの送信 |
| **invalid_number** | 有効な電話番号を入力してください |
| **intro_sms** | レコードに次の番号があります。 認証するために SMS を介してコードを送信します。 |
| **intro_entry_mixed** | 認証用に SMS でコードを受け取る、または電話を受けることを希望する番号を以下に入力してください。 |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |レコードに、次の番号があります。 認証用に SMS でコードを受け取ることを希望する番号を以下に入力してください。 |
| **requiredField_countryCode** | 国コードを選択してください |
| **requiredField_number** | 電話番号を入力してください |
| **country_code_input_placeholder_text** |国または地域 |
| **number_label** | 電話番号 |
| **error_tryagain** | 指定した電話番号がビジー状態であるか、利用不可です。 番号を確認して、もう一度やり直してください。 |
| **error_incorrect_code** | 入力した確認コードは、弊社の記録と一致しません。 もう一度やり直すか、新しいコードを要求してください。 |
| **countryList** | {\"デフォルト\":\"国/地域\"、\"AF\":\"アフガニスタン\"、\"AX\":\"オーランド諸島\"、\"AL\":\"アルバニア\"、\"DZ\":\"アルジェリア\"、\"AS\":\"アメリカンサモア\"、\"AD\":\"アンドラ\"、\"AO\":\"アンゴラ\"、\"AI\":\"アンギラ\"、\"AQ\":\"南極大陸\"、\"AG\":\"アンティグアバーブーダ\"、\"AR\":\"アルゼンチン\"、\"AM\":\"アルメニア\"、\"AW\":\"アルバ\"、\"AU\":\"オーストラリア\"、\"AT\":\"オーストリア\"、\"AZ\":\"アゼルバイジャン\"、\"BS\":\"バハマ\"、\"BH\":\"バーレーン\"、\"BD\":\"バングラデシュ\"、\"BB\":\"バルバドス\"、\" \":\"ベラルーシ\"、\"BE\":\"ベルギー\"、\"BZ\":\"ベリーズ\"、\"BJ\":\"ベニン\"、\"BM\":\"バミューダ\"、\"BT\":\"ブータン\"、\"BO\":\"ボリビア\"、\"BQ\":\"ボネール島\"、\"BA\":\"ボスニア・ヘルツェゴビナ\"、\"BW\":\"ボツワナ\"、\"BV\":\"ブーベ島\"、\"BR\":\"ブラジル\"、\"IO\":\"英領インド洋領\"、\"VG\":\"英領ヴァージン諸島\"、\"BN\":\"ブルネイ\"、\"BG\":\"ブルガリア\"、\"BF\":\"ブルキナファソ\"、\"BI\":\"ブルンジ\"、\"CV\":\"カボベルデ\"、\"KH\":\"カンボジア\"、\"CM\":\"カメルーン\"、\"CA\":\"カナダ\"、\"KY\":\"ケイマン諸島\"、\"CF\":\"中央アフリカ共和国\"、\"TD\":\"チャド\"、\"CL\":\"チリ\"、\"CN\":\"中国\"、\"CX\":\"クリスマス島\"、\"CC\":\"ココス (キリング) 諸島\"、\"CO\":\"コロンビア\"、\"KM\":\"コモロ\"、\"CG\":\"コンゴ共和国\"、\"CD\":\"コンゴ民主共和国\"、\"CK\":\"クック諸島\"、\"CR\":\"コスタリカ\"、\"CI\":\"コートジボワール\"、\"HR\":\"クロアチア\"、\"CU\":\"キューバ\"、\"CW\":\"キュラソー\"、\"CY\":\"キプロス\"、\"CZ\":\"チェコ共和国\"、\"DK\":\"デンマーク\"、\"DJ\":\"ジブチ\"、\"DM\":\"ドミニカ\"、\"DO\":\"ドミニカ共和国\"、\"EC\":\"エクアドル\"、\"EG\":\"エジプト\"、\"SV\":\"エルサルバドル\"、\"GQ\":\"赤道ギニア\"、\"ER\":\"エリトリア\"、\"EE\":\"エストニア\"、\"ET\":\"エチオピア\"、\"FK\":\"フォークランド諸島\"、\"FO\":\"フェロー諸島\"、\"FJ\":\"フィジー\"、\"FI\":\"フィンランド\"、\"FR\":\"フランス\"、\"GF\":\"フランスガイアナン\"、\"PF\":\"フランス領ポリネシア\"、\"TF\":\"フランス南部準州\"、\"GA\":\"ガボン\"、\"GM\":\"ガンビア\"、\"GE\":\"ジョージア\"、\"DE\":\"ドイツ\"、\"GH\":\"ガーナ\"、\"GI\":\"ジブラルタル\"、\"GR\":\"ギリシャ\"、\"GL\":\"グリーンランド\"、\"GD\":\"グレナダ\"、\"GP\":\"グアドループ\"、\"GU\":\"グアム\"、\"GT\":\"グアテマラ\"、\"GG\":\"ガーンジー\"、\"GN\":\"ギニア\"、\"GW\":\"ギニア・ビサウ\"、\"GY\":\"ガイアナ\"、\"HT\":\"ハイチ\"、\"HM\":\"ヒアード島とマクドナルド諸島\"、\"HN\":\"ホンジュラス\"、\"香港\":\"香港特別行政区\"、\"HU\":\"ハンガリー\"、\"IS\":\"アイスランド\"、\"IN\":\"インド\"、\"ID\":\"インドネシア\"、\"IR\":\"イラン\"、\"IQ\":\"イラク\"、\"IE\":\"アイルランド\"、\"IM\":\"マン島\"、\"イリノイ\":\"イスラエル\"、\"IT\":\"イタリア\"、\"JM\":\"ジャマイカ\"、\"JP\":\"日本\"、\"JE\":\"ジャージ\"、\"JO\":\"ヨルダン\"、\"KZ\":\"カザフスタン\"、\"KE\":\"ケニア\"、\"KI\":\"キリバス\"、\"KR\":\"韓国\"、\"KW\":\"クウェート\"、\"KG\":\"キルギスタン\"、\"LA\":\"ラオス\"、\"LV\":\"ラトビア\"、\"LB\":\"レバノン\"、\"LS\":\"レソト\"、\"LR\":\"リベリア\"、\"LY\":\"リビア\"、\"LI\":\"リヒテンシュタイン\"、\"LT\":\"リトアニア\"、\"LU\":\"ルクセンブルグ\"、\"MO\":\"マカオ SAR\"、\"MK\":\"マケドニア旧ユーゴスラビア共和国\"、\"MG\":\"マダガスカル\"、\"MW\":\"マラウイ\"、\"MY\":\"マレーシア\"、\"MV\":\"モルディブ\"、\"ML\":\"マリ\"、\"MT\":\"マルタ\"、\"MH\":\"マーシャル諸島\"、\"MQ\":\"マルティニーク\"、\"MR\":\"モーリタニア\"、\"MU\":\"モーリシャス\"、\"YT\":\"マヨット\"、\"MX\":\"メキシコ\"、\"FM\":\"ミクロネシア\"、\"MD\":\"モルドバ\"、\"MC\":\"モナコ\"、\"MN\":\"モンゴル\"、\"ME\":\"モンテネグロ\"、\"MS\":\"モントセラト\"、\"MA\":\"モロッコ\"、\"MZ\":\"モザンビーク\"、\"MM\":\"ミャンマー\"、\"NA\":\"ナミビア\"、\"NR\":\"ナウル\"、\"NP\":\"ネパール\"、\"NL\":\"オランダ\"、\"NC\":\"ニューカレドニア\"、\"NZ\":\"ニュージーランド\"、\"NI\":\"ニカラグア\"、\"NE\":\"ニジェール\"、\"NG\":\"ナイジェリア\"、\"NU\":\"ニウエ\"、\"NF\":\"ノーフォーク島\"、\"KP\":\"北朝鮮\"、\"MP\":\"北マリアナ諸島\"、\"いいえ\":\"ノルウェー\"、\"OM\":\"オマーン\"、\"PK\":\"パキスタン\"、\"PW\":\"パラオ\"、\"PS\":\"パレスチナ自治政府\"、\"PA\":\"パナマ\"、\"PG\":\"パプアニューギニア\"、\"PY\":\"パラグアイ\"、\"PE\":\"ペルー\"、\"PH\":\"フィリピン\"、\"PN\":\"ピトケアン諸島\"、\"PL\":\"ポーランド\"、\"PT\":\"ポルトガル\"、\"PR\":\"プエルトリコ\"、\"QA\":\"カタール\"、\"RE\":\"Réunion\"、\"RO\":\"ルーマニア\"、\"RU\":\"ロシア\"、\"RW\":\"ルワンダ\"、\"BL\":\"聖バテルレミー\"、\"KN\":\"セントクリストファー・ネイビス\"、\"LC\":\"セントルシア\"、\"MF\":\"聖マーティン\"、\"PM\":\"サンピエールとミケロン\"、\"VC\":\"セントビンセントとグレナディーンズ\"、\"WS\":\"サモア\"、\"SM\":\"サンマリノ\"、\"ST\":\"サントメとプリンシペ\"、\"SA\":\"サウジアラビア\"、\"SN\":\"セネガル\"、\"RS\":\"セルビア\"、\"SC\":\"セイシェル\"、\"SL\":\"シエラレオネ\"、\"SG\":\"シンガポール\"、\"SX\":\"セント・マーチン島\"、\"SK\":\"スロバキア\"、\"SI\":\"スロベニア\"、\"SB\":\"ソロモン諸島\"、\"SO\":\"ソマリア\"、\"ZA\":\"南アフリカ\"、\"GS\":\"サウスジョージアとサウスサンドイッチ諸島\"、\"SS\":\"南スーダン\"、\"ES\":\"スペイン\"、\"LK\":\"スリランカ\"、\"SH\":\"聖ヘレナ、昇天、トリスタンダクンハ\"、\"SD\":\"スーダン\"、\"SR\":\"スリナム\"、\"SJ\":\"スバールバル\"、\"SZ\":\"スワジランド\"、\"SE\":\"スウェーデン\"、\"CH\":\"スイス\"、\"SY\":\"シリア\"、\"TW\":\"台湾\"、\"TJ\":\"タジキスタン\"、\"TZ\":\"タンザニア\"、\"TH\":\"タイ\"、\"TL\":\"東ティモール\"、\"TG\":\"トーゴ\"、\"TK\":\"トケラウ\"、\"TO\":\"トンガ\"、\"TT\":\"トリニダード・トバゴ\"、\"TN\":\"チュニジア\"、\"TR\":\"トルコ\"、\"TM\":\"トルクメニスタン\"​​、\"TC\":\"タークスカイコス諸島\"、\"テレビ\":\"ツバル\"、\"UM\":\"米国領有小離島\"、\"VI\":\"米国米領バージン諸島\"、\"UG\":\"ウガンダ\"、\"UA\":\"ウクライナ\"、\"AE\":\"アラブ首長国連邦\"、\"GB\":\"英国\"、\"US\":\"米国\"、\"UY\":\"ウルグアイ\"、\"UZ\":\"ウズベキスタン\"、\"VU\":\"バヌアツ\"、\"VA\":\"Vatican City\"、\"VE\":\"ベネズエラ\"、\"VN\":\"ベトナム\"、\"WF\":\"ワリス ・ フテュナ諸島\"、\"YE\":\"イエメン\"、\"ZM\":\"ザンビア\"、\"ZW\":\"ジンバブエ\"} |
| **error_448** | 指定された電話番号につながりません。 |
| **error_449** | ユーザーが再試行回数を超えました。 |
| **verification_code_input_placeholder_text** | 確認コード |

次の例は、MFA 登録ページの一部のユーザー インターフェイス要素の使用方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-mfa1.png)

次の例は、MFA 検証ページでユーザー インターフェイス要素の一部を使用する方法を示しています。

![電子メール検証のサインアップ ページ UX 要素](./media/localization-string-ids/localization-mfa2.png)








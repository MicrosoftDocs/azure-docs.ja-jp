---
title: Android MSAL 構成ファイル | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory でのアプリケーションの構成を表す Android Microsoft Authentication Library (MSAL) 構成ファイルの概要。
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92275598"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Android Microsoft Authentication Library 構成ファイル

Android Microsoft Authentication Library (MSAL) には[既定の構成の JSON ファイル](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json)が付属しています。このファイルをカスタマイズして、既定の機関や使用する機関など、使用するパブリック クライアント アプリの動作を定義することができます。

この記事では、構成ファイルでのさまざまな設定についてわかりやすく示すと共に、MSAL ベースのアプリで使用する構成ファイルの指定方法について説明します。

## <a name="configuration-settings"></a>構成設定

### <a name="general-settings"></a>全般設定

| プロパティ | データ型 | 必須 | Notes |
|-----------|------------|-------------|-------|
| `client_id` | String | はい | [アプリケーション登録ページ](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)からのアプリのクライアント ID |
| `redirect_uri`   | String | はい | [アプリケーション登録ページ](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)からのアプリのリダイレクト URI |
| `broker_redirect_uri_registered` | ブール型 | いいえ | 指定できる値: `true`、`false` |
| `authorities` | リスト\<Authority> | いいえ | アプリに必要な機関の一覧 |
| `authorization_user_agent` | AuthorizationAgent (列挙型) | いいえ | 指定できる値: `DEFAULT`、`BROWSER`、`WEBVIEW` |
| `http` | HttpConfiguration | いいえ | `HttpUrlConnection` `connect_timeout` と `read_timeout` を構成します |
| `logging` | LoggingConfiguration | いいえ | ログ記録の詳細レベルを指定します。 オプションの構成には次のものが含まれます: `pii_enabled` (ブール値を取ります)、`log_level` (`ERROR`、`WARNING`、`INFO`、または `VERBOSE` を取ります)。 |

### <a name="client_id"></a>client_id

アプリケーションを登録する際に作成されたクライアント ID またはアプリ ID。

### <a name="redirect_uri"></a>redirect_uri

アプリケーションを登録する際に登録したリダイレクト URI。 リダイレクト URI がブローカー アプリに対するものである場合は、「[パブリック クライアント アプリ用のリダイレクト URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)」を参照して、ご利用のブローカー アプリに対して正しいリダイレクト URI 形式を確実に使用してください。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

ブローカー認証を使用する場合は、`broker_redirect_uri_registered` プロパティを `true` に設定する必要があります。 ブローカー認証のシナリオでは、「[パブリック クライアント アプリ用のリダイレクト URI](msal-client-application-configuration.md#redirect-uri-for-public-client-apps)」で説明されているように、アプリケーションがブローカーと通信するための正しい形式ではない場合、アプリケーションによって、リダイレクト URI が検証され、起動時に例外がスローされます。

### <a name="authorities"></a>authorities

お客様によって認識され信頼されている機関の一覧。 ここに一覧されている機関に加えて、MSAL では Microsoft に問い合わせて、Microsoft が把握しているクラウドと機関の一覧が取得されます。 この機関の一覧では、機関の種類と追加の省略可能なパラメーターを指定します。たとえば、`"audience"` などのパラメーターが該当し、これは、ご利用のアプリの登録に基づいて、その対象ユーザーと一致させる必要があります。 次に、機関の一覧の例を示します。

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>AAD 機関および対象ユーザーを Microsoft ID プラットフォーム エンドポイントにマップする

| Type | 対象ユーザー | テナント ID | Authority_Url | 結果としてのエンドポイント | Notes |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` は、アカウントがあるテナントの別名です。 たとえば、特定の Azure Active Directory テナントや Microsoft アカウント システムなどです。 |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | contoso.com に存在するアカウントのみがトークンを取得できます。 検証されたドメインまたはテナント GUID は、テナント ID として使用できる可能性があります。 |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | このエンドポイントで使用できるのは、Azure Active Directory アカウントだけです。 Microsoft アカウントは、組織のメンバーとすることができます。 組織内のリソースに対して Microsoft アカウントを使用してトークンを取得するには、トークンの取得元とする組織テナントを指定します。 |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | このエンドポイントを使用できるのは Microsoft アカウントだけです。 |
| B2C | | | 結果としてのエンドポイントを参照してください | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | トークンを取得できるには、contoso.onmicrosoft.com テナントに存在するアカウントのみです。 この例では、B2C ポリシーは機関 URL パスの一部です。 |

> [!NOTE]
> MSAL では、機関の検証を有効および無効にすることはできません。
> 権限は、構成を介して指定される開発者としてお客様に認識されているか、またはメタデータを介して Microsoft に認識されています。
> 不明な機関へのトークンの要求を MSAL が受け取ると、`UnknownAuthority` 型の `MsalClientException` が結果して生じます。
> ブローカー認証は、Azure AD B2C では機能しません。

#### <a name="authority-properties"></a>機関のプロパティ

| プロパティ | データ型  | 必須 | Notes |
|-----------|-------------|-----------|--------|
| `type` | String | はい | ご利用のアプリがターゲットとする対象ユーザーまたはアカウントの種類をミラー化します。 指定できる値: `AAD`、`B2C` |
| `audience` | Object | いいえ | Type=`AAD` の場合にのみ適用されます。 ご利用のアプリがターゲットとする ID を指定します。 ご利用のアプリの登録からの値を使用します |
| `authority_url` | String | はい | Type=`B2C` の場合にのみ必要です。 ご利用のアプリで使用する必要がある機関の URL またはポリシーを指定します  |
| `default` | boolean | はい | 1 つまたは複数の機関が指定されている場合は、1 つの `"default":true` が必要です。 |

#### <a name="audience-properties"></a>対象ユーザーのプロパティ

| プロパティ | データ型  | 必須 | Notes |
|-----------|-------------|------------|-------|
| `type` | String | はい | ご利用のアプリでターゲットとする対象ユーザーを指定します。 指定できる値: `AzureADandPersonalMicrosoftAccount`、`PersonalMicrosoftAccount`、`AzureADMultipleOrgs`、`AzureADMyOrg` |
| `tenant_id` | String | はい | `"type":"AzureADMyOrg"` の場合にのみ必要です。 他の `type` 値の場合は省略可能です。 これには、`contoso.com` などのテナント ドメインや、`72f988bf-86f1-41af-91ab-2d7cd011db46` などのテナント ID にすることができます |

### <a name="authorization_user_agent"></a>authorization_user_agent

アカウントにサインインするとき、またはリソースへのアクセスを承認するときに、埋め込み Web ビューを使用するか、またはデバイス上の既定のブラウザーを使用するかを示します。

指定できる値
- `DEFAULT`: システム ブラウザーを優先します。 デバイス上でブラウザーを使用できない場合は、埋め込み Web ビューを使用します。
- `WEBVIEW`: 埋め込み Web ビューを使用します。
- `BROWSER`: デバイス上の既定のブラウザーを使用します。

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

各国のクラウドを複数サポートするクライアントの場合は、`true` を指定します。 承認およびトークンの使用の際に、Microsoft ID プラットフォームによって適切な各国のクラウドに自動的にリダイレクトされます。 `AuthenticationResult` に関連付けられている機関を調べることで、サインインしたアカウントの各国のクラウドを特定できます。 `AuthenticationResult` では、トークンを要求するリソースの各国のクラウドに固有のエンドポイント アドレスが指定されない注意してください。

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Microsoft ID ブローカーと互換性のあるブローカー内リダイレクト URI を使用しているかどうかを示すブール値です。 ご利用のアプリ内でブローカーを使用しない場合は `false` に設定します。

対象ユーザーが `"MicrosoftPersonalAccount"` に設定された AAD 機関を使用している場合、ブローカーは使用されません。

### <a name="http"></a>http

HTTP タイムアウトのグローバル設定を構成します。次に例を示します。

| プロパティ | データ型 | 必須 | Notes |
| ---------|-----------|------------|--------|
| `connect_timeout` | INT | いいえ | ミリ秒単位 |
| `read_timeout` | INT | いいえ | ミリ秒単位 |

### <a name="logging"></a>logging

次のグローバル設定は、ログ記録用のものです。

| プロパティ | データ型  | 必須 | Notes |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | いいえ | 個人データを出力するかどうか |
| `log_level`   | string | いいえ | どのログ メッセージを出力するか。 サポートされているログレベルには、`ERROR`、`WARNING`、`INFO`、および `VERBOSE` があります。 |
| `logcat_enabled` | boolean | いいえ | ログ記録のインターフェイスに加えて、ログ cat にも出力するかどうか |

### <a name="account_mode"></a>account_mode

ご利用のアプリ内で一度に使用できるアカウントの数を指定します。 指定できる値は、

- `MULTIPLE` (既定値)
- `SINGLE`

この設定に一致しないアカウント モードを使用して `PublicClientApplication` を構築すると、例外が発生します。

単一アカウントと複数アカウントの相違点の詳細については、[単一および複数アカウントのアプリ](single-multi-account.md)に関するページを参照してください。

### <a name="browser_safelist"></a>browser_safelist

MSAL と互換性のあるブラウザーの許可リストです。 これらのブラウザーでは、カスタム インテントへのリダイレクトが正しく処理されます。 この一覧に追加することができます。 次に示す既定の構成には、既定値が指定されています。
``
## <a name="the-default-msal-configuration-file"></a>既定の MSAL 構成ファイル

MSAL に付属する既定の MSAL 構成を次に示します。 [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) で最新バージョンを確認できます。

この構成は、ご自分で指定した値によって補完されます。 ご自分で指定した値が既定値よりも優先されます。

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>基本的な構成の例

次の例では、クライアント ID、リダイレクト URI、ブローカー リダイレクトの登録の有無、および機関の一覧を指定する基本的な構成を示しています。

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>構成ファイルを使用する方法

1. 構成ファイルを作成します。 `res/raw/auth_config.json` でご自分のカスタム構成ファイルを作成することをお勧めします。 ただし、それは希望する場所に配置することができます。
2. `PublicClientApplication` を構築するときに、ご利用の構成を探す場所を MSAL に指示します。 次に例を示します。

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```

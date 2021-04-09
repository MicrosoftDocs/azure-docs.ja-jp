---
title: 要求で Azure AD スキーマ拡張属性を使用する
titleSuffix: Microsoft identity platform
description: トークン要求でユーザー データをアプリケーションに送信するために、ディレクトリ スキーマ拡張属性を使用する方法について説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 0127c8d796126d1e99b1fa38a9506df477c7eb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98755730"
---
# <a name="using-directory-schema-extension-attributes-in-claims"></a>要求でディレクトリ スキーマ拡張属性を使用する

ディレクトリ スキーマ拡張属性を使用すると、Azure Active Directory で、ユーザー オブジェクトやその他のディレクトリ オブジェクト (グループ、テナントの詳細、サービス プリンシパルなど) に追加のデータを格納することができます。  アプリケーションに対する要求の出力に使用できるのは、ユーザー オブジェクトの拡張属性だけです。 この記事では、トークン要求でユーザー データをアプリケーションに送信するために、ディレクトリ スキーマ拡張属性を使用する方法について説明します。

> [!NOTE]
> Microsoft Graph には、Graph オブジェクトをカスタマイズするための 2 つの拡張メカニズムが他に用意されています。 これらは Microsoft Graph オープン拡張と Microsoft Graph スキーマ拡張と呼ばれています。 詳細については、[Microsoft Graph のドキュメント](/graph/extensibility-overview)を参照してください。 これらの機能を使用して Microsoft Graph オブジェクトに格納されたデータは、トークン内の要求のソースとしては使用できません。

ディレクトリ スキーマ拡張属性は、常にテナント内のアプリケーションに関連付けられており、アプリケーションの名前が付いた *applicationId* で参照されます。

ディレクトリ スキーマ拡張属性の識別子は、*Extension_xxxxxxxxx_AttributeName* の形式です。  ここで *xxxxxxxxx* は、拡張機能が定義されたアプリケーションの *applicationId* です。

## <a name="registering-and-using-directory-schema-extensions"></a>ディレクトリ スキーマ拡張の登録と使用
ディレクトリ スキーマ拡張属性は、次の 2 つの方法のいずれかで登録および設定できます。

- AD Connect を構成して、オンプレミス AD からそれらを作成し、データを同期する。 詳細については、[Azure AD Connect 同期のディレクトリ拡張](../hybrid/how-to-connect-sync-feature-directory-extensions.md)に関する記事を参照してください。
- Microsoft Graph を使用して、[スキーマ拡張](/graph/extensibility-overview)を登録し、その値を設定して、そこから読み取る。 [PowerShell コマンドレット](/powershell/azure/active-directory/using-extension-attributes-sample)も使用できます。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-with-ad-connect"></a>AD Connect を使用して作成されたディレクトリ スキーマ拡張属性からのデータを使用して要求を出力する
AD Connect を使用して作成および同期されるディレクトリ スキーマ拡張属性は、AD Connect によって使用されるアプリケーション ID と常に関連付けられます。 これらは、**Enterprise Applications** の Gallery または Gallery 以外のアプリケーション構成エクスペリエンスを使用して登録された SAML アプリケーションにある Portal UI の **Enterprise Applications** 構成でそれらを要求として構成するか、アプリケーションの登録エクスペリエンスを使用して登録されたアプリケーションの要求のマッピング ポリシー経由で、要求のソースとして使用することができます。  AD Connect を使用して作成されたディレクトリ拡張属性がディレクトリ内にあると、SAML SSO 要求の構成 UI に表示されます。

### <a name="emitting-claims-with-data-from-directory-schema-extension-attributes-created-for-an-application-using-graph-or-powershell"></a>Graph または PowerShell を使用してアプリケーション用に作成されたディレクトリ スキーマ拡張属性からのデータを使用して要求を出力する
Microsoft Graph または PowerShell を使用してアプリケーションにディレクトリ スキーマ拡張属性が登録された場合 (たとえば、アプリケーションの初期セットアップまたはプロビジョニング手順を使用)、ユーザーがサインインしたときに、ユーザー オブジェクトからその属性のデータを要求で受信するように Azure Active Directory で同じアプリケーションを構成できます。  このアプリケーションは、[オプションの要求](active-directory-optional-claims.md#configuring-directory-extension-optional-claims)を使用して同じアプリケーションに登録されているディレクトリ スキーマ拡張でデータを受信するように構成できます。  これらは、アプリケーション マニフェストで設定できます。  これにより、マルチテナント アプリケーションで、独自に使用するディレクトリ スキーマ拡張属性を登録できます。 アプリケーションがテナントにプロビジョニングされると、関連付けられているディレクトリ スキーマ拡張が、そのテナント内のユーザーに設定でき、使用できるようになります。  これがテナントで構成され、同意が付与されると、Graph を使用してデータの格納と取得を行うことや、Microsoft ID プラットフォームからアプリケーションに出力されるトークン内の要求にマッピングすることができます。

ディレクトリ スキーマ拡張属性は、任意のアプリケーションに対して登録および設定できます。

別のアプリケーションに登録されている拡張属性からのデータを使用して、アプリケーションから要求を送信する必要がある場合は、[要求のマッピング ポリシー](active-directory-claims-mapping.md)を使用して、拡張属性を要求にマッピングする必要があります。  ディレクトリ スキーマ拡張属性を管理するための一般的なパターンは、必要なすべてのスキーマ拡張の登録ポイントとなる、専用のアプリケーションを作成することです。  これは実際のアプリケーションである必要はなく、この手法は、すべての拡張の名前に同じアプリケーション ID が含まれることを意味します。

たとえば、次に示すのは、OAuth/OIDC トークンのディレクトリ スキーマ拡張属性から 1 つの要求を生成するための要求のマッピング ポリシーです。

```json
{
    "ClaimsMappingPolicy": {
        "Version": 1,
        "IncludeBasicClaimSet": "false",
        "ClaimsSchema": [{
                "Source": "User",
                "ExtensionID": "extension_xxxxxxx_test",
                "JWTClaimType": "http://schemas.contoso.com/identity/claims/exampleclaim"
            },
        ]
    }
}
```

ここで *xxxxxxx* は、拡張機能が登録されたアプリケーション ID です。

> [!TIP]
> オブジェクトのディレクトリ拡張属性を設定するときは、大文字と小文字の整合性が重要です。  拡張属性名は、設定時には大文字と小文字が区別されませんが、トークン サービスによってディレクトリから読み取られるときは大文字と小文字が区別されます。  "LegacyId" という名前のユーザー オブジェクトと、"legacyid" という名前の別のユーザー オブジェクトに拡張属性が設定された場合、"LegacyId" という名前を使用して属性が要求にマッピングされると、最初のユーザーのトークンではデータが正常に取得され、要求が含まれますが、2 番目のユーザーではこれが行われません。
>
> 組み込みのディレクトリ属性に使用される要求スキーマの "Id" パラメーターは、ディレクトリ拡張属性の "ExtensionID" です。

## <a name="next-steps"></a>次のステップ
- [SAML 2.0 および JSON Web Token (JWT) トークンに、カスタムまたは追加の要求を追加する](active-directory-optional-claims.md)方法について確認します。
- [特定のアプリ用のトークンに生成された要求をカスタマイズ](active-directory-claims-mapping.md)する方法について確認します。
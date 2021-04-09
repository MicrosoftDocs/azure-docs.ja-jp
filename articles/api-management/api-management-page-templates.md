---
title: Azure API Management のページ テンプレート | Microsoft Docs
description: Azure API Management で開発者ポータル ページ テンプレートの内容をカスタマイズする方法について説明します。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335587"
---
# <a name="page-templates-in-azure-api-management"></a>Azure API Management のページ テンプレート
Azure API Management には、開発者ポータルの各ページの内容を、内容を構成するテンプレート セットを使用してカスタマイズする機能があります。 [DotLiquid](http://dotliquidmarkup.org/) 構文、好みのエディター ([DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)など)、および用意されているローカライズされた[文字列リソース](api-management-template-resources.md#strings)、[グリフ リソース](api-management-template-resources.md#glyphs)、および[ページ コントロール](api-management-page-controls.md)のセットをテンプレートで使用して、表示されるページの内容を自由に構成できます。  
  
 このセクションのテンプレートを使用して、開発者ポータルの、サインイン ページ、サインアップ ページ、ページが見つかりませんページの内容をカスタマイズできます。  
  
-   [サインイン](#SignIn)  
  
-   [サインアップ](#SignUp)  
  
-   [ページが見つかりません](#PageNotFound)  
  
> [!NOTE]
>  このドキュメントには既定のテンプレートのサンプルが含まれていますが、それらは継続的な改善に伴って変更される可能性があります。 開発者ポータルで目的の個々のテンプレートに移動することで、最新の既定のテンプレートを表示できます。 テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](./api-management-developer-portal-templates.md)に関するページを参照してください。  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> サインイン  
 **サインイン** テンプレートを使用すると、開発者ポータルでサインイン ページをカスタマイズすることができます。  
  
 ![サインイン ページ](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM サインイン ページ開発者ポータル テンプレート")  
  
### <a name="default-template"></a>既定のテンプレート  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>コントロール  
 このテンプレートでは、次の[ページ コントロール](api-management-page-controls.md)を使用できます。  
  
-   [basic-signin](api-management-page-controls.md#basic-signin)  
  
-   [providers](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>データ モデル  
 [ユーザー サインイン](api-management-template-data-model-reference.md#UseSignIn) エンティティ。  
  
### <a name="sample-template-data"></a>サンプル テンプレート データ  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="sign-up"></a><a name="SignUp"></a> サインアップ  
 **サインアップ** テンプレートを使用すると、開発者ポータルでサインアップ ページをカスタマイズすることができます。  
  
 ![サインアップ ページ](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM サインアップ ページ開発者ポータル テンプレート")  
  
### <a name="default-template"></a>既定のテンプレート  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>コントロール  
 このテンプレートでは、次の[ページ コントロール](api-management-page-controls.md)を使用できます。  
  
-   [sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>データ モデル  
 [ユーザー サインアップ](api-management-template-data-model-reference.md#UserSignUp) エンティティ。  
  
### <a name="sample-template-data"></a>サンプル テンプレート データ  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> ページが見つかりません  
 **ページが見つかりません** テンプレートを使用すると、開発者ポータルでページが見つかりませんページをカスタマイズすることができます。  
  
 ![ページが見つかりませんページ](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM ページが見つかりませんページ開発者ポータル テンプレート")  
  
### <a name="default-template"></a>既定のテンプレート  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>コントロール  
 個のテンプレートでは、[ページ コントロール](api-management-page-controls.md)は使用できません。  
  
### <a name="data-model"></a>データ モデル  
  
|プロパティ|Type|説明|  
|--------------|----------|-----------------|  
|referenceCode|string|このページが内部エラーの結果として表示された場合に生成されるコード。|  
|errorCode|string|このページが内部エラーの結果として表示された場合に生成されるコード。|  
|emailBody|string|このページが内部エラーの結果として表示された場合に生成される電子メール本文。|  
|requestedUrl|string|ページが見つからなかったときに要求された URL。|  
|referrerUrl|string|要求された URL の参照元 URL。|  
  
### <a name="sample-template-data"></a>サンプル テンプレート データ  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>次のステップ
テンプレートの操作方法の詳細については、[テンプレートを使用して API Management 開発者ポータルをカスタマイズする方法](api-management-developer-portal-templates.md)に関するページを参照してください。

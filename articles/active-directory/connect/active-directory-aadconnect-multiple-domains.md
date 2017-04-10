---
title: "Azure AD Connect の複数のドメイン"
description: "このドキュメントでは、O365 と Azure AD で複数の最上位レベル ドメインを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: fa1c3d9cb07d417f5dbde41d6269fb1d157c3104
ms.openlocfilehash: a6a97cd187036222f5a47e55670da613117a2318


---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Azure AD とのフェデレーションに使用する複数ドメインのサポート
ここでは、Office 365 または Azure AD のドメインとのフェデレーション時に、複数のトップレベル ドメインとサブドメインを使用する方法について説明します。

## <a name="multiple-top-level-domain-support"></a>複数のトップレベル ドメインのサポート
複数のトップレベル ドメインと Azure AD のフェデレーションを行うには、単一のトップレベル ドメインを使用するフェデレーションでは行う必要のない構成を、いくつか実施する必要があります。

単一のドメインと Azure AD のフェデレーションを行う場合は、Azure でそのドメインに関する複数のプロパティが設定されます。  重要なプロパティの&1; つは IssuerUri です。  これは、トークンが関連付けられているドメインを識別するために Azure AD で使われる URI です。  この URI は解決される必要はありませんが、有効な URI である必要があります。  既定では、Azure AD により、このプロパティにオンプレミス AD FS 構成内のフェデレーション サービス識別子の値が設定されます。

> [!NOTE]
> フェデレーション サービス識別子は、フェデレーション サービスを一意に識別する URI です。  フェデレーション サービスは、セキュリティ トークン サービスとして機能する AD FS のインスタンスです。 
> 
> 

PowerShell コマンド `Get-MsolDomainFederationSettings -DomainName <your domain>`を使用して、IssuerUri を表示することができます。

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

複数のトップレベル ドメインを追加しようとすると、問題が生じます。  たとえば、Azure AD とオンプレミス環境の間でフェデレーションを設定しているとします。  このドキュメントでは、bmcontoso.com がその環境です。  この状況で、2 番目のトップレベル ドメインとして bmfabrikam.com を追加しました。

![ドメイン](./media/active-directory-multiple-domains/domains.png)

bmfabrikam.com ドメインを変換してフェデレーションしようとすると、エラーが発生します。  その原因は、IssuerUri プロパティで複数のドメインに同じ値を設定できないという Azure AD の制約にあります。  

![Federation error](./media/active-directory-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain パラメーター
この問題に対処するには、 `-SupportMultipleDomain` パラメーターを使用して、別の IssuerUri を追加する必要があります。  このパラメーターは以下のコマンドレットで使用します。

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

このパラメーターを使用すると、Azure AD で IssuerUri がドメイン名に基づくように構成されます。  ドメイン名は Azure AD 内のディレクトリ間で一意になります。  このパラメーターを指定することで、PowerShell コマンドが正常に実行されます。

![Federation error](./media/active-directory-multiple-domains/convert.png)

新しく導入した bmfabrikam.com ドメインの設定は、以下のようになっています。

![Federation error](./media/active-directory-multiple-domains/settings.png)

留意点として、 `-SupportMultipleDomain` は、adfs.bmcontoso.com 上のフェデレーション サービスをポイントするように構成されている他のエンドポイントを変更しません。

また、 `-SupportMultipleDomain` を使用すると、AD FS システムが Azure AD 用に発行するトークンに、適切な Issuer (発行者) の値を確実に含めることができます。 これは、ユーザーの UPN のドメイン部分を取得し、その値を IssuerUri でドメインとして設定することで達成されます (つまり、https://{upn suffix}/adfs/services/trust)。 

そのため、Azure AD または Office 365 に対する認証中に、ユーザーのトークンに含まれる IssuerUri 要素を使用して、Azure AD 内のドメインが特定されます。  一致するものが見つからなければ、認証は失敗します。 

たとえば、ユーザーの UPN が bsimon@bmcontoso.com, である場合、AD FS が発行するトークンの IssuerUri 要素は http://bmcontoso.com/adfs/services/trust に設定されます。 これは Azure AD の構成と一致し、認証は成功します。

次の規則は、このロジックを満たすカスタマイズ済みの要求規則です。

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> 新しいドメインを追加する際や既存のドメインを変換する際に -SupportMultipleDomain スイッチを使用するには、あらかじめそれらのドメインをサポートするフェデレーションによる信頼を設定しておく必要があります。  
> 
> 

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>AD FS と Azure AD の間の信頼を更新するには
AD FS と Azure AD インスタンスとの間でフェデレーションによる信頼を設定していない場合は、改めてこの信頼の作成が必要になることがあります。  というのも、 `-SupportMultipleDomain` パラメーターを使用せずに設定すると、IssuerUri が既定値で設定されるからです。  下のスクリーンショットでは、IssuerUri に https://adfs.bmcontoso.com/adfs/services/trust が設定されています。

ここで、Azure AD ポータルから新しいドメインを正常に追加した後、 `Convert-MsolDomaintoFederated -DomainName <your domain>`でドメインを変換しようとすると、次のエラーが発生します。

![Federation error](./media/active-directory-multiple-domains/trust1.png)

そこで、 `-SupportMultipleDomain` スイッチを追加して試してみると、次のエラーが発生します。

![Federation error](./media/active-directory-multiple-domains/trust2.png)

単純に元のドメインで `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` を実行してみても、エラーが生じます。

![Federation error](./media/active-directory-multiple-domains/trust3.png)

下記の手順で、もう&1; つのトップレベル ドメインを追加します。  既にドメインを&1; つ、 `-SupportMultipleDomain` パラメーターを使用せずに追加してある場合は、最初に元のドメインを削除して更新します。  トップレベル ドメインをまだ追加していない場合は、Azure AD Connect の PowerShell を使用してドメインを追加することから始めます。

以下の手順で、Microsoft Online の信頼を削除し、元のドメインを更新します。

1. AD FS フェデレーション サーバーで、 **AD FS 管理** 
2. 左側で、**[信頼関係]**、**[証明書利用者信頼]** の順に展開します。
3. 右側で、 **[Microsoft Office 365 ID プラットフォーム]** エントリを削除します。
   ![Remove Microsoft Online](./media/active-directory-multiple-domains/trust4.png)
4. [Windows PowerShell 用 Azure Active Directory モジュール](https://msdn.microsoft.com/library/azure/jj151815.aspx)をインストールしているマシンで、次のコードを実行します: `$cred=Get-Credential`。  
5. フェデレーションの対象である Azure AD ドメインのグローバル管理者のユーザー名とパスワードを入力します。
6. PowerShell で、 `Connect-MsolService -Credential $cred`
7. PowerShell で、 `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain`を実行します。  これは元のドメインに対するコマンドです。  上記のドメインを使用するので、次のようになります: `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

次の手順で、PowerShell を使用して新しいトップレベル ドメインを追加します。

1. [Windows PowerShell 用 Azure Active Directory モジュール](https://msdn.microsoft.com/library/azure/jj151815.aspx)をインストールしているマシンで、次のコードを実行します: `$cred=Get-Credential`。  
2. フェデレーションの対象である Azure AD ドメインのグローバル管理者のユーザー名とパスワードを入力します。
3. PowerShell で、 `Connect-MsolService -Credential $cred`
4. PowerShell で、 `New-MsolFederatedDomain –SupportMultipleDomain –DomainName`

次の手順で、Azure AD Connect を使用して新しいトップレベル ドメインを追加します。

1. デスクトップまたは [スタート] メニューから、Azure AD Connect を起動します。
2. [Azure AD ドメインを追加します] を選択します。![Add an additional Azure AD domain](./media/active-directory-multiple-domains/add1.png)
3. Azure AD と Active Directory の資格情報を入力します。
4. フェデレーションを構成する&2; つ目のドメインを選択します。
   ![Add an additional Azure AD domain](./media/active-directory-multiple-domains/add2.png)
5. [インストール] をクリックします。

### <a name="verify-the-new-top-level-domain"></a>新しいトップレベル ドメインの確認
PowerShell コマンド `Get-MsolDomainFederationSettings -DomainName <your domain>`を使用して、更新された IssuerUri を確認できます。  下のスクリーンショットは、元のドメイン http://bmcontoso.com/adfs/services/trust でフェデレーション設定が更新されたことを示しています。

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/MsolDomainFederationSettings.png)

また、新しいドメインの IssuerUri として、https://bmfabrikam.com/adfs/services/trust が設定されています。

![Get-MsolDomainFederationSettings](./media/active-directory-multiple-domains/settings2.png)

## <a name="support-for-sub-domains"></a>サブドメインのサポート
サブドメインの追加では、Azure AD がドメインを処理する方法のために、親の設定を継承します。  つまり、IssuerUri は親に一致させる必要があります。

たとえば、bmcontoso.com を運用している状況で、corp.bmcontoso.com を追加するとします。  この場合、corp.bmcontoso.com に属するユーザーの IssuerUri は、**http://bmcontoso.com/adfs/services/trust** とする必要があります。  しかし、上で Azure AD に適用した標準ルールでは、発行者を **http://corp.bmcontoso.com/adfs/services/trust** としてトークンを生成するので、ドメインに必要な値と一致せず、認証に失敗します。

### <a name="how-to-enable-support-for-sub-domains"></a>サブドメインのサポートを有効にする方法
この問題を回避するには、Microsoft Online 用 AD FS 証明書利用者の信頼を更新する必要があります。  そのためには、カスタム Issuer 値の構築時にユーザーの UPN サフィックスからサブドメインを削除するよう、カスタム要求規則を構成する必要があります。 

次の要求で実行します。

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
正規表現の最後の数字は、ルート ドメインにある親ドメインの数を設定します。 ここでは bmcontoso.com があるので、2 つの親ドメインが必要です。 3 つの親ドメインを保持する (つまり corp.bmcontoso.com) 場合、この数字は&3; になります。 範囲を指示できますが、常にドメインの最大数と一致させるための突き合わせが行われます。 "{2,3}" は、2 ～&3; つのドメインと一致します (つまり、bmfabrikam.com と corp.bmcontoso.com)。

以下の手順に従い、サブドメインをサポートするためのカスタム要求を追加します。

1. AD FS 管理を開きます。
2. [Microsoft Online RP 信頼] を右クリックして、[要求規則の編集] を選択します。
3. 3 番目の要求規則を選択し、置き換えます。![Edit claim](./media/active-directory-multiple-domains/sub1.png)
4. 現在の要求:
   
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));
   
       with
   
        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Replace claim](./media/active-directory-multiple-domains/sub2.png)

5. [OK] をクリックします。  [適用] をクリックします。  [OK] をクリックします。  AD FS 管理を閉じます。




<!--HONumber=Jan17_HO2-->



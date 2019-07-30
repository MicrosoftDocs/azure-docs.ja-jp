---
title: Azure AD ドメイン サービスでのセキュリティで保護された LDAP (LDAPS) の構成 | Microsoft Docs
description: Azure AD Domain Services のマネージド ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: iainfou
ms.openlocfilehash: 1cb9ff70b41b63f4a3e3d45d744037195fdf28a7
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473769"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインに対するセキュリティで保護された LDAP (LDAPS) の構成
この記事では、Azure AD Domain Services のマネージド ドメインに対して、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を有効にする方法について説明します。 セキュリティで保護された LDAP は、「Secure Sockets Layer (SSL)/トランスポート層セキュリティ (TLS) 経由のライトウェイト ディレクトリ アクセス プロトコル (LDAP)」としても知られています。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](create-instance.md)に記載されているすべてのタスクを実行してください。
4. **セキュリティで保護された LDAP を有効にするために使用する証明書**。

   * **推奨** - 信頼できる開証明機関から証明書を取得します。 この構成方法の方が安全です。
   * または、この記事で後ほど説明するように、 [自己署名証明書を作成](#task-1---obtain-a-certificate-for-secure-ldap) することもできます。

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>セキュリティで保護された LDAP 証明書の要件
セキュリティで保護された LDAP を有効にする前に、以下のガイドラインに従って有効な証明書を取得します。 マネージド ドメインに対して、セキュリティで保護された LDAP を無効な証明書または正しくない証明書で有効にしようとすると、エラーが発生します。

1. **信頼された発行者** - 証明書は、セキュリティで保護された LDAP を使用してマネージド ドメインに接続するコンピューターによって信頼された機関から発行される必要があります。 この機関は、これらのコンピューターによって信頼された公開証明機関 (CA) またはエンタープライズ CA です。
2. **有効期間** - 証明書は少なくとも、今後 3 ～ 6 か月間有効である必要があります。 証明書の有効期限が切れると、マネージド ドメインへのセキュリティで保護された LDAP のアクセスが切断されます。
3. **サブジェクト名** - 証明書のサブジェクト名は、マネージド ドメインである必要があります。 たとえば、ドメインが 'contoso100.com' という名前である場合、証明書のサブジェクト名は 'contoso100.com' である必要があります。 DNS 名 (サブジェクト代替名) をマネージド ドメインのワイルドカード名に設定します。
4. **キー使用法** - 証明書は、デジタル署名およびキーの暗号化に対して構成される必要があります。
5. **証明書の目的** - 証明書は、SSL サーバー認証に対して有効である必要があります。

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>タスク 1 - セキュリティで保護された LDAP 用の証明書の取得
最初のタスクでは、セキュリティで保護された LDAP からのマネージド ドメインへのアクセスに使用する証明書を取得します。 2 つのオプションがあります。

* 公開 CA またはエンタープライズ CA から証明書を取得します。
* 自己署名証明書を作成します。

> [!NOTE]
> セキュリティで保護された LDAP を使用してマネージド ドメインに接続する必要があるクライアント コンピューターでは、セキュリティで保護された LDAP 証明書の発行者を信頼する必要があります。
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>オプション A (推奨) - セキュリティで保護された LDAP 証明書を証明機関から取得する
組織が公開 CA から証明書を取得する場合は、その公開 CA から Secure LDAP 証明書を取得します。 エンタープライズ CA を展開している場合は、そのエンタープライズ CA から Secure LDAP 証明書を取得します。

> [!TIP]
> **ドメイン サフィックスが ".onmicrosoft.com" であるマネージド ドメインには、自己署名証明書を使用します。**
> マネージド ドメインの DNS ドメイン名の末尾が ".onmicrosoft.com" の場合、公開証明機関から Secure LDAP 証明書を取得することはできません。 "onmicrosoft.com" ドメインは Microsoft が所有しているため、公開証明機関はこのサフィックスのドメインの Secure LDAP 証明書を発行することを拒否します。 このシナリオでは、自己署名証明書を作成し、その証明書を使用して Secure LDAP を構成します。
>

公開証明機関から取得した証明書が、「[セキュリティで保護された LDAP 証明書の要件](#requirements-for-the-secure-ldap-certificate)」に記載されているすべての要件を満たしていることを確認します。


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>オプション B - セキュリティで保護された LDAP 用の自己署名証明書を作成する
公開証明機関からの証明書の使用を見込めない場合は、セキュリティで保護された LDAP 用の自己署名証明書を作成することができます。 このオプションは、マネージド ドメインの DNS ドメイン名の末尾が ".onmicrosoft.com" の場合に選択します。

**PowerShell を使用した自己署名証明書の作成**

Windows コンピューターで **管理者** として新しい PowerShell ウィンドウを開き、次のコマンドを入力して新しい自己署名証明書を作成します。

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

上記のサンプルの "contoso100.com" は、マネージド ドメインの DNS ドメイン名に置き換えます。 たとえば、"contoso100.onmicrosoft.com" というマネージド ドメインを作成した場合は、Subject 属性の "contoso100.com" を "contoso100.onmicrosoft.com" で、DnsName 属性の " *.contoso100.com" を "* .contoso100.onmicrosoft.com" で置き換えます。

![Azure AD ディレクトリの選択](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

新しく作成した自己署名証明書は、ローカル コンピューターの証明書ストアに配置されます。


## <a name="next-step"></a>次のステップ
[タスク 2 - セキュリティで保護された LDAP 証明書を .PFX ファイルにエクスポートする](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)

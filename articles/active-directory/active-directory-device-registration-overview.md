---
title: "Azure Active Directory Device Registration の概要 | Microsoft Docs"
description: "Azure Active Directory デバイス登録は、デバイスに基づいて条件付きでアクセスを許可するというシナリオの基礎となる機能です。 デバイスが登録されると、Azure Active Directory Device Registration によってそのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。"
services: active-directory
keywords: "デバイス登録, デバイス登録の有効化, デバイス登録と MDM"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 1e92c1a2-01b8-4225-950b-373cd601b035
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: d19956e4964f57251f51eb8ffe5041c6a49da1a7
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録の使用
Azure Active Directory デバイス登録は、デバイスに基づいて条件付きでアクセスを許可するというシナリオの基礎となる機能です。 デバイスが登録されると、Azure Active Directory デバイス登録によって、そのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。 認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。

Microsoft Intune などのモバイル デバイス管理 (MDM) ソリューションと組み合わせて使用すると、Azure Active Directory のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。 Microsoft Intune へのデバイスの登録の詳細については、「[管理するデバイスを Intune に登録する](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)」をご覧ください。

## <a name="scenarios-enabled-by-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration で実現されるシナリオ
Azure Active Directory Device Registration は、iOS、Android、Windows の各デバイスをサポートしています。 Azure AD Device Registration を使用する個々のシナリオでは、より具体的な要件とプラットフォームのサポートが存在する場合があります。 そうしたシナリオを次に示します。

* **オンプレミスでホストされるアプリケーションへの条件付きアクセス**: Windows Server 2012 R2 で AD FS を使用するように構成されたアプリケーションに対して、アクセス ポリシーの登録済みのデバイスを使用できます。 オンプレミスの条件付きアクセスを設定する方法の詳細については、「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](active-directory-device-registration-on-premises-setup.md)」を参照してください。
* **Microsoft Intune を使用した Office 365 アプリケーションへの条件付きアクセス** : IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時にインフォメーション ワーカーに準拠デバイスからサービスへのアクセスを許可できます。 詳細については、「 [Office 365 サービス用条件付きアクセスのデバイス ポリシー](active-directory-conditional-access-device-policies.md)」を参照してください。

## <a name="setting-up-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration の設定
モバイル デバイスで既知の DNS レコードを探してサービスを検出できるように、Azure Portal で Azure AD Device Registration を有効にする必要があります。 Windows 10、Windows 8.1、Windows 7、Android、iOS のデバイスでサービスが検出されて使用されるように、会社の DNS を構成する必要があります。
Azure Active Directory の管理者ポータルを使用して、登録されているデバイスを表示し、有効または無効にすることができます。

> [!NOTE]
> デバイスの自動登録をセットアップする最新手順については、「 [Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の設定方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。
> 
> 

### <a name="enable-azure-active-directory-device-registration-service"></a>Azure Active Directory Device Registration サービスを有効にする
1. Microsoft Azure ポータルに管理者としてサインインします。
2. 左ウィンドウで、 **[Active Directory]**を選択します。
3. **[ディレクトリ]** タブで、ディレクトリを選択します。
4. **[構成]** タブをクリックします。
5. **[デバイス]**セクションまでスクロールします。
6. **[デバイスの社内参加が許可されるユーザー]** で **[すべて]** を選択します。
7. ユーザーごとに承認するデバイスの最大数を選択します。

> [!NOTE]
> Office 365 用に Microsoft Intune またはモバイル デバイス管理を使用して登録するには、社内参加が必要です。 これらのサービスのいずれかを構成している場合、[すべて] が選択され、[なし] は無効になります。
> 
> 

既定では、サービスに対する&2; 要素認証は有効になっていません。 ただし、デバイスを登録するときに&2; 要素認証を使用することをお勧めします。

* このサービスに対して&2; 要素認証を要求するには、Azure Active Directory で&2; 要素認証プロバイダーを構成し、Multi-Factor Authentication 用のユーザー アカウントを構成しておく必要があります。[Azure Active Directory への Multi-Factor Authentication の追加](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)に関する記事をご覧ください。
* Windows Server 2012 R2 で AD FS を使用している場合は、AD FS で 2 要素認証モジュールを構成する必要があります。[Active Directory フェデレーション サービスでの Multi-Factor Authentication の使用](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)に関する記事をご覧ください。

## <a name="configure-azure-active-directory-device-registration-discovery"></a>Azure Active Directory Device Registration の検出を構成する
Windows 7 デバイスおよび Windows 8.1 デバイスでは、ユーザー アカウント名と既知のデバイス登録サーバー名とを組み合わせることによって Device Registration サービスが検出されます。

Azure Active Directory Device Registration サービスに関連付けられた A レコードを参照する DNS CNAME レコードを作成する必要があります。 CNAME レコードでは、既知のプレフィックス enterpriseregistration の後に、組織のユーザー アカウントで使用されている UPN サフィックスを使用する必要があります。 組織で複数の UPN サフィックスを使用している場合は、DNS に複数の CNAME レコードを作成する必要があります。

たとえば、組織で @contoso.com と @region.contoso.com の&2; つの UPN サフィックスを使用している場合は、次の DNS レコードを作成します。

| エントリ | 型 | Address |
| --- | --- | --- |
| enterpriseregistration.contoso.com |CNAME |enterpriseregistration.windows.net |
| enterpriseregistration.region.contoso.com |CNAME |enterpriseregistration.windows.net |

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Azure Active Directory のデバイス オブジェクトを表示および管理する
1. Azure 管理者ポータルから、デバイスの表示、ブロック、およびブロック解除を行うことができます。 ブロックされたデバイスからは、登録済みデバイスのみを許可するように構成されたアプリケーションにアクセスできなくなります。
2. Microsoft Azure ポータルに管理者としてサインインします。
3. 左ウィンドウで、 **[Active Directory]**を選択します。
4. ディレクトリを選択します。
5. **[ユーザー]** タブをクリックします。 デバイスを表示するユーザーを選択します。
6. **[デバイス]** タブを選択します。
7. ドロップダウン メニューから **[登録済みのデバイス]** を選択します。
8. ここで、ユーザーの登録済みデバイスを表示、ブロック、またはブロック解除することができます。

## <a name="additional-topics"></a>関連トピック
Azure AD Device Registration を使用して、ドメイン参加済みの Windows 7 デバイスおよび Windows 8.1 デバイスを登録することができます。 次のトピックでは、Windows 7 デバイスおよび Windows 8.1 デバイス上でデバイス登録を構成するために必要な前提条件と手順について詳しく説明します。

* [Azure Active Directory への Windows ドメイン参加済みデバイスの自動デバイス登録](active-directory-conditional-access-automatic-device-registration.md)
* [Azure Active Directory への Windows 10 ドメイン参加済みデバイスの自動デバイス登録](active-directory-azureadjoin-devices-group-policy.md)



---
title: "Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法 | Microsoft Docs"
description: "ドメイン参加済み Windows デバイスを自動的かつサイレントに Azure Active Directory に登録するための設定を行います。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 9934902811354ffa4047d70d995a6dd44be0229b
ms.lasthandoff: 03/10/2017


---
# <a name="get-started-with-azure-active-directory-device-registration"></a>Azure Active Directory デバイス登録の使用

Azure Active Directory デバイス登録は、デバイスに基づいて条件付きでアクセスを許可するというシナリオの基礎となる機能です。 デバイスが登録されると、Azure Active Directory デバイス登録によって、そのデバイスの ID がプロビジョニングされます。この ID は、ユーザーのサインイン時のデバイス認証に使用されます。 認証済みのデバイスおよびデバイスの属性を使用して、クラウドおよびオンプレミスでホストされるアプリケーションに条件付きアクセス ポリシーを適用できます。

Microsoft Intune などのモバイル デバイス管理 (MDM) ソリューションと組み合わせて使用すると、Azure Active Directory のデバイスの属性は、デバイスに関する情報が追加されて更新されます。 これにより、条件付きアクセス規則を作成できます。この規則に従い、デバイスからのアクセス時にセキュリティおよび法令遵守の基準を満たす必要があります。 Microsoft Intune へのデバイスの登録の詳細については、「管理するデバイスを Intune に登録する」を参照してください。
Azure Active Directory Device Registration で実現されるシナリオ。Azure Active Directory Device Registration は、iOS、Android、Windows の各デバイスをサポートしています。 Azure AD Device Registration を使用する個々のシナリオでは、より具体的な要件とプラットフォームのサポートが存在する場合があります。 

そうしたシナリオを次に示します。

- **Microsoft Intune を使用した Office 365 アプリケーションへの条件付きアクセス:** IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時にインフォメーション ワーカーに準拠デバイスからサービスへのアクセスを許可できます。 詳細については、「Office 365 サービス用条件付きアクセスのデバイス ポリシー」を参照してください。

- **オンプレミスでホストされるアプリケーションへの条件付きアクセス:** Windows Server 2012 R2 で AD FS を使用するように構成されたアプリケーションに対して、アクセス ポリシーの登録済みのデバイスを使用できます。 オンプレミスの条件付きアクセスを設定する方法の詳細については、「[Azure Active Directory Device Registration を使用したオンプレミスの条件付きアクセスの設定](active-directory-device-registration-on-premises-setup.md)」を参照してください。

## <a name="setting-up-azure-active-directory-device-registration"></a>Azure Active Directory Device Registration の設定

デバイス登録をセットアップするには、次のような複数のオプションがあります。

- Azure AD ドメインに参加しているときに、デバイスを登録できます。 このトピックに関しては、Azure AD Join と、ユーザーが Azure AD ドメインに参加するために必要な設定について、より詳細に学習できます。

- ユーザーが職場または学校のアカウントを個人のデバイスの Windows に追加するとき、または登録を必要とする職場のリソースにモバイル デバイスが接続されるときに、デバイスを登録できます。 これを確実に行うには、Azure Portal で Azure AD Device Registration を有効にする必要があります。 

- 従来のドメイン参加コンピューターでは、自動デバイス登録を使用して、デバイスを登録することができます。 これを確実に行うには、自動デバイス登録を続行する前に、Azure AD Connect をセットアップする必要があります。

最新の手順については、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。  
Azure Active Directory の管理者ポータルを使用して、登録されているデバイスを確認し、有効または無効にすることもできます。

## <a name="enable-the-azure-active-directory-device-registration-service"></a>Azure Active Directory デバイス登録サービスを有効にする

**Azure Active Directory デバイス登録サービスを有効にするには:**

1.    Microsoft Azure Portal に管理者としてサインインします。

2.    左ウィンドウで、 **[Active Directory]**を選択します。

3.    [ディレクトリ] タブで、ディレクトリを選択します。

4.    **[構成]**をクリックします。

5.    **[デバイス]** までスクロールします。

6.    [ユーザーはデバイスを Azure AD に登録できます] で [すべて] を選択します。

7.    ユーザーごとに承認するデバイスの最大数を選択します。

Office 365 用に Microsoft Intune またはモバイル デバイス管理を使用して登録するには、デバイス登録が必要です。 これらのサービスのいずれかを構成している場合は、**[すべて]** が選択され、**[なし]** は無効になります。 正しく構成されていて、適切なライセンスがあることを確認してください。

既定では、サービスに対する&2; 要素認証は有効になっていません。 ただし、デバイスを登録するときに&2; 要素認証を使用することをお勧めします。

- このサービスに対して&2; 要素認証を必要とするように設定する場合は、事前に Azure Active Directory で&2; 要素認証プロバイダーを構成し、Multi-Factor Authentication に対してユーザー アカウントを構成する必要があります。「Multi-Factor Authentication を Azure Active Directory に追加する」を参照してください。

- Windows Server 2012 R2 で AD FS を使用している場合は、AD FS で 2 要素認証モジュールを構成する必要があります。Active Directory フェデレーション サービスでの Multi-Factor Authentication の使用に関する記事を参照してください。

## <a name="view-and-manage-device-objects-in-azure-active-directory"></a>Azure Active Directory のデバイス オブジェクトを表示および管理する

Azure 管理者ポータルから、デバイスの表示、ブロック、およびブロック解除を行うことができます。 ブロックされたデバイスからは、登録済みデバイスのみを許可するように構成されたアプリケーションにアクセスできなくなります。

**Azure Active Directory のデバイス オブジェクトを表示および管理するには:**
 
1.    Microsoft Azure Portal に管理者としてログオンします。

2.    左ウィンドウで、 **[Active Directory]**を選択します。

3.    ディレクトリを選択します。

4.    **[ユーザー]** を選択します。 

5.  表示するデバイスのユーザーをクリックします。

6.    **[デバイス]** を選択します。

7.    **[登録済みのデバイス]** を選択します。

これで、ユーザーの登録済みデバイスを表示、ブロック、またはブロック解除することができます。
オンプレミスのドメインに参加済みで自動的に登録された Windows 10 デバイスは、[ユーザー] タブには表示されません。 企業のすべてのデバイスを検索するには、Get-MsolDevice PowerShell コマンドを使用してください。 


## <a name="next-steps"></a>次のステップ

自動化されたデバイス登録をセットアップするには、「[Azure Active Directory への Windows ドメイン参加済みデバイスの自動登録の構成方法](active-directory-conditional-access-automatic-device-registration-setup.md)」を参照してください。




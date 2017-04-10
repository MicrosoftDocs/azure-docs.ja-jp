---
title: "ユーザーの Azure AD 参加の設定 | Microsoft Docs"
description: "管理者が、オンプレミス ディレクトリとデバイス登録のために Azure AD 参加を設定する方法について説明します。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: bfc5d415-c918-4d8b-afee-b3f41cc28469
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0480c4f89b6036897bc8d7eff81b56262ea8806c
ms.openlocfilehash: e4e0fa77552c4df2ea5bb9ddae916e7c661824d1
ms.lasthandoff: 02/23/2017


---
# <a name="setting-up-azure-ad-join-in-your-organization"></a>組織での Azure AD 参加の設定
Azure Active Directory 参加 (Azure AD 参加) を設定する前に、ユーザーのオンプレミスのディレクトリをクラウドに同期するか、Azure AD に管理アカウントを手動で作成する必要があります。

オンプレミスのユーザーを Azure AD に同期するための詳しい手順については、「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」を参照してください。

Azure AD にユーザーを手動で作成して管理する方法については、「 [Azure AD でのユーザー管理](https://msdn.microsoft.com/library/azure/hh967609.aspx)」を参照してください。

## <a name="set-up-device-registration"></a>デバイスの登録の設定
1. Azure ポータルに管理者としてサインインします。
2. 左ウィンドウで、 **[Active Directory]**を選択します。
3. **[ディレクトリ]** タブで、ディレクトリを選択します。
4. **[構成]** タブをクリックします。
5. **[デバイス]** セクションに移動します。
6. **[デバイス]** タブで、次のように設定します。  
   * **[ユーザーごとのデバイスの最大数]**: Azure AD でユーザーが持つことができるデバイスの最大数を選択します。  ユーザーがこのクォータに達した場合、1 つ以上の既存のデバイスを削除するまでデバイスを追加できなくなります。
   * **[デバイスを参加させるには Multi-factor Auth が必要]**: 有効にした場合、ユーザーはデバイスを Azure AD に参加させるために&2; つ目の認証要素を提供する必要があります。 Azure Multi-Factor Authentication の詳細については、[クラウドでの Azure Multi-Factor Authentication の概要](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)に関するページをご覧ください。
   * **[ユーザーはデバイスの Azure AD 参加を実行できます]**: デバイスを Azure AD に参加させることができるユーザーとグループを選択します。
   * **[Azure AD 参加済みデバイスの追加の管理者]**: Azure AD Premium または Enterprise Mobility Suite (EMS) では、デバイスに対するローカル管理者権限が許可されるユーザーを選択できます。 全体管理者とデバイスの所有者には、既定でローカル管理者権限が付与されます。

<center>![デバイスの登録の設定](./media/active-directory-azureadjoin/active-directory-aadjoin-configure-devices.png) </center>

ユーザーの Azure AD 参加を設定すると、ユーザーは、会社のデバイスまたは個人のデバイスから Azure AD に接続できます。

Azure AD 参加をユーザーが設定できるようにする&3; つのシナリオを次に示します。

* ユーザーが会社所有のデバイスを直接 Azure AD に参加させる。
* ユーザーが、会社所有のデバイスをオンプレミスの Active Directory にドメイン参加させ、Azure AD に拡張する。
* ユーザーが個人のデバイス上の Windows に職場または学校のアカウントを追加する。

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)



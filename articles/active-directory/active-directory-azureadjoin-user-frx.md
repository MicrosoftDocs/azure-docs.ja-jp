---
title: "セットアップ中に新しいデバイスを Azure AD に設定する | Microsoft Docs"
description: "最初の実行エクスペリエンスで Azure AD 参加を設定する方法を説明するトピック。"
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
tags: azure-classic-portal
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a6227096559af9b9beba7081ba770db80189dc18


---
# <a name="set-up-a-new-device-with-azure-ad-during-setup"></a>セットアップ中に新しいデバイスを Azure AD に設定する
Windows 10 では、ユーザーは、最初の実行エクスペリエンス (FRX) 中に自分のデバイスを Azure Active Directory (Azure AD) に参加させることができます。 これによって、組織は、市販のデバイスを従業員や学生に配布するか、ユーザーに自分のデバイスを選択させる (CYOD) ことができます。
Windows 10 Professional または Windows 10 Enterprise のいずれかのエディションがデバイスにインストールされている場合、既定のエクスペリエンスは、会社所有のデバイスのセットアップ プロセスになります。

## <a name="to-join-a-device-to-azure-ad"></a>デバイスを Azure AD に参加させるには
1. 新しいデバイスの電源をオンにしてセットアップ プロセスを開始すると、**準備** メッセージが表示されます。 指示に従って、デバイスを設定します。
2. 地域と言語をカスタマイズすることから開始します。 次に、マイクロソフト ソフトウェア ライセンス条項に同意します。
   ![地域をカスタマイズする](./media/active-directory-azureadjoin/active-directory-azureadjoin-customize-region.png)
3. インターネットに接続するために使用するネットワークを選択します。
4. 個人のデバイスまたは会社所有のデバイスのどちらを使用しているかを選択します。 会社所有の場合は、 **[このデバイスは私の組織に属しています]**をクリックします。 これで、Azure AD 参加エクスペリエンスが開始されます。 次に示すのは、Windows 10 Professional を使用している場合に表示される画面です。
   <center>
   ![この PC の所有者画面](./media/active-directory-azureadjoin/active-directory-azureadjoin-who-owns-pc.png)
5. 組織によって提供された資格情報を入力します。
   <center>
   ![サインイン画面](./media/active-directory-azureadjoin/active-directory-azureadjoin-sign-in.png)
6. ユーザー名を入力すると、Azure AD で一致するテナントが検索されます。 フェデレーション ドメインに属している場合は、オンプレミスのセキュリティ トークン サービス (STS) サーバー、たとえば Active Directory フェデレーション サービス (AD FS) にリダイレクトされます。
7. フェデレーション ドメインのユーザーでない場合は、Azure AD でホストされるページに資格情報を直接入力します。 会社のブランド化が構成されている場合は、組織のロゴとサポート テキストも表示されます。
8. Multi-Factor Authentication で必要な情報の入力を求められます。 これは IT 管理者によって構成されています。
9. Azure AD によって、このユーザー/デバイスをモバイル デバイス管理に登録する必要があるかどうかがチェックされます。
10. Windows によって、デバイスが Azure AD の組織のディレクトリに登録され、必要に応じてモバイル デバイス管理に登録されます。
11. 管理対象ユーザーの場合は、自動サインイン プロセスによりデスクトップに移動します。
12. フェデレーション ユーザーの場合は、資格情報を入力するための Windows サインイン画面が表示されます。

> [!NOTE]
> Windows の OOBE (Out-of-Box Experience) でオンプレミスの Windows Server Active Directory ドメインに参加することはサポートされていません。 このため、コンピューターをドメインに参加させる予定の場合は、 **[代わりにローカル アカウントで Windows をセットアップしてください]** リンクを選択する必要があります。 その後で、前に実行したように、コンピューターの [設定] メニューからドメインに参加できます。
> 
> 

## <a name="additional-information"></a>追加情報
* [エンタープライズ向け Windows 10: デバイスを仕事に使用する方法](active-directory-azureadjoin-windows10-devices-overview.md)
* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-user-upgrade.md)
* [Microsoft Passport 経由でのパスワードを使用しない ID の認証](active-directory-azureadjoin-passport.md)
* [Azure AD 参加の使用シナリオについて](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Windows 10 エクスペリエンスのためのドメイン参加済みデバイスの Azure AD への接続](active-directory-azureadjoin-devices-group-policy.md)
* [Azure AD Join の設定](active-directory-azureadjoin-setup.md)




<!--HONumber=Feb17_HO2-->



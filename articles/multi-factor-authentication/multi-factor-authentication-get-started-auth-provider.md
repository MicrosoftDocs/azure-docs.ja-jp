---
title: "Azure 多要素認証プロバイダーの概要 | Microsoft Docs"
description: "Azure Multi-Factor Auth プロバイダーの作成方法について説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/24/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b0c3c5dfd695b28b75bf44c9f835b34966413a9a
ms.lasthandoff: 03/04/2017


---
# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Azure Multi-Factor Auth プロバイダーの概要
Azure Active Directory を持つグローバル管理者と Office 365 ユーザーは、既定で 2 段階認証を使用できます。 ただし、[高度な機能](multi-factor-authentication-whats-next.md)が必要な場合は、通常版の Multi-Factor Authentication (MFA) をご購入ください。

> [!NOTE]
> Azure Multi-Factor Auth プロバイダーは、通常版の Azure MFA に備わっている機能を活用するために使用されます。 その対象となるのは、 **Azure MFA、Azure AD Premium、EMS のいずれのライセンスも持たないユーザー**です。  Azure MFA、Azure AD Premium、EMS には、通常版の Azure MFA が既定で含まれています。  ライセンスを所有している場合、Azure 多要素認証プロバイダーは必要ありません。

SDK をダウンロードする場合は、Azure 多要素認証プロバイダーが必要です。

> [!IMPORTANT]
> SDK をダウンロードするには、Azure MFA、AAD Premium、または EMS のライセンスを所有していても、Azure 多要素認証プロバイダーを作成する必要があります。  既にライセンスがある状態で、SDK のダウンロードのために Azure 多要素認証プロバイダーを作成する場合には、プロバイダーの作成に **[有効化されたユーザーごと]** モデルを使用してください。 プロバイダーを作成したら、Azure MFA、Azure AD Premium、または EMS のライセンスが保存されているディレクトリにリンクします。  この構成により、所有しているライセンス数よりも&2; 段階認証を実行する一意のユーザーの数が多い場合でも、適切な課金が行われます。

## <a name="create-a-multi-factor-auth-provider"></a>Multi-Factor Auth プロバイダーを作成する
Azure Multi-Factor Auth プロバイダーを作成するには、次の手順に従います。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)に管理者としてサインインします。
2. 左側で、 **[Active Directory]**を選択します。
3. [Active Directory] ページの上部で **[Multi-Factor Authentication プロバイダー]**をクリックします。
   ![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. 下部にある **[新規]**をクリックします。
   ![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. [App Services] の下の **[多要素認証プロバイダー]** を選択します。
   ![MFA プロバイダーの作成](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. **[簡易作成]**を選択します。
   ![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
7. 次の各フィールドに入力し、 **[作成]**をクリックします。
   1. **名前** - Multi-Factor Auth プロバイダーの名前。
   2. **使用モデル** – 次の&2; つのオプションのいずれかを選ぶことができます。
      * 認証ごと – 認証ごとに課金される購入モデル。 通常、コンシューマー向けのアプリケーションで Azure Multi-factor Authentication を使用するシナリオで使用されます。
      * 有効ユーザーごと – 有効ユーザーごとに課金される購入モデル。 通常、Office 365 などのアプリケーションにアクセスに従業員向けに使用されます。 Azure MFA のライセンスを既に許諾されているユーザーがいる場合には、このオプションを選択します。
   3. **ディレクトリ** - Multi-Factor Authentication プロバイダーに関連付けられている Azure Active Directory テナント。 次の点に注意してください。
      * Multi-Factor Auth Provider の作成に、Azure AD ディレクトリは必要ありません。 Azure Multi-Factor Authentication Server または SDK を使用するだけであれば、空欄にしておいてください。
      * 高度な機能を利用するためには、Azure AD ディレクトリに Multi-Factor Auth プロバイダーを関連付ける必要があります。
      * Azure AD Connect、AAD Sync、または DirSync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。  非同期の Azure AD ディレクトリしか使用していない場合、これは不要です。
        ![MFA プロバイダーの作成](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
8. [作成] をクリックすると、Multi-Factor Authentication プロバイダーが作成され、 **"Multi-Factor Authentication プロバイダーが正常に作成されました"**というメッセージが表示されます。 **[OK]**をクリックします。
   ![Creating an MFA Provider](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)



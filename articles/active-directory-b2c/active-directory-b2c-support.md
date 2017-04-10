---
title: "Azure Active Directory B2C: サポート | Microsoft Docs"
description: "Azure Active Directory B2C のサポート要求を提出する方法"
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: 51922008-6fbd-487c-b2c7-a5db57d154b4
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: 4b13c040a15bef2f04d2cd2126e2270d061898bd
ms.openlocfilehash: 24a1e13b7865d25d56cb5a6f44ba3c9c097174a7
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-b2c-file-support-requests"></a>Azure Active Directory B2C: ファイル サポート要求
Azure Portal で Azure Active Directory (Azure AD) B2C のサポート要求を提出するには、次の手順に従います。

1. [この手順に従って、Azure Portal で B2C 機能ブレードに移動します](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C テナントを、Azure サブスクリプションが関連付けられている別のテナントに切り替えます。 通常、移動先のテナントは、従業員のテナント、または Azure サブスクリプションにサインアップしたときに作成された既定のテナントです。 詳細については、「[Azure サブスクリプションが Azure AD に関連付けられる方法](../active-directory/active-directory-how-subscriptions-associated-directory.md#how-an-azure-subscription-is-related-to-azure-ad)」を参照してください。
   
    ![Support - Switch tenants](./media/active-directory-b2c-support/support-switch-dir.png)
3. テナントを切り替えた後、 **[ヘルプとサポート]**をクリックします。
   
    ![Support - Help + Support](./media/active-directory-b2c-support/support-support.png)
4. **[新しいサポート要求]**をクリックします。
   
    ![Support - New](./media/active-directory-b2c-support/support-new.png)
5. **[基本]** ブレードで、以下の説明に従ってから、**[次へ]** をクリックします。
   
   * **[問題の種類]** は **[技術]** です。
   * 適切な **サブスクリプション**を選択します。
   * **[サービス]** は **[Active Directory]** です。
   * 適切な **サポート プラン**を選択します。 サポート プランがない場合は、 [ここ](https://azure.microsoft.com/en-us/support/plans/)からサインアップできます。
     
     ![Support - Basics](./media/active-directory-b2c-support/support-basics.png)
6. **[問題]** ブレードで、以下の説明に従ってから、**[次へ]** をクリックします。
   
   * 適切な **重要度** レベルを選択します。
   * **[問題の種類]** は **[B2C]** です。
   * 適切な **カテゴリ**を選択します。
   * **[詳細]** フィールドに問題の説明を入力します。 B2C テナントの名前、問題の説明、エラー メッセージ、関連付け ID (ある場合) などの詳細情報を入力します。
   * **[期間]** フィールドに問題が発生した日時 (タイム ゾーンを含む) を入力します。
   * **[ファイルのアップロード]**で、問題の解決に役立つと思われるすべてのスクリーンショットやファイルをアップロードします。
     
     ![Support - Problem](./media/active-directory-b2c-support/support-problem.png)
7. **[連絡先情報]** ブレードで、自分の連絡先情報を追加します。 **[作成]**をクリックします。
   
    ![Support - Contact](./media/active-directory-b2c-support/support-contact.png)
8. サポート要求を送信した後は、スタート画面の **[ヘルプとサポート]** をクリックした後 **[サポート要求の管理]** をクリックして、監視できます。

## <a name="known-issue-filing-a-support-request-in-the-context-of-a-b2c-tenant"></a>既知の問題点: B2C テナントのコンテキストでのサポート要求の提出
上で説明した手順 2 を実行せず、自分の B2C テナントのコンテキストでサポート要求を作成しようとすると、次のエラーが表示されます。

> [!IMPORTANT]
> B2C テナントで新しい Azure サブスクリプションにサインアップしないでください。  
> 
> 

![サポート - サブスクリプションなし](./media/active-directory-b2c-support/support-no-sub.png)



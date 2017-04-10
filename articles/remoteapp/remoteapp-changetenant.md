---
title: "Azure RemoteApp で Azure Active Directory テナントを変更する | Microsoft Docs"
description: "Azure RemoteApp に関連付けられている Azure Active Directory テナントの変更方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 20faf169-6e48-428a-8bdd-f231daff19fa
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 5326c779d766e075a54f5ec43f92b6c5c973be8b


---
# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Azure RemoteApp で Azure Active Directory テナントを変更する
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp では Azure Active Directory (Azure AD) を使用してユーザーのアクセスを許可します。 Azure RemoteApp で使用可能な唯一の Azure AD テナントは、Azure サブスクリプションに関連付けられているテナントです。 ポータルの **[設定]** ページで、関連付けられているサブスクリプションを確認できます。 **[サブスクリプション]** タブの **[ディレクトリ]** 列に注目します。

> [!NOTE]
> この変更を完了するには、まず、すべての Azure RemoteApp コレクションの既存の Azure Active Directory テナントからすべてのユーザーを削除します。 これを行うには、Azure ポータルに移動し、 **[Azure RemoteApp]** タブに進み、すべての Azure RemoteApp コレクションを開きます。 **[ユーザー]** タブに進み、現在の Azure Active Directory テナントに属しているユーザーを削除します。 すべての既存の Azure RemoteApp コレクションに対して繰り返します。 この作業を行わないと、コレクションを作成またはパッチできません。
> 
> 

別のテナントを使用する場合は、この手順を使用して、サブスクリプションの関連付けを変更します。

1. ポータルで、Azure RemoteApp コレクションへのアクセス許可を付与した Azure AD ユーザーを削除します。 (これを行う手順については、上の注を参照してください。)
2. サービス管理者として、Microsoft アカウント (以前の Live ID) を設定します。 (サービス管理者になっているかわかりませんか。 **[設定] -> [管理者]** の順にクリックすると、わかります。)それでは、変更方法です。
   
   1. 右上隅のユーザーをクリックし、 **[明細の表示]**をクリックします。
   2. サブスクリプションをクリックします。 次に、新しいページで、下にスクロールし、右にある **[サブスクリプションの詳細を編集]** をクリックします。 (正確に言うと、真ん中の一番下の右です。)
   3. ユーザーの Microsoft アカウントを入力します。このユーザーはサービス管理者にする必要があります。
3. ポータルからサインアウトし、前の手順で指定した Microsoft アカウントを使用して再度サインインします。
4. **[新規]、[App Services]、[Active Directory]、[ディレクトリ]、[カスタム作成]** の順にクリックします。
5. **[ディレクトリ]** の下で、**[既存のディレクトリの使用]** を選択します。 これからサインアウトしなければならないので、 **[サインアウトする準備ができました]**を選択します。
6. 追加するディレクトリのグローバル管理者としてポータルに再度サインインします。 (グローバル管理者になっていなかった場合、サインインしてサインアウトした後にグローバル管理者になります。)
7. サインインすると、サブスクリプションで既存の AD テナントを使用するか問われます。 **[続行]** をクリックし、**[今すぐサインアウト]** をクリックします。
8. 再度サインインし、**[設定] -> [サブスクリプション]** に戻ります。 サブスクリプションを選択し、 **[ディレクトリの編集]**をクリックします。 使用する Azure AD テナントを選択します。

これで新しい Azure AD テナントを使用し、Azure サブスクリプションへのアクセスを制御したり、Azure RemoteApp 内のユーザー アクセスを構成したりできます。




<!--HONumber=Dec16_HO2-->



---
title: "Azure AD テナントを取得する方法 | Microsoft Docs"
description: "アプリケーションを登録および構築するために Azure Active Directory テナントを取得する方法を説明します。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: terrylan
editor: 
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 09/28/2015
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 146d1377a017becdcdcd7fed7b97f07c2cb2bb39
ms.openlocfilehash: 151b9bd8f29a1112c9ad839f2729309dba97c283


---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>Azure Active Directory テナントを取得する方法
Azure Active directory (Azure AD) では、 [テナント](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) は組織を代表するものです。  テナントは、Azure AD サービスの専用インスタンスであり、組織が Azure、Microsoft Intune、Office 365 などの Microsoft クラウド サービスにサインアップしたときに提供されて所有します。  各 Azure AD テナントは、他の Azure AD テナントと区別され分離されています。  

テナントは、企業内のユーザーおよびパスワード、ユーザー プロファイル データ、アクセス許可などのユーザーに関する情報を保持しています。  また、グループ、アプリケーション、および組織とそのセキュリティに関するその他の情報も含まれます。

Azure AD ユーザーがアプリケーションにサインインできるようにするには、自分のテナントにアプリケーションを登録する必要があります。  Azure AD テナントへのアプリケーションの発行は **完全に無料**です。  実際、ほとんどの開発者は、実験、開発、ステージング、テストのために複数のテナントとアプリケーションを作成します。  アプリケーションにサインアップして使用する組織は、高度なディレクトリ機能を利用したければライセンスの購入を選択できます。

それでは、Azure AD テナントを取得するにはどうすればよいでしょうか。  ユーザーが次のどれに該当するかによって、プロセスは少しずつ異なる場合があります。

* [既存の Office 365 サブスクリプションを所有している](#use-an-existing-office-365-subscription)
* [Microsoft アカウントに関連付けられた既存の Azure サブスクリプションを所有している](#use-an-msa-azure-subscription)
* [組織のアカウントに関連付けられた既存の Azure サブスクリプションを所有している](#use-an-organizational-azure-subscription)
* [上記のいずれも所有していず、最初から開始する](#start-from-scratch)

## <a name="use-an-existing-office-365-subscription"></a>既存の Office 365 サブスクリプションを使用する
既に Office 365 サブスクリプションがある場合は、Azure AD テナントも持っています。 O365 アカウントで [Azure Portal](https://portal.azure.com) にサインインし、Azure AD の使用を開始できます。

## <a name="use-an-msa-azure-subscription"></a>MSA Azure サブスクリプションを使用する
以前に個人の Microsoft アカウントで Azure サブスクリプションにサインアップしている場合は、既にテナントを持っています。  [Azure Portal](https://portal.azure.com) にログインすると、自動的に既定のテナントへのログインが行われます。 このテナントは自由に使用できますが、組織の管理者アカウントを作成する必要がある場合があります。

これを行うには、次の手順に従います。  または、新しいテナントを作成し、同様のプロセスでそのテナントに管理者を作成することもできます。

1. 個人アカウントで [Azure Portal](https://portal.azure.com) にログインします
2. ポータルの [Azure Active Directory] セクションに移動します (左側のナビゲーション バーの **[その他のサービス]** の下)
3. 自動的に "既定のディレクトリ" にサインインされるはずですが、そうならない場合は、右上隅のアカウント名をクリックしてディレクトリを切り替えることができます。
4. **[クイック タスク]** セクションで **[ユーザーの追加]** を選択します。
5. [ユーザーの追加] フォームで、次の詳細を指定します。
   
   * 名前: (適切な値を選択)
   * ユーザー名: (この管理者のユーザー名を選択)
   * プロファイル: (名、姓、役職、および部署の適切な値を入力)
   * ロール: グローバル管理者
6. [ユーザーの追加] フォームの入力を完了し、新しい管理ユーザーの一時パスワードを受け取ったら、パスワードを変更するためにこの新しいユーザーでログインする必要があるので、このパスワードを忘れずに記録しておきます。 連絡用電子メールを使用して、ユーザーにパスワードを直接送信することもできます。
7. **[作成]** をクリックして、新しいユーザーを作成します。
8. 一時的なパスワードを変更するには、この新しいユーザー アカウントで [https://login.microsoftonline.com](https://login.microsoftonline.com) にログインし、要求された場合にはパスワードを変更します。

## <a name="use-an-organizational-azure-subscription"></a>組織の Azure サブスクリプションを使用する
以前に組織のアカウントで Azure サブスクリプションにサインアップしている場合は、既にテナントを持っています。  [Azure Portal](https://portal.azure.com) で [その他のサービス]、[Azure Active Directory] に移動すると、テナントが見つかるはずです。  必要に応じて、このテナントを自由に使用できます。 

## <a name="start-from-scratch"></a>最初から行う
上記のいずれにも当てはまらなくても、心配しないでください。  [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) にアクセスして、新しい組織で Azure にサインアップするだけです。  プロセスが完了すると、サインアップ時に選択したドメイン名で専用の Azure AD テナントが作成されます。  [Azure Portal](https://portal.azure.com) の左側にあるナビゲーションで [Azure Active Directory] に移動すると、テナントを見つけることができます。

Azure へのサインアップ プロセスの中で、クレジット カードの詳細を提供する必要があります。  Azure AD でのアプリケーションの発行または新しいテナントの作成には料金は発生しないので安心してください。




<!--HONumber=Dec16_HO2-->



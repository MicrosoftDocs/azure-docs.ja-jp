---
title: "Azure サブスクリプションまたは管理グループを検索する - Azure | Microsoft Docs"
description: "複数のディレクトリを移動して、管理グループとサブスクリプションを確認する方法"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="find-an-azure-subscription-or-management-group"></a>Azure のサブスクリプションまたは管理グループを検索する

Azure でサブスクリプションまたは管理グループが見つからない場合、見ているディレクトリが間違っている可能性があります。 こうした状況は、お使いのアカウントが複数の Azure Active Directory に存在する場合に発生することがあります。 各 [Active Directory は独立している](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence)ので、ディレクトリ間でアクセスは継承されません。      

![ディレクトリの切り替えメニュー](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>ディレクトリの切り替え 
ディレクトリは、Azure Portal で簡単に切り替えることができます。
1.  [Azure Portal](https://portal.azure.com) にサインインします。
2.  画面の右上で自分の名前を選択します。 
3.  メニューの下部に、アクセス権を持っているディレクトリがすべて一覧表示されます。
4.  アクセスするディレクトリの名前を選択します。 

![ディレクトリの切り替えメニュー](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>アセットを使用できない場合 
サブスクリプションまたは管理グループにアクセスしようとした際に "このアセットは使用できません" というエラー メッセージが表示された場合、この項目を表示するための正しいロールを持っていないということになります。  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

アクセスを付与してもらうには、サブスクリプションまたは管理グループの管理者に問い合わせてください。  
* サブスクリプションの場合、[Azure ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) に関するドキュメントをお読みになり、必要なロールの判断に役立ててください。
* 管理グループの場合、現在は RBAC アクセスを使用できません。まもなく使用可能になります。 アクセスを割り当ててもらうには、エンタープライズ ポータルの管理者に連絡してください。   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>管理グループとサブスクリプションを同じディレクトリに格納して、エクスペリエンスを改善 
すべてのものが同じ場所に存在するように、選択したディレクトリにサブスクリプションや管理グループを移動できます。  サブスクリプションと管理グループを同一のディレクトリにまとめておくと、ディレクトリを切り替える必要性が減ると共に、ポリシーも継承できるようになります。  


### <a name="transfer-your-subscriptions"></a>サブスクリプションの移動 
サブスクリプションは、お使いの管理グループに関連付けられているディレクトリに移動できます。 この移動を行うには、登録管理者に、ご自分のサブスクリプションをターゲット ディレクトリ内のアカウントに移動してもらいます。 詳細については、[Enterprise Portal](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription) にサインインしてください。


 







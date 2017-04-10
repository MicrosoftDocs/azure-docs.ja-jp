---
title: "Office 365 ユーザー アカウントで Azure RemoteApp を使用する方法 | Microsoft Docs"
description: "Office 365 ユーザー アカウントで Azure RemoteApp を使用する方法について説明します"
services: remoteapp
documentationcenter: 
author: piotrci
manager: mbaldwin
ms.assetid: aba70fd3-60b0-4b44-9321-1ab18760ccd5
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8744c5a133738fc60b86e7abd4b0d4b1cb28314c


---
# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Office 365 ユーザー アカウントで Azure RemoteApp を使用する方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Office 365 サブスクリプションがある場合、Office 365 サービスへのアクセスに使用されているユーザー名とパスワードが格納された Azure Active Directory もあります。 たとえば、ユーザーが Office 365 ProPlus をアクティブ化した場合、Azure AD に対して認証してライセンスを確認します。 ほとんどのユーザーは、Azure RemoteApp と同じディレクトリを使用したいと考えます。

Azure RemoteApp をデプロイするとき、多くの場合、別の Azure AD と関連付けられた Azure サブスクリプションを使用しています。 Office 365 ディレクトリを使用するには、Azure サブスクリプションをそのディレクトリに移動する必要があります。

Office 365 クライアント アプリケーションをデプロイする方法については、「 [Azure RemoteApp で Office 365 サブスクリプションを使用する方法](remoteapp-officesubscription.md)」を参照してください。

## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>フェーズ 1: 無料の Office 365 Azure Active Directory サブスクリプションを登録する
Azure クラシック ポータルを使用している場合は、「[無料の Azure Active Directory サブスクリプションの登録](https://technet.microsoft.com/library/dn832618.aspx)」の手順に従って、Microsoft Azure 管理ポータルから Azure AD への管理アクセスを取得します。 このプロセスを実行すると、Azure ポータルにログインし、ディレクトリを確認できるようになります。この時点では、Azure RemoteApp に使用している完全な Azure サブスクリプションは別のディレクトリにあるので、あまり情報が表示されません。

この手順で作成した管理者アカウントの名前とパスワードを記憶しておきます。これらの情報はフェーズ 2 で必要になります。

Azure ポータルを使用している場合は、「 [How to register and activate a free Azure Active Directory using Office 365 portal](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/)」を確認します。

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>フェーズ 2: Azure サブスクリプションに関連付けられている Azure AD を変更する
Azure サブスクリプションを、現在のディレクトリから、フェーズ 1 で操作した Office 365 ディレクトリに変更します。

「 [Azure RemoteApp で Azure Active Directory テナントを変更する](remoteapp-changetenant.md)」の手順に従って操作してください。 特に次の手順に気を付けてください。

* 手順 1: このサブスクリプションで Azure RemoteApp (ARA) をデプロイした場合、すべての ARA コレクションからすべての Azure AD ユーザー アカウントを削除してから、他の処理を実行してください。 または、既存のコレクションをすべて削除する方法もあります。
* 手順 2: これは重要な手順です。 Microsoft アカウント (例: @outlook.com) をサブスクリプションのサービス管理者として使用する必要があります。これは、サブスクリプションに関連付けられている既存の Azure AD のユーザー アカウントは使用できないためです。使用すると、別の Azure AD に移動できなくなります。
* 手順 4: 既存のディレクトリを追加すると、そのディレクトリの管理者アカウントでサインインするように求められます。 このとき、必ずフェーズ 1 の管理者アカウントを使用してください。
* 手順 5: サブスクリプションの親ディレクトリを Office 365 ディレクトリに変更します。 最終的に、[設定] -> [サブスクリプション] に、Office 365 ディレクトリの一覧が表示されます。 
  ![サブスクリプションの親ディレクトリを変更します](./media/remoteapp-o365user/settings.png)

この時点で、Azure RemoteApp サブスクリプションは Office 365 Azure AD に関連付けられているので、Azure RemoteApp に既存の Office 365 ユーザー アカウントを使用できます。




<!--HONumber=Dec16_HO2-->



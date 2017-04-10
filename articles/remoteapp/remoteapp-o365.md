---
title: "Azure RemoteApp で Outlook を使用する | Microsoft Docs"
description: "Azure RemoteApp と Office を連携させる方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: f1773baf-8aa1-423c-a2f9-e4679e0463d3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 8c04dc5a91b7859b5d75f919da55846599038f61


---
# <a name="using-office-with-azure-remoteapp"></a>Azure RemoteApp で Office を使用する
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp で Office アプリケーションをホストする場合、Office 365 ProPlus と Office 2013 Professional Plus 試用版の 2 つの選択肢があります。

**これは、より新しい充実した記事に差し替わる予定です。詳しくは、「[Azure RemoteApp で Office 365 サブスクリプションを使用する方法](remoteapp-officesubscription.md)」を参照してください。ここでは、Office 365 + Azure RemoteApp を使用する際に必要な情報を掲載しています。**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Office 365 ProPlus テンプレート イメージを使用して RemoteApp コレクションを作成することができます。 このオプションでは、Office 365 サービスを RemoteApp に拡張することができます。 既存のサブスクリプション プランが必要で、ユーザーはスタンドアロンまたは Office 365 ProPlus サービスを介したライセンスを取得している必要があります。

RemoteApp は、Office 365 共有コンピューターのライセンス認証をサポートします。 共有コンピューターのライセンス認証を有効にし、インストール用に [Office 展開ツール](http://www.microsoft.com/download/details.aspx?id=36778)を使用する場合、Office 365 ProPlus はライセンス認証されることなくインストールされます。 Office 365 を含むコレクションにユーザーがサインインすると、Office によって、ユーザーが Office 365 ProPlus に対してプロビジョニングされているかどうかがチェックされます。 プロビジョニングされている場合、Office によって、Office 365 ProPlus が一時的にライセンス認証されます。このライセンス認証は、ユーザーがサービスからサインアウトするまで継続します。

Office 365 共有コンピューターのライセンス認証を使用するには、[カスタム テンプレート](remoteapp-create-custom-image.md)を作成し、「[リモート デスクトップ サービスを使用して Office 365 ProPlus を展開する](https://technet.microsoft.com/library/dn782858.aspx)」の説明に従って、Office 365 ProPlus をインストールする必要があります。

ユーザーの Office 365 ライセンスは、 [Office 365 管理ポータル](https://portal.office365.com/)で管理できます。 詳細については、「 [Office 365 プランのオプション](http://technet.microsoft.com/library/office-365-plan-options.aspx)」を参照してください。  

## <a name="office-2013-professional-plus-trial"></a>Office 2013 Professional Plus 評価版
RemoteApp の 30 日間の無償試用期間中は、Office 2013 Professional Plus (評価版) テンプレート イメージを使用して RemoteApp コレクションを作成できます。 Azure Active Directory 作業アカウントまたは Microsoft アカウントを使用して、この評価版のコレクションをユーザーに割り当てることができます。 追加のサブスクリプションは必要ありません。

これは Office で RemoteApp を試用できる便利なオプションです。 ただし、このオプションは評価とテストのみを目的としています。 Office 2013 Professional Plus (評価版) のテンプレート イメージを使用して作成した RemoteApp コレクションは、実稼働モードに切り替えることはできず、試用期間の終了後に無効になります。

## <a name="switching-from-trial-to-production"></a>評価版から実稼働環境への切り替え
30 日間の無償評価版を開始すると、ポータルの [RemoteApp] セクション内のメモに有料アカウントに切り替えるまでの残りの試用期間が表示されます。 自分のアカウントをアクティブ化し、このメモのリンクを使用して実稼働モードに切り替えることができます。

自分のアカウントをアクティブ化すると、アカウント内のすべての RemoteApp コレクションに影響します。

* Windows Server 2012 R2 または Office 365 ProPlus のテンプレート イメージで実行されているコレクションは実稼働環境にシームレスに移行します。 実行中のセッションを含むすべてのユーザー データおよび設定は、そのまま移行されます。
* カスタム テンプレート イメージをアップロードしている場合は、それらのイメージを使用しているコレクションもシームレスに移行されます。
* Office 2013 Professional Plus (評価版) のテンプレート イメージは、評価のみを目的としています。 このテンプレート イメージで実行されているコレクションは、実稼働環境に移行することはできません。 これらが "無効" の状態になります。

試用期間の終了後に実稼働モードに切り替えないと、RemoteApp コレクションは無効になります。 データは保存されます - 設定とユーザーのデータはあと 90 日間保存されますので、サービスをアクティブ化してデータを失うことなく、実稼働モードに切り替えることができます。




<!--HONumber=Dec16_HO2-->



---
title: "Azure RemoteApp テンプレート イメージの内容 | Microsoft Docs"
description: "Azure RemoteApp に含まれるテンプレート イメージについて説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 7f8442b2-81da-421e-a453-aa53ba2066b7
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: fcc53e4073a6957fae263cfb5d38023ed9710d21
ms.openlocfilehash: 21ae6458d1d65698c0c8fc7081696cb7406a918c


---
# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Azure RemoteApp テンプレート イメージの内容
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp サブスクリプションには、次の 3 つのテンプレート イメージが含まれています。

* Windows Server 2012
* Microsoft Office 365 ProPlus (Office 365 サブスクリプションが必要)
* Microsoft Office 2013 Professional Plus (評価版のみ)

> [!IMPORTANT]
> Azure RemoteApp サブスクリプションを取得すると、イメージ内のソフトウェアへのアクセス権が付与されます。ただし、別途サブスクリプションが必要な Office 365 ProPlus と、運用環境では使用できない Office 2013 を除きます。 つまり、テンプレート イメージ上のプログラムまたはアプリをユーザーと共有できます。 たとえば、Windows Server 2012 R2 のイメージを使用するコレクションを作成する場合、ユーザーが RemoteApp でアクセスできる System Center Endpoint Protection を発行できます。
> 
> 詳細については、「 [How does licensing work in Azure RemoteApp?](remoteapp-licensing.md) 」を参照してください。 また、Office のライセンス情報については、「 [Azure RemoteApp で Office を使用する](remoteapp-o365.md) 」を参照してください。
> 
> 

各イメージが含んでいるものについては、詳細を参照してください。

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("バニラ イメージ")
このイメージは Microsoft Windows Server 2012 R2 Datacenter オペレーティング システムに基づいており、Azure RemoteApp テンプレート イメージの要件を満たすために、次のようなロールと機能がインストールされています。

* .NET Framework 4.5、3.5.1、3.5
* デスクトップ エクスペリエンス
* インクと手書きサービス
* メディア ファンデーション
* リモート デスクトップ セッション ホスト
* Windows PowerShell 4.0
* Windows PowerShell ISE
* WoW64 サポート

このイメージには次のようなアプリケーションがインストールされています。

* Adobe Flash Player
* Microsoft Silverlight
* Microsoft System Center 2012 Endpoint Protection
* Microsoft Windows Media Player

## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (サブスクリプションが必要)
Office 365 は最もリクエストの多いアプリケーションなので、連携する "カスタム" イメージを作成しました。

このイメージは、バニラ イメージの拡張機能であり、Windows Server 2012 R2 のイメージで説明するコンポーネントに加え、次の Microsoft Office 365 ProPlus を含んでいます。

* Access
* Excel
* Lync
* OneNote
* OneDrive for Business (同期エージェントを Azure RemoteApp で使用することはサポートされていません)
* Outlook
* PowerPoint
* Word
* Microsoft Office Proofing Tools

また、イメージにも Visio Pro と Project Pro が含まれています。

次のアプリケーションも同様にインストールされます。

* SQL Native Client
* ODBC ドライバー
* SQL Server データ マイニング クライアント
* MasterDataServices クライアント
* Microsoft Publisher
* Power Query
* Power Map

Office 365 ProPlus のアプリのすべての機能は、Office 365 ProPlus のプランを持つユーザーのみが使用できます。 Office 365 のサブスクリプション プランの詳細については、[Office 365 のサービス プラン](http://technet.microsoft.com/library/office-365-plan-options.aspx)に関するページを参照してください。 まだ不明な点がある場合は、 [Office 365 と RemoteApp](remoteapp-o365.md) の連携に関する情報を参照してください。 また、新しい記事「[Azure RemoteApp で Office 365 サブスクリプションを使用する方法](remoteapp-officesubscription.md)」もご確認ください。

Office 365 ProPlus、Visio Pro、Project Pro のライセンスは個別に取得する必要があります (それぞれ独自のライセンスを持ちます)。

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (評価版のみ)
無料試用期間中には、Office 2013 のイメージを使用してサービスをテストすることができます。

このイメージは、バニラ イメージの拡張機能であり、Windows Server 2012 R2 のイメージで説明するコンポーネントに加え、インストールされている次の Microsoft Office 2013 Professional Plus のコンポーネントを含んでいます。

* Access
* Excel
* Lync
* OneNote
* OneDrive for Business (同期エージェントを Azure RemoteApp で使用することはサポートされていません)
* Outlook
* PowerPoint
* Project
* Visio
* Word
* Microsoft Office Proofing Tools

> [!IMPORTANT]
> **法的情報:** このイメージには、Microsoft Office のライセンスは含まれていないため、" *運用環境では使用できません*"。 Office 2013 Professional Plus イメージは評価版を使用する目的でのみ提供されます。 運用環境で Azure RemoteApp の Office アプリケーションを使用する場合は、Office 365 ProPlus のイメージを使用する必要があります。 Office のライセンスの詳細については、 [Azure RemoteApp での Office 365 の使用](remoteapp-o365.md)
> 
> 




<!--HONumber=Dec16_HO1-->



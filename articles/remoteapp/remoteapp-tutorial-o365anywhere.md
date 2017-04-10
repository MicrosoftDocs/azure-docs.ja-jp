---
title: "Azure RemoteApp を使用して、どのデバイスでも同じ Office 365 のエクスペリエンスを得るには | Microsoft Docs"
description: "Azure RemoteApp を使用して、ユーザーと任意の Office 365 アプリを共有する方法について説明します。"
services: remoteapp
documentationcenter: 
author: guscatalano
manager: mbaldwin
editor: 
ms.assetid: 0c971ce9-7d45-4cfb-9737-15b6706047e8
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: guscatal;elizapo
translationtype: Human Translation
ms.sourcegitcommit: 300ee477a00cdd463a2abf87dd73550777fd9e05
ms.openlocfilehash: 13634bec8abbefdf941066dece8390f2fd064422


---
# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Azure RemoteApp を使用して、どのデバイスでも同じ Office 365 のエクスペリエンスを得るには
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

この記事では、会社内の任意のデバイスで Office 365 をデプロイする方法を説明します。 ユーザーには、Android、Apple、Windows のいずれからでも、同じ機能と UI 操作が提供されます。

これは、Azure RemoteApp を使用し、ユーザーが接続できる Azure のスケーラブルな仮想マシンで Office 365 をホストすることにより実現されます。 この仮想マシンのセットを "クラウド コレクション" と呼びます。

## <a name="create-a-cloud-collection"></a>Create a cloud collection (クラウド コレクションを作成する)
まず、Azure アカウントを作成してから、左側にあるリンクをクリックして **RemoteApp** に移動します。
![Azure ポータルでの Azure RemoteApp の表示](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

次に、下部にある **[新規]** をクリックし、コレクションを "簡易作成" します。 名前、リージョン、サブスクリプション、プラン、提供されている "Office Proffesional 2013" イメージを指定します。
![[作成] ダイアログ](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

フォームを完了すると、コレクションの作成プロセスが開始されます。 これには 1 時間ほどかかる場合があります。

![待機中](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

プロセスが完了すると、以下のようになります。 **[発行]** をクリックすると、ほとんどの Office アプリケーションが既に発行されていることを確認できます。
![作成されたコレクション](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![発行されたアプリ](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

この時点で、 **[ユーザー アクセス]**をクリックし、このコレクションへのアクセスが可能なユーザーを追加できます。
![ユーザー アクセスの構成](./media/remoteapp-tutorial-o365anywhere/6-user.png)

それでは、Office 365 への接続を試してみましょう。

## <a name="connect-to-office-365"></a>Office 365 への接続
[https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/) に移動します。下へスクロールして **[Download clients (クライアントのダウンロード)]** をクリックし、使用中のデバイスに Azure RemoteApp クライアントをインストールします。 次のスクリーンショットは、Windows の場合です。

アプリケーションが開始されると、Microsoft アカウント (以前の "Live ID") でサインインするように求められます。ここでは、Azure アカウントと同じアカウントを使用してください。 サインインすると、新しい招待に関する通知が表示されます。これをクリックすると、次のようなリストが表示されます。 Azure アカウント所有者の電子メールと一致する招待を承諾してください。

![新しい招待](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

新しい招待がある場合、このように表示されます。

![アプリケーションの承諾](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

招待を承諾すると、Azure RemoteApp クライアントにあるすべての Office アプリが表示されます。

![アプリのリスト](./media/remoteapp-tutorial-o365anywhere/9-work.png)

そのいずれかをクリックすると、アプリケーションが Azure 仮想マシンで開始されます。これですべて完了です。 機能を有効にご活用ください。

![開始中](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)




<!--HONumber=Dec16_HO1-->



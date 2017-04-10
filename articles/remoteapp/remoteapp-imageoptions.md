---
title: "Azure RemoteApp イメージの作成 | Microsoft Docs"
description: "Azure RemoteApp のイメージ作成に使用できるオプションについて説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: cb0f9424-6185-45a1-abe9-c23f1edf34f2
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 12f27315348040ac897c1ff286b54d38e9950c7d


---
# <a name="create-an-azure-remoteapp-image"></a>Azure RemoteApp イメージの作成
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp は、イメージを使用してユーザーと共有しているアプリを保持します。 (Azure はユーザーのイメージを取得し、それを使用して VM を作成します。ユーザーは、Azure RemoteApp にサインインするとそのイメージにアクセスします。)クラウド型かハイブリッド型かに関係なく、選択したアプリケーションの Azure RemoteApp コレクションを作成するには、まず、これらのアプリケーションがインストールされた状態のイメージを作成します。 次に、そのイメージを使用するコレクションを作成し、コレクションにユーザーを割り当てて、それらのユーザーにアプリを発行します。

イメージの作成または使用には、いくつかのオプションあります。 イメージの基本的な [要件](remoteapp-imagereqs.md) として、 イメージが Windows Server 2012 R2 を実行すること、およびリモート デスクトップ セッション ホスト (RDSH) のロールがインストールされていることが必要です。 興味深いのは、その取得方法です。

イメージの場合、次のオプションがあります。

* [Azure 仮想マシンに基づくイメージ](remoteapp-image-on-azurevm.md)をインポートして使用できます。 これは、カスタム設定を必要とする基幹業務アプリに最適です。 イメージをカスタマイズして、アプリ用に動作させることができます。
* [カスタム イメージを作成してアップロード](remoteapp-create-custom-image.md)できます。 オンプレミスのリモート デスクトップ サービス デプロイに使用するイメージが既にある場合は、これが最適です。
* RemoteApp サブスクリプションに含まれる [テンプレート イメージ](remoteapp-images.md) のいずれか 1 つを使用できます。 これらのイメージは、RemoteApp チームによって作成および管理されているもので、イメージに含まれる標準的なアプリケーション (Office スイートなど) をユーザーが使用できます。 なお、運用設定では Office 365 Pro Plus イメージのみ使用可能です。

イメージの取得および作成方法に関わりなく、 [アプリ要件](remoteapp-appreqs.md) を正しく理解し、アプリが RemoteApp で正常に動作することを確認してください。 次の手順は、[クラウド](remoteapp-create-cloud-deployment.md) コレクションまたは[ハイブリッド](remoteapp-create-hybrid-deployment.md) コレクションの作成です。




<!--HONumber=Nov16_HO3-->



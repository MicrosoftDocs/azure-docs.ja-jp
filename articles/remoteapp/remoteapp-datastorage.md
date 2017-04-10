---
title: "カスタム イメージに Azure RemoteApp の機密データを格納しない | Microsoft Docs"
description: "Azure RemoteApp のカスタム イメージにデータを格納するためのガイドラインについて説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 5a19903b-15f9-49d9-9bc1-ae80f2671aa1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 6cc74e3d3bd704dab1a43b66374b51c1f3e2a0a2


---
# <a name="never-store-sensitive-data-on-custom-images"></a>カスタム イメージに機密データを格納しない
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp で独自のアプリケーションをホストする場合、最初の手順はカスタム イメージの作成です。 ここではカスタム イメージを使用して VM インスタンスを作成し、ユーザーにアプリを提供します。 カスタム イメージには、SQL データベース、人事ファイル、QuickBooks 会社ファイルなどの特殊なデータ ファイルなど、失っても構わないアプリケーション データ、機密ではないデータなどのみが含まれます。 すべての機密データは、ファイル サーバー、別の Azure VM、または SQL Azure 上の Azure RemoteApp とは別の場所に配置する必要があります。 イメージは、データ ソースに接続してデータを提示するアプリケーションをホストする作業のみを行います。 詳細については、「 [Azure RemoteApp イメージの要件](remoteapp-imagereqs.md) 」を参照してください。 

機密データを格納すべきではない理由を理解するには、Azure RemoteApp のしくみを理解する必要があります。 コレクションが作成または更新されると、背後でイメージの複数の複製またはコピーが作成されます。 これらすべての VM インスタンスは、カスタム イメージの正確なレプリカです。ユーザーがアプリケーションを起動すると、それらの VM インスタンスのいずれかに接続されます。 ただし、同じインスタンスは保証されておらず、永続的ではないため、問題にはなりません。 アプリケーションをホストする VM インスタンスは永続的ではなく、たとえば、コレクションの更新中などに破棄または削除される可能性があります。 

コレクションがプロビジョニングされ、ユーザーが VM への接続を開始すると、ユーザー データは、[ユーザー プロファイル ディスク (UPD)](remoteapp-upd.md) (c:\users\<userprofile> 内のユーザー プロファイル) を呼び出す VHD 内の別の記憶域に保存されているため、維持および保護されます。 アプリケーションが起動すると、オペレーティング システムによって UPD はマウントされ、ローカル ユーザー プロファイルと同様に扱われます。 詳細については、「 [Azure RemoteApp によるユーザー データと設定の保存方法](remoteapp-upd.md)」を参照してください。

イメージ内に格納しない方がよいデータの例:

* アクセスするユーザーの共有データ
* SQL DB または QuickBooks DB
* D:\ 内のすべてのデータ

すべてのユーザーの UPD にコピーする既定のプロファイル内に配置できるデータの例:

* ユーザーごとの構成ファイル
* ユーザーが UPD に保存する必要があるスクリプト

重要なポイント:

* カスタム イメージの作成時には、機密データをイメージに格納しないでください。データが失われる可能性があります。
* 機密データは、常に別のファイル サーバー、別の Azure VM、クラウドに格納し、Azure RemoteApp 内のアプリケーションをホストする VM インスタンスとは常に別に格納することをお勧めします。 
* ユーザー データはユーザー プロファイル ディスク (UPD) に保存され、維持されます




<!--HONumber=Dec16_HO2-->



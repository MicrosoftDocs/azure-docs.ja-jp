---
title: "名前付き認証資格情報の設定 | Microsoft Docs"
description: "Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視したりできるように、Visual Studio が Azure への要求の認証に使用できる資格情報を指定する方法について説明します。"
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 8/22/2017
ms.author: kraigb
ms.openlocfilehash: 953b1aa459ddf5b7be00b9d32432e6dda97143e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-named-authentication-credentials"></a>名前付き認証資格情報の設定
Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視するには、Azure への要求を認証するために使用できる資格情報を指定する必要があります。 Visual Studio には、こうした資格情報を指定するためのサインインのインターフェイスがいくつか設けられています。 たとえば、サーバー エクスプローラーから **[Azure]** ノードのショートカット メニューを開き、**[Microsoft Azure サブスクリプションへの接続]** を選択できます。 サインインすると、Azure アカウントに関連付けられているサブスクリプション情報が Visual Studio で利用できるようになります。 それ以上何もする必要はありません。

Azure Tools では、サブスクリプション ファイル (.publishsettings ファイル) を使用して資格情報を指定する従来の方法もサポートしています。 この記事では、Azure SDK 2.2 で引き続きサポートされているこの方法について説明します。

Azure に対する認証には、次の項目が必要です。

* サブスクリプション ID
* 有効な X.509 v3 証明書

> [!NOTE]
> X.509 v3 証明書のキーの長さは 2,048 ビット以上である必要があります。 この要件を満たしていない証明書や無効な証明書はすべて拒否されます。
>
>

Visual Studio では、サブスクリプション ID が証明書データと共に資格情報として使用されます。 該当する資格情報はサブスクリプション ファイル (.publishsettings ファイル) 内で参照され、このファイルに証明書の公開キーが格納されます。 サブスクリプション ファイルには、複数のサブスクリプションの資格情報を含めることができます。

この記事で後述するように、サブスクリプション情報は、**[新しいサブスクリプション]** または **[サブスクリプションの編集]** ダイアログ ボックスから編集できます。

証明書を自分で作成する場合は、[Azure の管理証明書の作成とアップロード](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)に関する記事に記載された手順を参照し、[Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)に証明書を手動でアップロードします。

> [!NOTE]
> これらは、ユーザーのクラウド サービスを管理するために Visual Studio が必要とする資格情報であり、Azure ストレージ サービスに対する要求を認証するために必要な資格情報とは異なります。
>
>

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Visual Studio で認証資格情報をインポート、設定、または編集する

1. サーバー エクスプローラーで、**[Azure]** ノードのショートカット メニューを開き、**[サブスクリプションの管理およびフィルター]** を選択します。
2. **[証明書]** タブを選択し、次のいずれかの方法を使用します。

    * **[インポート]** を選択して、**[Microsoft Azure サブスクリプションのインポート]** ダイアログ ボックスを開きます。 このダイアログ ボックスで、現在読み込まれているサブスクリプションのサブスクリプション ファイルをダウンロードし、ダウンロード先の場所を参照して、認証用にインポートできます。
    * **[新規]** を選択して、**[新しいサブスクリプション]** ダイアログ ボックスを開きます。 このダイアログ ボックスで、認証に使用する新しいサブスクリプションを設定できます。
    * (アクティブなサブスクリプションの選択後) **[編集]** を選択して、**[サブスクリプションの編集]** ダイアログ ボックスを開きます。 このダイアログ ボックスで、認証に使用する既存のサブスクリプションを編集できます。 

次の手順は、 **[新しいサブスクリプション]** ダイアログ ボックスが開いていることを前提にしています。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Visual Studio で認証資格情報を設定するには
1. **[認証用の既存の証明書を選択します]** の一覧で証明書を選択します。
2. **[完全なパスのコピー]** リンクをクリックします。 証明書 (.cer ファイル) のパスがクリップボードにコピーされます。

   > [!IMPORTANT]
   > Visual Studio から Azure アプリケーションを発行するには、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にこの証明書をアップロードする必要があります。
   >
   >
3. 証明書を Azure Portal にアップロードするには:

   a. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)を開きます。
   
   b. メッセージが表示されたら、ポータルにサインインし、**[設定]** > **[管理証明書]** に移動します。
   
   c. **[管理証明書]** ウィンドウで **[アップロード]** を選択します。
   
   d. Azure サブスクリプションを選択し、作成した .cer ファイルの完全なパスを貼り付けて、**[アップロード]** を選択します。

## <a name="next-steps"></a>次のステップ
* [Web Apps の概要](https://docs.microsoft.com/azure/app-service/)
* [Azure App Service へのアプリのデプロイ](https://docs.microsoft.com/en-us/azure/app-service/app-service-deploy-local-git) 
* [Visual Studio を使用した Web ジョブの展開](https://docs.microsoft.com/en-us/azure/app-service/websites-dotnet-deploy-webjobs)
* [クラウド サービスの作成とデプロイ](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-create-deploy-portal)
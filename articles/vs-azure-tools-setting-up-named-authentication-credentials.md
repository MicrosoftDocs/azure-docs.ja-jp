---
title: "名前付き認証資格情報の設定 | Microsoft Docs"
description: "Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視したりする際に、送信する要求の本人性を Visual Studio が Azure に証明するための資格情報を指定する方法について説明します。 "
services: visual-studio-online
documentationcenter: na
author: TomArcher
manager: douge
editor: 
ms.assetid: 61570907-42a1-40e8-bcd6-952b21a55786
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/18/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 01623fa76175091439d5a571fb8b8f96aee01c4c
ms.openlocfilehash: 03875b4215943a8bbabeb15610776a221cbf7b71


---
# <a name="setting-up-named-authentication-credentials"></a>名前付き認証資格情報の設定
Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視するには、Azure への要求を認証するために使用できる資格情報を指定する必要があります。 Visual Studio には、こうした資格情報を指定するためのサインインのインターフェイスがいくつか設けられています。 たとえば、サーバー エクスプローラーから **Azure** ノードのショートカット メニューを開いて **[Azure への接続]** を選択できます。 サインインすると、Azure のアカウントに関連付けられたサブスクリプション情報が Visual Studio で利用できるようになるため、それ以上の設定は不要です。

Azure Tools では、サブスクリプション ファイル (.publishsettings ファイル) を使用して資格情報を指定する従来の方法もサポートされます。 このトピックでは、Azure SDK 2.2 で現在もサポートされているこの方法について説明します。

Azure に対する認証には、次の項目が必要です。

* サブスクリプション ID
* 有効な X.509 v3 証明書

> [!NOTE]
> X.509 v3 証明書のキーの長さは 2,048 ビット以上にする必要があります。 この要件を満たさない証明書や無効な証明書は、Azure によってすべて拒否されます。
>
>

Visual Studio では、サブスクリプション ID が証明書データと共に資格情報として使用されます。 該当する資格情報はサブスクリプション ファイル (.publishsettings ファイル) 内で参照され、このファイルに証明書の公開キーが格納されます。 サブスクリプション ファイルには、複数のサブスクリプションの資格情報を含めることができます。

サブスクリプション情報は、**[新しいサブスクリプション/サブスクリプションの編集]** ダイアログ ボックスから編集することができます。この点については、このトピックの中で説明しています。

証明書を自分で作成する場合は、[Azure の管理証明書の作成とアップロード](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx)に関する記事に記載された手順を確認し、[Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)に証明書を手動でアップロードします。

> [!NOTE]
> これらは、ユーザーのクラウド サービスを管理するために Visual Studio が必要とする資格情報であり、Azure ストレージ サービスに対する要求を認証するために必要な資格情報とは異なります。
>
>

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Visual Studio の認証資格情報の変更またはエクスポート
認証資格情報の設定、変更、エクスポートは、**[新しいサブスクリプション]** ダイアログ ボックスで行うこともできます。次のいずれかの操作を行った場合にこのダイアログ ボックスが表示されます。

* **サーバー エクスプローラー**で **Azure** ノードのショートカット メニューを開き、**[サブスクリプションの管理]**、**[証明書]** タブの順に選択し、**[新規]** または **[編集]** をクリックします。
* **Azure アプリケーションの公開**ウィザードから Azure クラウド サービスを発行する場合、**[サブスクリプションの選択]** の一覧から **[管理]** を選択し、[証明書] タブを選択してから、**[新規]** または **[編集]** をクリックします。

次の手順は、 **[新しいサブスクリプション]** ダイアログ ボックスが開いていることを前提にしています。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Visual Studio で認証資格情報を設定するには
1. **[認証用の既存の証明書を選択します]** の一覧で証明書を選択します。
2. **[完全なパスのコピー]** をクリックします。 証明書 (.cer ファイル) のパスがクリップボードにコピーされます。

   > [!IMPORTANT]
   > Visual Studio から Azure アプリケーションを発行するには、この証明書を [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)にアップロードする必要があります。
   >
   >
3. 証明書を [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)にアップロードするには、次の手順に従います。

   1. [Azure ポータル] リンクを選択します。

        [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885) が開きます。
   2. [Azure クラシック ポータル](http://go.microsoft.com/fwlink/?LinkID=213885)にサインインし、 **[Cloud Services]** ボタンをクリックします。
   3. 必要なクラウド サービスを選択します。

       そのサービスのページが表示されます。
   4. **[証明書]** タブで **[アップロード]** をクリックします。
   5. 作成した .cer ファイルのフル パスを貼り付け、指定したパスワードを入力します。



<!--HONumber=Dec16_HO2-->



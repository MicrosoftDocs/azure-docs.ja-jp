---
title: "名前付き認証資格情報の設定 | Microsoft Docs"
description: "Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視したりする際に、送信する要求の本人性を Visual Studio が Azure に証明するための資格情報を指定する方法について説明します。 "
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
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: c486676a70e195ec85ad40540ea4b7caaa86bc48
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="setting-up-named-authentication-credentials"></a>名前付き認証資格情報の設定
Visual Studio から Azure にアプリケーションを発行したり、既存のクラウド サービスを監視するには、Azure への要求を認証するために使用できる資格情報を指定する必要があります。 Visual Studio には、こうした資格情報を指定するためのサインインのインターフェイスがいくつか設けられています。 たとえば、サーバー エクスプローラーから **Azure** ノードのショートカット メニューを開いて **[Microsoft Azure サブスクリプションへの接続]** を選択できます。サインインすると、Azure のアカウントに関連付けられたサブスクリプション情報が Visual Studio で利用できるようになるため、それ以上の設定は不要です。

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

## <a name="import-set-up-or-edit-authentication-credentials-in-visual-studio"></a>Visual Studio で認証資格情報をインポート、設定、または編集する
認証資格情報のインポート、設定、編集は、**[新しいサブスクリプション]** ダイアログ ボックスで行うことができます。このダイアログ ボックスは次の操作を実行すると表示されます。

* **サーバー エクスプローラー**で **Azure** ノードのショートカット メニューを開き、**[サブスクリプションの管理およびフィルター]**、**[証明書]** タブの順に選択し、次のいずれかを実行します。

    * **[インポート]** を選択して [Microsoft Azure サブスクリプションのインポート] ダイアログを開きます。このダイアログで、現在読み込まれているサブスクリプションのサブスクリプション ファイルをダウンロードし、ダウンロード先の場所を参照して、認証用にインポートできます。
    * **[新規]** を選択して [新しいサブスクリプション] ダイアログを開きます。このダイアログで新しいサブスクリプションを設定して、認証に使用できます。
    * アクティブなサブスクリプションを選択した後、**[編集]** を選択して [サブスクリプションの編集] ダイアログを開きます。このダイアログで既存のサブスクリプションを編集して、認証に使用できます。 

次の手順は、 **[新しいサブスクリプション]** ダイアログ ボックスが開いていることを前提にしています。

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Visual Studio で認証資格情報を設定するには
1. **[認証用の既存の証明書を選択します]** の一覧で証明書を選択します。
2. **[Copy the full path]\(完全なパスのコピー\)** リンクをクリックします。 証明書 (.cer ファイル) のパスがクリップボードにコピーされます。

   > [!IMPORTANT]
   > Visual Studio から Azure アプリケーションを発行するには、[Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) にこの証明書をアップロードする必要があります。
   >
   >
3. 証明書を Azure Portal にアップロードするには:

   1. [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040)を開きます。
   2. メッセージが表示されたら、ポータルにサインインし、**[設定]**、**[管理証明書]** の順に移動します。
   3. [管理証明書] ウィンドウで **[アップロード]** を選択します。
   4. Azure サブスクリプションを選択し、作成した .cer ファイルの完全なパスを貼り付けて、**[アップロード]** を選択します。


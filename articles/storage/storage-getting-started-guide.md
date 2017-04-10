---
title: "Azure Storage を&5; 分で使い始める | Microsoft Docs"
description: "Azure Storage クイック スタート、Visual Studio、Azure ストレージ エミュレーターを使用して、Microsoft Azure の BLOB、テーブル、キューをすばやく導入します。 初めての Azure Storage アプリケーションを&5; 分で実行します。"
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 582f76f8-c814-4a69-8a5c-1fd0e0d5d8f2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 02/23/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: a8e5d36d31aabc9226206f52d8543566c5218494
ms.openlocfilehash: 6c833cd40439fef1fe9cca2591d36a6c49630579
ms.lasthandoff: 02/23/2017


---
# <a name="get-started-with-azure-storage-in-five-minutes"></a>Azure Storage を&5; 分で使い始める
## <a name="overview"></a>Overview
Azure Storage を使用した開発は簡単です。 このチュートリアルでは、Azure Storage アプリケーションを短時間で稼動させる方法について説明します。 Azure SDK for .NET に付属するクイック スタート テンプレートを使用します。 これらのクイック スタートには、すぐに実行できるコードが含まれています。今回はそのコードを使って、Azure Storage の基本的なプログラミング シナリオのデモをいくつか実行します。

コードの説明に入る前に Azure Storage の詳細について学習するには、「[次のステップ](#next-steps)」をご覧ください。

## <a name="prerequisites"></a>前提条件
開始する前に、次の前提条件を満たす必要があります。

1. アプリケーションをコンパイルし、ビルドするには、コンピューターに [Visual Studio 2015](https://www.visualstudio.com/) 以降をインストールしておく必要があります。
2. 最新バージョンの [Azure SDK for .NET](https://azure.microsoft.com/downloads/) をインストールします。 SDK には、Azure QuickStart サンプル プロジェクト、Azure ストレージ エミュレーター、および [Azure Storage Client Library for .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)が含まれます。
3. [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) がコンピューターにインストールされていることを確認します。このチュートリアルで使用する Azure QuickStart サンプル プロジェクトで必要です。

    コンピューターにどのバージョンの .NET Framework がインストールされているか不明な場合、「 [方法: インストールされている .NET Framework バージョンを確認する](https://msdn.microsoft.com/vstudio/hh925568.aspx)」を参照してください。 または、**[スタート]** をクリックするか Windows キーを押し、「**コントロール パネル**」と入力します。 次に、**[プログラム]** > **[プログラムと機能]** をクリックして、インストールされているプログラムの一覧に .NET Framework 4.5 が含まれることを確認します。
4. Azure サブスクリプションと Azure ストレージ アカウントが必要になります。

   * Azure サブスクリプションを入手するには、[無料試用版](https://azure.microsoft.com/pricing/free-trial/)、[購入オプション](https://azure.microsoft.com/pricing/purchase-options/)、[メンバー プラン](https://azure.microsoft.com/pricing/member-offers/) (MSDN、Microsoft Partner Network、BizSpark、その他の Microsoft プログラムのメンバーの場合) の各ページをご覧ください。
   * Azure でストレージ アカウントを作成するには、「 [ストレージ アカウントの作成方法](storage-create-storage-account.md#create-a-storage-account)」をご覧ください

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>クラウドの Azure Storage で最初の Azure Storage アプリケーションを実行する
アカウントができたら、Visual Studio で Azure クイック スタート サンプル プロジェクトの&1; つを使って簡単な Azure Storage アプリケーションを作成できます。 このチュートリアルでは、**Azure Storage: Blobs**、**Azure Storage: Files**、**Azure Storage: Queues**、**Azure Storage: Tables** など、Azure Storage のサンプル プロジェクトに重点を置いて説明します。

1. Visual Studio を起動します。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストール済み]** > **[テンプレート]** > **[Visual C#]** > **[クラウド]** > **[クイック スタート]** > **[Data Services]** をクリックします。
   
   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **Azure Storage: Blobs**、**Azure Storage: Files**、**Azure Storage: Queues**、**Azure Storage: Tables** のいずれかのテンプレートを選択します。
   
   b. ターゲット フレームワークとして **[.NET Framework 4.5]** が選択されていることを確認します。
   
   c. プロジェクトの名前を指定し、次のように新しい Visual Studio ソリューションを作成します。

    ![Azure Quick Starts][Image1]

アプリケーションを実行する前に、ソース コードを確認できます。 コードを確認するには、Visual Studio の **[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。 その後、Program.cs ファイルをダブルクリックします。

次に、サンプル アプリケーションを実行します。

1. Visual Studio で、**[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。 **App.config** ファイルを開き、次の Azure ストレージ エミュレーターの接続文字列をコメントアウトします。

   `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2. App.config ファイルの Azure Storage サービスの接続文字列をコメント解除し、`[AccountName]` と `[AccountKey]` をご利用のアカウントの資格情報で置き換えてください。

   `<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

   ストレージ アカウントのアクセス キーを取得するには、「 [ストレージ アクセス キーの管理](storage-create-storage-account.md#manage-your-storage-access-keys)」を参照してください。
3. App.config ファイルにストレージ アカウント名とアクセス キーを入力した後、**[ファイル]** メニューの **[すべてを保存]** をクリックし、すべてのプロジェクト ファイルを保存します。
4. **[ビルド]** メニューの **[ソリューションのビルド]** をクリックします。
5. **[デバッグ]** メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを実行します。

## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Azure ストレージ エミュレーターで最初の Azure Storage アプリケーションをローカルに実行する
[Azure ストレージ エミュレーター](storage-use-emulator.md) では、Azure の BLOB、Queue、Table サービスを開発用にエミュレートするローカル環境が利用できます。 ストレージ エミュレーターを使用すると、Azure サブスクリプションまたはストレージ アカウントを作成せずにストレージ アプリケーションをローカルでテストでき、コストもかかりません。

それでは、Visual Studio で Azure Quick Starts サンプル プロジェクトの&1; つを使用する簡単な Azure Storage アプリケーションを作成しましょう。 このチュートリアルでは、**Azure Blob Storage**、**Azure Table Storage**、**Azure Queue Storage** の各サンプル プロジェクトに着目します。

1. Visual Studio を起動します。
2. **[ファイル]** メニューの **[新しいプロジェクト]** をクリックします。
3. **[新しいプロジェクト]** ダイアログ ボックスで、**[インストール済み]** > **[テンプレート]** > **[Visual C#]** > **[クラウド]** > **[クイック スタート]** > **[Data Services]** をクリックします。
    
    a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが Yardi eLearning アプリケーションへのサインオンに使用する URL を入力します。 **Azure Storage: Blobs**、**Azure Storage: Files**、**Azure Storage: Queues**、**Azure Storage: Tables** のいずれかのテンプレートを選択します。
    
    b. ターゲット フレームワークとして **[.NET Framework 4.5]** が選択されていることを確認します。
    
    c. プロジェクトの名前を指定し、次のように新しい Visual Studio ソリューションを作成します。

    ![Azure Quick Starts][Image1]

4. Visual Studio で、**[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。 Azure ストレージ アカウントを既に&1; つ追加している場合は、App.config ファイルを開き、お使いのストレージ アカウントの接続文字列をコメントアウトします。 その後、Azure ストレージ エミュレーターの接続文字列をコメント解除します。

   `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

アプリケーションを実行する前に、ソース コードを確認できます。 コードを確認するには、Visual Studio の **[表示]** メニューにある **[ソリューション エクスプローラー]** を選択します。 その後、Program.cs ファイルをダブルクリックします。

次に、Azure ストレージ エミュレーターでサンプル アプリケーションを実行します。

1. **[スタート]** ボタンを押すか Windows キーを押し、 *Microsoft Azure ストレージ エミュレーター*を検索してアプリケーションを起動します。 エミュレーターを開始すると、Windows タスク ビューの領域にアイコンと通知が表示されます。
2. Visual Studio で、[ **ソリューションのビルド** on the **ソリューションのビルド** ] を選択します。
3. **[デバッグ]** メニューで、**F11** キーを押してソリューションを段階的に実行するか、**F5** キーを押してソリューションを開始から終了まで実行します。

## <a name="next-steps"></a>次のステップ
Azure Storage の詳細については、次のリソースをご覧ください。

* [Microsoft Azure Storage の概要](storage-introduction.md)
* [Azure ストレージ エクスプローラーの概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)
* [.NET を使用して Azure Table Storage を使用する](storage-dotnet-how-to-use-tables.md)
* [.NET を使用して Azure Queue Storage を使用する](storage-dotnet-how-to-use-queues.md)
* [Windows で Azure File Storage を使用する](storage-dotnet-how-to-use-files.md)
* [AzCopy コマンド ライン ユーティリティを使用してデータを転送する](storage-use-azcopy.md)
* [Azure Storage のドキュメント](https://azure.microsoft.com/documentation/services/storage/)
* [.NET 用の Microsoft Azure Storage クライアント ライブラリ](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Azure Storage Services REST API (Azure Storage サービスの REST API)](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png


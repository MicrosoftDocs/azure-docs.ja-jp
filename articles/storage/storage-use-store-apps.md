---
title: "Windows ストア アプリでの Azure ストレージの使用 | Microsoft Docs"
description: "Azure BLOB、Queue、Table、または File ストレージを使用する Windows ストア アプリを作成する方法について説明します。"
services: storage
documentationcenter: 
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 63c4b29d-b2f2-4d7c-b164-a0d38f4d14f6
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 12/08/2016
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 5b86154414c7745430af11d59355a937fc525d54
ms.openlocfilehash: 3284f94b28d814b3442d8088f69a301ef4dabc79


---
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Windows ストア アプリでの Azure Storage の使用方法
## <a name="overview"></a>概要
このガイドでは、Azure ストレージを利用する Windows ストア アプリケーションの開発に着手する方法を紹介します。

## <a name="download-required-tools"></a>必要なツールのダウンロード
* [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) では、Windows ストア アプリケーションのビルド、デバッグ、ローカライズ、パッケージ化、展開を簡単に実行できます。 Visual Studio 2012 以降が必要です。
* [Windows ランタイム用 Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage) は、Azure Storage を操作するためのクラス ライブラリを提供します。
* [Windows ストア アプリケーション用 WCF Data Services ツール](http://www.microsoft.com/download/details.aspx?id=30714) は、Visual Studio の [サービス参照の追加] 機能を拡張し、Windows ストア アプリケーション用のクライアント側 OData サポートを追加します。

## <a name="develop-apps"></a>アプリケーションの開発
### <a name="getting-ready"></a>開発の準備
Visual Studio 2012 以降で、新しい Windows ストア アプリケーション プロジェクトを作成します。

![store-apps-storage-vs-project][store-apps-storage-vs-project]

次に、Azure Storage クライアント ライブラリへの参照を追加します。そのためには、**[参照]** を右クリックし、**[参照の追加]** をクリックして、ダウンロードした Windows ランタイム用 Storage クライアント ライブラリに移動します。

![store-apps-storage-choose-library][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>BLOB およびキュー サービスでのライブラリの使用
この時点で、アプリケーションは、Azure BLOB およびキュー サービスを呼び出すことができる状態になっています。 Azure ストレージの型を直接参照できるようにするために、次の **using** ステートメントを追加します。

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
```

次に、ページにボタンを追加します。 次のコードをボタンの **Click** イベントに追加し、イベント ハンドラー メソッドを [async キーワード](http://msdn.microsoft.com/library/vstudio/hh156513.aspx)で変更します。

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var blobClient = account.CreateCloudBlobClient();
var container = blobClient.GetContainerReference("container1");
await container.CreateIfNotExistsAsync();
```

このコードでは、2 種類の文字列変数、*accountName* と *accountKey* があると想定しています。 これらは、ストレージ アカウントの名前と、そのアカウントに関連付けられたアカウント キーを表します。

アプリケーションをビルドし、実行します。 ボタンをクリックすると、 *container1* というコンテナーがアカウントに存在するかどうかがチェックされ、存在しない場合は作成されます。

### <a name="using-the-library-with-the-table-service"></a>ライブラリとテーブル サービスの使用
Azure Table サービスとの通信に使用される型は、Windows ストア アプリケーション ライブラリ用の WCF Data Services に依存します。 ここでは、Package Manager Console を使用して、必要な WCF ライブラリへの参照を追加します。

![store-apps-storage-package-manager][store-apps-storage-package-manager]

次のコマンドを使用して、パッケージ マネージャーをコンピューター上の場所に指定します。

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

このコマンドにより、必要なすべての参照が自動的にプロジェクトに追加されます。 Package Manager Console を使用しない場合は、「 [Managing NuGet Packages Using the Dialog (ダイアログを使用した NuGet パッケージの管理)](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)」の説明に従って、UI 上でローカル コンピューターの WCF Data Services NuGet フォルダーをパッケージ ソースの一覧に追加してから、参照を追加できます。

WCF Data Services NuGet パッケージを参照したら、ボタンの **Click** イベントのコードを変更します。

```csharp
var credentials = new StorageCredentials(accountName, accountKey);
var account = new CloudStorageAccount(credentials, true);
var tableClient = account.CreateCloudTableClient();
var table = tableClient.GetTableReference("table1");
await table.CreateIfNotExistsAsync();
```

このコードで、" *table1* " というテーブルがアカウントに存在するかどうかがチェックされ、存在しない場合は作成されます。

ダウンロードした同じパッケージに含まれている Microsoft.WindowsAzure.Storage.Table.dll への参照を追加することもできます。 このライブラリには、リフレクション ベースのシリアル化や汎用クエリなどの追加機能が含まれます。 ただし、このライブラリは JavaScript をサポートしていないことに注意してください。

[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png



<!--HONumber=Dec16_HO1-->



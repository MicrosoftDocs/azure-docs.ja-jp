---
title: "Xamarin.Forms アプリで Azure Storage に接続する"
description: "Azure Blob Storage に接続することで、Todo リスト Xamarin.Forms モバイル アプリにイメージを追加する"
documentationcenter: xamarin
author: adrianhall
manager: adrianha
editor: 
services: app-service\mobile
ms.assetid: bb1a1437-0a31-46bb-9237-1b692b0ede21
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: c8568846837f404eee0293be284c70bd27f06380
ms.lasthandoff: 01/20/2017


---
# <a name="connect-to-azure-storage-in-your-xamarinforms-app"></a>Xamarin.Forms アプリで Azure Storage に接続する
## <a name="overview"></a>Overview
Azure Mobile Apps クライアントとサーバー SDK は、/tables エンドポイントに対する CRUD 操作での構造化データのオフライン同期をサポートします。 通常、このデータはデータベースや同様のストアに格納されますが、これらのデータ ストアは一般的に大きなバイナリ データを効率的に保存することができません。 また、アプリケーションの中には、他の場所 (Blob Storage やファイル共有など) に格納されている関連データを持っているものもあるため、/tables エンドポイント内のレコードとその他のデータ間の関連付けを作成できると便利です。

このトピックでは、Mobile Apps の Todo リスト クイックスタートにイメージのサポートを追加する方法を説明します。 先に、 [Xamarin.Forms アプリの作成]に関するチュートリアルを完了している必要があります。

このチュートリアルでは、ストレージ アカウントを作成し、Mobile App バックエンドに接続文字列を追加します。 次に、新しい Mobile Apps 型 `StorageController<T>` から新しい継承をサーバー プロジェクトに追加します。

> [!TIP]
> このチュートリアルでは、[付属のサンプル](https://azure.microsoft.com/documentation/samples/app-service-mobile-dotnet-todo-list-files/)が使用できます。このサンプルはユーザーの Azure アカウントにデプロイできます。 
> 
> 

## <a name="prerequisites"></a>前提条件
* 「[Xamarin.Forms アプリの作成]」チュートリアルを完了していること。このチュートリアルには、他の前提条件も記載されています。 この記事では、そのチュートリアルで完成させたアプリケーションを使用します。

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service を開始する場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/mobile/)に関するページにアクセスしてください。 有効期間が短いスターター モバイル アプリを App Service ですぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="create-a-storage-account"></a>ストレージ アカウントの作成
1. [Azure Storage アカウントの作成]のチュートリアルに従って、ストレージ アカウントを作成します。 
2. Azure ポータルで、新しく作成したストレージ アカウントに移動し、 **キー** のアイコンをクリックします。 **プライマリ接続文字列**をコピーします。
3. モバイル アプリ バックエンドに移動します。 **[すべての設定]** -> **[アプリケーションの設定]** -> **[接続文字列]** の順に選択し、`MS_AzureStorageAccountConnectionString` という名前の新しいキーを作成し、ストレージ アカウントからコピーした値を使用します。 キーの種類として **[カスタム]** を使用します。

## <a name="add-a-storage-controller-to-the-server"></a>ストレージ コントローラーをサーバーに追加する
Azure Storage の SAS トークンの要求に応答するサーバー プロジェクトに新しいコント ローラーを追加すると共に、レコードに対応するファイルの一覧を返す必要があります。

* [ストレージ コントローラーをサーバー プロジェクトに追加する](#add-controller-code)
* [ストレージ コントローラーによって登録されているルート](#routes-registered)
* [クライアントとサーバーの通信](#client-communication)

### <a name="add-controller-code"></a>ストレージ コントローラーをサーバー プロジェクトに追加する
1. Visual Studio で、.NET サーバー プロジェクトを開きます。 NuGet パッケージ [Microsoft.Azure.Mobile.Server.Files]を追加します。 **[プレリリースを含める]**を選択していることを確認します。
2. Visual Studio で、.NET サーバー プロジェクトを開きます。 **[コントローラー]** フォルダーを右クリックしてから、**[追加]** -> **[コントローラー]** -> **[Web API 2 コントローラー – 空]** の順にクリックします。 コントローラー `TodoItemStorageController`を指定します。
3. 次の using ステートメントを追加します。
   
        using Microsoft.Azure.Mobile.Server.Files;
        using Microsoft.Azure.Mobile.Server.Files.Controllers;
4. 基本クラスを `StorageController`に変更します。
   
        public class TodoItemStorageController : StorageController<TodoItem>
5. 次のメソッドをクラスに追加します。
   
        [HttpPost]
        [Route("tables/TodoItem/{id}/StorageToken")]
        public async Task<HttpResponseMessage> PostStorageTokenRequest(string id, StorageTokenRequest value)
        {
            StorageToken token = await GetStorageTokenAsync(id, value);
   
            return Request.CreateResponse(token);
        }
   
        // Get the files associated with this record
        [HttpGet]
        [Route("tables/TodoItem/{id}/MobileServiceFiles")]
        public async Task<HttpResponseMessage> GetFiles(string id)
        {
            IEnumerable<MobileServiceFile> files = await GetRecordFilesAsync(id);
   
            return Request.CreateResponse(files);
        }
   
        [HttpDelete]
        [Route("tables/TodoItem/{id}/MobileServiceFiles/{name}")]
        public Task Delete(string id, string name)
        {
            return base.DeleteFileAsync(id, name);
        }
6. Web API 構成を更新して属性のルーティングを設定します。 **Startup.MobileApp.cs** で、`config` 変数を定義した後に、`ConfigureMobileApp()` メソッドの下に次の行を追加します。
   
        config.MapHttpAttributeRoutes();
7. サーバー プロジェクトをモバイル アプリ バックエンドに発行します。

### <a name="routes-registered"></a>ストレージ コントローラーによって登録されているルート
新しい `TodoItemStorageController` は、管理するレコードの下で次の&2; つのサブリソースを公開します。

* StorageToken
  
  * HTTP POST: ストレージ トークンを作成します
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
* MobileServiceFiles
  
  * HTTP GET: レコードに関連付けられたファイルの一覧を取得します
    
      `/tables/TodoItem/{id}/MobileServiceFiles`
  * HTTP DELETE: ファイル リソース識別子で指定されたファイルを削除します。
    
      `/tables/TodoItem/{id}/MobileServiceFiles/{fileid}`

### <a name="client-communication"></a>クライアントとサーバーの通信
`TodoItemStorageController` には、Blob をアップロードまたはダウンロードするためのルートが *ない* ことに注意してください。 これはモバイル クライアントが、特定の BLOB またはコンテナーに安全にアクセスするために、最初に SAS トークン (Shared Access Signature) を取得した後、これらの操作を実行するために Blob Storage と *直接* 対話するためです。 これは重要なアーキテクチャの仕様で、ストレージにアクセスする場合を除き、モバイル バックエンドのスケーラビリティと可用性の制限を受けます。 代わりに、Azure Storage に直接接続することで、モバイル クライアントが自動パーティション分割や地理的分散などの機能を利用できるようになります。

Shared Access Signature を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 つまり、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。 詳細については、「[Shared Access Signatures (SAS) の使用]」を参照してください。

次の図は、クライアントとサーバーの相互作用を示しています。 ファイルをアップロードする前に、クライアントはサービスからの SAS トークンを要求します。 サービスはストレージ接続文字列を使用して新しい SAS を生成します。この SAS はその後クライアントに返されます。 SAS は期間限定で、アクセス許可を特定のファイルまたはコンテナーだけに制限します。 モバイル クライアントはこの SAS と Azure Storage クライアント SDK を使用して、ファイルを Blob Storage にアップロードします。

![SAS トークンの要求](./media/app-service-mobile-xamarin-forms-blob-storage/storage-token-diagram.png)

## <a name="update-your-client-app-to-add-image-support"></a>クライアント アプリを更新してイメージのサポートを追加する
Visual Studio または Xamarin Studio のいずれかで、Xamarin.Forms のクイック スタート プロジェクトを開きます。 NuGet パッケージをインストールし、ポータブル ライブラリ プロジェクト、iOS プロジェクト、Android プロジェクト、および Windows プロジェクトを更新します。

* [NuGet パッケージを追加する](#add-nuget)
* [IPlatform インターフェイスを追加します。](#add-iplatform)
* [FileHelper クラスを追加する](#add-filehelper)
* [ファイル同期ハンドラーを追加する](#file-sync-handler)
* [TodoItemManager を更新する](#update-todoitemmanager)
* [詳細ビューを追加する](#add-details-view)
* [メイン ビューを更新する ](#update-main-view)
* [Android プロジェクト](#update-android)、[iOS プロジェクト](#update-ios)、[Windows プロジェクトを更新する](#update-windows)

> [!NOTE]
> このチュートリアルに含まれているのは、Android、iOS、Windows ストア (Windows Phone ではなく) プラットフォームの手順だけです。
> 
> 

### <a name="add-nuget"></a>NuGet パッケージを追加する
ソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]**を選択します。 次の NuGet パッケージをソリューション内の **すべて** のプロジェクトに追加します。 **[プレリリースを含める]**がオンになっていることを確認します。

* [Microsoft.Azure.Mobile.Client.Files]
* [Microsoft.Azure.Mobile.Client.SQLiteStore]
* [PCLStorage]

便宜上、このサンプルでは [PCLStorage] ライブラリを使用していますが、Azure Mobile Apps クライアント SDK では必要ありません。

[PCLStorage]: https://www.nuget.org/packages/PCLStorage/

### <a name="add-iplatform"></a>IPlatform インターフェイスを追加します。
メインのポータブル ライブラリ プロジェクトで新しいインターフェイス `IPlatform` を作成します。 これは [Xamarin.Forms DependencyService] パターンに従って、実行時に正しいプラットフォーム固有のクラスをロードします。 後から、各クライアント プロジェクトでプラットフォーム固有の実装を追加します。

1. 次の using ステートメントを追加します。
   
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Sync;
2. この実装を以下に置き換えます。
   
        public interface IPlatform
        {
            Task <string> GetTodoFilesPathAsync();
   
            Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata);
   
            Task<string> TakePhotoAsync(object context);
   
            Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename);
        }

### <a name="add-filehelper"></a>FileHelper クラスを追加する
1. メインのポータブル ライブラリ プロジェクトで新しいクラス `FileHelper` を作成します。 次の using ステートメントを追加します。
   
        using System.IO;
        using PCLStorage;
        using System.Threading.Tasks;
        using Xamarin.Forms;
2. クラス定義を追加します。
   
        public class FileHelper
        {
            public static async Task<string> CopyTodoItemFileAsync(string itemId, string filePath)
            {
                IFolder localStorage = FileSystem.Current.LocalStorage;
   
                string fileName = Path.GetFileName(filePath);
                string targetPath = await GetLocalFilePathAsync(itemId, fileName);
   
                var sourceFile = await localStorage.GetFileAsync(filePath);
                var sourceStream = await sourceFile.OpenAsync(FileAccess.Read);
   
                var targetFile = await localStorage.CreateFileAsync(targetPath, CreationCollisionOption.ReplaceExisting);
   
                using (var targetStream = await targetFile.OpenAsync(FileAccess.ReadAndWrite)) {
                    await sourceStream.CopyToAsync(targetStream);
                }
   
                return targetPath;
            }
   
            public static async Task<string> GetLocalFilePathAsync(string itemId, string fileName)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
   
                string recordFilesPath = Path.Combine(await platform.GetTodoFilesPathAsync(), itemId);
   
                    var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(recordFilesPath);
                    if (checkExists == ExistenceCheckResult.NotFound) {
                        await FileSystem.Current.LocalStorage.CreateFolderAsync(recordFilesPath, CreationCollisionOption.ReplaceExisting);
                    }
   
                return Path.Combine(recordFilesPath, fileName);
            }
   
            public static async Task DeleteLocalFileAsync(Microsoft.WindowsAzure.MobileServices.Files.MobileServiceFile fileName)
            {
                string localPath = await GetLocalFilePathAsync(fileName.ParentId, fileName.Name);
                var checkExists = await FileSystem.Current.LocalStorage.CheckExistsAsync(localPath);
   
                if (checkExists == ExistenceCheckResult.FileExists) {
                    var file = await FileSystem.Current.LocalStorage.GetFileAsync(localPath);
                    await file.DeleteAsync();
                }
            }
        }

### <a name="file-sync-handler"></a> ファイル同期ハンドラーを追加する
メインのポータブル ライブラリ プロジェクトで新しいクラス `TodoItemFileSyncHandler` を作成します。 このクラスには、ファイルが追加または削除された場合にコードに通知するための Azure SDK からのコールバックが含まれています。

Azure Mobile クライアント SDK は、実際にはどのファイル データも格納しません。クライアント SDK は `IFileSyncHandler` の実装を呼び出し、この実装がローカル デバイスにファイルを格納するかどうか、およびその格納方法を決定します。

1. 次の using ステートメントを追加します。
   
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Xamarin.Forms;
2. クラス定義を以下に置き換えます。 
   
        public class TodoItemFileSyncHandler : IFileSyncHandler
        {
            private readonly TodoItemManager todoItemManager;
   
            public TodoItemFileSyncHandler(TodoItemManager itemManager)
            {
                this.todoItemManager = itemManager;
            }
   
            public Task<IMobileServiceFileDataSource> GetDataSource(MobileServiceFileMetadata metadata)
            {
                IPlatform platform = DependencyService.Get<IPlatform>();
                return platform.GetFileDataSource(metadata);
            }
   
            public async Task ProcessFileSynchronizationAction(MobileServiceFile file, FileSynchronizationAction action)
            {
                if (action == FileSynchronizationAction.Delete) {
                    await FileHelper.DeleteLocalFileAsync(file);
                }
                else { // Create or update. We're aggressively downloading all files.
                    await this.todoItemManager.DownloadFileAsync(file);
                }
            }
        }

### <a name="update-todoitemmanager"></a>TodoItemManager を更新する
1. **TodoItemManager.cs** で、行 `#define OFFLINE_SYNC_ENABLED` をコメント解除します。
2. **TodoItemManager.cs**で、次の using ステートメントを追加します。
   
        using System.IO;
        using Xamarin.Forms;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Eventing;
3. `TodoItemManager` のコンストラクターで、`DefineTable()` の呼び出しの後に次を追加します。
   
        // Initialize file sync
        this.client.InitializeFileSyncContext(new TodoItemFileSyncHandler(this), store);
4. コンストラクターで、`InitializeAsync` の呼び出しを以下に置き換えます。 これにより、ローカル ストアでレコードが変更されたときに、コールバックがあることが保証されます。 ファイル同期機能はこれらのコールバックを使用して、ファイルの同期ハンドラーをトリガーします。
   
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
5. `SyncAsync()` で、`PushAsync()` の呼び出しの後に次を追加します。
   
        await this.todoTable.PushFileChangesAsync();
6. `TodoItemManager` に次のメソッドを追加します。
   
        internal async Task DownloadFileAsync(MobileServiceFile file)
        {
            var todoItem = await todoTable.LookupAsync(file.ParentId);
            IPlatform platform = DependencyService.Get<IPlatform>();
   
            string filePath = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name); 
            await platform.DownloadFileAsync(this.todoTable, file, filePath);
        }
   
        internal async Task<MobileServiceFile> AddImage(TodoItem todoItem, string imagePath)
        {
            string targetPath = await FileHelper.CopyTodoItemFileAsync(todoItem.Id, imagePath);
            return await this.todoTable.AddFileAsync(todoItem, Path.GetFileName(targetPath));
        }
   
        internal async Task DeleteImage(TodoItem todoItem, MobileServiceFile file)
        {
            await this.todoTable.DeleteFileAsync(file);
        }
   
        internal async Task<IEnumerable<MobileServiceFile>> GetImageFilesAsync(TodoItem todoItem)
        {
            return await this.todoTable.GetFilesAsync(todoItem);
        }

### <a name="add-details-view"></a>詳細ビューを追加する
このセクションでは、Todo 項目の新しい詳細ビューを追加します。 このビューは、ユーザーが Todo 項目を選択した場合に作成されます。このビューでは、新しいイメージを項目に追加することができます。

1. 以下を実装して、新しいクラス **TodoItemImage** をポータブル ライブラリ プロジェクトに追加します。
   
        public class TodoItemImage : INotifyPropertyChanged
        {
            private string name;
            private string uri;
   
            public MobileServiceFile File { get; private set; }
   
            public string Name
            {
                get { return name; }
                set
                {
                    name = value;
                    OnPropertyChanged(nameof(Name));
                }
            }
   
            public string Uri
            {
                get { return uri; }      
                set
                {
                    uri = value;
                    OnPropertyChanged(nameof(Uri));
                }
            }
   
            public TodoItemImage(MobileServiceFile file, TodoItem todoItem)
            {
                Name = file.Name;
                File = file;
   
                FileHelper.GetLocalFilePathAsync(todoItem.Id, file.Name).ContinueWith(x => this.Uri = x.Result);
            }
   
            public event PropertyChangedEventHandler PropertyChanged;
   
            private void OnPropertyChanged(string propertyName)
            {
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
            }
        }
2. **App.cs**を編集します。 `MainPage` の初期化を以下に置き換えます。
   
        MainPage = new NavigationPage(new TodoList());
3. **App.cs**で次のプロパティを追加します。
   
        public static object UIContext { get; set; }
4. ポータブル ライブラリ プロジェクトを右クリックして、**[追加]** -> **[新しい項目]** -> **[クロスプラットフォーム]** -> **[Forms Xaml Page]** の順に選択します。 ビューに `TodoItemDetailsView`という名前を付けます。
5. **TodoItemDetailsView.xaml** を開き、ContentPage の本文を以下に置き換えます。
   
          <Grid>
            <Grid.RowDefinitions>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="Auto"/>
              <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
            <Button Clicked="OnAdd" Text="Add image"></Button>
            <ListView x:Name="imagesList"
                      ItemsSource="{Binding Images}"
                      IsPullToRefreshEnabled="false"
                      Grid.Row="2">
              <ListView.ItemTemplate>
                <DataTemplate>
                  <ImageCell ImageSource="{Binding Uri}"
                             Text="{Binding Name}">
                  </ImageCell>
                </DataTemplate>
              </ListView.ItemTemplate>
            </ListView>
          </Grid>
6. **TodoItemDetailsView.xaml.cs** を編集し、次の using ステートメントを追加します。
   
        using System.Collections.ObjectModel;
        using Microsoft.WindowsAzure.MobileServices.Files;
7. `TodoItemDetailsView` の実装を以下に置き換えます。
   
        public partial class TodoItemDetailsView : ContentPage
        {
            private TodoItemManager manager;
   
            public TodoItem TodoItem { get; set; }        
            public ObservableCollection<TodoItemImage> Images { get; set; }
   
            public TodoItemDetailsView(TodoItem todoItem, TodoItemManager manager)
            {
                InitializeComponent();
                this.Title = todoItem.Name;
   
                this.TodoItem = todoItem;
                this.manager = manager;
   
                this.Images = new ObservableCollection<TodoItemImage>();
                this.BindingContext = this;
            }
   
            public async Task LoadImagesAsync()
            {
                IEnumerable<MobileServiceFile> files = await this.manager.GetImageFilesAsync(TodoItem);
                this.Images.Clear();
   
                foreach (var f in files) {
                    var todoImage = new TodoItemImage(f, this.TodoItem);
                    this.Images.Add(todoImage);
                }
            }
   
            public async void OnAdd(object sender, EventArgs e)
            {
                IPlatform mediaProvider = DependencyService.Get<IPlatform>();
                string sourceImagePath = await mediaProvider.TakePhotoAsync(App.UIContext);
   
                if (sourceImagePath != null) {
                    MobileServiceFile file = await this.manager.AddImage(this.TodoItem, sourceImagePath);
   
                    var image = new TodoItemImage(file, this.TodoItem);
                    this.Images.Add(image);
                }
            }
        }

### <a name="update-main-view"></a>メイン ビューを更新する
Todo 項目が選択されたときに、メイン ビューを更新して詳細ビューを開きます。

**TodoList.xaml.cs** で、`OnSelected` の実装を以下に置き換えます。

    public async void OnSelected(object sender, SelectedItemChangedEventArgs e)
    {
        var todo = e.SelectedItem as TodoItem;

        if (todo != null) {
            var detailsView = new TodoItemDetailsView(todo, manager);
            await detailsView.LoadImagesAsync();
            await Navigation.PushAsync(detailsView);
        }

        todoList.SelectedItem = null;
    }

### <a name="update-android"></a>Android プロジェクトを更新する
プラットフォーム固有のコード (ファイルをダウンロードし、カメラを使用して新しいイメージをキャプチャするためのコードも含める) を Android プロジェクトに追加します。 

このコードは Xamarin.Forms [DependencyService](https://developer.xamarin.com/guides/xamarin-forms/dependency-service/) を使用して、実行時に正しいプラットフォーム固有のクラスをロードします。

1. コンポーネント **Xamarin.Mobile** を Android プロジェクトに追加します。
2. 次を実装して新しいクラス `DroidPlatform` を追加します。 "YourNamespace"をプロジェクトのメインの名前空間に置き換えます。
   
        using System;
        using System.IO;
        using System.Threading.Tasks;
        using Android.Content;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.Droid.DroidPlatform))]
        namespace YourNamespace.Droid
        {
            public class DroidPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var uiContext = context as Context;
                        if (uiContext != null) {
                            var mediaPicker = new MediaPicker(uiContext);
                            var photo = await mediaPicker.TakePhotoAsync(new StoreCameraMediaOptions());
   
                            return photo.Path;
                        }
                    }
                    catch (TaskCanceledException) {
                    }
   
                    return null;
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string appData = Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments);
                    string filesPath = Path.Combine(appData, "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. **MainActivity.cs**を編集します。 `OnCreate` で、`LoadApplication()` の呼び出しの前に以下を追加します。
   
        App.UIContext = this;

### <a name="update-ios"></a>iOS プロジェクトを更新する
プラットフォーム固有のコードを iOS プロジェクトに追加します。

1. コンポーネント **Xamarin.Mobile** を iOS プロジェクトに追加します。
2. 次を実装して新しいクラス `TouchPlatform` を追加します。 "YourNamespace"をプロジェクトのメインの名前空間に置き換えます。
   
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Xamarin.Media;
   
        [assembly: Xamarin.Forms.Dependency(typeof(YourNamespace.iOS.TouchPlatform))]
        namespace YourNamespace.iOS
        {
            class TouchPlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        var mediaPicker = new MediaPicker();
                        var mediaFile = await mediaPicker.PickPhotoAsync();
                        return mediaFile.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
   
                public Task<string> GetTodoFilesPathAsync()
                {
                    string filesPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments), "TodoItemFiles");
   
                    if (!Directory.Exists(filesPath)) {
                        Directory.CreateDirectory(filesPath);
                    }
   
                    return Task.FromResult(filesPath);
                }
            }
        }
3. **AppDelegate.cs** を編集し、`SQLitePCL.CurrentPlatform.Init()` の呼び出しをコメント解除します。

### <a name="update-windows"></a>Windows プロジェクトを更新する
1. Visual Studio の拡張機能 [SQLite for Windows 8.1](http://go.microsoft.com/fwlink/?LinkID=716919)をインストールします。 
   詳細については、「 [Windows アプリのオフライン同期を有効にする](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)」チュートリアルを参照してください。 
2. **Package.appxmanifest** を編集して、**Web カメラ**機能をチェックします。
3. 次を実装して新しいクラス `WindowsStorePlatform` を追加します。 "YourNamespace"をプロジェクトのメインの名前空間に置き換えます。
   
        using System;
        using System.Threading.Tasks;
        using Microsoft.WindowsAzure.MobileServices.Files;
        using Microsoft.WindowsAzure.MobileServices.Files.Metadata;
        using Microsoft.WindowsAzure.MobileServices.Files.Sync;
        using Microsoft.WindowsAzure.MobileServices.Sync;
        using Windows.Foundation;
        using Windows.Media.Capture;
        using Windows.Storage;
        using YourNamespace;
   
        [assembly: Xamarin.Forms.Dependency(typeof(WinApp.WindowsStorePlatform))]
        namespace WinApp
        {
            public class WindowsStorePlatform : IPlatform
            {
                public async Task DownloadFileAsync<T>(IMobileServiceSyncTable<T> table, MobileServiceFile file, string filename)
                {
                    var path = await FileHelper.GetLocalFilePathAsync(file.ParentId, file.Name);
                    await table.DownloadFileAsync(file, path);
                }
   
                public async Task<IMobileServiceFileDataSource> GetFileDataSource(MobileServiceFileMetadata metadata)
                {
                    var filePath = await FileHelper.GetLocalFilePathAsync(metadata.ParentDataItemId, metadata.FileName);
                    return new PathMobileServiceFileDataSource(filePath);
                }
   
                public async Task<string> GetTodoFilesPathAsync()
                {
                    var storageFolder = ApplicationData.Current.LocalFolder;
                    var filePath = "TodoItemFiles";
   
                    var result = await storageFolder.TryGetItemAsync(filePath);
   
                    if (result == null) {
                        result = await storageFolder.CreateFolderAsync(filePath);
                    }
   
                    return result.Name; // later operations will use relative paths
                }
   
                public async Task<string> TakePhotoAsync(object context)
                {
                    try {
                        CameraCaptureUI dialog = new CameraCaptureUI();
                        Size aspectRatio = new Size(16, 9);
                        dialog.PhotoSettings.CroppedAspectRatio = aspectRatio;
   
                        StorageFile file = await dialog.CaptureFileAsync(CameraCaptureUIMode.Photo);
                        return file.Path;
                    }
                    catch (TaskCanceledException) {
                        return null;
                    }
                }
            }
        }

## <a name="summary"></a>概要
この記事では、Azure Storage で使用するために、Azure Mobile クライアントとサーバー SDK での新しいファイル サポートの使用方法について説明しました。 

* ストレージ アカウントを作成し、モバイル アプリ バックエンドに接続文字列を追加します。 Azure Storage へのキーを持っているのはバックエンドだけです。モバイル クライアントは Azure Storage にアクセスする必要がある場合に、SAS (Shared Access Signature) トークンを要求します。 Azure Storage での SAS トークンの詳細については、「[Shared Access Signatures (SAS) の使用]」を参照してください。
* SAS トークンの要求を処理してレコードに関連付けられているファイルを取得するため、`StorageController` をサブクラス化するコントローラーを作成します。 既定では、ファイルはレコード ID をコンテナー名の一部として使用して、レコードに関連付けられます。この動作は `IContainerNameResolver` の実装を指定することでカスタマイズすることができます。 SAS トークン ポリシーもカスタマイズすることができます。
* Azure Mobile クライアント SDK は実際にはどのファイル データも格納しません。 代わりに、クライアント SDK は `IFileSyncHandler` を呼び出します。これにより、ファイルをローカルのデバイスに格納する場合は、その方法が決まります。 同期ハンドラーは、次のように登録されます。
  
        client.InitializeFileSync(new MyFileSyncHandler(), store);
  
      + `IFileSyncHandler.GetDataSource` は、Azure Mobile クライアント SDK がファイル データを (たとえばアップロード プロセスの一部として) 必要とする場合に呼び出されます。 これにより、ファイルのローカル デバイスへの格納方法 (する場合) を管理し、必要に応じてその情報を返すことができます。
  
      + `IFileSyncHandler.ProcessFileSynchronizationAction` はファイル同期フローの一部として呼び出されます。 ファイル参照と FileSynchronizationAction 列挙値が提供されるため、アプリケーションでそのイベントをどのように処理するかを決定できます (例: ファイルが作成または更新されたときに自動的にそのファイルをダウンロードする、ファイルがサーバーで削除されたときに、ローカル デバイスからもそのファイルを削除する)。
* `MobileServiceFile` は、`IMobileServiceTable` または `IMobileServiceSyncTable` をそれぞれ使用することで、オンラインまたはオフラインのいずれかのモードで使用できます。 オフラインの場合は、アプリによって `PushFileChangesAsync` が呼び出されると、アップロードが行われます。 これにより、オフライン操作キューが処理されます。Azure Mobile クライアント SDK は各操作ファイルに対して、`IFileSyncHandler` インスタンス上で `GetDataSource` メソッドを呼び出して、アップロードのためにファイルの内容を取得します。
* 項目のファイルを取得するためには、`IMobileServiceTable<T>` または `IMobileServiceSyncTable<T>` インスタンスで `GetFilesAsync` メソッドを呼び出します。 このメソッドは、提供されたデータ項目に関連付けられたファイルの一覧を返します。 (注: これは "*ローカル*" 操作であり、オブジェクトが最後に同期されたときの状態に基づいてファイルを返します。 サーバーからファイルの最新の一覧を取得するには、最初に同期操作を開始する必要があります。)
  
        IEnumerable<MobileServiceFile> files = await myTable.GetFilesAsync(myItem);
* ファイルの同期機能は、クライアントがプッシュ操作またはプル操作の一部として受け取ったレコードを取得するために、ローカル ストアでレコードの変更通知を使用します。 これは、 `StoreTrackingOptions` パラメーターを使用して、同期コンテキストのローカルおよびサーバーの通知をオンにすることで行うことができます。 
  
        this.client.SyncContext.InitializeAsync(store, StoreTrackingOptions.NotifyLocalAndServerOperations);
  
      + ローカルのみまたはサーバーのみの通知など、その他のストア追跡オプションも使用できます。 `IMobileServiceClient` の `EventManager` プロパティを使用して、カスタム コールバックを追加または所有することができます。
  
            jobService.MobileService.EventManager.Subscribe<StoreOperationCompletedEvent>(StoreOperationEventHandler);
* 関連付けは名前付け規則によって行われるため、Blob Storage を直接変更して、レコードにファイルを追加またはレコードからファイルを削除することができまます。 ただし、この場合は、 **関連付けられた BLOB が変更されたらレコードのタイムスタンプも必ず更新する**必要があります。 Azure Mobile クライアント SDK では、ファイルが追加または削除されると、レコードも常に更新されます。 
  
    これが必要な理由は、一部のモバイル クライアントはローカル ストレージに既にレコードがあるからです。 これらのクライアントが増分プルを実行すると、このレコードは返されず、クライアントは新しい関連付けられているファイルに対してクエリを実行しません。 この問題を回避するため、Azure Mobile クライアント SDK を使用しない Blob Storage の変更を実行するときに、レコードのタイムスタンプを更新することをお勧めします。
* クライアント プロジェクトは、 [Xamarin.Forms DependencyService] パターンを使用して、実行時に適切なプラットフォーム固有のクラスをロードします。 このサンプルでは、各プラットフォームに固有のプロジェクトに実装することで、インターフェイス `IPlatform` を定義しました。

<!-- URLs. -->

[Visual Studio Community 2013]: https://go.microsoft.com/fwLink/p/?LinkID=534203
[Xamarin.Forms アプリの作成]: app-service-mobile-xamarin-forms-get-started.md
[Xamarin.Forms DependencyService]: https://developer.xamarin.com/guides/xamarin-forms/dependency-service/
[Microsoft.Azure.Mobile.Client.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.Files/
[Microsoft.Azure.Mobile.Client.SQLiteStore]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[Microsoft.Azure.Mobile.Server.Files]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Files/
[Shared Access Signatures (SAS) の使用]: ../storage/storage-dotnet-shared-access-signature-part-1.md
[Azure Storage アカウントの作成]:  ../storage/storage-create-storage-account.md#create-a-storage-account


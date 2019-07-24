---
title: Xamarin からオブジェクト (BLOB) ストレージを使用する方法 | Microsoft Docs
description: Azure Storage Client Library for Xamarin を利用すれば、開発者は iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。 このチュートリアルでは、Xamarin を利用し、Azure BLOB ストレージを使用するアプリケーションを作成する方法を紹介します。
services: storage
documentationcenter: xamarin
author: michaelhauss
ms.service: storage
ms.topic: article
ms.date: 05/11/2017
ms.author: michaelhauss
ms.openlocfilehash: b9c707dcc1628f685661f88aaed29612465a5469
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58010062"
---
# <a name="how-to-use-blob-storage-from-xamarin"></a>Xamarin から BLOB ストレージを使用する方法

Xamarin を利用すれば、開発者は共有 C# コードベースを利用して iOS、Android、Windows Store アプリをネイティブ ユーザー インターフェイスで作成できます。 このチュートリアルでは、Xamarin アプリケーションで Azure BLOB ストレージを使用する方法を紹介します。 コードの説明に入る前に Azure Storage について学習するには、「 [Microsoft Azure Storage の概要](../common/storage-introduction.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)」を参照してください。

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="create-a-new-xamarin-application"></a>新しい Xamarin アプリケーションの作成
このチュートリアルでは、Android、iOS、および Windows を対象とするアプリケーションを作成します。 このアプリケーションは、コンテナーを作成し、そのコンテナーに BLOB をアップロードします。 ここでは Windows で Visual Studio を使用しますが、macOS で Xamarin Studio を使用しても同じようにアプリケーションを作成できます。

次の手順でアプリケーションを作成します。

1. [Xamarin for Visual Studio](https://www.xamarin.com/download)をダウンロードしてインストールします (まだ、インストールしていない場合)。
2. Visual Studio を開き、空のアプリ (ネイティブ ポータブル) を作成します (**[ファイル]、[新規]、[プロジェクト]、[クロス プラットフォーム]、[空のアプリ (ネイティブ ポータブル)]** の順に移動)。
3. [ソリューション エクスプローラー] ウィンドウでソリューションを右クリックし、 **[ソリューションの NuGet パッケージの管理]** を選択します。 **WindowsAzure.Storage** を検索し、最新の安定バージョンを、ソリューション内のすべてのプロジェクトにインストールします。
4. プロジェクトをビルドして実行します。

これで、ボタンをクリックすることでカウンターをインクリメントできるアプリケーションが作成されます。

## <a name="create-container-and-upload-blob"></a>コンテナーの作成および BLOB のアップロード
次に、`(Portable)` プロジェクトで、コードを `MyClass.cs` に追加します。 このコードでは、コンテナーを作成し、このコンテナーに BLOB をアップロードします。 `MyClass.cs` は次のようになります。

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System.Threading.Tasks;

namespace XamarinApp
{
    public class MyClass
    {
        public MyClass ()
        {
        }

        public static async Task performBlobOperation()
        {
            // Retrieve storage account from connection string.
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here");

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            // Retrieve reference to a previously created container.
            CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

            // Create the container if it doesn't already exist.
            await container.CreateIfNotExistsAsync();

            // Retrieve reference to a blob named "myblob".
            CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

            // Create the "myblob" blob with the text "Hello, world!"
            await blockBlob.UploadTextAsync("Hello, world!");
        }
    }
}
```

"Your_account_name_here" と "your_account_key_here" は、実際のアカウント名とアカウント キーに置き換えます。 

iOS、Android、および Windows Phone プロジェクトすべてが、ポータブル プロジェクトを参照します。つまり、すべての共有コードを 1 か所に記述して、すべてのプロジェクトで使用できます。 これで、次のコード行を各プロジェクトに追加して、利用を開始することができます: `MyClass.performBlobOperation()`

### <a name="xamarinappdroid--mainactivitycs"></a>XamarinApp.Droid > MainActivity.cs

```csharp
using Android.App;
using Android.Widget;
using Android.OS;

namespace XamarinApp.Droid
{
    [Activity (Label = "XamarinApp.Droid", MainLauncher = true, Icon = "@drawable/icon")]
    public class MainActivity : Activity
    {
        int count = 1;

        protected override async void OnCreate (Bundle bundle)
        {
            base.OnCreate (bundle);

            // Set our view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get our button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            button.Click += delegate {
                button.Text = string.Format ("{0} clicks!", count++);
            };

            await MyClass.performBlobOperation();
            }
        }
    }
}
```

### <a name="xamarinappios--viewcontrollercs"></a>XamarinApp.iOS > ViewController.cs

```csharp
using System;
using UIKit;

namespace XamarinApp.iOS
{
    public partial class ViewController : UIViewController
    {
        int count = 1;

        public ViewController (IntPtr handle) : base (handle)
        {
        }

        public override async void ViewDidLoad ()
        {
            int count = 1;

            public ViewController (IntPtr handle) : base (handle)
            {
            }

            public override async void ViewDidLoad ()
            {
                base.ViewDidLoad ();
                // Perform any additional setup after loading the view, typically from a nib.
                Button.AccessibilityIdentifier = "myButton";
                Button.TouchUpInside += delegate {
                    var title = string.Format ("{0} clicks!", count++);
                    Button.SetTitle (title, UIControlState.Normal);
                };

                await MyClass.performBlobOperation();
            }

            public override void DidReceiveMemoryWarning ()
            {
                base.DidReceiveMemoryWarning ();
                // Release any cached data, images, etc. that aren't in use.
            }
        }
    }
}
```

### <a name="xamarinappwinphone--mainpagexaml--mainpagexamlcs"></a>XamarinApp.WinPhone > MainPage.xaml > MainPage.xaml.cs

```csharp
using Windows.UI.Xaml.Controls;
using Windows.UI.Xaml.Navigation;

// The Blank Page item template is documented at https://go.microsoft.com/fwlink/?LinkId=391641

namespace XamarinApp.WinPhone
{
    /// <summary>
    /// An empty page that can be used on its own or navigated to within a Frame.
    /// </summary>
    public sealed partial class MainPage : Page
    {
        int count = 1;

        public MainPage()
        {
            this.InitializeComponent();

            this.NavigationCacheMode = NavigationCacheMode.Required;
        }

        /// <summary>
        /// Invoked when this page is about to be displayed in a Frame.
        /// </summary>
        /// <param name="e">Event data that describes how this page was reached.
        /// This parameter is typically used to configure the page.</param>
        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            int count = 1;

            public MainPage()
            {
                this.InitializeComponent();

                this.NavigationCacheMode = NavigationCacheMode.Required;
            }

            /// <summary>
            /// Invoked when this page is about to be displayed in a Frame.
            /// </summary>
            /// <param name="e">Event data that describes how this page was reached.
            /// This parameter is typically used to configure the page.</param>
            protected override async void OnNavigatedTo(NavigationEventArgs e)
            {
                // TODO: Prepare page for display here.

                // TODO: If your application contains multiple pages, ensure that you are
                // handling the hardware Back button by registering for the
                // Windows.Phone.UI.Input.HardwareButtons.BackPressed event.
                // If you are using the NavigationHelper provided by some templates,
                // this event is handled for you.
                Button.Click += delegate {
                    var title = string.Format("{0} clicks!", count++);
                    Button.Content = title;
                };

                await MyClass.performBlobOperation();
            }
        }
    }
}
```

## <a name="run-the-application"></a>アプリケーションの実行
これで、このアプリケーションを、Android または Windows Phone エミュレーターで実行できます。 iOS エミュレーターで、このアプリケーションを実行することもできますが、それには Mac が必要です。 これを行う具体的な手順については、 [Mac への Visual Studio の接続](https://developer.xamarin.com/guides/ios/getting_started/installation/windows/connecting-to-mac/)

アプリケーションを実行すると、コンテナー `mycontainer` がストレージ アカウントに作成されます。 これには、テキスト `Hello, world!` が示された BLOB `myblob` が含まれています。 これを確認するには、 [Microsoft Azure ストレージ エクスプ ローラー](https://storageexplorer.com/)を使用します。

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Blob Storage の 1 つのシナリオに焦点を当て、Xamarin で Azure Storage を使用するクロスプラットフォームのアプリケーションを作成する方法を学習しました。 ただし、Blob Storage だけでなく、Table Storage、File Storage、および Queue Storage を使用すると、さらに多くのことを行うことができます。 詳細については、次の記事を確認してください。

* [.NET を使用して Azure Blob Storage を使用する](storage-dotnet-how-to-use-blobs.md)
* [Azure Files の概要](../files/storage-files-introduction.md)
* [.NET での Azure Files 用の開発](../files/storage-dotnet-how-to-use-files.md)
* [.NET を使用して Azure Table Storage を使用する](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.NET を使用して Azure Queue Storage を使用する](../queues/storage-dotnet-how-to-use-queues.md)

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
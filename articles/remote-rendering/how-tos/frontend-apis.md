---
title: 認証のための Azure フロントエンド API
description: 認証に C# フロントエンド API を使用する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/12/2010
ms.topic: how-to
ms.openlocfilehash: dcff550b5411ac00dc54d2103278812f32f56543
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509979"
---
# <a name="use-the-azure-frontend-apis-for-authentication"></a>認証に Azure フロントエンド API を使用する

このセクションでは、認証に C# の API を使用する方法について説明します。

## <a name="azurefrontendaccountinfo"></a>AzureFrontendAccountInfo

AzureFrontendAccountInfo は、SDK の ```AzureFrontend``` インスタンスの認証情報を設定するために使用します。

重要なフィールドは次のとおりです。

```cs

public class AzureFrontendAccountInfo
{
    // Something akin to "<region>.mixedreality.azure.com"
    public string AccountDomain;

    // Can use one of:
    // 1) ID and Key.
    // 2) ID and AuthenticationToken.
    // 3) ID and AccessToken.
    public string AccountId = Guid.Empty.ToString();
    public string AccountKey = string.Empty;
    public string AuthenticationToken = string.Empty;
    public string AccessToken = string.Empty;
}
```

C++ の場合は、次のようになります。

```cpp
struct AzureFrontendAccountInfo
{
    std::string AccountDomain{};
    std::string AccountId{};
    std::string AccountKey{};
    std::string AuthenticationToken{};
    std::string AccessToken{};
};

```

ドメインの _region_ 部分には、[お近くのリージョン](../reference/regions.md)を使用します。

アカウント情報は、[アカウント情報の取得](create-an-account.md#retrieve-the-account-information)に関する説明の段落で説明したように、ポータルから取得できます。

## <a name="azure-frontend"></a>Azure フロントエンド

関連するクラスは ```AzureFrontend``` と ```AzureSession``` です。 ```AzureFrontend``` は、資産の変換とレンダリング セッションの作成など、アカウントの管理とアカウント レベルの機能に使用します。 ```AzureSession``` はセッション レベルの機能に使用し、セッションの更新、クエリ、更新、使用停止などを含みます。

開かれた、または作成された各 ```AzureSession``` は、それを作成したフロントエンドに対する参照を保持します。 完全にシャットダウンするには、フロントエンドが割り当て解除される前にすべてのセッションを割り当て解除する必要があります。

セッションの割り当てを解除しても、Azure 上のサーバーは停止されないため、明示的に `AzureSession.StopAsync` を呼び出す必要があります。

セッションが作成され、その状態が準備完了としてマークされると、`AzureSession.ConnectToRuntime` を使用してリモートのレンダリング ランタイムに接続できます。

### <a name="threading"></a>スレッド処理

すべての AzureSession および AzureFrontend 非同期呼び出しは、メイン アプリケーションのスレッドではなく、バックグラウンド スレッドで完了します。

### <a name="conversion-apis"></a>変換 API

変換サービスの詳細については、[モデル変換 REST API](conversion/conversion-rest-api.md) に関する説明を参照してください。

#### <a name="start-asset-conversion"></a>資産の変換を開始します

```cs
private StartConversionAsync _pendingAsync = null;

void StartAssetConversion(AzureFrontend frontend, string storageContainer, string blobinputpath, string bloboutpath, string modelName, string outputName)
{
    _pendingAsync = frontend.StartConversionAsync(
        new AssetConversionInputParams(storageContainer, blobinputpath, "", modelName),
        new AssetConversionOutputParams(storageContainer, bloboutpath, "", outputName)
        );
    _pendingAsync.Completed +=
        (StartConversionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to start asset conversion!");
            }
        };

        _pendingAsync = null;
}
```

```cpp
void StartAssetConversion(ApiHandle<AzureFrontend> frontend, std::string storageContainer, std::string blobinputpath, std::string bloboutpath, std::string modelName, std::string outputName)
{
    AssetConversionInputParams input;
    input.BlobContainerInformation.BlobContainerName = blobinputpath;
    input.BlobContainerInformation.StorageAccountName = storageContainer;
    input.BlobContainerInformation.FolderPath = "";
    input.InputAssetPath = modelName;

    AssetConversionOutputParams output;
    output.BlobContainerInformation.BlobContainerName = blobinputpath;
    output.BlobContainerInformation.StorageAccountName = storageContainer;
    output.BlobContainerInformation.FolderPath = "";
    output.OutputAssetPath = outputName;

    ApiHandle<StartAssetConversionAsync> conversionAsync = *frontend->StartAssetConversionAsync(input, output);
    conversionAsync->Completed([](ApiHandle<StartAssetConversionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to start asset conversion!");
        }
    }
    );
}
```


#### <a name="get-conversion-status"></a>変換の状態を取得する

```cs
private ConversionStatusAsync _pendingAsync = null
void GetConversionStatus(AzureFrontend frontend, string assetId)
{
    _pendingAsync = frontend.GetAssetConversionStatusAsync(assetId);
    _pendingAsync.Completed +=
        (ConversionStatusAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get status of asset conversion!");
            }

            _pendingAsync = null;
        };
}
```

```cpp
void GetConversionStatus(ApiHandle<AzureFrontend> frontend, std::string assetId)
{
    ApiHandle<ConversionStatusAsync> pendingAsync = *frontend->GetAssetConversionStatusAsync(assetId);
    pendingAsync->Completed([](ApiHandle<ConversionStatusAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // use res->Result
        }
        else
        {
            printf("Failed to get status of asset conversion!");
        }

    });
}
```


### <a name="rendering-apis"></a>API のレンダリング

セッション管理の詳細については、[セッション管理 REST API](session-rest-api.md) に関する説明を参照してください。

レンダリング セッションは、サービス上に動的に作成することも、既存のセッション ID を AzureSession オブジェクトに "開く" こともできます。

#### <a name="create-rendering-session"></a>レンダリング セッションを作成する

```cs
private CreateSessionAsync _pendingAsync = null;
void CreateRenderingSession(AzureFrontend frontend, RenderingSessionVmSize vmSize, ARRTimeSpan maxLease)
{
    _pendingAsync = frontend.CreateNewRenderingSessionAsync(
        new RenderingSessionCreationParams(vmSize, maxLease));

    _pendingAsync.Completed +=
        (CreateSessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to create session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, RenderingSessionVmSize vmSize, const ARRTimeSpan& maxLease)
{
    RenderingSessionCreationParams params;
    params.MaxLease = maxLease;
    params.Size = vmSize;
    ApiHandle<CreateSessionAsync> pendingAsync = *frontend->CreateNewRenderingSessionAsync(params);

    pendingAsync->Completed([] (ApiHandle<CreateSessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res->Result
        }
        else
        {
            printf("Failed to create session!");
        }
    });
}
```

#### <a name="open-an-existing-rendering-session"></a>既存のレンダリング セッションを開く

既存のセッションを開くことは同期呼び出しです。

```cs
void CreateRenderingSession(AzureFrontend frontend, string sessionId)
{
    AzureSession session = frontend.OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend, std::string sessionId)
{
    ApiHandle<AzureSession> session = *frontend->OpenRenderingSession(sessionId);
    // Query session status, etc.
}
```


#### <a name="get-current-rendering-sessions"></a>現在のレンダリング セッションを取得する

```cs
private SessionPropertiesArrayAsync _pendingAsync = null;
void GetCurrentRenderingSessions(AzureFrontend frontend)
{
    _pendingAsync = frontend.GetCurrentRenderingSessionsAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesArrayAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get current rendering sessions!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetCurrentRenderingSessions(ApiHandle<AzureFrontend> frontend)
{
    ApiHandle<SessionPropertiesArrayAsync> pendingAsync = *frontend->GetCurrentRenderingSessionsAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesArrayAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // use res.Result
        }
        else
        {
            printf("Failed to get current rendering sessions!");
        }
    });
}
```

### <a name="session-apis"></a>セッション API

#### <a name="get-rendering-session-properties"></a>レンダリング セッションのプロパティを取得する

```cs
private SessionPropertiesAsync _pendingAsync = null;
void GetRenderingSessionProperties(AzureSession session)
{
    _pendingAsync = session.GetPropertiesAsync();
    _pendingAsync.Completed +=
        (SessionPropertiesAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                //use res.Result
            }
            else
            {
                Console.WriteLine("Failed to get properties of session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void GetRenderingSessionProperties(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionPropertiesAsync> pendingAsync = *session->GetPropertiesAsync();
    pendingAsync->Completed([](ApiHandle<SessionPropertiesAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            //use res.Result
        }
        else
        {
            printf("Failed to get properties of session!");
        }
    });
}
```

#### <a name="update-rendering-session"></a>レンダリング セッションを更新する

```cs
private SessionAsync _pendingAsync;
void UpdateRenderingSession(AzureSession session, ARRTimeSpan updatedLease)
{
    _pendingAsync = session.RenewAsync(
        new RenderingSessionUpdateParams(updatedLease));
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session renewed succeeded!");
            }
            else
            {
                Console.WriteLine("Failed to renew rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void UpdateRenderingSession(ApiHandle<AzureSession> session, const ARRTimeSpan& updatedLease)
{
    RenderingSessionUpdateParams params;
    params.MaxLease = updatedLease;
    ApiHandle<SessionAsync> pendingAsync = *session->RenewAsync(params);
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            printf("Rendering session renewed succeeded!");
        }
        else
        {
            printf("Failed to renew rendering session!");
        }
    });
}
```

#### <a name="stop-rendering-session"></a>レンダリング セッションを停止する

```cs
private SessionAsync _pendingAsync;
void StopRenderingSession(AzureSession session)
{
    _pendingAsync = session.StopAsync();
    _pendingAsync.Completed +=
        (SessionAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                Console.WriteLine("Rendering session stopped successfully!");
            }
            else
            {
                Console.WriteLine("Failed to stop rendering session!");
            }
            _pendingAsync = null;
        };
}
```

```cpp
void StopRenderingSession(ApiHandle<AzureSession> session)
{
    ApiHandle<SessionAsync> pendingAsync = *session->StopAsync();
    pendingAsync->Completed([](ApiHandle<SessionAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            printf("Rendering session stopped successfully!");
        }
        else
        {
            printf("Failed to stop rendering session!");
        }
    });
}
```

#### <a name="connect-to-arr-inspector"></a>ARR インスペクターに接続する

```cs
private ArrInspectorAsync _pendingAsync = null;
void ConnectToArrInspector(AzureSession session, string hostname)
{
    _pendingAsync = session.ConnectToArrInspectorAsync(hostname);
    _pendingAsync.Completed +=
        (ArrInspectorAsync res) =>
        {
            if (res.IsRanToCompletion)
            {
                // Launch the html file with default browser
                string htmlPath = res.Result;
#if WINDOWS_UWP
                UnityEngine.WSA.Application.InvokeOnUIThread(async () =>
                {
                    var file = await Windows.Storage.StorageFile.GetFileFromPathAsync(htmlPath);
                    await Windows.System.Launcher.LaunchFileAsync(file);
                }, true);
#else
                InvokeOnAppThreadAsync(() =>
                {
                    System.Diagnostics.Process.Start("file:///" + htmlPath);
                });
#endif
            }
            else
            {
                Console.WriteLine("Failed to connect to ARR inspector!");
            }
        };
}
```

```cpp
void ConnectToArrInspector(ApiHandle<AzureSession> session, std::string hostname)
{
    ApiHandle<ArrInspectorAsync> pendingAsync = *session->ConnectToArrInspectorAsync(hostname);
    pendingAsync->Completed([](ApiHandle<ArrInspectorAsync> res)
    {
        if (res->IsRanToCompletion())
        {
            // Launch the html file with default browser
            std::string htmlPath = "file:///" + *res->Result();
            ShellExecuteA(NULL, "open", htmlPath.c_str(), NULL, NULL, SW_SHOWDEFAULT);
        }
        else
        {
            printf("Failed to connect to ARR inspector!");
        }
    });
}
```

## <a name="next-steps"></a>次のステップ

* [アカウントの作成](create-an-account.md)
* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)

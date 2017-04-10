---
title: "Java で信頼性の高い Azure マイクロサービスを初めて作成する | Microsoft Docs"
description: "ステートレス サービスとステートフル サービスを使用して Microsoft Azure Service Fabric アプリケーションを作成する方法。"
services: service-fabric
documentationcenter: java
author: vturecek
manager: timlt
editor: 
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/10/2017
ms.author: vturecek
translationtype: Human Translation
ms.sourcegitcommit: cf8f717d5343ae27faefdc10f81b4feaccaa53b9
ms.openlocfilehash: 5a29d6838af7f3952ad96158e5962b17c0f4cb6b
ms.lasthandoff: 01/24/2017


---
# <a name="get-started-with-reliable-services"></a>Reliable Services 使用
> [!div class="op_single_selector"]
> * [Windows での C# ](service-fabric-reliable-services-quick-start.md)
> * [Linux での Java](service-fabric-reliable-services-quick-start-java.md)
>
>

ここでは、Azure Service Fabric Reliable Services の基本と、Java で記述された簡単な Reliable Services アプリケーションを作成およびデプロイする手順について説明します。 また、次の Microsoft Virtual Academy のビデオでは、ステートレスな Reliable Services を作成する方法を紹介しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-reliable-services-quick-start-java/ReliableServicesJavaVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="installation-and-setup"></a>インストールとセットアップ
開始する前に、マシン上に Service Fabric 開発環境がセットアップされていることを確認します。
設定する必要がある場合は、[Mac での作業](service-fabric-get-started-mac.md)または [Linux での作業](service-fabric-get-started-linux.md)を参照してください。

## <a name="basic-concepts"></a>基本的な概念
Reliable Services の使用を開始するには、いくつかの基本的な概念を理解する必要があります。

* **サービスの種類**: 使用するサービス実装です。 `StatelessService` を拡張する記述したクラスと、そこで使用する他のコードまたは依存関係のほか、名前やバージョン番号によって定義されます。
* **名前付きサービス インスタンス**: サービスを実行するには、サービスの種類の名前付きインスタンスを作成します。これは、クラスの種類のオブジェクト インスタンスを作成するのに似ています。 サービス インスタンスは、実際には、記述するサービス クラスのオブジェクト インスタンスです。
* **サービス ホスト**: 作成した名前付きサービス インスタンスは、ホスト内で実行する必要があります。 サービス ホストは単なる&1; プロセスで、ここでサービスのインスタンスを実行できます。
* **サービス登録**: 登録はすべてを&1; つにまとめます。 サービス ホストでサービスの種類を Service Fabric ランタイムに登録して、Service Fabric がそのインスタンスを作成して実行できるようにする必要があります。  

## <a name="create-a-stateless-service"></a>ステートレス サービスの作成
最初に、Service Fabric アプリケーションを作成します。 Linux 用の Service Fabric SDK には、ステートレス サービスの Service Fabric アプリケーションのスキャフォールディングを提供する Yeoman ジェネレーターが含まれています。 最初に次の Yeoman コマンドを実行します。

```bash
$ yo azuresfjava
```

指示に従って、**信頼性の高いステートレス サービス**を作成します。 このチュートリアルでは、アプリケーションに "HelloWorldApplication" という名前を付け、サービスに "HelloWorld" という名前を付けます。 結果には、`HelloWorldApplication` と `HelloWorld` のディレクトリが含まれます。

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```

## <a name="implement-the-service"></a>サービスの実装
**HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java** を開きます。 このクラスは、サービスの種類を定義し、任意のコードを実行することができます。 サービス API には、コードのエントリ ポイントが&2; つあります。

* `runAsync()` という変更可能なエントリ ポイント メソッドでは、実行時間の長いコンピューティング ワークロードなどの任意のワークロードの実行を開始できます。

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* 選択した通信スタックをプラグインできる通信エントリ ポイント。 これは、ユーザーおよびその他のサービスからの要求の受信を開始できる場所です。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

このチュートリアルでは、`runAsync()` エントリ ポイント メソッドを取り上げます。 これは、コードの実行をすぐに開始できる場所です。

### <a name="runasync"></a>RunAsync
プラットフォームは、サービスのインスタンスが配置され実行準備ができたときに、このメソッドを呼び出します。 ステートレス サービスの場合、これは単にサービス インスタンスが開いたことを意味します。 サービス インスタンスを終了する必要がある場合のために、キャンセル トークンが提供されています。 Service Fabric では、サービス インスタンスの開始から終了のサイクルは、サービスのライフタイムで何度も発生する可能性があります。 これは、さまざまな理由で発生する可能性があります。

* システムがリソース分散のためにサービス インスタンスを移動している。
* コードでエラーが発生している。
* アプリケーションまたはシステムがアップグレードされている。
* 基礎となるハードウェアで障害が発生している。

この調整は、サービスの可用性を高めて適切なバランスを取るために、Service Fabric によって管理されます。

`runAsync()` は同期的なブロックを行いません。 CompletableFuture の内部で行う必要がある実行時間の長いタスクをワークロードで実装する必要がある場合は、 ランタイムが続行できるように CompletableFuture を RunAsync の実装で返す必要があります。

#### <a name="cancellation"></a>キャンセル
ワークロードの取り消しは、提供されたキャンセル トークンを使用して協調的に調整されます。 システムはタスクが完了 (正常に完了、キャンセル、または失敗) するまで待機してから、次に進みます。 システムからキャンセルが要求された場合は、キャンセル トークンを利用して作業を完了し、できるだけ早く `runAsync()` を終了することが重要です。 次の例は、キャンセル イベントを処理する方法を示しています。

```java
    @Override
    protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

        // TODO: Replace the following sample code with your own logic
        // or remove this runAsync override if it's not needed in your service.

        CompletableFuture.runAsync(() -> {
          long iterations = 0;
          while(true)
          {
            cancellationToken.throwIfCancellationRequested();
            logger.log(Level.INFO, "Working-{0}", ++iterations);

            try
            {
              Thread.sleep(1000);
            }
            catch (IOException ex) {}
          }
        });
    }
```

### <a name="service-registration"></a>サービス登録
サービスの種類は、Service Fabric ランタイムに登録する必要があります。 サービスの種類は、`ServiceManifest.xml` で定義され、さらにサービス クラスを実装する `StatelessService` で定義されます。 サービスの登録は、プロセスのメイン エントリ ポイントで実行されます。 この例では、プロセスのメイン エントリ ポイントは `HelloWorldServiceHost.java` です。

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="run-the-application"></a>アプリケーションの実行
Yeoman スキャフォールディングには、アプリケーションをビルドするための gradle スクリプトと、アプリケーションをデプロイおよびデプロイ解除するための bash スクリプトが含まれています。 アプリケーションを実行するには、最初に gradle を使用してアプリケーションをビルドします。

```bash
$ gradle
```

これにより、Service Fabric Azure CLI を使ってデプロイできる Service Fabric アプリケーション パッケージが生成されます。 Install.sh スクリプトには、アプリケーション パッケージを展開するために必要な Azure CLI コマンドが含まれています。 install.sh スクリプトを実行してデプロイします。

```bash
$ ./install.sh
```


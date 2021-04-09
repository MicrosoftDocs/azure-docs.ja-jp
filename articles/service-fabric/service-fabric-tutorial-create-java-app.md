---
title: 'チュートリアル: Azure Service Fabric で Java アプリを作成する'
description: このチュートリアルでは、フロントエンドを備えたリライアブル サービス Java アプリケーションと Reliable Services ステートフル バックエンドを作成し、クラスターにアプリケーションをデプロイする方法について説明します。
ms.topic: tutorial
ms.date: 09/01/2018
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-js
ms.openlocfilehash: d1f66177b1939d612a5ee80c99109c397b56cb37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91532195"
---
# <a name="tutorial-create-an-application-with-a-java-api-front-end-service-and-a-stateful-back-end-service-on-azure-service-fabric"></a>チュートリアル: Azure Service Fabric 上に Java API フロントエンド サービスとステートフル バックエンド サービスを含むアプリケーションを作成する

このチュートリアルは、シリーズの第 1 部です。 最後まで読み進めていけば、Azure Service Fabric 上のステートフルなバックエンド サービスに投票結果を保存する Java Web フロントエンドを備えた投票アプリケーションが完成します。 このチュートリアル シリーズでは、作業用の Mac OSX または Linux 開発者マシンが必要です。 投票アプリケーションを手動で作成しない場合は、[完成したアプリケーションのソース コードをダウンロード](https://github.com/Azure-Samples/service-fabric-java-quickstart)し、「[投票のサンプル アプリケーションの概要](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application)」に進むことができます。 また、[Java Reliable Services のクイックスタート](service-fabric-quickstart-java-reliable-services.md)に従うことも検討してください。

![Service Fabric の投票サンプル](./media/service-fabric-tutorial-create-java-app/service-fabric-java-voting-app-sample.png)

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Java Service Fabric Reliable Services アプリケーションをビルドする
> * [ローカル クラスターでアプリケーションをデプロイおよびデバッグする](service-fabric-tutorial-debug-log-local-cluster.md)
> * [アプリケーションを Azure クラスターにデプロイする](service-fabric-tutorial-java-deploy-azure.md)
> * [アプリケーションの監視と診断を設定する](service-fabric-tutorial-java-elk.md)
> * [CI/CD を設定します](service-fabric-tutorial-java-jenkins.md)


シリーズの第 1 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Java ステートフル リライアブル サービスを作成する
> * Java ステートレス Web アプリケーション サービスを作成する
> * サービスのリモート処理を使用してステートフル サービスと通信する
> * ローカル Service Fabric クラスターにアプリケーションをデプロイする

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Mac](service-fabric-get-started-mac.md) または [Linux](service-fabric-get-started-linux.md) の開発環境を設定します。 Eclipse プラグイン、Gradle、Service Fabric SDK、および Service Fabric CLI (sfctl) を手順に従ってインストールします。

## <a name="create-the-front-end-java-stateless-service"></a>フロントエンド Java ステートレス サービスを作成する

まず、投票アプリケーションの Web フロントエンドを作成します。 AngularJS を使用する Web UI は、軽量 HTTP サーバーを実行する Java ステートレス サービスに要求を送信します。 このサービスは各要求を処理し、投票を保存するためにリモート プロシージャ呼び出しをステートフル サービスに送信します。 

1. Eclipse を開きます。

2. **[File]\(ファイル\)**  >  **[New]\(新規\)**  >  **[Other]\(その他\)**  >  **[Service Fabric]**  >  **[Service Fabric Project]\(Service Fabric プロジェクト\)** の順に選択して、プロジェクトを作成します。

    ![Eclipse での新しい Service Fabric プロジェクト](./media/service-fabric-tutorial-create-java-app/service-fabric-project-wizard.png)

3. **[ServiceFabric Project Wizard]\(ServiceFabric プロジェクト ウィザード\)** ダイアログで、プロジェクトに **Voting** という名前を付け、**[Next]\(次へ\)** を選択します。

    ![新しいサービス ダイアログで Java ステートレス サービスを選択する](./media/service-fabric-tutorial-create-java-app/name-service-fabric-project-wizard.png) 

4. **[Add Service]\(サービスの追加\)** ページで、**[Stateless Service]\(ステートレス サービス\)** を選択し、サービスに **VotingWeb** という名前を付けます。 **[Finish]\(完了\)** を選択してプロジェクトを作成します。

    ![Service Fabric プロジェクトのステートレス サービスを作成する]( ./media/service-fabric-tutorial-create-java-app/add-service-fabric-votingweb-service.png)

    Eclipse によってアプリケーションとサービス プロジェクトが作成され、Package Explorer に表示されます。

    ![アプリケーションの作成後の Eclipse Package Explorer]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

前のスクリーンショットの Package Explorer の各項目を次の表で簡単に説明します。 

| **Package Explorer の項目** | **説明** |
| --- | --- |
| PublishProfiles | ローカルおよび Azure Service Fabric クラスターのプロファイルの詳細が記述された JSON ファイルが含まれています。 アプリケーションをデプロイするときに、これらのファイルの内容がプラグインによって使用されます。 |
| スクリプト | アプリケーションとクラスターをコマンド ラインから迅速に管理するためのヘルパー スクリプトが含まれています。 |
| VotingApplication | Service Fabric クラスターにプッシュされる Service Fabric アプリケーションが含まれています。 |
| VotingWeb | フロントエンド ステートレス サービスのソース ファイルが、関連する gradle ビルド ファイルと共に含まれています |
| build.gradle | プロジェクトを管理するために使用される Gradle ファイルです。 |
| settings.gradle | このフォルダー内の Gradle プロジェクトの名前が含まれています。 |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>HTML と JavaScript を VotingWeb サービスに追加する

ステートレス サービスによってレンダリングできる UI を追加するには、HTML ファイルを追加します。 これにより、この HTML ファイルは、ステートレス Java サービスに埋め込まれた軽量 HTTP サーバーによってレンダリングされるようになります。

1. *VotingApplication* ディレクトリを展開して *VotingApplication/VotingWebPkg/Code* ディレクトリに移動します。

2. *Code* ディレクトリを右クリックし、**[New]\(新規\)** > **[Folder]\(フォルダー\)** の順に選択します。

3. フォルダーに *wwwroot* という名前を付け、**[Finish]\(完了\)** を選択します。

    ![Eclipse によって wwwroot フォルダーが作成される](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. **wwwroot** に **index.html** という名前のファイルを追加し、次の内容をこのファイルに貼り付けます。

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>VotingWeb.java ファイルを更新する

**VotingWeb** サブプロジェクトの *VotingWeb/src/statelessservice/VotingWeb.java* ファイルを開きます。 **VotingWeb** サービスは、ステートレス サービスへのゲートウェイであり、フロントエンド API の通信リスナーを設定します。

ファイル内の既存の **createServiceInstanceListeners** メソッドを以下の内容に置き換え、変更を保存します。

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>HTTPCommunicationListener.java ファイルを追加する

HTTP 通信リスナーは、HTTP サーバーを設定し、投票アクションを定義する API を公開するコントローラーとして機能します。 *VotingWeb/src/statelessservice* フォルダーの *statelessservice* パッケージを右クリックして **[New]\(新規\)** > **[File]\(ファイル\)** の順に選択します。  ファイルに *HttpCommunicationListener.java* という名前を付け、**[Finish]\(完了\)** を選択します。

ファイルの内容を次のように置き換え、変更を保存します。  後の HttpCommunicationListener.java ファイルの更新では、バックエンド サービスの投票データのレンダリング、読み取り、および書き込みを行うための変更をこのファイルに加えます。  ここでは、リスナーは単に投票アプリの静的 HTML を返します。

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>リスニング ポートを構成する

VotingWeb サービス フロントエンド サービスが作成されると、Service Fabric によって、サービスがリッスンするポートが選択されます。  VotingWeb サービスは、このアプリケーションのフロント エンドとして機能して外部のトラフィックを受け入れるため、このサービスを固定のウェルノウン ポートにバインドしましょう。 Package Explorer で、*VotingApplication/VotingWebPkg/ServiceManifest.xml* を開きます。  **Resources** セクションの **Endpoint** リソースを検索し、**Port** 値を 8080 に変更します (チュートリアル全体で、このポートを使い続けます)。 アプリケーションをローカルでデプロイして実行するには、アプリケーションのリスニング ポートをコンピューター上で開いて、使用できるようにする必要があります。 **ServiceManifest** 要素内 (```<DataPackage>``` 要素のすぐ下) に次のコード スニペットを貼り付けます。

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>アプリケーションにステートフルなバックエンド サービスを追加する

これで、Java Web API サービスのスケルトンが完成しました。次に進み、ステートフル バックエンド サービスを完成させましょう。

Service Fabric では、Reliable Collection によってデータがサービス内に一貫して確実に格納されます。 Reliable Collection は、可用性が高く信頼できる一連のコレクション クラスです。 Java コレクションを使用したことがある方は、これらのクラスの使用法をもうよくご存じでしょう。

1. Package Explorer で、アプリケーション プロジェクト内の **Voting** を右クリックし、**[Service Fabric]** > **[Add Service Fabric Service]\(Service Fabric サービスの追加\)** の順に選択します。

2. **[Add Service]\(サービスの追加\)** ダイアログで、**[Stateful Service]\(ステートフル サービス\)** を選択し、サービスに **VotingDataService** という名前を付けて **[Add Service]\(サービスの追加\)** を選択します。

    作成したサービス プロジェクトでは、2 つのサービスがアプリケーションに含まれます。 アプリケーションのビルドを続けながら、同じ方法でさらにサービスを追加することができます。 それぞれを個別にバージョン管理およびアップグレードできます。

3. Eclipse によってサービス プロジェクトが作成され、Package Explorer に表示されます。

    ![Eclipse Project Explorer](./media/service-fabric-tutorial-create-java-app/service-fabric-package-explorer-java.png)

### <a name="add-the-votingdataservicejava-file"></a>VotingDataService.java ファイルを追加する

*VotingDataService.java* ファイルには、Reliable Collection の投票を取得、追加、および削除するためのロジックを含むメソッドが含まれています。 *VotingDataService/src/statefulservice/VotingDataService.java* ファイルに次の **VotingDataService** クラス メソッドを追加します。

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

これで、フロントエンド ステートレス サービスとバックエンド サービスのスケルトンを作成できました。

## <a name="create-the-communication-interface-to-your-application"></a>アプリケーションへの通信インターフェイスを作成する

 次の手順は、フロントエンド ステートレス サービスとバックエンド サービスを接続することです。 どちらのサービスも、投票アプリケーションの動作を定義する VotingRPC と呼ばれるインターフェイスを利用しています。 このインターフェイスは、フロントエンド サービスとバックエンド サービスの両方によって実装され、2 つのサービス間のリモート プロシージャ コール (RPC) を可能にします。 残念ながら、Eclipse では Gradle サブプロジェクトの追加がサポートされていないため、このインターフェイスを含むパッケージを手動で追加する必要があります。

1. Package Explorer で **Voting** プロジェクトを右クリックし、**[New]\(新規\)** > **[Folder]\(フォルダー\)** の順に選択します。 フォルダーに **VotingRPC/src/rpcmethods** という名前を付けます。

    ![Eclipse Package Explorer で VotingRPC パッケージを作成する](./media/service-fabric-tutorial-create-java-app/create-voting-rpc-package-java.png)

3. *Voting/VotingRPC/src/rpcmethods* の下に *VotingRPC.java* という名前のファイルを作成し、この **VotingRPC.java** ファイル内に以下を貼り付けます。 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. *Voting/VotingRPC* ディレクトリに *build.gradle* という名前の空のファイルを作成し、ファイル内に以下を貼り付けます。 この gradle ファイルは、他のサービスによってインポートされる jar ファイルをビルドおよび作成するために使用されます。 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. *Voting/settings.gradle* ファイルに、新しく作成したプロジェクトをビルドに含めるための行を追加します。 

    ```gradle
    include ':VotingRPC'
    ```

6. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* ファイル内のコメント ブロックを以下に置き換えます。  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* ファイルの先頭に適切な import ステートメントを追加します。 

    ```java
    import rpcmethods.VotingRPC; 
    ```

この段階で、フロントエンド、バックエンド、および RPC インターフェイスの機能が完成しました。 次の段階では、Service Fabric クラスターにデプロイする前に Gradle スクリプトを適切に構成します。 

## <a name="walk-through-the-voting-sample-application"></a>投票のサンプル アプリケーションの概要
この投票アプリケーションは次の 2 つのサービスから成ります。
- Web フロントエンド サービス (VotingWeb) - Web ページを提供し、バックエンド サービスとやり取りするための API を公開する Java Web フロントエンド サービス。
- バックエンド サービス (VotingDataService) - 投票を保持するためにリモート プロシージャ コール (RPC) を介して呼び出されるメソッドを定義する Java Web サービス。

![投票のサンプルの図](./media/service-fabric-tutorial-create-java-app/walkthrough-java-voting.png)

アプリケーションでアクション (項目の追加、投票、項目の削除) を実行すると、次のイベントが発生します。
1. JavaScript が、適切な要求を Web フロントエンド サービスの Web API に HTTP 要求として送信します。

2. Web フロントエンド サービスが、Service Fabric の組み込みのサービスのリモート処理機能を使用して要求を見つけ、バックエンド サービスに転送します。 

3. バックエンド サービスが、信頼できる辞書の結果を更新するメソッドを定義します。 この信頼できる辞書の内容は、クラスター内の複数のノードに複製され、ディスク上に保持されます。 アプリケーションのデータは、すべてクラスターに保存されます。 

## <a name="configure-gradle-scripts"></a>Gradle スクリプトを構成する 

このセクションでは、プロジェクトの Gradle スクリプトを構成します。 

1. *Voting/build.gradle* ファイルの内容を以下に置き換えます。

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. *Voting/VotingWeb/build.gradle* ファイルの内容を以下に置き換えます。

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. *Voting/VotingDataService/build.gradle* ファイルの内容を置き換えます。 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>アプリケーションをローカル クラスターにデプロイする

これで、アプリケーションをローカル Service Fabric クラスターにデプロイする準備が整いました。

1. Package Explorer で **Voting** プロジェクトを右クリックし、**[Service Fabric]** > **[Build Application]\(アプリケーションのビルド\)** の順に選択して、アプリケーションをビルドします。

2. ローカル Service Fabric クラスターを実行します。 この手順は開発環境 (Mac または Linux) によって異なります。

    Mac を使用している場合は、次のコマンドを使用してローカル クラスターを実行します。**-v** パラメーターに渡すコマンドを自分のワークスペースへのパスに置き換えてください。

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
    ```
    [OS X の設定ガイド](service-fabric-get-started-mac.md)で、詳細な手順を参照してください。

    Linux マシンで実行している場合は、次のコマンドを使用してローカル クラスターを起動します。 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    [Linux の設定ガイド](service-fabric-get-started-linux.md)で、詳細な手順を参照してください。

4. Eclipse の Package Explorer で、**Voting** プロジェクトを右クリックし、**[Service Fabric]** > **[Publish Application]\(アプリケーションの発行\)** の順に選択します 
5. **[Publish Application]\(アプリケーションの発行\)** ウィンドウで、ドロップダウンから **[Local.json]** を選択し、**[Publish]\(発行\)** を選択します。
6. Web ブラウザーを使用して http:\//localhost:8080 にアクセスし、ローカル Service Fabric クラスターで実行されているアプリケーションを表示します。 

## <a name="next-steps"></a>次のステップ

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Java サービスをステートフル リライアブル サービスとして作成する
> * Java サービスをステートレス Web サービスとして作成する
> * サービス間のリモート プロシージャ コール (RPC) を処理する Java インターフェイスを追加する
> * Gradle スクリプトを構成する
> * アプリケーションをビルドしてローカル Service Fabric クラスターにデプロイする

次のチュートリアルに進みます。
> [!div class="nextstepaction"]
> [ローカル クラスター上のアプリケーションのデバッグとログ記録](service-fabric-tutorial-debug-log-local-cluster.md)

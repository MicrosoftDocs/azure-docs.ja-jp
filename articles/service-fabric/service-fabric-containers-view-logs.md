---
title: Azure Service Fabric でコンテナー ログを表示する
description: Service Fabric Explorer を使用して実行中の Service Fabric コンテナー サービスのコンテナー ログを表示する方法について説明します。
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75458187"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Service Fabric コンテナー サービスのログを表示する
Azure Service Fabric はコンテナー オーケストレーターであり、[Linux コンテナーと Windows コンテナー](service-fabric-containers-overview.md)の両方をサポートします。  この記事では、問題を診断してトラブルシューティングできるように、実行中のコンテナー サービスまたはデッド コンテナー (クラッシュしたコンテナー) のコンテナー ログを表示する方法について説明します。

## <a name="access-the-logs-of-a-running-container"></a>実行中のコンテナーのログにアクセスする
コンテナー ログには、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用してアクセスできます。  Web ブラウザーで、`http://mycluster.region.cloudapp.azure.com:19080/Explorer` に移動することによってクラスターの管理エンドポイントから Service Fabric Explorer を開きます。  

コンテナー ログは、コンテナー サービス インスタンスが実行しているクラスター ノードにあります。 例として、[Linux Voting サンプル アプリケーション](service-fabric-quickstart-containers-linux.md)の Web フロントエンド コンテナーのログを取得します。 ツリー ビューで、 **[クラスター]** > **[アプリケーション]** > **[VotingType]** > **[fabric:/Voting/azurevotefront]** の順に展開します。  次に、パーティション (この例では d1aa737e-f22a-e347-be16-eec90be24bc1) を展開し、コンテナーがクラスター ノード *_lnxvm_0* 上で実行していることを確認します。

ツリー ビューで、**[ノード]**>**[_lnxvm_0]**>**[fabric:/Voting]**>**[azurevotfrontPkg]**>**[コード パッケージ]**>**[コード]** の順に展開し、*_lnxvm_0* ノード上でコード パッケージを探します。  その後、**[コンテナー ログ]** オプションを選択してコンテナー ログを表示します。

![Service Fabric platform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>デッド コンテナー (クラッシュしたコンテナー) のログにアクセスする
v6.2 以降、[REST API](/rest/api/servicefabric/sfclient-index) または [Service Fabric CLI (SFCTL)](service-fabric-cli.md) コマンドを利用し、デッド コンテナー (クラッシュしたコンテナー) のログも取得できるようになりました。

### <a name="set-container-retention-policy"></a>コンテナーの保持ポリシーを設定する
コンテナーのスタートアップ エラーの診断を支援するために、Service Fabric (バージョン 6.1 以降) では、終了または起動に失敗したコンテナーの保持をサポートしています。 このポリシーは、次のスニペットで示すように、**ApplicationManifest.xml** ファイルで設定できます。
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

**ContainersRetentionCount** の設定で、エラーが発生したときに保持するコンテナーの数を指定します。 負の値が指定されている場合は、エラーが発生したすべてのコンテナーが保持されます。 **ContainersRetentionCount** 属性が指定されていない場合、コンテナーは保持されません。 **ContainersRetentionCount** 属性はアプリケーション パラメーターもサポートしているため、ユーザーはテスト クラスターと運用環境クラスターで別の値を指定できます。 コンテナー サービスが他のノードに移動することを防ぐためにこの機能を使用する場合は、配置の制約を使用して、コンテナー サービスの対象を特定のノードに制約してください。 この機能を使用して保持されるコンテナーは、手動で削除する必要があります。

**RunInteractive** 設定は、Docker の `--interactive` および `tty` [フラグ](https://docs.docker.com/engine/reference/commandline/run/#options)に対応します。 マニフェスト ファイルでこの設定を true にすると、これらのフラグを使用してコンテナーが開始されます。  

### <a name="rest"></a>REST
「[Get Container Logs Deployed On Node](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode)」 (ノードにデプロイされているコンテナーのログを取得する) の操作を使用し、クラッシュしたコンテナーのログを取得します。 コンテナーが実行されたノードの名前、アプリケーション名、サービス マニフェスト名、コード パッケージ名を指定します。  `&Previous=true`を指定します。 応答には、コード パッケージ インスタンスのデッド コンテナーのコンテナー ログが含まれます。

要求 URI の形式は次のようになります。

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

要求の例:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 応答本文:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
[sfctl service get-container-logs](service-fabric-sfctl-service.md) コマンドを使用し、クラッシュしたコンテナーのログを取得します。  コンテナーが実行されたノードの名前、アプリケーション名、サービス マニフェスト名、コード パッケージ名を指定します。 `--previous` フラグを指定します。  応答には、コード パッケージ インスタンスのデッド コンテナーのコンテナー ログが含まれます。

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
応答:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>次のステップ
- [Linux コンテナー アプリケーションの作成チュートリアル](service-fabric-tutorial-create-container-images.md)を行う。
- [Service Fabric とコンテナー](service-fabric-containers-overview.md)についてさらに詳しく学習する

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png

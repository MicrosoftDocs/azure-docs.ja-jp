---
title: 運用環境の準備状況とベスト プラクティス - Azure
description: この記事では、運用環境で Live Video Analytics on IoT Edge モジュールを構成してデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 56982d84b7ffac718072683076657d56a2691d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97400558"
---
# <a name="production-readiness-and-best-practices"></a>運用環境の準備状況とベスト プラクティス

この記事では、運用環境で Live Video Analytics on IoT Edge モジュールを構成してデプロイする方法について説明します。 また、IoT Edge ソリューションの準備について、記事「[IoT Edge ソリューションを運用環境にデプロイするための準備を行う](../../iot-edge/production-checklist.md)」を確認する必要があります。 

> [!NOTE]
> セキュリティに関連する側面については、組織の IT 部門に問い合わせてください。

## <a name="running-the-module-as-a-local-user"></a>ローカル ユーザーとしてのモジュールの実行

Live Video Analytics on IoT Edge モジュールを Edge デバイスにデプロイすると、既定では昇格された特権で実行されます。 この操作を実行し、モジュールのログを確認すると (`sudo iotedge logs {name-of-module}`)、次のように表示されます。

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
       See https://aka.ms/lva-iot-edge-prod-checklists-user-accounts for more information.
```

以下のセクションでは、上記の警告に対処する方法について説明します。

### <a name="creating-and-using-a-local-user-account"></a>ローカル ユーザー アカウントの作成と使用

Live Video Analytics on IoT Edge モジュールは、可能な限り少ない特権を持つローカル ユーザー アカウントで実行でき、またそのようにする必要があります。 たとえば、次のコマンドは、Linux VM でローカル ユーザー アカウントを作成する方法を示しています。

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

次に、配置マニフェストで、LOCAL_USER_ID と LOCAL_GROUP_ID 環境変数をその非ルート ユーザーとグループに設定できます。

```
"lvaEdge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>デバイスのストレージへのアクセス許可を付与する

Live Video Analytics on IoT Edge モジュールでは、次の場合にローカル ファイル システムにファイルを書き込める必要があります。

* モジュール ツインの [`applicationDataDirectory`](module-twin-configuration-schema.md#module-twin-properties) プロパティを使用する場合。ここでは、構成データを格納するローカル ファイル システムのディレクトリを指定する必要があります。
* メディア グラフを使用してビデオをクラウドに記録します。モジュールで、エッジ デバイス上のディレクトリをキャッシュとして使用する必要があります (詳細については、記事「[継続的なビデオ記録](continuous-video-recording-concept.md)」を参照してください)。
* [ローカル ファイルに記録する](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources)場合。記録されるビデオのファイル パスを指定する必要があります。

上記のいずれかを使用する場合は、前述のユーザー アカウントに、関連するディレクトリへのアクセス権があることを確認する必要があります。 たとえば、applicationDataDirectory を考えてみましょう。 エッジ デバイス上にディレクトリを作成し、デバイス ストレージをモジュール ストレージにリンクすることができます。 

```
sudo mkdir /var/local/mediaservices
sudo chown -R edgeuser /var/local/mediaservices
```

次に、配置マニフェストのエッジ モジュールの作成オプションで、上記のディレクトリ ("var/local/mediaservices/") をモジュール内のディレクトリ ("/var/lib/azuremediaservices/" など) にマッピングするバインド設定を追加できます。 また、applicationDataDirectory の値として後者のディレクトリを使用します。

```
"lvaEdge": {
    "version": "1.0",
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
        "image": "mcr.microsoft.com/media/live-video-analytics:1.0",
        "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"\",\"Config\":{\"max-size\":\"10m\",\"max-file\":\"10\"}},\"Binds\":[\"/var/local/mediaservices/:/var/lib/azuremediaservices/\"]}}"
    },
    "env": {
        "LOCAL_USER_ID": 
        {
            "value": "1010"
        },
        "LOCAL_GROUP_ID": {
            "value": "1010"
        }
    }
    },
    …
    
    "lvaEdge": {
    "properties.desired": {
    "applicationDataDirectory": "/var/lib/azuremediaservices",
    …
    }
}
```

クイックスタートやチュートリアル (「[継続的なビデオ記録](continuous-video-recording-tutorial.md)」など) のサンプル メディア グラフからわかるように、メディア キャッシュ ディレクトリ (localMediaCachePath) は applicationDataDirectory の下のサブディレクトリを使用します。 キャッシュには一時的なデータが含まれているため、これが推奨される方法です。

### <a name="naming-video-assets-or-files"></a>ビデオ アセットまたはファイルの名前付け

メディア グラフでは、クラウド内のアセットまたはエッジ上に mp4 ファイルを作成できます。 メディア アセットは、[継続的なビデオ記録](continuous-video-recording-tutorial.md)、または[イベントベースのビデオ記録](event-based-video-recording-tutorial.md)によって生成できます。 これらのアセットとファイルには必要に応じて名前を付けることができますが、継続的なビデオ記録に基づくメディア アセットに推奨される名前付け構造は、"&lt;anytext&gt;-${System.GraphTopologyName}-${System.GraphInstanceName}" です。   

置換パターンは、 **${variableName}** のように、$ 記号、中かっこの順で指定します。  

例として、アセット シンクで assetNamePattern を次のように設定できます。

```
"assetNamePattern": "sampleAsset-${System.GraphTopologyName}-${System.GraphInstanceName}
```

イベントベースのビデオ記録で生成されるアセットの場合、推奨される名前付けパターンは、"&lt;anytext&gt;-${System.DateTime}" です。 システム変数を利用すると、イベントが同時発生した場合にアセットが上書きされないようにすることができます。 例として、アセット シンクで assetNamePattern を次のように設定できます。

```
"assetNamePattern": "sampleAssetFromEVR-LVAEdge-${System.DateTime}"
```

同じグラフの複数のインスタンスを実行している場合は、グラフのトポロジ名とインスタンス名を使用して区別できます。 例として、アセット シンクで assetNamePattern を次のように設定できます。

```
"assetNamePattern": "sampleAssetFromEVR-${System.GraphTopologyName}-${System.GraphInstanceName}-${System.DateTime}"
```

エッジでイベントベースのビデオ記録で生成される mp4 ビデオ クリップの場合、推奨される名前付けパターンには、DateTime を含める必要があります。また、同じグラフの複数のインスタンスがある場合は、システム変数 GraphTopologyName と GraphInstanceName を使用することが推奨されます。 例として、ファイル シンクで filePathPattern を次のように設定できます。 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}-${System.DateTime}"
```

または 

```
"fileNamePattern": "/var/media/sampleFilesFromEVR-${fileSinkOutputName}--${System.GraphTopologyName}-${System.GraphInstanceName} ${System.DateTime}"
```
>[!NOTE]
> 上の例では、変数 **fileSinkOutputName** は、グラフ トポロジで定義したサンプル変数名です。 これは、システム変数では **ありません**。 

#### <a name="system-variables"></a>システム変数
使用できるシステム定義変数には、次のものがあります。

|システム変数|説明|例|
|-----------|-----------|-----------|
|System.DateTime|ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYYMMDDThhmmss)。|20200222T173200Z|
|System.PreciseDateTime|ミリ秒付きの ISO8601 ファイルに準拠した形式の UTC 日付時刻 (基本表現 YYYYMMDDThhmmss.sss)。|20200222T173200.123Z|
|System.GraphTopologyName|ユーザーが指定した実行中のグラフ トポロジの名前。|IngestAndRecord|
|System.GraphInstanceName|ユーザーが指定した実行中のグラフ インスタンスの名前。|camera001|

>[!TIP]
> 名前に "." が含まれるため、アセットに System.PreciseDateTime という名前を付けることは できません。
### <a name="keeping-your-vm-clean"></a>VM をクリーンな状態に保つ

エッジ デバイスとして使用している Linux VM は、定期的に管理されていない場合、応答しなくなる可能性があります。 キャッシュをクリーンな状態にし、不要なパッケージを排除し、未使用のコンテナーを VM から削除することが不可欠です。 これを行うため、ここではエッジ VM で使用できる一連の推奨コマンドを紹介します。

1. `sudo apt-get clean`

    apt-get clean コマンドは、/var/cache に残されている取得済みパッケージ ファイルのローカル リポジトリをクリアします。 クリーンアップするディレクトリは /var/cache/apt/archives/ と /var/cache/apt/archives/partial/ です。 /Var/cache/apt/archives に残されるファイルは、ロック ファイルと partial サブディレクトリのみです。 apt-get clean コマンドは、通常、定期的な予定メンテナンスの一環として、必要に応じてディスク領域をクリアするために使用されます。 詳細については、[apt-get を使用したクリーンアップ](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)に関する記事を参照してください。
1. `sudo apt-get autoclean`

    apt-get autoclean オプションは、apt-get clean のように、取得済みパッケージ ファイルのローカル リポジトリをクリアします。ただし、ダウンロードできなくなり、役に立たないファイルのみが削除されます。 これは、キャッシュが大きくなりすぎないようにするために役立ちます。
1. `sudo apt-get autoremove1`

    autoremove オプションは、他のパッケージで必要になったために自動的にインストールされたものの、そのパッケージが削除されたために不要になったパッケージを削除します
1. `sudo docker image ls` – エッジ システム上の Docker イメージの一覧を提供します
1. `sudo docker system prune`

    Docker は、イメージ、コンテナー、ボリューム、ネットワークなど、使用されていないオブジェクトをクリーンアップするための控えめなアプローチ ("ガベージ コレクション" と呼ばれることもあります) を採用しています。これらのオブジェクトは、通常、Docker に明示的に指示しない限り削除されません。 これにより、Docker は追加のディスク領域を使用する可能性があります。 オブジェクトの種類ごとに、Docker には prune コマンドが用意されています。 さらに、docker system prune を使用して、複数の種類のオブジェクトを一度にクリーンアップできます。 詳細については、[未使用の Docker オブジェクトを排除する方法](https://docs.docker.com/config/pruning/)に関するページを参照してください。
1. `sudo docker rmi REPOSITORY:TAG`

    エッジ モジュールで更新が行われても、Docker に古いバージョンのエッジ モジュールがまだ存在している可能性があります。 このような場合は、docker rmi コマンドを使用して、イメージ バージョン タグで識別される特定のイメージを削除することをお勧めします。

## <a name="next-steps"></a>次のステップ

[クイック スタート: はじめに - Live Video Analytics on IoT Edge](get-started-detect-motion-emit-events-quickstart.md)

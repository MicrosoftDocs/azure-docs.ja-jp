---
title: 運用環境のためにデバイスとデプロイを準備する - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge ソリューションを開発環境から運用環境に移行する方法について説明します。これには、適切な証明書でのデバイスの設定と、今後のコード更新のためのデプロイ計画が含まれます。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: c64db6b35aa2f1daa4484f137c8505b1415c5a0b
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521756"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>IoT Edge ソリューションを運用環境にデプロイするための準備を行う

IoT Edge ソリューションを開発環境から運用環境に移行する準備ができたら、それが継続的なパフォーマンスのために構成されていることを確認します。

この記事で提供される情報はすべて同等であるとは限りません。 優先順位を付けやすくするために、各セクションの始めに、作業を**重要** (運用環境に移行する前に完了すること) および**有用** (知っておくと便利なこと) という 2 つのセクションに分けたリストが示されています。

## <a name="device-configuration"></a>デバイス構成

IoT Edge デバイスとして、Raspberry Pi から、ノート PC、サーバー上で実行されている仮想マシンまで、任意のものを指定できます。 デバイスには物理的に、または仮想接続を介してアクセスできる場合があります。また、デバイスは長時間分離される場合もあります。 いずれにせよ、適切に実行されるように構成されていることを確認する必要があります。 

* **重要**
    * 運用環境の証明書をインストールする
    * デバイスの管理を計画する
    * コンテナー エンジンとして Moby を使用する

* **有用**
    * アップストリーム プロトコルを選ぶ

### <a name="install-production-certificates"></a>運用環境の証明書をインストールする

運用環境内のすべての IoT Edge デバイスには、デバイス証明機関 (CA) の証明書をインストールする必要があります。 その CA 証明書は、その後、config.yaml ファイルの IoT Edge ランタイムに宣言されます。 開発とテストをより簡単に行うために、IoT Edge ランタイムでは、config.yaml ファイルで証明書が宣言されていない場合に一時証明書が作成されます。 しかし、これらの一時証明書は 3 か月後に有効期限が切れるため、運用環境シナリオでは安全ではありません。 

デバイス CA 証明書のロールの詳細については、[Azure IoT Edge での証明書の使用方法](iot-edge-certs.md)に関するページを参照してください。

IoT Edge デバイスに証明書をインストールし、config.yaml ファイルから参照する方法の詳細については、「[Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md)」 (透過的なゲートウェイとして機能するように IoT Edge デバイスを構成する) を参照してください。 証明書を構成する手順は、デバイスがゲートウェイとして使用される予定であるかどうかに関係なく同じです。 この記事では、テスト専用のサンプル証明書を生成するためのスクリプトを提供します。 運用環境ではこれらのサンプル証明書を使用しないでください。 

### <a name="have-a-device-management-plan"></a>デバイスの管理を計画する

運用環境に任意のデバイスを配置する前に、今後の更新をどのように管理するかを知っておく必要があります。 IoT Edge デバイスでは、更新するコンポーネントのリストに以下のものが含まれる可能性があります。

* デバイス ファームウェア
* オペレーティング システム ライブラリ
* コンテナー エンジン (Moby など)
* IoT Edge デーモン
* CA 証明書

IoT Edge デーモンを更新する手順については、「[Update the IoT Edge runtime](how-to-update-iot-edge.md)」 (IoT Edge ランタイムの更新) を参照してください。 IoT Edge デーモンを更新するための現在の方法では、IoT Edge デバイスへの物理的アクセスまたは SSH アクセスが必要になります。 更新するデバイスの数が多い場合は、スクリプトに更新手順を追加することを検討するか、Ansible などの自動化ツールを使用して大規模な更新を行います。

### <a name="use-moby-as-the-container-engine"></a>コンテナー エンジンとして Moby を使用する

デバイスでコンテナー エンジンを使用することは、すべての IoT Edge デバイスの前提条件となります。 運用環境では moby-engine のみがサポートされます。 Docker などのその他のコンテナー エンジンは IoT Edge で動作します。開発用にこれらのエンジンを使用してもかまいません。 Azure IoT Edge で使用する場合は moby-engine を再配布でき、Microsoft ではこのエンジンのサービスを提供しています。 IoT Edge デバイス上でのその他のコンテナー エンジンの使用はサポートされていません。

### <a name="choose-upstream-protocol"></a>アップストリーム プロトコルを選ぶ

IoT Hub へのアップストリーム通信用のプロトコル (そのため、使用されるポート) を、Edge エージェントと Edge ハブの両方に対して構成することができます。 既定のプロトコルは AMQP ですが、ネットワーク設定に応じてそれを変更できます。 

2 つのランタイム モジュールの両方に **UpstreamProtocol** 環境変数があります。 変数の有効な値は次のとおりです。 

* MQTT
* AMQP
* MQTTWS
* AMQPWS

デバイス自体の config.yaml ファイルで、Edge エージェント用に UpstreamProtocol 変数を構成します。 たとえば、IoT Edge デバイスが、AMQP ポートをブロックするプロキシ サーバーの背後にある場合、IoT Hub への初期接続を確立するために WebSocket (AMQPWS) 経由で AMQP を使用するように Edge エージェントを構成する必要があることがあります。 

IoT Edge デバイスが接続されたら、必ず、以降のデプロイでも引き続き両方のランタイム モジュールに対して UpstreamProtocol 変数を構成してください。 このプロセスの例については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

## <a name="deployment"></a>Deployment

* **有用**
    * アップストリーム プロトコルに合わせる
    * Edge ハブで使用されるメモリ領域を減らす
    * デバッグ バージョンのモジュール イメージを使用しない

### <a name="be-consistent-with-upstream-protocol"></a>アップストリーム プロトコルに合わせる

既定の AMQP とは異なるプロトコルを使用するように IoT Edge デバイス上の Edge エージェントを構成した場合は、以降のすべてのデプロイで同じプロトコルを宣言する必要があります。 たとえば、IoT Edge デバイスが、AMQP ポートをブロックするプロキシ サーバーの背後にある場合、WebSocket (AMQPWS) 経由の AMQP で接続するようにデバイスを構成したと考えられます。 デバイスにモジュールをデプロイするときに、Edge エージェントと Edge ハブに対して同じ APQPWS プロトコルを構成しない場合、既定の AMQP で設定がオーバーライドされ、再度接続することはできません。 

Edge エージェントおよび Edge ハブ モジュールには、UpstreamProtocol 環境変数のみを構成する必要があります。 追加モジュールではすべて、ランタイム モジュールに設定されている任意のプロトコルが採用されます。 

このプロセスの例については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

### <a name="reduce-memory-space-used-by-edge-hub"></a>Edge ハブで使用されるメモリ領域を減らす

使用可能なメモリが限られている制約付きデバイスをデプロイする場合は、より効率的な容量で実行し、より少ないディスク領域を使用するように Edge ハブを構成できます。 しかし、これらの構成では Edge ハブのパフォーマンスが制限されるため、ソリューションに適したバランスを見つけてください。 

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>制限付きデバイスのパフォーマンスを最適化しない

Edge ハブは既定でパフォーマンスが最適化されているため、大きなメモリ チャンクの割り当てが試行されます。 この構成によって、Raspberry Pi などのより小さなデバイスで安定性の問題が発生する場合があります。 リソースに制約があるデバイスをデプロイする場合は、Edge ハブで **OptimizeForPerformance** 環境変数を **false** に設定することができます。 

詳細については、「[リソースに制約があるデバイスでの安定性の問題](troubleshoot.md#stability-issues-on-resource-constrained-devices)」を参照してください。

#### <a name="disable-unused-protocols"></a>未使用のプロトコルを無効にする

Edge ハブのパフォーマンスを最適化し、そのメモリ使用量を減らす別の方法は、ソリューションで使用していないすべてのプロトコルのプロトコル ヘッドを無効にすることです。 

プロトコル ヘッドは、デプロイ マニフェストで Edge ハブ モジュールに対してブール型の環境変数を設定することで構成されます。 変数には以下の 3 つがあります。

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

3 つの変数にはすべて *2 つのアンダースコア* があり、true または false に設定できます。 

#### <a name="reduce-storage-time-for-messages"></a>メッセージのストレージ時間を短縮する

何らかの理由でメッセージを IoT Hub に配信できない場合、そのメッセージは一時的に Edge ハブ モジュールに格納されます。 配信されなかったメッセージの有効期限が切れるまで Edge ハブで保持する期間を構成することができます。 デバイス上のメモリが心配な場合は、Edge ハブ モジュール ツインで **timeToLiveSecs** の値を下げることができます。 

timeToLiveSecs パラメーターの既定値は 7,200 秒 (2 時間) です。 

### <a name="do-not-use-debug-versions-of-module-images"></a>デバッグ バージョンのモジュール イメージを使用しない

テスト シナリオから運用環境シナリオに移行する場合は、必ず、デプロイ マニフェストからデバッグ構成を削除してください。 デプロイ マニフェストのモジュール イメージのいずれにも **\.debug** サフィックスがないことを確認します。 デバッグ用のモジュールでポートを公開するための作成オプションを追加した場合は、その作成オプションも削除します。 

## <a name="container-management"></a>コンテナー管理

* **重要**
    * コンテナー レジストリへのアクセスを管理する
    * タグを使用してバージョンを管理する

### <a name="manage-access-to-your-container-registry"></a>コンテナー レジストリへのアクセスを管理する

運用環境の IoT Edge デバイスにモジュールをデプロイする前に、部外者がコンテナー イメージにアクセスしたり、変更を加えたりできないように、コンテナー レジストリへのアクセスを制御していることを確認してください。 コンテナー イメージを管理するには、パブリックではなく、プライベート コンテナー レジストリを使用します。 

チュートリアルやその他のドキュメントでは、開発コンピューターで使用するものと同じコンテナー レジストリの資格情報を IoT Edge デバイスでも使用するように指示しています。 これらの指示は、単にテストおよび開発環境をより簡単に設定できるようにするためのものです。運用環境シナリオではこれらの指示に従わないでください。 Azure Container Registry では、IoT Edge デバイスの場合と同様に、自動的にまたはそれ以外の無人の方法でアプリケーションまたはサービスでコンテナー イメージがプルされるときに、[サービス プリンシパルによる認証](../container-registry/container-registry-auth-service-principal.md)が推奨されます。 コンテナー レジストリへの読み取り専用アクセス権を持つサービス プリンシパルを作成し、デプロイ マニフェストでそのユーザー名とパスワードを指定します。

### <a name="use-tags-to-manage-versions"></a>タグを使用してバージョンを管理する

タグは、Docker コンテナーのバージョンを区別するために使用できる Docker 概念です。 タグは、コンテナー リポジトリの末尾に付加される **1.0** などのサフィックスです。 たとえば、**mcr.microsoft.com/azureiotedge-agent:1.0** のようになります。 タグは可変であり、別のコンテナーを指すようにいつでも変更できます。したがって、チームは、今後モジュール イメージを更新する際に従う規則に同意する必要があります。 

また、タグは、IoT Edge デバイスに更新プログラムを適用するのに役立ちます。 更新バージョンのモジュールをコンテナー レジストリにプッシュするときに、タグを増分します。 次に、増分されたタグでデバイスに新しいデプロイをプッシュします。 コンテナー エンジンでは、増分されたタグが新しいバージョンとして認識され、最新バージョンのモジュールがご利用のデバイスにプルダウンされます。 

タグ規則の例については、[IoT Edge ランタイムの更新](how-to-update-iot-edge.md#understand-iot-edge-tags)に関する記述を参照してください。そこでは、IoT Edge でローリング タグと特定のタグを使用して、バージョンを追跡する方法について説明されています。 

## <a name="networking"></a>ネットワーク

* **有用**
    * アウトバウンド/インバウンド構成を確認する
    * 接続をホワイトリストに登録する
    * プロキシを介した通信を構成する

### <a name="review-outboundinbound-configuration"></a>アウトバウンド/インバウンド構成を確認する

Azure IoT Hub および IoT Edge の間の通信チャネルは、常にアウトバウンドに構成されます。 ほとんどの IoT Edge シナリオでは、3 つの接続のみが必要になります。 コンテナー エンジンは、モジュール イメージを保持するコンテナー レジストリと接続する必要があります。 IoT Edge ランタイムは、デバイス構成情報を取得する場合、またメッセージとテレメトリを送信する場合に IoT Hub と接続する必要があります。 また、自動プロビジョニングを使用する場合、IoT Edge デーモンはデバイス プロビジョニング サービスに接続する必要があります。 詳細については、[ファイアウォールとポート構成ルール](troubleshoot.md#firewall-and-port-configuration-rules-for-iot-edge-deployment)に関する記述を参照してください。

### <a name="whitelist-connections"></a>接続をホワイトリストに登録する

ネットワーク設定で、IoT Edge デバイスから行われた接続をホワイトリストに明示的に登録する必要がある場合、次の IoT Edge コンポーネントのリストを確認します。

* **IoT Edge エージェント**: 場合によっては WebSockets 経由で、IoT Hub への永続的な AMQP/MQTT 接続が開かれます。 
* **IoT Edge ハブ**: 場合によっては WebSockets 経由で、IoT Hub への 1 つの永続的な AMQP 接続または複数の MQTT 接続が開かれます。 
* **IoT Edge デーモン**: IoT Hub の間欠的な HTTPS 呼び出しが行われます。 

これら 3 つのいずれの場合も、DNS 名は \*.azure-devices.net というパターンと一致します。 

さらに、**コンテナー エンジン**では、HTTPS 経由でコンテナー レジストリを呼び出します。 IoT Edge ランタイムのコンテナー イメージを取得する場合、DNS 名は mcr.microsoft.com となります。 コンテナー エンジンは、デプロイで構成されているその他のレジストリに接続されます。 

このチェックリストを使用して、ファイアウォール規則を開始できます。

   | URL (\* = ワイルドカード) | 送信 TCP ポート | 使用法 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft コンテナー レジストリ |
   | global.azure-devices-provisioning.net  | 443 | DPS でのアクセス (任意指定) |
   | \*.azurecr.io | 443 | 個人やサード パーティのコンテナー レジストリ |
   | \*.blob.core.windows.net | 443 | イメージ差分のダウンロード | 
   | \*.azure-devices.net | 5671、8883、443 | IoT Hub でのアクセス |
   | \*.docker.io  | 443 | Docker Hub でのアクセス (任意指定) |

### <a name="configure-communication-through-a-proxy"></a>プロキシを介した通信を構成する

プロキシ サーバーを使用するネットワークにデバイスをデプロイする予定の場合は、そのデバイスでプロキシを介して通信を行い、IoT Hub およびコンテナー レジストリに到達できる必要があります。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

## <a name="solution-management"></a>ソリューション管理

* **有用**
    * ログと診断を設定する
    * テストおよび CI/CD パイプラインを検討する

### <a name="set-up-logs-and-diagnostics"></a>ログと診断を設定する

Linux では、IoT Edge デーモンで既定のログ ドライバーとしてジャーナルが使用されます。 コマンドライン ツール `journalctl` を使用して、デーモン ログのクエリを実行することができます。 Windows では、IoT Edge デーモンで PowerShell 診断が使用されます。 デーモンからのログのクエリを実行するには、`Get-WinEvent` を使用します。 IoT Edge モジュールでは、ログ用に JSON ドライバーが使用されます (既定)。  

IoT Edge のデプロイをテストする場合、通常はデバイスにアクセスしてログを取得し、トラブルシューティングを行うことができます。 デプロイ シナリオでは、そのオプションがない場合があります。 運用環境でデバイスに関する情報をどのように収集するかを検討してください。 1 つのオプションとして、他のモジュールから情報を収集し、クラウドに送信するログ モジュールを使用する方法があります。 ログ モジュールの一例として [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics) があります。独自のものを設計することもできます。 

### <a name="place-limits-on-log-size"></a>ログ サイズを制限する

既定では、Moby コンテナー エンジンでは、コンテナー ログ サイズの制限が設定されません。 これにより、時間の経過と共に、デバイスがログでいっぱいになり、ディスク容量が不足する可能性があります。 これを防ぐには、以下のオプションを検討してください。

**オプション:すべてのコンテナー モジュールに適用されるグローバル制限を設定する**

コンテナー エンジンのログ オプションで、すべてのコンテナー ログ ファイルのサイズを制限できます。 次の例では、ログ ドライバーを `json-file` (推奨) に設定し、サイズとファイル数を制限します。

    {
        "log-driver": "json-file",
        "log-opts": {
            "max-size": "10m",
            "max-file": "3"
        }
    }

この情報を `daemon.json` という名前のファイルに追加 (またはアペンド) して、デバイス プラットフォームの適切な場所に配置します。

| プラットフォーム | Location |
| -------- | -------- |
| Linux | `/etc/docker/` |
|  Windows | `C:\ProgramData\iotedge-moby-data\config\` |

変更を有効にするには、コンテナー エンジンを再起動する必要があります。

**オプション:各コンテナー モジュールのログ設定を調整する**

これは、各モジュールの **createOptions** で行うことができます。 例: 

    "createOptions": {
        "HostConfig": {
            "LogConfig": {
                "Type": "json-file",
                "Config": {
                    "max-size": "10m",
                    "max-file": "3"
                }
            }
        }
    }


**Linux システムにおけるその他のオプション**

* 既定のロギング ドライバーとして `journald` を設定することで、`systemd` [ジャーナル](https://docs.docker.com/config/containers/logging/journald/)にログを送信するようにコンテナー エンジンを構成します。 

* logrotate ツールをインストールすることで、デバイスから古いログを定期的に削除します。 次のファイルの指定を使用します。 

   ```
   /var/lib/docker/containers/*/*-json.log{
       copytruncate
       daily
       rotate7
       delaycompress
       compress
       notifempty
       missingok
   }
   ```

### <a name="consider-tests-and-cicd-pipelines"></a>テストおよび CI/CD パイプラインを検討する

最も効率的な IoT Edge のデプロイ シナリオの場合は、運用環境のデプロイをテストおよび CI/CD パイプラインに統合することを検討してください。 Azure IoT Edge では、Azure DevOps を含む、複数の CI/CD プラットフォームがサポートされます。 詳細については、「[Azure IoT Edge に対する継続的インテグレーションと継続的配置](how-to-ci-cd.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* 詳細については、[IoT Edge の自動デプロイ](module-deployment-monitoring.md)に関するページをご覧ください。
* IoT Edge で[継続的インテグレーションと継続的配置](how-to-ci-cd.md)がどのようにサポートされるのかを確認します。

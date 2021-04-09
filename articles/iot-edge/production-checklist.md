---
title: ソリューションを運用環境にデプロイするための準備を行う - Azure IoT Edge
description: Azure IoT Edge ソリューションを開発環境から運用環境に移行する方法について説明します。これには、適切な証明書でのデバイスの設定と、今後のコード更新のためのデプロイ計画が含まれます。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 1fc229b04ac317578e9e90686496cd081b279afd
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489757"
---
# <a name="prepare-to-deploy-your-iot-edge-solution-in-production"></a>IoT Edge ソリューションを運用環境にデプロイするための準備を行う

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge ソリューションを開発環境から運用環境に移行する準備ができたら、それが継続的なパフォーマンスのために構成されていることを確認します。

この記事で提供される情報はすべて同等であるとは限りません。 優先順位を付けやすくするために、各セクションの始めに、作業を **重要** (運用環境に移行する前に完了すること) および **有用** (知っておくと便利なこと) という 2 つのセクションに分けたリストが示されています。

## <a name="device-configuration"></a>デバイス構成

IoT Edge デバイスとして、Raspberry Pi から、ノート PC、サーバー上で実行されている仮想マシンまで、任意のものを指定できます。 デバイスには物理的に、または仮想接続を介してアクセスできる場合があります。また、デバイスは長時間分離される場合もあります。 いずれにせよ、適切に動作するように構成されていることを確認する必要があります。

* **重要**
  * 運用環境の証明書をインストールする
  * デバイスの管理を計画する
  * コンテナー エンジンとして Moby を使用する

* **有用**
  * アップストリーム プロトコルを選ぶ

### <a name="install-production-certificates"></a>運用環境の証明書をインストールする

運用環境内のすべての IoT Edge デバイスには、デバイス証明機関 (CA) の証明書をインストールする必要があります。 その CA 証明書は、その後、config ファイルの IoT Edge ランタイムに宣言されます。 開発とテストのシナリオ用に、IoT Edge ランタイムでは、config ファイルで証明書が宣言されていない場合に一時証明書が作成されます。 しかし、これらの一時証明書は 3 か月後に有効期限が切れるため、運用環境シナリオでは安全ではありません。 運用環境のシナリオでは、自己署名の証明機関、または商用認証局から購入した自分独自のデバイス CA 証明書を指定する必要があります。

<!--1.1-->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> 現時点では、libiothsm の制限により、2038 年 1 月 1 日以降に有効期限が切れる証明書は使用できません。

:::moniker-end

デバイス CA 証明書のロールの詳細については、[Azure IoT Edge での証明書の使用方法](iot-edge-certs.md)に関するページを参照してください。

IoT Edge デバイスに証明書をインストールし、config ファイルからそれらを参照する方法の詳細については、「[IoT Edge デバイスで証明書を管理する](how-to-manage-device-certificates.md)」を参照してください。

### <a name="have-a-device-management-plan"></a>デバイスの管理を計画する

運用環境に任意のデバイスを配置する前に、今後の更新をどのように管理するかを知っておく必要があります。 IoT Edge デバイスでは、更新するコンポーネントのリストに以下のものが含まれる可能性があります。

* デバイス ファームウェア
* オペレーティング システム ライブラリ
* コンテナー エンジン (Moby など)
* IoT Edge
* CA 証明書

詳細については、[IoT Edge ランタイムの更新](how-to-update-iot-edge.md)に関する記事を参照してください。 IoT Edge を更新するための現在の方法では、IoT Edge デバイスへの物理的アクセスまたは SSH アクセスが必要になります。 更新するデバイスの数が多い場合は、スクリプトに更新手順を追加することを検討するか、Ansible などの自動化ツールを使用します。

### <a name="use-moby-as-the-container-engine"></a>コンテナー エンジンとして Moby を使用する

コンテナー エンジンは、すべての IoT Edge デバイスの前提条件となります。 運用環境では moby-engine のみがサポートされます。 Docker などのその他のコンテナー エンジンは IoT Edge で動作します。開発用にこれらのエンジンを使用してもかまいません。 Azure IoT Edge で使用する場合は moby-engine を再配布でき、Microsoft ではこのエンジンのサービスを提供しています。

### <a name="choose-upstream-protocol"></a>アップストリーム プロトコルを選ぶ

(使用するポートが決定される) IoT Hub へのアップストリーム通信用のプロトコルを、IoT Edge エージェントと IoT Edge ハブの両方に対して構成することができます。 既定のプロトコルは AMQP ですが、ネットワーク設定に応じてそれを変更できます。

2 つのランタイム モジュールの両方に **UpstreamProtocol** 環境変数があります。 変数の有効な値は次のとおりです。

* MQTT
* AMQP
* MQTTWS
* AMQPWS

デバイス自体の config ファイルで、IoT Edge エージェント用に UpstreamProtocol 変数を構成します。 たとえば、IoT Edge デバイスが、AMQP ポートをブロックするプロキシ サーバーの背後にある場合、IoT Hub への初期接続を確立するために WebSocket (AMQPWS) 経由で AMQP を使用するように IoT Edge エージェントを構成する必要があることがあります。

IoT Edge デバイスが接続されたら、必ず、以降のデプロイでも引き続き両方のランタイム モジュールに対して UpstreamProtocol 変数を構成してください。 このプロセスの例については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

## <a name="deployment"></a>デプロイ

* **有用**
  * アップストリーム プロトコルに合わせる
  * システム モジュール用にホスト ストレージを設定する
  * IoT Edge ハブで使用されるメモリ領域を減らす
  * デバッグ バージョンのモジュール イメージを使用しない

### <a name="be-consistent-with-upstream-protocol"></a>アップストリーム プロトコルに合わせる

既定の AMQP とは異なるプロトコルを使用するように IoT Edge デバイス上の IoT Edge エージェントを構成した場合は、今後のすべてのデプロイで同じプロトコルを宣言する必要があります。 たとえば、IoT Edge デバイスが、AMQP ポートをブロックするプロキシ サーバーの背後にある場合、WebSocket (AMQPWS) 経由の AMQP で接続するようにデバイスを構成したと考えられます。 デバイスにモジュールをデプロイするときは、IoT Edge エージェントと IoT Edge ハブに対して同じ AMQPWS プロトコルを構成します。そうしない場合、既定の AMQP で設定がオーバーライドされ、再度接続することはできません。

IoT Edge エージェントおよび IoT Edge ハブ モジュールには、UpstreamProtocol 環境変数のみを構成する必要があります。 追加モジュールではすべて、ランタイム モジュールに設定されている任意のプロトコルが採用されます。

このプロセスの例については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

### <a name="set-up-host-storage-for-system-modules"></a>システム モジュール用にホスト ストレージを設定する

IoT Edge ハブおよびエージェント モジュールでは、ローカル ストレージの使用により状態が維持され、モジュール、デバイス、およびクラウド間のメッセージングが有効となります。 信頼性とパフォーマンスを向上させるために、ホスト ファイルシステム上のストレージを使用するようにシステム モジュールを構成します。

詳細については、「[システム モジュール用のホスト ストレージ](how-to-access-host-storage-from-module.md)」を参照してください。

### <a name="reduce-memory-space-used-by-iot-edge-hub"></a>IoT Edge ハブで使用されるメモリ領域を減らす

使用可能なメモリが限られている制約付きデバイスをデプロイする場合は、より効率的な容量で実行し、より少ないディスク領域を使用するように IoT Edge ハブを構成できます。 しかし、これらの構成では IoT Edge ハブのパフォーマンスが制限されるため、ソリューションに適したバランスを見つけてください。

#### <a name="dont-optimize-for-performance-on-constrained-devices"></a>制限付きデバイスのパフォーマンスを最適化しない

IoT Edge ハブは既定でパフォーマンスが最適化されているため、大きなメモリ チャンクの割り当てが試行されます。 この構成によって、Raspberry Pi などのより小さなデバイスで安定性の問題が発生する場合があります。 リソースに制約があるデバイスをデプロイする場合は、IoT Edge ハブで **OptimizeForPerformance** 環境変数を **false** に設定することができます。

**OptimizeForPerformance** が **true** に設定されている場合、MQTT プロトコル ヘッドで PooledByteBufferAllocator が使用されます。この場合、パフォーマンスは向上しますが、より多くのメモリが割り当てられます。 アロケーターは、32 ビット オペレーティング システムまたはメモリが不足しているデバイスでは適切に機能しません。 また、パフォーマンスを最適化する場合、RocksDb は、そのローカル ストレージ プロバイダーとしての役割に、より多くのメモリを割り当てます。

詳細については、「[小型のデバイスでの安定性の問題](troubleshoot-common-errors.md#stability-issues-on-smaller-devices)」を参照してください。

#### <a name="disable-unused-protocols"></a>未使用のプロトコルを無効にする

IoT Edge ハブのパフォーマンスを最適化し、そのメモリ使用量を減らす別の方法は、ソリューションで使用していないすべてのプロトコルのプロトコル ヘッドを無効にすることです。

プロトコル ヘッドは、デプロイ マニフェストで IoT Edge ハブ モジュールに対してブール型の環境変数を設定することで構成されます。 変数には以下の 3 つがあります。

* **amqpSettings__enabled**
* **mqttSettings__enabled**
* **httpSettings__enabled**

3 つの変数にはすべて *2 つのアンダースコア* があり、true または false に設定できます。

#### <a name="reduce-storage-time-for-messages"></a>メッセージのストレージ時間を短縮する

何らかの理由でメッセージを IoT Hub に配信できない場合、そのメッセージは一時的に IoT Edge ハブ モジュールに格納されます。 配信されなかったメッセージの有効期限が切れるまで IoT Edge ハブで保持する期間を構成することができます。 デバイス上のメモリが心配な場合は、IoT Edge ハブ モジュール ツインで **timeToLiveSecs** の値を下げることができます。

timeToLiveSecs パラメーターの既定値は 7,200 秒 (2 時間) です。

### <a name="do-not-use-debug-versions-of-module-images"></a>デバッグ バージョンのモジュール イメージを使用しない

テスト シナリオから運用環境シナリオに移行する場合は、必ず、デプロイ マニフェストからデバッグ構成を削除してください。 デプロイ マニフェストのモジュール イメージのいずれにも **\.debug** サフィックスがないことを確認します。 デバッグ用のモジュールでポートを公開するための作成オプションを追加した場合は、その作成オプションも削除します。

## <a name="container-management"></a>コンテナー管理

* **重要**
  * コンテナー レジストリへのアクセスを管理する
  * タグを使用してバージョンを管理する
* **有用**
  * プライベート レジストリにランタイム コンテナーを格納する

### <a name="manage-access-to-your-container-registry"></a>コンテナー レジストリへのアクセスを管理する

運用環境の IoT Edge デバイスにモジュールをデプロイする前に、部外者がコンテナー イメージにアクセスしたり、変更を加えたりできないように、コンテナー レジストリへのアクセスを制御していることを確認してください。 コンテナー イメージを管理するには、パブリックではなく、プライベート コンテナー レジストリを使用します。

チュートリアルやその他のドキュメントでは、開発コンピューターで使用するものと同じコンテナー レジストリの資格情報を IoT Edge デバイスでも使用するように指示しています。 これらの指示は、単にテストおよび開発環境をより簡単に設定できるようにするためのものです。運用環境シナリオではこれらの指示に従わないでください。

レジストリへのセキュリティで保護されたアクセスのために、[認証オプション](../container-registry/container-registry-authentication.md)を選択できます。 一般的で推奨される認証は、Active Directory サービス プリンシパルを使用することです。これは、IoT Edge デバイスと同様に、自動的にまたは無人方式でコンテナー イメージをプルするアプリケーションやサービスによく適しています。

サービス プリンシパルを作成するには、「[サービス プリンシパルの作成](../container-registry/container-registry-auth-service-principal.md#create-a-service-principal)」の説明に従って、2 つのスクリプトを実行します。 これらのスクリプトは次のタスクを実行します。

* 最初のスクリプトでは、サービス プリンシパルが作成されます。 ここでは、サービス プリンシパル ID とサービス プリンシパル パスワードが出力されます。 これらの値は、安全に書き留めておきます。

* 2 番目のスクリプトは、サービス プリンシパルに付与するロールの割り当てを作成します。これは、必要に応じて後で実行できます。 `role` パラメーターに **acrPull** ユーザー ロールを適用することをお勧めします。 ロールの一覧については、「[Azure Container Registry のロールとアクセス許可](../container-registry/container-registry-roles.md)」をご覧ください。

サービス プリンシパルを使用して認証するには、最初のスクリプトで取得したサービス プリンシパル ID とパスワードを指定します。 デプロイ マニフェストにこれらの資格情報を指定します。

* ユーザー名またはクライアント ID には、サービス プリンシパル ID を指定します。

* パスワードまたはクライアント シークレットには、サービス プリンシパルのパスワードを指定します。

> [!NOTE]
> 強化されたセキュリティ認証を実装した後、**管理者ユーザー** 設定を無効にして、既定のユーザー名とパスワードにアクセスできないようにします。 Azure portal のコンテナー レジストリで、左側のウィンドウ メニューの **[設定]** の下にある **[アクセスキー]** を選択します。

### <a name="use-tags-to-manage-versions"></a>タグを使用してバージョンを管理する

タグは、Docker コンテナーのバージョンを区別するために使用できる Docker 概念です。 タグは、コンテナー リポジトリの末尾に付加される **1.0** などのサフィックスです。 たとえば、**mcr.microsoft.com/azureiotedge-agent:1.0** のようになります。 タグは可変であり、別のコンテナーを指すようにいつでも変更できます。したがって、チームは、今後モジュール イメージを更新する際に従う規則に同意する必要があります。

また、タグは、IoT Edge デバイスに更新プログラムを適用するのに役立ちます。 更新バージョンのモジュールをコンテナー レジストリにプッシュするときに、タグを増分します。 次に、増分されたタグでデバイスに新しいデプロイをプッシュします。 コンテナー エンジンでは、増分されたタグが新しいバージョンとして認識され、最新バージョンのモジュールがご利用のデバイスにプルダウンされます。

タグ規則の例については、[IoT Edge ランタイムの更新](how-to-update-iot-edge.md#understand-iot-edge-tags)に関する記述を参照してください。そこでは、IoT Edge でローリング タグと特定のタグを使用して、バージョンを追跡する方法について説明されています。

### <a name="store-runtime-containers-in-your-private-registry"></a>プライベート レジストリにランタイム コンテナーを格納する

あなたはカスタム コード モジュールのコンテナー イメージをプライベート Azure レジストリに格納する方法は理解していますが、それを使用して、edgeAgent や edgHub ランタイム モジュールなどのパブリック コンテナー イメージを格納することもできます。 このような処理が必要になるのは、これらのランタイム コンテナーが Microsoft Container Registry (MCR) に格納されているため、ファイアウォールの制限が非常に厳しい場合です。

docker pull コマンドを使用してイメージを取得し、プライベート レジストリに配置します。 IoT Edge ランタイムの新しいリリースごとにイメージを更新する必要があることに注意してください。

| IoT Edge ランタイム コンテナー | docker pull コマンド |
| --- | --- |
| [Azure IoT Edge エージェント](https://hub.docker.com/_/microsoft-azureiotedge-agent) | `docker pull mcr.microsoft.com/azureiotedge-agent` |
| [Azure IoT Edge ハブ](https://hub.docker.com/_/microsoft-azureiotedge-hub) | `docker pull mcr.microsoft.com/azureiotedge-hub` |

次に、edgeAgent および edgeHub システム モジュールの deployment.template.json ファイル内のイメージ参照を必ず更新します。 `mcr.microsoft.com` を、両方のモジュールのレジストリ名とサーバーに置き換えます。

* edgeAgent:

    `"image": "<registry name and server>/azureiotedge-agent:1.1",`

* edgeHub:

    `"image": "<registry name and server>/azureiotedge-hub:1.1",`

## <a name="networking"></a>ネットワーク

* **有用**
  * アウトバウンド/インバウンド構成を確認する
  * IoT Edge デバイスからの接続を許可する
  * プロキシを介した通信を構成する

### <a name="review-outboundinbound-configuration"></a>アウトバウンド/インバウンド構成を確認する

Azure IoT Hub および IoT Edge の間の通信チャネルは、常にアウトバウンドに構成されます。 ほとんどの IoT Edge シナリオでは、3 つの接続のみが必要になります。 コンテナー エンジンは、モジュール イメージを保持するコンテナー レジストリと接続する必要があります。 IoT Edge ランタイムは、デバイス構成情報を取得する場合、またメッセージとテレメトリを送信する場合に IoT Hub と接続する必要があります。 また、自動プロビジョニングを使用する場合、IoT Edge はデバイス プロビジョニング サービスに接続する必要があります。 詳細については、[ファイアウォールとポート構成ルール](troubleshoot.md#check-your-firewall-and-port-configuration-rules)に関する記述を参照してください。

### <a name="allow-connections-from-iot-edge-devices"></a>IoT Edge デバイスからの接続を許可する

ネットワーク設定で、IoT Edge デバイスから行われた接続を明示的に許可する必要がある場合、次の IoT Edge コンポーネントのリストを確認します。

* **IoT Edge エージェント**: 場合によっては WebSockets 経由で、IoT Hub への永続的な AMQP/MQTT 接続が開かれます。
* **IoT Edge ハブ**: 場合によっては WebSockets 経由で、IoT Hub への 1 つの永続的な AMQP 接続または複数の MQTT 接続が開かれます。
* **IoT Edge サービス**: IoT Hub の間欠的な HTTPS 呼び出しが行われます。

これら 3 つのいずれの場合も、DNS 名は \*.azure-devices.net というパターンと一致します。

さらに、**コンテナー エンジン** では、HTTPS 経由でコンテナー レジストリを呼び出します。 IoT Edge ランタイムのコンテナー イメージを取得する場合、DNS 名は mcr.microsoft.com となります。 コンテナー エンジンは、デプロイで構成されているその他のレジストリに接続されます。

このチェックリストを使用して、ファイアウォール規則を開始できます。

   | URL (\* = ワイルドカード) | 送信 TCP ポート | 使用法 |
   | ----- | ----- | ----- |
   | mcr.microsoft.com  | 443 | Microsoft Container Registry |
   | global.azure-devices-provisioning.net  | 443 | DPS でのアクセス (任意指定) |
   | \*.azurecr.io | 443 | 個人やサード パーティのコンテナー レジストリ |
   | \*.blob.core.windows.net | 443 | BLOB ストレージから Azure Container Registry イメージの差分をダウンロードする |
   | \*.azure-devices.net | 5671、8883、443 | IoT Hub でのアクセス |
   | \*.docker.io  | 443 | Docker Hub でのアクセス (任意指定) |

これらのファイアウォール規則の一部は Azure Container Registry から継承されます。 詳細については、「[ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する](../container-registry/container-registry-firewall-access-rules.md)」を参照してください。

> [!NOTE]
> REST エンドポイントとデータ エンドポイント間に一貫性のある FQDN を提供するために、**2020 年 6 月 15 日** 以降、Microsoft Container Registry データ エンドポイントは `*.cdn.mscr.io` から `*.data.mcr.microsoft.com` に変更されます  
> 詳細については、[Microsoft Container Registry クライアント ファイアウォール規則の構成](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)に関する記事を参照してください

パブリック コンテナー レジストリへのアクセスを許可するようにファイアウォールを構成しない場合は、「[プライベート レジストリにランタイム コンテナーを格納する](#store-runtime-containers-in-your-private-registry)」で説明されているように、イメージをプライベート コンテナー レジストリに格納することができます。

### <a name="configure-communication-through-a-proxy"></a>プロキシを介した通信を構成する

プロキシ サーバーを使用するネットワークにデバイスをデプロイする予定の場合は、そのデバイスでプロキシを介して通信を行い、IoT Hub およびコンテナー レジストリに到達できる必要があります。 詳細については、「[IoT Edge デバイスを構成してプロキシ サーバー経由で通信する](how-to-configure-proxy-support.md)」を参照してください。

## <a name="solution-management"></a>ソリューション管理

* **有用**
  * ログと診断を設定する
  * テストおよび CI/CD パイプラインを検討する

### <a name="set-up-logs-and-diagnostics"></a>ログと診断を設定する

Linux では、IoT Edge デーモンで既定のログ ドライバーとしてジャーナルが使用されます。 コマンドライン ツール `journalctl` を使用して、デーモン ログのクエリを実行することができます。

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

バージョン 1.2 以降、IoT Edge は複数のデーモンに依存します。 各デーモンのログには `journalctl` で個別にクエリを実行できますが、`iotedge system` コマンドは、結合されたログにクエリを実行する便利な方法です。

* 統合された `iotedge` コマンド:

  ```bash
  sudo iotedge system logs
  ```

* 同等の `journalctl` コマンド:

  ```bash
  journalctl -u aziot-edge -u aziot-identityd -u aziot-keyd -u aziot-certd -u aziot-tpmd
  ```

:::moniker-end

Windows では、IoT Edge デーモンで PowerShell 診断が使用されます。 デーモンからのログのクエリを実行するには、`Get-IoTEdgeLog` を使用します。 IoT Edge モジュールでは、ログ用に JSON ドライバーが使用されます (既定)。  

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

IoT Edge のデプロイをテストする場合、通常はデバイスにアクセスしてログを取得し、トラブルシューティングを行うことができます。 デプロイ シナリオでは、そのオプションがない場合があります。 運用環境でデバイスに関する情報をどのように収集するかを検討してください。 1 つのオプションとして、他のモジュールから情報を収集し、クラウドに送信するログ モジュールを使用する方法があります。 ログ モジュールの一例として [logspout loganalytics](https://github.com/veyalla/logspout-loganalytics) があります。独自のものを設計することもできます。

### <a name="place-limits-on-log-size"></a>ログ サイズを制限する

既定では、Moby コンテナー エンジンでは、コンテナー ログ サイズの制限が設定されません。 これにより、時間の経過と共に、デバイスがログでいっぱいになり、ディスク容量が不足する可能性があります。 これを防ぐには、以下のオプションを検討してください。

#### <a name="option-set-global-limits-that-apply-to-all-container-modules"></a>オプション:すべてのコンテナー モジュールに適用されるグローバル制限を設定する

コンテナー エンジンのログ オプションで、すべてのコンテナー ログ ファイルのサイズを制限できます。 次の例では、ログ ドライバーを `json-file` (推奨) に設定し、サイズとファイル数を制限します。

```JSON
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "10m",
        "max-file": "3"
    }
}
```

この情報を `daemon.json` という名前のファイルに追加 (またはアペンド) して、デバイス プラットフォームの適切な場所に配置します。

| プラットフォーム | 場所 |
| -------- | -------- |
| Linux | `/etc/docker/` |
| Windows | `C:\ProgramData\iotedge-moby\config\` |

変更を有効にするには、コンテナー エンジンを再起動する必要があります。

#### <a name="option-adjust-log-settings-for-each-container-module"></a>オプション:各コンテナー モジュールのログ設定を調整する

これは、各モジュールの **createOptions** で行うことができます。 次に例を示します。

```yml
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
```

#### <a name="additional-options-on-linux-systems"></a>Linux システムにおけるその他のオプション

* コンテナーエンジンを構成して、既定のログ ドライバーとして `journald` を設定することによって、`systemd`[ジャーナル](https://docs.docker.com/config/containers/logging/journald/) にログを送信します。

* logrotate ツールをインストールすることで、デバイスから古いログを定期的に削除します。 次のファイルの指定を使用します。

   ```txt
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

最も効率的な IoT Edge のデプロイ シナリオの場合は、運用環境のデプロイをテストおよび CI/CD パイプラインに統合することを検討してください。 Azure IoT Edge では、Azure DevOps を含む、複数の CI/CD プラットフォームがサポートされます。 詳細については、「[Azure IoT Edge に対する継続的インテグレーションと継続的配置](how-to-continuous-integration-continuous-deployment.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

* 詳細については、[IoT Edge の自動デプロイ](module-deployment-monitoring.md)に関するページをご覧ください。
* IoT Edge で[継続的インテグレーションと継続的配置](how-to-continuous-integration-continuous-deployment.md)がどのようにサポートされるのかを確認します。

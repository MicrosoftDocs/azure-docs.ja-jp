---
title: cloud-init の使用のトラブルシューティング
description: cloud-init を使用した Azure VM のプロビジョニングをトラブルシューティングします。
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582155"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>cloud-init を使用した VM プロビジョニングのトラブルシューティング

cloud-init を使用してプロビジョニングを行って、一般化されたカスタム イメージを作成していたが、VM が正常に作成されなかったことがわかった場合、カスタム イメージのトラブルシューティングを行う必要があります。

プロビジョニングに関する問題の例:
- VM が "作成中" で 40 分間スタック、VM の作成が失敗としてマークされる
- `CustomData` が処理されない
- エフェメラル ディスクをマウントできない
- ユーザーが作成されないか、ユーザー アクセスの問題がある
- ネットワークが正しくセットアップされない
- スワップ ファイルまたはパーティションのエラー

この記事では、cloud-init のトラブルシューティングを行う方法について説明します。 詳細については、[cloud-init の詳細](./cloud-init-deep-dive.md)に関するページを参照してください。

## <a name="step-1-test-the-deployment-without-customdata"></a>手順 1:`customData` を使用せずにデプロイをテストする

cloud-init で、渡される `customData` を受け取ることができるのは、VM が作成されるときです。 最初に、これによってデプロイに問題が発生していないことを確認する必要があります。 何も構成を渡さずに VM をプロビジョニングしてみてください。 VM のプロビジョニングが失敗した場合は、以下の手順に進みます。渡した構成が適用されていないことがわかったら、[手順 4]() に進みます。 

## <a name="step-2-review-image-requirements"></a>手順 2:イメージ要件を確認する
VM のプロビジョニング エラーの主な原因は、OS イメージが Azure で実行するための前提条件を満たしていないことです。 Azure でのプロビジョニングを試行する前に、イメージが適切に準備されていることを確認します。 


次の記事では、Azure でサポートされているさまざまな Linux ディストリビューションを準備する方法について説明しています。

- [CentOS ベースのディストリビューション](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES と openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [その他:動作保証外のディストリビューション](create-upload-generic.md)

[サポートされている Azure cloud-init イメージ](./using-cloud-init.md)の場合、Linux ディストリビューションでは、Azure にイメージを正しくプロビジョニングするために必要なパッケージと構成が既に用意されています。 独自にキュレートしたイメージから VM が作成できない場合は、既に cloud-init 用に構成されている、サポート対象の Azure Marketplace イメージに対して、オプションの `customData` を使用してみてください。 `customData` が Azure Marketplace イメージで正常に動作する場合は、キュレートしたイメージに問題がある可能性があります。

## <a name="step-3-collect--review-vm-logs"></a>手順 3:VM ログを収集して確認する

VM がプロビジョニングできないとき、Azure では 20 分間 "作成中" の状態が表示されてから、VM を再起動します。さらに 20 分間待機した後で最終的に VM デプロイを失敗としてマークし、最後に `OSProvisioningTimedOut` エラーでマークします。

VM が実行されているときは、プロビジョニングが失敗した理由を理解するために、VM のログが必要になります。  VM のプロビジョニングが失敗した理由を理解するには、VM を停止しないでください。 VM を実行させたままにします。 ログを収集するために、障害が発生した VM を実行中の状態のままにしておく必要があります。 ログを収集するには、次のいずれかの方法を使用します。

- [シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [ブート診断を有効化](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics)してから、VM を作成し、ブート中に[表示](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics)します。

- [AZ VM Repair を実行](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands)し、OS ディスクを接続してマウントします。これにより、これらのログを収集できます。
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
最初のトラブルシューティングを開始するには、cloud-init のログから開始し、障害が発生した場所を把握してから、他のログを詳細に使用して分析情報を提供します。 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Serial/boot logs

すべてのログで、"Failed"、"WARNING"、"WARN"、"err"、"error"、"ERROR" の検索を開始します。 大文字と小文字を区別せずに検索するように構成を設定することをお勧めします。 

> [!TIP]
> カスタム イメージのトラブルシューティングを行う場合は、イメージにユーザーを追加することを検討してください。 プロビジョニングで管理者ユーザーを設定できない場合でも、OS にログインできます。

## <a name="analyzing-the-logs"></a>ログを分析する

ここでは、cloud-init の各ログで検索する項目をさらに詳しく説明します。

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

既定では、優先度がデバッグ以上のすべての cloud-init イベントが `/var/log/cloud-init.log` に書き込まれます。 これによって、cloud-init 初期化中に発生したすべてのイベントの詳細なログが提供されます。 

次に例を示します。

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


エラーまたは警告が見つかったら、cloud-init 化ログを逆方向に読んで、エラーまたは警告に到達する前に cloud-init が何をしようとしたかを把握します。 多くの場合、エラーの前には cloud-init が OS コマンドを実行するか、プロビジョニング操作を実行しています。このように、エラーがログに出現した理由について分析情報が得られます。 次の例では、エラーが発生する直前に、cloud-init がデバイスをマウントしようとしました。

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

[シリアル コンソール](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)を利用できる場合は、cloud-init が実行しようとしたコマンドを再実行してみてください。

`/var/log/cloud-init.log` のログは、/etc/cloud/cloud.cfg.d/05_logging.cfg 内で再構成することもできます。 cloud-init のログについて詳しくは、[cloud-init のドキュメント](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)をご覧ください 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

[cloud-init のステージ](cloud-init-deep-dive.md)で、`stdout` や `stderr` から情報を取得できます。 通常、これには、ルーティング テーブル情報、ネットワーク情報、ssh ホスト キー検証情報、cloud-init の各ステージの `stdout` と `stderr` および各ステージのタイムスタンプが含まれます。 必要であれば、`stderr` および `stdout` のログを `/etc/cloud/cloud.cfg.d/05_logging.cfg` から再構成できます。

### <a name="serialboot-logs"></a>Serial/boot logs 

cloud-init には、複数の依存関係があります。これらは、Azure のイメージの必要な前提条件として説明されています。たとえば、ネットワーク、ストレージ、ISO のマウントの可否、一時ディスクのマウントとフォーマットの可否などです。 これらのいずれかによってエラーがスローされ、cloud-init が失敗する可能性があります。 たとえば、VM が DHCP リースを取得できない場合、cloud-init は失敗します。

引き続き、cloud-init がプロビジョニングに失敗した理由を特定できない場合は、cloud-init のどのステージで、いつモジュールが実行されたかを把握する必要があります。 詳しくは、[cloud-init の詳細](cloud-init-deep-dive.md)に関するページを参照してください。


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>手順 4:構成が適用されてない理由を調査する
cloud-init のすべての障害で、致命的なプロビジョニング エラーが発生するわけではありません。 たとえば、cloud-init の構成で `runcmd` モジュールを使用している場合、実行しているコマンドのゼロ以外の終了コードによって、VM プロビジョニングが失敗します。 これは、cloud-init の最初の 3 つのステージで行われるコア プロビジョニング機能の後に実行されるためです。 構成が適用されなかった理由をトラブルシューティングするには、手順 3 のログと cloud-init モジュールを手動で確認します。 次に例を示します。

- `runcmd` - スクリプトはエラーなしで実行されますか。 ターミナルから手動で構成を実行し、想定どおりに動作することを確認します。
- パッケージのインストール - VM はパッケージ リポジトリにアクセスできますか。
- VM に提供された `customData` データ構成もチェックする必要があります。これは `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` にあります。


## <a name="next-steps"></a>次のステップ

それでも、cloud-init で構成が実行されなかった理由を特定できない場合は、cloud-init の各ステージで何が行われたか、およびモジュールがいつ実行されたかを詳しく調べる必要があります。 詳しくは、[cloud-init 構成の詳細](./cloud-init-deep-dive.md)に関するページを参照してください。
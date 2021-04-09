---
title: StorSimple 8000 デバイスをトラブルシューティングする診断ツール | Microsoft Docs
description: この記事では、診断ツールの概要を示し、StorSimple デバイスでのツールの使用方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954071"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>StorSimple 診断ツールを使用して、8000 シリーズ デバイスに関する問題のトラブルシューティングを行います。

## <a name="overview"></a>概要

StorSimple の診断ツールは、StorSimple デバイスのシステム、パフォーマンス、ネットワーク、およびハードウェア コンポーネントの正常性に関する問題を診断します。 診断ツールは、さまざまなシナリオで使用できます。 これらのシナリオには、ワークロードの計画、StorSimple デバイスのデプロイメント、ネットワーク環境の評価、および運用デバイスのパフォーマンスの確認が含まれます。 この記事では、診断ツールの概要を示し、StorSimple デバイスでのツールの使用方法について説明します。

診断ツールの主な対象は、StorSimple 8000 シリーズのオンプレミス デバイス (8100 および 8600) です。

## <a name="run-diagnostics-tool"></a>診断ツールを実行する

このツールは、StorSimple デバイスの Windows PowerShell インターフェイスを使用して実行できます。 デバイスのローカル インターフェイスにアクセスするには、次の 2 つの方法があります。

* [PuTTY を使用してデバイスのシリアル コンソールに接続](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)します。
* [StorSimple 用 Windows PowerShell を使用してリモートでツールにアクセス](storsimple-8000-remote-connect.md)します。

この記事では、PuTTY を使用してデバイスのシリアル コンソールに接続していることを想定しています。

#### <a name="to-run-the-diagnostics-tool"></a>診断ツールを実行するには

デバイスの Windows PowerShell インターフェイスに接続したら、次の手順に従ってコマンドレットを実行します。
1. 「 [PuTTY を使用してデバイスのシリアル コンソールに接続する](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)」の手順に従って、デバイスのシリアル コンソールにログオンします。

2. 次のコマンドを入力します。

    `Invoke-HcsDiagnostics`

    Scope パラメーターを指定しなかった場合は、すべての診断テストが実行されます。 これらのテストには、システム、ハードウェア コンポーネントの正常性、ネットワーク、およびパフォーマンスが含まれます。 
    
    特定のテストのみを実行するには、Scope パラメーターを指定します。 たとえば、ネットワーク テストのみを実行するには、次のように入力します。

    `Invoke-HcsDiagnostics -Scope Network`

3. PuTTY ウィンドウの出力を選択してテキスト ファイルにコピーし、詳しく分析します。

## <a name="scenarios-to-use-the-diagnostics-tool"></a>診断ツールを使用するシナリオ

診断ツールを使用して、ネットワーク、パフォーマンス、システム、およびシステムのハードウェアの正常性に関するトラブルシューティングを行います。 考えられるシナリオを次にいくつか示します。

* **デバイスのオフライン** - StorSimple 8000 シリーズ デバイスがオフラインになっています。 しかし、Windows PowerShell インターフェイスからは、両方のコントローラーが稼働しているように見えます。
    * この場合は、このツールを使用してネットワークの状態を確認できます。
         
         > [!NOTE]
         > 登録 (または、セットアップ ウィザードによる構成) を行う前に、このツールを使用してデバイスのパフォーマンスやネットワーク設定を評価しないでください。 デバイスの有効な IP は、セットアップ ウィザードおよび登録の実行中に割り当てられます。 登録されていないデバイスでは、ハードウェアの正常性とシステムに関してこのコマンドレットを実行できます。 たとえば、次の Scope パラメーターを使用します。
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **デバイスの永続的な問題** - 今後も続くと思われるデバイスの問題が発生しています。 たとえば、登録が失敗します。 デバイスが正常に登録され、しばらく稼働した後で、デバイスの問題が発生する場合もあります。
    * この場合は、Microsoft サポートに対するサービス要求を作成する前に、このツールを使用して事前のトラブルシューティングを行います。 このツールを実行し、このツールの出力をキャプチャすることをお勧めします。 この出力をサポートに提供することで、トラブルシューティングを迅速に進めることができます。
    * ハードウェア コンポーネントまたはクラスターに障害が発生した場合は、サポート要求を作成する必要があります。

* **デバイスのパフォーマンス低下** - StorSimple デバイスの動作速度が低下しています。
    * この場合は、Scope パラメーターを Performance に設定して、このコマンドレットを実行します。 出力を分析します。 クラウドの読み取り/書き込み待機時間を取得します。 報告された待機時間を達成可能な最大のターゲットとして使用し、内部のデータ処理のオーバーヘッドを考慮に入れて、システムにワークロードをデプロイします。 詳細については、「[ネットワーク テストを使用してデバイスのパフォーマンスに関するトラブルシューティングを行う](#network-test)」をご覧ください。


## <a name="diagnostics-test-and-sample-outputs"></a>診断テストと出力例

### <a name="hardware-test"></a>ハードウェア テスト

このテストでは、システムで動作しているハードウェア コンポーネント、USM ファームウェア、およびディスク ファームウェアの状態を確認します。

* 報告されるハードウェア コンポーネントは、テストに失敗したコンポーネント、またはシステムに存在しないコンポーネントです。
* システム内のコントローラー 0、コントローラー 1、および共有コンポーネントについては、USM ファームウェアとディスク ファームウェアのバージョンが報告されます。 ハードウェア コンポーネントの一覧については、次を参照してください。

    * [主エンクロージャのコンポーネント](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [EBOD エンクロージャのコンポーネント](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> ハードウェア テストで失敗したコンポーネントが報告された場合は、[Microsoft サポートに対するサービス要求を作成](storsimple-8000-contact-microsoft-support.md)してください。

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>8100 デバイスで実行されたハードウェア テストの出力例

StorSimple 8100 デバイスの出力例を次に示します。 8100 モデル デバイスに EBOD エンクロージャは存在しません。 そのため、EBOD コントローラーのコンポーネントは報告されません。

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>システム テスト

このテストでは、デバイスのシステム情報、使用可能な更新プログラム、クラスター情報、およびサービス情報が報告されます。

* システム情報には、モデル、デバイスのシリアル番号、タイム ゾーン、コントローラーの状態、およびシステムで実行されているソフトウェアの詳細なバージョンが含まれています。 出力として報告される各種のシステム パラメーターについては、「[システム情報の解釈](#appendix-interpreting-system-information)」をご覧ください。

* 更新プログラムの可用性については、通常モードとメンテナンス モードが使用可能かどうか、および関連するパッケージの名前が報告されます。 `RegularUpdates`と`MaintenanceModeUpdates`が`false`の場合、これは更新プログラムが使用可能でないことを示します。 ご利用のデバイスは最新の状態です。
* クラスター情報には、すべての HCS クラスター グループのさまざまな論理コンポーネントとそれぞれの状態に関する情報が含まれています。 レポートのこのセクションにオフラインのクラスター グループが表示された場合は、[Microsoft サポートにお問い合わせ](storsimple-8000-contact-microsoft-support.md)ください。
* サービス情報には、ご利用のデバイスで実行されているすべての HCS および CIS サービスの名前と状態が含まれています。 この情報は、Microsoft サポートがデバイスに関する問題のトラブルシューティングを行う際に役立ちます。

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>8100 デバイスで実行されたシステム テストの出力例

8100 デバイスで実行されたシステム テストの出力例を次に示します。

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>ネットワーク テスト

このテストでは、StorSimple デバイスのネットワーク インターフェイス、ポート、DNS および NTP サーバーへの接続、TLS/SSL 証明書、ストレージ アカウントの資格情報、更新サーバーへの接続、および Web プロキシ接続の状態が検証されます。

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>DATA 0 のみが有効になっているときのネットワーク テストの出力例

8100 デバイスの出力例を次に示します。 この出力には次の情報が含まれています。
* ネットワーク インターフェイス DATA 0 および DATA 1 のみが有効化され、構成されています。
* このポータルでは DATA 2 ～ 5 は有効になっていません。
* DNS サーバーの構成が有効であり、DNS サーバーを介してデバイスを接続できます。
* NTP サーバーの接続も問題ありません。
* ポート 80 および 443 が開いています。 しかし、ポート 9354 はブロックされています。 [システムのネットワーク要件](./storsimple-8000-system-requirements.md)に基づいて、このポートを Service Bus 通信のために開く必要があります。
* TLS/SSL 証明書は有効です。
* ストレージ アカウント _myss8000storageacct_ にデバイスを接続できます。
* 更新サーバーへの接続は有効です。
* このデバイスに Web プロキシは構成されていません。

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>DATA 0 および DATA 1 が有効になっているときのネットワーク テストの出力例

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>パフォーマンス テスト

このテストでは、デバイスのクラウド読み取り/書き込み待機時間を使用してクラウドのパフォーマンスが報告されます。 このツールを使用して、StorSimple で達成できるクラウドのパフォーマンスのベースラインを設定できます。 このツールは、接続で得られる最大のパフォーマンス (読み取り/書き込み待機時間の最適なシナリオ) を報告します。

達成可能な最大のパフォーマンスが報告されるため、報告された読み取り/書き込み待機時間を、ワークロードをデプロイする際のターゲットとして使用できます。

このテストでは、デバイス上の異なるタイプのボリュームに関連付けられた BLOB サイズをシミュレートします。 標準の階層化ボリュームとローカル固定ボリュームのバックアップでは、64 KB の BLOB サイズを使用します。 アーカイブ オプションをオンにした階層化ボリュームでは、512 KB の BLOB データ サイズを使用します。 デバイスに階層化ボリュームとローカル固定ボリュームが構成されている場合は、64 KB の BLOB データ サイズに対応するテストのみが実行されます。

このツールを使用するには、次の手順を実行します。

1.  最初に、階層化ボリュームとアーカイブ オプションをオンにした階層化ボリュームの組合せを作成します。 このアクションにより、64 KB と 512 KB の両方の BLOB のサイズのテストが実行されます。

2. ボリュームを作成して構成したら、コマンドレットを実行します。 型:

    `Invoke-HcsDiagnostics -Scope Performance`

3. このツールによって報告された読み取り/書き込み待機時間を書き留めておきます。 このテストを実行してから結果が報告されるまでに、数分かかる場合があります。

4. 接続の待機時間がすべて予想の範囲内である場合は、ツールによって報告された待機時間を、ワークロードを展開する際の達成可能な最大のターゲットとして使用できます。 内部のデータ処理のオーバーヘッドを考慮に入れてください。

    診断ツールによって報告された読み取り/書き込み待機時間が長い場合:

    1. BLOB サービスに対して Storage Analytics を構成し、出力を分析して Azure ストレージ アカウントの待機時間を理解します。 詳細な手順については、「[Storage Analytics の有効化と構成](../storage/blobs/monitor-blob-storage.md)」を参照してください。 これらの待機時間が長く、StorSimple 診断ツールが示した数値と同程度である場合は、Azure Storage に対するサービス要求を作成する必要があります。

    2. ストレージ アカウントの待機時間が短い場合は、ネットワーク管理者に連絡して、ネットワーク内の待機時間に関する問題を調査してください。

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>8100 デバイスで実行されたパフォーマンス テストの出力例

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>付録: システム情報の解釈

次の表では、システム情報に含まれるさまざまな Windows PowerShell パラメーターが何に対応するかを示します。 

| PowerShell パラメーター    | 説明  |
|-------------------------|------------------|
| インスタンス ID             | すべてのコントローラーに一意の識別子または GUID が関連付けられています。|
| 名前                    | Azure ポータルでデバイスのデプロイ中に構成されるデバイスのフレンドリ名。 既定のフレンドリ名は、デバイスのシリアル番号です。 |
| モデル                   | ご利用の StorSimple 8000 シリーズ デバイスのモデル。 モデルは 8100 または 8600 です。|
| SerialNumber            | デバイスのシリアル番号は、出荷時に割り当てられる 15 個の文字です。 たとえば、8600-SHX0991003G44HT は次の内容を示します。<br> 8600 – デバイス モデルです。<br>SHX – 製造サイトです。<br> 0991003 - 特定の製品を示します。 <br> G44HT- 最後の 5 桁は、一意のシリアル番号を作成するためにインクリメントされます。 これは連番でない場合があります。|
| TimeZone                | Azure ポータルでデバイスのデプロイ中に構成されるデバイスのタイム ゾーン。|
| CurrentController       | StorSimple デバイスの Windows PowerShell インターフェイスを使用して接続されているコントローラー。|
| ActiveController        | デバイス上でアクティブになっていて、すべてのネットワークおよびディスク操作を制御しているコントローラー。 これはコントローラー 0 またはコントローラー 1 です。  |
| Controller0Status       | デバイスのコントローラー 0 の状態。 コントローラーの状態は、正常、回復モード、または到達不能です。|
| Controller1Status       | デバイスのコントローラー 1 の状態。  コントローラーの状態は、正常、回復モード、または到達不能です。|
| SystemMode              | StorSimple デバイスの全体的な状態。 デバイスの状態は、正常、メンテナンス、または使用停止 (Azure ポータルの非アクティブ化済みに対応) です。|
| FriendlySoftwareVersion | デバイス ソフトウェアのバージョンに対応するフレンドリ文字列。 Update 4 を実行しているシステムの場合、ソフトウェアのフレンドリ バージョンは StorSimple 8000 Series Update 4.0 です。|
| HcsSoftwareVersion      | デバイスで実行されている HCS ソフトウェアのバージョン。 たとえば、StorSimple 8000 シリーズ Update 4.0 に対応する HCS ソフトウェアのバージョンは 6.3.9600.17820 です。 |
| ApiVersion              | HCS デバイスの Windows PowerShell API のソフトウェア バージョン。|
| VhdVersion              | デバイスに付属する工場出荷時イメージのソフトウェア バージョン。 デバイスを出荷時の状態にリセットすると、このソフトウェア バージョンが実行されます。|
| OSVersion               | デバイスで実行されている Windows Server オペレーティング システムのソフトウェア バージョン。 StorSimple デバイスは、6.3.9600 に対応する Windows Server 2012 R2 に基づいています。|
| CisAgentVersion         | StorSimple デバイスで実行されている CIS エージェントのバージョン。 このエージェントにより、Azure で実行されている StorSimple Manager サービスと通信できるようになります。|
| MdsAgentVersion         | StorSimple デバイスで実行されている MDS エージェントに対応するバージョン。 このエージェントは、データを監視および診断サービス (MDS) に移動します。|
| Lsisas2Version          | StorSimple デバイスの LSI ドライバーに対応するバージョン。|
| 容量                | デバイスの合計容量 (バイト単位)。|
| RemoteManagementMode    | Windows PowerShell インターフェイスを使用してデバイスをリモート管理できるかどうかを示します。 |
| FipsMode                | デバイスで米国連邦情報処理標準 (FIPS) モードが有効になっているかどうかを示します。 FIPS 140 標準には、機密データ保護のために米国連邦政府のコンピューター システムに使用することが承認されている暗号化アルゴリズムが定義されています。 Update 4 以降を実行しているデバイスでは、既定で FIPS モードが有効になっています。 |

## <a name="next-steps"></a>次のステップ

* [Invoke-HcsDiagnostics コマンドレットの構文](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630))を確認します。

* StorSimple デバイスの[デプロイメントに関する問題のトラブルシューティングを行う](./storsimple-8000-troubleshoot-deployment.md)方法について理解します。
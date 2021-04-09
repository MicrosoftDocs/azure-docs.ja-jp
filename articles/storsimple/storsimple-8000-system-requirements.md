---
title: StorSimple 8000 シリーズのシステム要件 | Microsoft Docs
description: Microsoft Azure StorSimple ソリューションのソフトウェア、ネットワーク、高可用性の要件とベスト プラクティスについて説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: fa7616a740e8246fa08e950494428095f41ee404
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382856"
---
# <a name="storsimple-8000-series-software-high-availability-and-networking-requirements"></a>StorSimple 8000 シリーズのソフトウェア、高可用性、ネットワークの要件

## <a name="overview"></a>概要

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Microsoft Azure StorSimple へようこそ。 この記事では、StorSimple デバイスおよび StorSimple デバイスにアクセスするストレージ クライアントにおける重要なシステム要件とベスト プラクティスについて説明します。 StorSimple システムをデプロイする前に以下の情報を慎重に確認します。さらにデプロイと後続の操作中にも必要に応じて以下の情報を参照することをお勧めします。

システム要件は次のとおりです。

* **ストレージ クライアントのソフトウェア要件** - サポートされているオペレーティング システムと、これらのオペレーティング システムに関する追加要件について説明します。
* **、StorSimple デバイスのネットワーク要件** - iSCSI、クラウド、または管理トラフィックを使用できるようにするため、ファイアウォールで開いておく必要があるポートについての情報を提供します。
* **StorSimple の高可用性の要件** - StorSimple デバイスおよびホスト コンピューターの高可用性要件およびベスト プラクティスについて説明します。

## <a name="software-requirements-for-storage-clients"></a>ストレージ クライアントのソフトウェア要件

次のソフトウェア要件は、StorSimple デバイスにアクセスするストレージ クライアントに適用されます。

| サポートされるオペレーティング システム | 必須のバージョン | その他の要件/注意事項 |
| --- | --- | --- |
| Windows Server |2008 R2 SP1、2012、2012 R2、2016 |StorSimple iSCSI ボリュームの使用は以下の Windows ディスク タイプのみでサポートされます。<ul><li>ベーシック ディスク上のシンプル ボリューム</li><li>ダイナミック ディスク上のシンプルおよびミラー ボリューム</li></ul>サポートされるのは、オペレーティング システムにネイティブで存在するソフトウェア iSCSI イニシエーターだけです。 ハードウェア iSCSI イニシエーターはサポートされません。<br></br>Windows Server 2012 および 2016 のシン プロビジョニングおよび ODX 機能は StorSimple iSCSI ボリュームを使用している場合にサポートされます。<br><br>StorSimple は、シン プロビジョニングされたボリュームと完全にプロビジョニングされたボリュームを作成できます。 部分的にプロビジョニングされたボリュームは作成できません。<br><br>シン プロビジョニングされたボリュームを再フォーマットすると長い時間がかかる場合があります。 再フォーマットするのではなく、ボリュームを削除して、新しいボリュームを作成することをお勧めします。 それでもボリュームを再フォーマットする場合は以下を実行します。<ul><li>領域の再利用による遅延を避けるために、再フォーマットする前に次のコマンドを実行します。 <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>フォーマットが完了したら、次のコマンドを使用して領域の再利用を再び有効にします。<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>[KB 2878635](https://support.microsoft.com/kb/2870270) の説明に従って、Windows Server 2012 の修正プログラムを Windows Server コンピューターに適用します。</li></ul></li></ul></ul> StorSimple Snapshot Manager または SharePoint 用 StorSimple アダプターを構成する場合は、「[オプション コンポーネントのソフトウェア要件](#software-requirements-for-optional-components)」を参照してください。 <br> Windows Server クライアントが SMB プロトコルを使用して StorSimple デバイスにアクセスしている場合、並列処理の増加に関するガイダンスを確認するには、「[SMB ファイル サーバーのパフォーマンス チューニング](/windows-server/administration/performance-tuning/role/file-server/smb-file-server)」を参照してください。|
| VMware ESX |5.5 および 6.0 |iSCSI クライアントとして VMware vSphere でサポートされます。 VAAI ブロック機能は、StorSimple デバイス上の VMware vSphere でサポートされます。 |
| Linux RHEL/CentOS |5、6 および 7 |Open-iSCSI イニシエーター バージョン 5、6 および 7 での Linux iSCSI クライアントのサポート。 |
| Linux |SUSE Linux 11 | |

> [!NOTE]
> 現在、IBM AIX は StorSimple ではサポートされていません。


## <a name="software-requirements-for-optional-components"></a>オプション コンポーネントのソフトウェア要件用のソフトウェア要件

オプションの StorSimple コンポーネント (StorSimple Snapshot Manager および SharePoint 用 StorSimple アダプター) のソフトウェア要件を次に示します。

| コンポーネント | ホスト プラットフォーム | その他の要件/注意事項 |
| --- | --- | --- |
| StorSimple Snapshot Manager |Windows Server 2008 R2 SP1、2012、2012 R2 |ミラー化されたダイナミック ディスクをバックアップ/復元する場合、およびアプリケーション整合性バックアップを実行する場合は、Windows Server 上で StorSimple Snapshot Manager を使用する必要があります。<br> StorSimple Snapshot Manager は、Windows Server 2008 R2 SP1 (64 ビット)、Windows Server 2012 R2、Windows Server 2012 でのみサポートされます。<ul><li>Window Server 2012 を使用している場合は、StorSimple Snapshot Manager をインストールする前に .NET 3.5 ～ 4.5 をインストールする必要があります。</li><li>Windows Server 2008 R2 SP1 を使用している場合は、StorSimple Snapshot Manager をインストールする前に Windows Management Framework 3.0 をインストールする必要があります。</li></ul> |
| SharePoint 用 StorSimple アダプター |Windows Server 2008 R2 SP1、2012、2012 R2 |<ul><li>SharePoint 用 StorSimple アダプターは SharePoint 2010 および SharePoint 2013 でのみサポートされます。</li><li>RBS には SQL Server Enterprise Edition のバージョン 2008 R2 または 2012 が必要です。</li></ul> |

## <a name="networking-requirements-for-your-storsimple-device"></a>StorSimple デバイスのネットワーク要件

StorSimple デバイスはロックされたデバイスです。 ただし、iSCSI、クラウド、管理トラフィックを許可するには、ファイアウォールでポートを開く必要があります。 次の表は、ファイアウォールで開く必要があるポートの一覧です。 この表では、*イン* または *受信* はデバイスにアクセスするクライアント要求が入ってくる方向を意味します。 *アウト* または *送信* は StorSimple デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

| ポート番号<sup>1,2</sup> | インまたはアウト | ポート範囲 | 必須 | Notes |
| --- | --- | --- | --- | --- |
| TCP 80 (HTTP)<sup>3</sup> |アウト |WAN |いいえ |<ul><li>送信ポートは、更新プログラムを取得するためのインターネット アクセスに使用します。</li><li>送信 Web プロキシは、ユーザーが構成できます。</li><li>システムの更新を許可するために、コントローラーの固定 IP に対してもこのポートを開く必要があります。</li></ul> |
| TCP 443 (HTTPS)<sup>3</sup> |アウト |WAN |はい |<ul><li>送信ポートは、クラウドのデータへのアクセスに使用します。</li><li>送信 Web プロキシは、ユーザーが構成できます。</li><li>システムの更新を許可するために、コントローラーの固定 IP に対してもこのポートを開く必要があります。</li><li>このポートは、ガベージ コレクション用のコントローラーでも使用されます。</li></ul> |
| UDP 53 (DNS) |アウト |WAN |場合によっては、メモを参照してください。 |このポートは、インターネット ベースの DNS サーバーを使用する場合にのみ必要です。 |
| UDP 123 (NTP) |アウト |WAN |場合によっては、メモを参照してください。 |このポートは、インターネット ベースの NTP サーバーを使用する場合にのみ必要です。 |
| TCP 9354 |アウト |WAN |はい |送信ポートは、StorSimple デバイス マネージャー サービスと通信するために StorSimple デバイスによって使用されます。 |
| 3260 (iSCSI) |/ |LAN |いいえ |このポートは、iSCSI を介してデータにアクセスするために使用されます。 |
| 5985 |/ |LAN |いいえ |受信ポートは、StorSimple デバイスと通信するために StorSimple Snapshot Manager によって使用されます。<br>このポートは、HTTP 経由で Windows PowerShell for StorSimple にリモート接続する場合にも使用されます。 |
| 5986 |/ |LAN |いいえ |このポートは、HTTPS 経由で Windows PowerShell for StorSimple にリモート接続する場合にも使用されます。 |

<sup>1</sup> 受信ポートがパブリック インターネットで開かれている必要はありません。

<sup>2</sup> 複数のポートでゲートウェイ構成が行われる場合、送信トラフィックの順序は、以下の「[ポートのルーティング](#routing-metric)」で説明するポート ルーティング順序で決定されます。

<sup>3</sup> StorSimple デバイスのコントローラーの固定の IP アドレスがルーティング可能でインターネットに直接または構成済みの Web プロキシ経由で接続可能である必要があります。 固定 IP アドレスは、デバイスに更新プログラムを提供したりガベージ コレクションを実行したりするために使用されます。 デバイスのコントローラーが固定の IP を使用してインターネットに接続できない場合は、StorSimple デバイスを更新できず、ガベージ コレクションは正しく動作しません。

> [!IMPORTANT]
> StorSimple デバイスと Azure 間でファイアウォールが TLS トラフィックの変更や暗号化解除を行っていないことを確認します。


### <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン

多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 StorSimple デバイスと StorSimple デバイス マネージャー サービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなど、他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 これにより、ネットワーク管理者は必要に応じて StorSimple のファイアウォール ルールを監視し更新する必要があります。

ほとんどの場合、StorSimple 固定 IP アドレスに基づき、送信トラフィックのファイアウォール ルールを設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> デバイスの (ソース) IP は、常にすべての有効なネットワーク インターフェイスに合わせて設定するようにします。 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653)に合わせて設定するようにします。


#### <a name="url-patterns-for-azure-portal"></a>Azure ポータルの URL パターン

| URL パターン | コンポーネント/機能 | デバイスの IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`<br>`https://login.windows.net` |StorSimple デバイス マネージャー サービス<br>Access Control Service<br>Azure Service Bus<br>認証サービス |クラウド対応のネットワーク インターフェイス |
| `https://*.backup.windowsazure.com` |デバイス登録 |DATA 0 のみ |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |証明書の失効 |クラウド対応のネットワーク インターフェイス |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure ストレージ アカウントと監視 |クラウド対応のネットワーク インターフェイス |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update サーバー<br> |コントローラーの固定 IP のみ |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |コントローラーの固定 IP のみ |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |サポート パッケージ |クラウド対応のネットワーク インターフェイス |

#### <a name="url-patterns-for-azure-government-portal"></a>Azure Government ポータルの URL パターン

| URL パターン | コンポーネント/機能 | デバイスの IP |
| --- | --- | --- |
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`<br>`https://login.microsoftonline.us` |StorSimple デバイス マネージャー サービス<br>Access Control Service<br>Azure Service Bus<br>認証サービス |クラウド対応のネットワーク インターフェイス |
| `https://*.backup.windowsazure.us` |デバイス登録 |DATA 0 のみ |
| `https://crl.microsoft.com/pki/*`<br>`https://www.microsoft.com/pki/*` |証明書の失効 |クラウド対応のネットワーク インターフェイス |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` |Azure ストレージ アカウントと監視 |クラウド対応のネットワーク インターフェイス |
| `https://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`https://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`https://download.microsoft.com`<br>`http://wustat.windows.com`<br>`https://ntservicepack.microsoft.com` |Microsoft Update サーバー<br> |コントローラーの固定 IP のみ |
| `http://*.deploy.akamaitechnologies.com` |Akamai CDN |コントローラーの固定 IP のみ |
| `https://*.partners.extranet.microsoft.com/*`<br>`https://dcupload.microsoft.com/`<br>`https://*.support.microsoft.com/` |サポート パッケージ |クラウド対応のネットワーク インターフェイス |

### <a name="routing-metric"></a>ルーティング メトリック

ルーティング メトリックは、指定したネットワークにデータをルーティングするインターフェイスとゲートウェイに関連付けられています。 ルーティング プロトコルによって、指定された宛先への最適なパスを計算するために使用されます (同じ宛先への複数のパスが存在することがわかった場合)。 ルーティング メトリックが低いと、優先順位が高くなります。

StorSimple のコンテキストで、複数のネットワーク インターフェイスとゲートウェイがトラフィックを伝送するように構成されている場合、ルーティング メトリックはインターフェイスの相対的な使用順序を決定する役割を果たします。 ルーティング メトリックをユーザーが変更することはできません。 ただし、 `Get-HcsRoutingTable` コマンドレットを使用して、StorSimple デバイスのルーティング テーブル (およびメトリック) を出力することはできます。 Get-HcsRoutingTable コマンドレットの詳細については、 [StorSimple デプロイのトラブルシューティング](./storsimple-8000-troubleshoot-deployment.md)に関するページをご覧ください。

Update 2 以降のバージョンに使用されるルーティング メトリック アルゴリズムは以下のとおりです。

* 事前に定義された一連の値が、ネットワーク インターフェイスに割り当てられています。
* 以下に示す表の例では、さまざまなネットワーク インターフェイスがクラウド対応の場合とクラウド非対応 (ただしゲートウェイが構成済み) の場合に、割り当てられる値を示しています。 ここで割り当てられている値はあくまでも一例です。

    | ネットワーク インターフェイス | クラウド対応 | クラウド非対応 (ゲートウェイを使用) |
    |-----|---------------|---------------------------|
    | Data 0  | 1            | -                        |
    | Data 1  | 2            | 20                       |
    | Data 2  | 3            | 30                       |
    | Data 3  | 4            | 40                       |
    | Data 4  | 5            | 50                       |
    | Data 5  | 6            | 60                       |


* クラウド トラフィックがネットワーク インターフェイスを介してルーティングされる順序は、次のとおりです。
  
    *Data 0 > Data 1 > Date 2 > Data 3 > Data 4 > Data 5*
  
    これは、次の例で説明できます。
  
    2 つのクラウド対応ネットワーク インターフェイスとして Data 0 と Data 5 を使用する StorSimple デバイスを考えてみます。 Data 1 から Data 4 まではクラウドに対応しておらず、ゲートウェイが構成されています。 このデバイスでトラフィックがルーティングされる順序は、次のようになります。
  
    *Data 0 (1) > Data 5 (6) > Data 1 (20) > Data 2 (30) > Data 3 (40) > Data 4 (50)*
  
    *かっこ内の数値は、それぞれのルーティング メトリックを示しています。*
  
    Data 0 が失敗すると、クラウド トラフィックは Data 5 を介してルーティングされます。 ゲートウェイがその他のすべてのネットワークで構成されている場合、Data 0 と Data 5 の両方が失敗すると、クラウド トラフィックは Data 1 を介して送信されます。
* クラウド対応ネットワーク インターフェイスが失敗すると、そのインターフェイスへの接続が 30 秒間隔で 3 回試行されます。 すべての再試行が失敗した場合、トラフィックはルーティング テーブルによって決定されるとおりに、次に利用可能なクラウド対応インターフェイスにルーティングされます。 すべてのクラウド対応ネットワーク インターフェイスが失敗した場合、デバイスは他のコントローラーにフェールオーバーされます (この場合、再起動はありません)。
* iSCSI 対応のネットワーク インターフェイスで VIP エラーが発生した場合、2 秒間隔で 3 回まで再試行されます。 この動作は、以前のリリースと同じです。 すべての iSCSI ネットワーク インターフェイスが失敗すると、コントローラー フェールオーバーが発生します (再起動が伴います)。
* VIP エラーが発生すると、StorSimple デバイスでアラートも生成されます。 詳細については、 [アラートのクイック リファレンス](storsimple-8000-manage-alerts.md)に関するページを参照してください。
* 再試行に関しては、クラウドよりも iSCSI が優先されます。
  
    次の例で考えてみましょう。StorSimple デバイスで、2 つのネットワーク インターフェイス Data 0 と Data 1 が有効になっているとします。 Data 0 はクラウド対応ですが、Data 1 はクラウドと iSCSI の両方に対応しています。 このデバイス上の他のネットワーク インターフェイスは、クラウドにも iSCSI にも対応していません。
  
    Data 1 が失敗した場合、これが最後の iSCSI ネットワーク インターフェイスであるため、他のコントローラー上の Data 1 に対するコントローラー フェールオーバーが発生します。

### <a name="networking-best-practices"></a>ネットワークのベスト プラクティス

StorSimple ソリューションの最適なパフォーマンスを得るためには、上記のネットワーク要件に加えて、次のベスト プラクティスに従ってください。

* StorSimple デバイスで専用の 40 Mbps 帯域幅 (またはそれ以上) が常に利用できるようにします。 この帯域幅は他のいかなるアプリケーションとも共有できません (あるいは、QoS ポリシーを利用して確実に配分する必要があります)。
* インターネットへのネットワーク接続を常に利用できるようにします。 インターネット接続が途切れるなど、デバイスのインターネット接続が信頼できないと、結果的にサポートされない構成になります。
* iSCSI とクラウドのアクセスに対してデバイスに専用のネットワーク インターフェイスを置くことで、iSCSI とクラウド トラフィックを分離します。 詳細については、StorSimple デバイスで [ネットワーク インターフェイスを変更する](storsimple-8000-modify-device-config.md#modify-network-interfaces) 方法を参照してください。
* ネットワーク インターフェイスには、Link Aggregation Control Protocol (LACP) 構成を使用しないでください。 このような構成はサポートされていません。

## <a name="high-availability-requirements-for-storsimple"></a>StorSimple の高可用性の要件

StorSimple ソリューションに含まれるハードウェア プラットフォームには、可用性と信頼性の機能があります。これらの機能は、可用性が高くフォールト トレラントなストレージ インフラストラクチャの基礎をデータセンターで実現します。 ただし、StorSimple ソリューションの可用性が得られるようにするには、特定の要件とベスト プラクティスに準拠する必要があります。 StorSimple をデプロイする前に、StorSimple デバイスおよび接続されているホスト コンピューターについて次の要件とベスト プラクティスを慎重に確認します。

StorSimple デバイスのハードウェア コンポーネントの監視と保守の詳細については、「[StorSimple デバイス マネージャー サービスを使用したハードウェア コンポーネントと状態の監視](storsimple-8000-monitor-hardware-status.md)」および「[StorSimple のハードウェア コンポーネントの交換](storsimple-8000-hardware-component-replacement.md)」を参照してください。

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>StorSimple デバイスの高可用性の要件と手順

StorSimple デバイスの高可用性を確保するには、次の情報を慎重に確認します。

#### <a name="pcms"></a>PCM

StorSimple デバイスには、冗長なホットスワップ型の電源および冷却モジュール (PCM) が含まれています。 各 PCM はシャーシ全体にサービスを提供するのに十分な容量を備えています。 高可用性を確保するには、PCM を両方とも取り付ける必要があります。

* 1 つの電源で障害が発生した場合に可用性を確保するには、PCM をそれぞれ別々の電源に接続しておきます。
* PCM で障害が発生した場合は、すぐに交換を要求します。
* 故障した PCM は必ず、交換部品の取り付け準備ができてから取り外します。
* 両方の PCM を同時に取り外さないでください。 PCM モジュールには、バックアップ バッテリ モジュールが含まれます。 両方の PCM を取り外すと、バッテリの保護なしでシャットダウンされ、デバイスの状態は保存されません。 バッテリの詳細については、「 [バックアップ バッテリ モジュールを保守する](storsimple-8000-battery-replacement.md#maintain-the-backup-battery-module)」を参照してください。

#### <a name="controller-modules"></a>コントローラー モジュール

StorSimple デバイスには、冗長なホットスワップ型のコントローラー モジュールが含まれています。 コントローラー モジュールは、アクティブ/パッシブで動作します。 どのような場合も、一方のコントローラー モジュールがアクティブでサービスを提供し、もう一方のコントローラー モジュールがパッシブになります。 アクティブ コントローラー モジュールが故障したか取り外された場合は、パッシブ コントローラー モジュールの電源が入って動作可能になります。 各コントローラー モジュールには、シャーシ全体にサービスを提供できるだけの十分な容量があります。 高可用性を確保するには、両方のコントローラー モジュールを取り付ける必要があります。

* 両方のコントローラー モジュールが常に取り付けられている状態にします。
* コントローラー モジュールで障害が発生した場合は、すぐに交換を要求します。
* 故障したコントローラーは必ず、交換部品の取り付け準備ができてから取り外します。 長時間モジュールを取り外すと通気に影響し、その結果、システムの冷却に影響します。
* 両方のコントローラー モジュールへのネットワーク接続が同じであり、接続したネットワーク インターフェイスのネットワーク構成が同じになっていることを確認します。
* コントローラー モジュールの障害または交換が必要な場合は、障害状態にあるコントローラー モジュールを交換する前に、もう 1 つのコントローラー モジュールがアクティブ状態であることを確認します。 コントローラーがアクティブであることを確認するには、「 [デバイスのアクティブなコントローラーを識別する](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)」を参照してください。
* 両方のコントローラー モジュールを同時に取り外さないでください。 コントローラー フェールオーバーが進行中の場合、スタンバイ コントローラー モジュールをシャットダウンしたり、シャーシから取り外したりしないでください。
* コントローラー フェールオーバー後、いずれかのコントローラー モジュールを取り外す前に少なくとも 5 分待機します。

#### <a name="network-interfaces"></a>ネットワーク インターフェイス

StorSimple デバイス コントローラー モジュールはそれぞれ、1 ギガ ビットのイーサネット ネットワーク インターフェイスを 4 つと、10 ギガビットのイーサネット ネットワーク インターフェイスを 2 つ備えています。

* 両方のコントローラー モジュールへのネットワーク接続が同じであり、コントローラー モジュール インターフェイスが接続されているネットワーク インターフェイスのネットワーク構成が同じになっていることを確認します。
* ネットワーク デバイスで障害が発生した場合にサービスの可用性を確保できるようにするには、ネットワーク接続をそれぞれ異なる複数のスイッチでデプロイします。
* 唯一のまたは最後に残った iSCSI 対応インターフェイス (IP アドレスが割り当てられた) のプラグを抜く場合は、そのインターフェイスをまず無効にしてから、ケーブルを外します。 先にインターフェイスのプラグを抜くと、アクティブ コントローラーはパッシブ コントローラーにフェール オーバーします。 パッシブ コントローラーの対応するインターフェイスもプラグが抜かれている場合は、両方のコントローラーが複数回再起動してから 1 つのコントローラーに落ち着きます。
* 少なくとも 2 つのデータ インターフェイスを各コントローラー モジュールからネットワークに接続します。
* 2 つの 10 GbE インターフェイスを有効にした場合は、これらをそれぞれ異なる複数のスイッチでデプロイします。
* 可能であれば、サーバー上で MPIO を使用して、サーバーがリンク、ネットワーク、またはインターフェイスの障害に耐え得るようにします。

高可用性および高パフォーマンスを実現するためのデバイスのネットワーク接続方法の詳細については、「[StorSimple 8100 デバイスの取り付け](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device)」または「[StorSimple 8600 デバイスの取り付け](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device)」を参照してください。

#### <a name="ssds-and-hdds"></a>SSD と HDD

StorSimple デバイスには、ミラー化されたスペースを使用して保護されているソリッド ステート ドライブ (SSD) とハード ディスク ドライブ (HDD) が含まれています。 ミラー化されたスペースを使用すると、デバイスは 1 台または複数台の SSD または HDD で障害が発生してもそれを許容できます。

* すべての SSD と HDD モジュールが取り付けられていることを確認します。
* SSD または HDD で障害が発生した場合は、すぐに交換を要求します。
* SSD または HDD の障害または交換が必要な場合、交換が必要な SSD または HDD のみを取り外すようにします。
* どの時点でもシステムから複数台の SSD または HDD を取り外さないでください。
  特定の種類のディスク (HDD、SSD) での 2 つ以上のエラー、または短時間の連続したエラーは、システムの誤作動やデータ損失を発生させる可能性があります。 その場合は、 [Microsoft サポート](storsimple-8000-contact-microsoft-support.md) にお問い合わせください。
* 交換中は、SSD と HDD のドライブの **[ハードウェアの正常性]** ブレードで **[共有コンポーネント]** を監視します。 緑のチェック状態は、ディスクが正常または OK であると示しています。一方、赤の感嘆符は障害中の SSD または HDD を示しています。
* システム エラーに備えて、保護する必要があるすべてのボリュームのクラウド スナップショットを構成することもお勧めします。

#### <a name="ebod-enclosure"></a>EBOD エンクロージャ

StorSimple デバイス モデル 8600 には、主エンクロージャに加えて、Extended Bunch of Disks (EBOD) エンクロージャが含まれています。 EBOD には、EBOD コントローラーと、ミラー化されたスペースを使用して保護されるハード ディスク ドライブ (HDD) が含まれます。 ミラー化されたスペースを使用すると、デバイスは 1 台または複数台の HDD で障害が発生してもそれを許容できます。 EBOD エンクロージャは、冗長な SAS ケーブルを使用して、主エンクロージャに接続されます。

* 両方の EBOD エンクロージャ コントローラー モジュール、両方の SAS ケーブル、およびすべてのハード ディスク ドライブは必ず常時取り付けておきます。
* EBOD エンクロージャ コントローラー モジュールで障害が発生した場合は、すぐに交換を要求します。
* EBOD エンクロージャ コントローラー モジュールで故障が発生した場合は、該当するモジュールを交換する前に、もう 1 つのコントローラー モジュールがアクティブ状態であることを確認します。 コントローラーがアクティブであることを確認するには、「 [デバイスのアクティブなコントローラーを識別する](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device)」を参照してください。
* EBOD コントローラー モジュールの交換中に、 **[監視]**  >  **[ハードウェアの正常性]** にアクセスして、StorSimple デバイス マネージャー サービスのコンポーネントの状態を継続して監視します。
* SAS ケーブルの障害または交換が必要な場合 (Microsoft サポートはこのような判断にかかわる必要があります)、交換が必要な SAS ケーブルのみを取り外すようにします。
* どの時点でも両方の SAS ケーブルをシステムから同時に取り外さないでください。

### <a name="high-availability-recommendations-for-your-host-computers"></a>ホスト コンピューターの高可用性の推奨事項

StorSimple デバイスに接続されているホストの高可用性を確保するには、以下のベスト プラクティスを慎重に確認してください。

* [2 ノード ファイル サーバー クラスター構成][1]を使用した StorSimple を構成します。 単一障害点を削除し、ホスト側の冗長性を構築することにより、ソリューション全体の可用性が向上します。
* ストレージ コントローラーのフェールオーバー中の高可用性を目的として、Windows Server 2012 (SMB 3.0) で利用できる継続的可用性 (CA) 共有を使用します。 ファイル サーバー クラスターと Windows Server 2012 との継続的可用性の共有を構成するための追加情報については、この [ビデオ メモ](https://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares)を参照してください。

## <a name="next-steps"></a>次のステップ

* [StorSimple システムの制限の詳細](storsimple-8000-limits.md)
* [StorSimple ソリューションをデプロイする方法](storsimple-8000-deployment-walkthrough-u2.md)

<!--Reference links-->
[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731844(v=ws.10)
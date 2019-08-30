---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b13b809b04f6cf878d68311b756ed2ca826f9697
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935305"
---
**ドキュメントの最終更新日**:2019 年 8 月 9 日午前 10:00 (PST)。

投機的実行のサイドチャネル攻撃として知られる[新たな CPU 脆弱性クラス](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)を開示したところ、よりわかりやすい説明を求めて、お客様からさまざまな質問が寄せられています。  

Microsoft は、クラウド サービス全体に軽減策を展開しました。 Azure を実行し、顧客ワークロードを互いに分離するインフラストラクチャは保護されています。 つまり同じインフラストラクチャを使用する潜在的な攻撃者が、これらの脆弱性を利用してお客様のアプリケーションを攻撃することはできません。

Azure では、可能な限り[メモリ保持メンテナンス](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot)を利用することで、お客様への影響を極力少なくし、再起動を不要にしています。 システム全体に及ぶ更新をホストに対して行う際は、引き続きこうした手法を活用しながらお客様を保護してまいります。

Azure にさまざまな角度から組み込まれているセキュリティについて詳しくは、「[Azure のセキュリティのドキュメント](https://docs.microsoft.com/azure/security/)」サイトをご覧ください。 

> [!NOTE] 
> このドキュメントが初めて公開されて以来、この脆弱性クラスについて同様の記事がいくつか開示されてきました。 Microsoft は今後もお客様の保護とガイダンスの提供に積極的に投資していく予定です。 引き続き解決策を公開していくために、このページは随時更新されます。 
> 
> 2019 年 5 月 14 日、[Intel](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00233.html) は Microarchitectural Data Sampling (MDS) と呼ばれる、新たな投機的実行サイド チャネルの脆弱性を公開しました。MDS については、Microsoft のセキュリティ ガイダンス [ADV190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013) を参照してください。これには、複数の CVE が割り当てられています。 
> - CVE-2019-11091 - Microarchitectural Data Sampling Uncacheable Memory (MDSUM)
> - CVE-2018-12126 - Microarchitectural Store Buffer Data Sampling (MSBDS) 
> - CVE-2018-12127 - Microarchitectural Load Port Data Sampling (MLPDS)
> - CVE-2018-12130 - Microarchitectural Fill Buffer Data Sampling (MFBDS)
>
> この脆弱性が影響を及ぼすのは Intel® Core® プロセッサと Intel® Xeon® プロセッサです。  Microsoft Azure はオペレーティング システムの更新プログラムをリリースしており、この脆弱性からお客様を保護するために、Intel から公開された新しいマイクロコードをすべてのフリートでデプロイしています。   Azure は Intel と緊密に連携し、プラットフォーム上での正式なリリース前に新しいマイクロコードのテストと検証を行っています。 
>
> **VM 内で信頼されていないコードを実行しているお客様**は、すべての投機的実行サイド チャネルの脆弱性に関する追加のガイダンス (Microsoft アドバイザリ ADV [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)、[180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)、および [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)) を参照し、この脆弱性から保護するための対策を講じる必要があります。
>
> それ以外のお客様は、多層防御の観点から脆弱性を評価し、選択した構成のセキュリティとパフォーマンスに関する関連事項を考慮する必要があります。



## <a name="keeping-your-operating-systems-up-to-date"></a>オペレーティング システムを最新の状態に保つ

Azure 上で実行するアプリケーションを他の Azure ユーザーから切り離すために必ずしも OS の更新は必要ありませんが、ソフトウェアは常に最新に保つことをお勧めします。 Windows の最新のセキュリティ ロールアップには、投機的実行について、いくつかのサイドチャネルの脆弱性に対する軽減策が含まれています。 同様に、Linux ディストリビューションからも、これらの脆弱性に対処するための更新プログラムが複数リリースされています。 オペレーティング システムを更新するうえで Microsoft が推奨している対策は以下のとおりです。

| サービス | 推奨される操作  |
|----------|---------------------|
| Azure クラウド サービス  | [自動更新](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal)を有効にするか、最新のゲスト OS を実行していることを確認します。 |
| Azure Linux Virtual Machines | オペレーティング システムの提供元から入手した更新プログラムをインストールします。 詳細については、このドキュメントで後述する [Linux](#linux) に関する情報を参照してください。 |
| Azure Windows Virtual Machines  | 最新のセキュリティ ロールアップをインストールします。
| その他の Azure PaaS サービス | これらのサービスを使用している顧客がとる必要のある操作はありません。 Azure では、お使いの OS バージョンが自動的に最新に保たれます。 |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>信頼できないコードを実行する場合の追加のガイダンス 

信頼できないユーザーに任意のコードの実行を許可している場合、Azure Virtual Machines または Cloud Services 内に追加的なセキュリティ機能を導入したいことがあります。 投機的実行に関するいくつかの脆弱性によって説明されるプロセス間の情報漏えいベクターは、こうした機能によって保護することになります。

追加的なセキュリティ機能が推奨されるシナリオの例を次に示します。

- 信頼できないコードに VM 内での実行を許可している。  
    - "*バイナリまたはスクリプトのアップロードをいずれかの顧客に許可していて、その後、そのコードを自分がアプリケーション内で実行してしまうなど*"。 
- 信頼できないユーザーに、権限の低いアカウントを使って自分の VM にログインすることを許可している。   
    - "*リモート デスクトップや SSH を使っていずれかの VM にログインすることを権限の低いユーザーに許可しているなど*"。  
- 入れ子になった仮想化を使って導入された仮想マシンにアクセスすることを、信頼できないユーザーに許可している。  
    - "*Hyper-V ホストを管理しているのは自分だが、その VM を信頼できないユーザーに割り当てているなど*"。 

信頼できないコードに関するシナリオが該当しないのであれば、追加のセキュリティ機能を有効にする必要はありません。 

## <a name="enabling-additional-security"></a>追加のセキュリティの有効化 

信頼されていないコードを実行している場合は、VM 内またはクラウド サービス内で追加的なセキュリティ機能を有効にすることができます。 VM 内またはクラウド サービス内でセキュリティ機能を有効にするために、オペレーティング システムが最新の状態になっていることも並行して確認してください

### <a name="windows"></a>Windows 

そうした追加的なセキュリティ機能を有効にするためには、対象のオペレーティング システムが最新の状態にあることが必要です。 投機的実行のサイドチャネルに関しては、さまざまな軽減策が既定で有効になっていますが、ここで説明する追加的な機能は手動で有効にする必要があり、またパフォーマンスに影響を及ぼすことがあります。 


**手順 1:VM 上でハイパースレッディングを無効にする** - ハイパースレッド化された VM 上で信頼されていないコードを実行しているお客様は、ハイパースレッディングを無効にするか、ハイパースレッド化されていない VM サイズに移す必要があります。 ハイパースレッド化された VM サイズの一覧については[こちらのドキュメント](https://docs.microsoft.com/azure/virtual-machines/windows/acu)を参照してください (ここで、vCPU とコアの比率は 2:1 です)。 VM でハイパースレッディングが有効になっているかどうかを確認するには、VM 内で Windows コマンド ラインを使用して以下のスクリプトを参照してください。

対話型インターフェイスを起動するには、`wmic` と入力します。 次に、以下を入力して、VM 上の物理および論理プロセッサの量を表示します。

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

論理プロセッサの数が物理プロセッサ (コア) よりも多い場合は、ハイパースレッディングが有効になっています。  ハイパースレッド化された VM を実行している場合は、[Azure サポートに問い合わせて](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)、ハイパースレッディングを無効にしてください。  ハイパースレッディングが無効になると、**VM の完全な再起動が必要になります**。 VM コア数が減少した理由については、「[コア数](#core-count)」を参照してください。


**手順 2**:手順 1 と並行して [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の手順に従い、[SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell モジュールを使って、保護が有効になっていることを確認します。

> [!NOTE]
> このモジュールをダウンロード済みであっても、インストールするのは最新バージョンであることが必要です。
>


PowerShell スクリプトの出力には、この脆弱性からの保護が有効になっていることを確認するための以下の値が含まれている必要があります。

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
```

出力に `MDS mitigation is enabled: False` と示されている場合、使用可能な軽減策のオプションについて [Azure サポートに問い合わせてください](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)。



**手順 3**:カーネル仮想アドレス シャドウ処理 (KVAS) とブランチ ターゲット インジェクション (BTI) の OS サポートを有効にするには、[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の指示に従い、`Session Manager` のレジストリ キーを使用して保護を有効にします。 再起動が必要となります。


**手順 4**:[入れ子式の仮想化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)でデプロイする場合 (D3 と E3 のみ):これらの手順は、Hyper-V ホストとして使用している VM 内で適用されます。

1.  [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の指示に従い、`MinVmVersionForCpuBasedMitigations` のレジストリ キーを使用して保護を有効にします。
2.  [こちら](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)の手順に従って、ハイパーバイザーのスケジューラの種類を `Core` に設定します。


### <a name="linux"></a>Linux

<a name="linux"></a>追加的なセキュリティ機能一式を内部で有効にするためには、対象のオペレーティング システムが完全に最新の状態にあることが必要です。 一部の軽減策については、既定で有効になります。 次のセクションで説明している内容は、既定でオフになっている機能や、ハードウェア サポート (マイクロコード) に依存している機能が対象となります。 これらの機能を有効にすると、パフォーマンスに影響が生じることがあります。 詳しい手順については、オペレーティング システムの提供元のドキュメントを参照してください。


**手順 1:VM 上でハイパースレッディングを無効にする** - ハイパースレッド化された VM 上で信頼されていないコードを実行しているお客様は、ハイパースレッディングを無効にするか、ハイパースレッド化されていない VM に移す必要があります。  ハイパースレッド化された VM サイズの一覧については[こちらのドキュメント](https://docs.microsoft.com/azure/virtual-machines/linux/acu)を参照してください (ここで、vCPU とコアの比率は 2:1 です)。 ハイパースレッド化された VM を実行しているかどうかを確認するには、Linux VM で `lscpu` コマンドを実行します。 

`Thread(s) per core = 2` の場合、ハイパースレッディングが有効になっています。 

`Thread(s) per core = 1` の場合、ハイパースレッディングが無効になっています。 

 
ハイパースレッディングが有効になっている VM の出力例: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

ハイパースレッド化された VM を実行している場合は、[Azure サポートに問い合わせて](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)、ハイパースレッディングを無効にしてください。  ハイパースレッディングが無効になると、**VM の完全な再起動が必要になります**。 VM コア数が減少した理由については、「[コア数](#core-count)」を参照してください。



**手順 2**:以下のいずれかの投機的実行サイド チャネルの脆弱性を軽減する場合は、お使いのオペレーティング システム プロバイダーのドキュメントを参照してください。   
 
- [Redhat およびCentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>コア数

ハイパースレッド化された VM が作成されると、Azure によってコアあたり 2 つのスレッドが割り当てられます。これらは vCPU と呼ばれます。 ハイパースレッディングが無効にされると、Azure によってスレッドが削除され、1 つのスレッド化されたコア (物理コア) が表示されます。 vCPU と CPU の比率は 2:1 なので、ハイパースレッディングが無効になると、VM 内の CPU 数は半分に減少したように見えます。 たとえば、D8_v3 VM は、8 個の vCPU (コアあたり 2 スレッド × 4 コア) 上で実行されるハイパースレッド化された VM です。  ハイパースレッディングが無効にされると、CPU はコアあたり 1 スレッドの 4 物理コアに低下します。 

## <a name="next-steps"></a>次の手順

この記事では、多くの最新のプロセッサに影響を与える、以下の投機的実行サイド チャネル攻撃のガイダンスを提供しています。

[Spectre Meltdown](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715 - ブランチ ターゲット インジェクション (BTI)  
- CVE-2017-5754 - カーネル ページ テーブル アイソレーション (KPTI)
- CVE-2018-3639 - 投機的ストア バイパス (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows カーネル情報 – Spectre Variant 1 の変種
 
[L1 ターミナル フォールト (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615 - Intel ソフトウェア ガード エクステンションズ (Intel SGX)
- CVE-2018-3620 - オペレーティング システム (OS) およびシステム管理モード (SMM)
- CVE-2018-3646 - Virtual Machine Manager (VMM) への影響

[Microarchitectural Data Sampling](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091 - Microarchitectural Data Sampling Uncacheable Memory (MDSUM)
- CVE-2018-12126 - Microarchitectural Store Buffer Data Sampling (MSBDS)
- CVE-2018-12127 - Microarchitectural Load Port Data Sampling (MLPDS)
- CVE-2018-12130 - Microarchitectural Fill Buffer Data Sampling (MFBDS)









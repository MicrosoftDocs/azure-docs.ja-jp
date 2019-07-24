---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 4c5b4c5eacd4be751004af551e3753a61873c7a7
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2019
ms.locfileid: "59551645"
---
**ドキュメントの最終更新日**:2018 年 8 月 14 日午前 10:00 (PST)。

投機的実行のサイドチャネル攻撃として知られる[新たな CPU 脆弱性クラス](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002)を開示したところ、よりわかりやすい説明を求めて、お客様からさまざまな質問が寄せられています。  

Microsoft は、クラウド サービス全体に軽減策を展開しました。 Azure を実行し、顧客ワークロードを互いに分離するインフラストラクチャは保護されています。 つまり同じインフラストラクチャを使用する潜在的な攻撃者が、これらの脆弱性を利用してお客様のアプリケーションを攻撃することはできません。

Azure では、可能な限り[メモリ保持メンテナンス](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-not-requiring-a-reboot)を利用することで、お客様への影響を極力少なくし、再起動を不要にしています。 システム全体に及ぶ更新をホストに対して行う際は、引き続きこうした手法を活用しながらお客様を保護してまいります。

Azure にさまざまな角度から組み込まれているセキュリティについて詳しくは、「[Azure のセキュリティのドキュメント](https://docs.microsoft.com/azure/security/)」サイトをご覧ください。 

> [!NOTE] 
> このドキュメントが初めて公開されて以来、この脆弱性クラスについて同様の記事がいくつか開示されてきました。 Microsoft は今後もお客様の保護とガイダンスの提供に積極的に投資していく予定です。 引き続き解決策を公開していくために、このページは随時更新されます。 
> 
> 2018 年 8 月 14 日には、投機的実行に関する新たなサイドチャネルの脆弱性が業界により開示されました。この脆弱性は [L1 Terminal Fault](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018) (L1TF) と呼ばれ、複数の CVE ([CVE-2018-3615、CVE-2018-3620、CVE-2018-3646](https://www.intel.com/content/www/us/en/security-center/advisory/intel-sa-00161.html)) が割り当てられています。 この脆弱性が影響を及ぼすのは Intel® Core® プロセッサと Intel® Xeon® プロセッサです。 Microsoft のクラウド サービスには、顧客間の分離性を高める軽減策が導入されています。 L1TF や以前の脆弱性 ([Spectre Variant 2 CVE-2017-5715 および Meltdown Variant 3 CVE-2017-5754](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution)) から保護するための詳しいガイダンスについては、以降の情報をご覧ください。
>  






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

VM 内または Cloud Services 内で追加的なセキュリティ機能を有効にすることができます。

### <a name="windows"></a> Windows 

そうした追加的なセキュリティ機能を有効にするためには、対象のオペレーティング システムが最新の状態にあることが必要です。 投機的実行のサイドチャネルに関しては、さまざまな軽減策が既定で有効になっていますが、ここで説明する追加的な機能は手動で有効にする必要があり、またパフォーマンスに影響を及ぼすことがあります。 

**手順 1.**:[Azure サポートに連絡](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)して、ご利用の Virtual Machines に最新のファームウェア (マイクロコード) を公開してもらいます。 

**手順 2**:Kernel Virtual Address Shadowing (KVAS) と Branch Target Injection (BTI) OS サポートを有効にします。 [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の指示に従い、`Session Manager` のレジストリ キーで保護を有効にします。 再起動が必要となります。 

**手順 3**:[入れ子式の仮想化](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)でデプロイする場合 (D3 と E3 のみ):これらの手順は、Hyper-V ホストとして使用している VM 内で適用されます。 

1. [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の指示に従い、`MinVmVersionForCpuBasedMitigations` のレジストリ キーで保護を有効にします。  
 
1. [こちら](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)の手順に従って、ハイパーバイザーのスケジューラの種類を **[コア]** に設定します。 

**手順 4**:[KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) の手順に従い、[SpeculationControl](https://aka.ms/SpeculationControlPS) PowerShell モジュールを使って、保護が有効になっていることを確認します。 

> [!NOTE]
> このモジュールをダウンロード済みであっても、インストールするのは最新バージョンであることが必要です。
>

すべての VM で次のように表示される必要があります。

```
branch target injection mitigation is enabled: True

kernel VA shadow is enabled: True  

L1TFWindowsSupportEnabled: True
```


### <a name="linux"></a>Linux

<a name="linux"></a>追加的なセキュリティ機能一式を内部で有効にするためには、対象のオペレーティング システムが完全に最新の状態にあることが必要です。 一部の軽減策については、既定で有効になります。 次のセクションで説明している内容は、既定でオフになっている機能や、ハードウェア サポート (マイクロコード) に依存している機能が対象となります。 これらの機能を有効にすると、パフォーマンスに影響が生じることがあります。 詳しい手順については、オペレーティング システムの提供元のドキュメントを参照してください。
 
**手順 1.**:[Azure サポートに連絡](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical)して、ご利用の Virtual Machines に最新のファームウェア (マイクロコード) を公開してもらいます。
 
**手順 2**:CVE-2017-5715 (Spectre Variant 2) を軽減するために、オペレーティング システムの提供元のドキュメントに従って、Branch Target Injection (BTI) OS サポートを有効にします。 
 
**手順 3**:CVE-2017-5754 (Meltdown Variant 3) を軽減するために、オペレーティング システムの提供元のドキュメントに従って、Kernel Page Table Isolation (KPTI) を有効にします。 
 
詳しい情報については、オペレーティング システムの提供元から入手してください。  
 
- [Redhat およびCentOS](https://access.redhat.com/security/vulnerabilities/speculativeexecution) 
- [Suse](https://www.suse.com/support/kb/doc/?id=7022512) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/SpectreAndMeltdown) 


## <a name="next-steps"></a>次の手順

詳しくは、[CPU の脆弱性からの Azure 顧客の保護](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/)に関する記事を参照してください。

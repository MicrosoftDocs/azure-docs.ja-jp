---
title: Azure Security Center での VM と サーバーの脅威検出 | Microsoft Docs
description: このトピックでは、Azure Security Center で使用可能な VM および サーバーのアラートについて説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295787"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Azure Security Center での VM と サーバーの脅威検出

このトピックでは、次のオペレーティング システムを使用する VM およびサーバーで使用可能なさまざまな種類の検出方法およびアラートを示します。 サポートされているバージョンの一覧については、「[Azure Security Center でサポートされている機能とプラットフォーム](https://docs.microsoft.com/azure/security-center/security-center-os-coverage)」をご覧ください。

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Security Center は Azure サービスと統合し、Windows ベースのマシンの監視と保護を行います。  Security Center は、これらすべてのサービスからのアラートと修復の提案を使いやすい形式で示します。

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Azure Security Center は、Windows Defender Advanced Threat Protection (ATP) との統合により、そのクラウド ワークロード保護プラットフォームを拡張します。 これにより、包括的なエンドポイントの検出と対応 (EDR) 機能が提供されます。

> [!NOTE]
> Windows Server Defender ATP センサーは、Azure Security Center にオンボードされた Windows サーバーで自動的に有効になります。

Windows Server Defender ATP で脅威が検出されると、アラートがトリガーされます。 アラートは、[Security Center] ダッシュボードに表示されます。 ダッシュボードから Windows Defender ATP コンソールにピボットして、詳細な調査を実行し、攻撃の範囲を明らかにすることができます。 Windows Server Defender ATP の詳細については、「[Windows Defender ATP サービスに対するサーバーのオンボード](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints)」をご覧ください。

### クラッシュ ダンプ分析 <a nanme="windows-dump"></a>

ソフトウェアがクラッシュすると、クラッシュ時のメモリが部分的にクラッシュ ダンプにキャプチャされます。

クラッシュは、マルウェアによって発生したか、マルウェアを含んでいる可能性があります。 セキュリティ製品によって検出されないようにするために、さまざまな形式のマルウェアがファイルレス攻撃を使用して、ディスクへの書き込みやディスクに書き込まれたソフトウェア コンポーネントの暗号化を回避します。 この種の攻撃は、従来のディスク ベースのアプローチを使用して検出することは困難です。

しかし、このような攻撃は、メモリ分析を使用して検出できます。 Security Center では、クラッシュ ダンプのメモリを分析することによって、攻撃がソフトウェアの脆弱性の悪用、機密データへのアクセス、および侵入したマシン内での潜伏に使用している手法を検出できます。 これは、Security Center バックエンドによって、ホストへのパフォーマンスの影響を最小限に抑えながら行われます。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Code injection discovered (コード インジェクションが検出されました)**|コード インジェクションとは、実行中のプロセスやスレッドに実行可能なモジュールを挿入することです。 この手法は、マルウェアが、検出されて削除されるのを防ぐためにうまく隠れながら、データにアクセスするために使用されます。 <br/>このアラートは、挿入されたモジュールがクラッシュ ダンプに存在することを示します。 悪意のある挿入モジュールと悪意のない挿入モジュールを区別するために、Security Center は、挿入されたモジュールが疑わしい動作の特徴に合致するかどうかをチェックします。|
|**Suspicious code segment discovered (疑わしいコード セグメントが検出されました)**|反射型インジェクションやプロセス ハロウイングなど、標準以外の方法を使用してコード セグメントが割り当てられたことを示します。 このアラートは、処理されたコード セグメントの他の特性を提供して、報告されるコード セグメントの能力および動作に関するコンテキストを提供します。|
|**Shellcode discovered (シェルコードが検出されました)**|シェルコードは、マルウェアがソフトウェアの脆弱性を突破した後に実行されるペイロードです。<br/>このアラートは、悪意のあるペイロードに共通した振る舞いをする実行可能コードがクラッシュ ダンプ分析によって検出されたことを示します。 悪意のないソフトウェアもこの振る舞いをする可能性がありますが、これはソフトウェア開発の標準的な慣例からは逸脱しています。|

### ファイルレス攻撃の検出 <a nanme="windows-fileless"></a>

Azure では、お客様のエンドポイントを対象としたファイルレス攻撃が定期的に確認されます。

ファイルレス攻撃は、検出を回避するために悪意のあるペイロードをメモリに挿入します。 攻撃者のペイロードは、侵害されたプロセスのメモリ内に存続し、さまざまな悪意のあるアクティビティを実行します。

自動メモリ フォレンジック手法は、ファイルレス攻撃の検出を使用して、ファイルレス攻撃のツールキット、手法、および動作を識別します。 このソリューションは、実行時にマシンを定期的にスキャンし、セキュリティ クリティカルなプロセスのメモリから直接分析情報を抽出します。

これは、悪用、コード インジェクション、および悪意のあるペイロードの実行の証拠を見つけます。 ファイルレス攻撃の検出は、アラートのトリアージ、相関関係、およびダウン ストリームの応答時間を速めるために詳細なセキュリティ アラートを生成します。 このアプローチは、イベント ベースの EDR ソリューションを補完し、検出範囲を拡大します。

> [!NOTE]
> Windows のアラートをシミュレートするには、「[Azure Security Center プレイブック](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef):セキュリティ アラート」をダウンロードして、提供されているガイドラインに従ってください

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Fileless attack technique detected (ファイルレス攻撃手法が検出されました)**|以下に示すプロセスのメモリには、ファイルレス攻撃ツールキットが含まれています:Meterpreter。 ファイルレス攻撃ツールキットは通常、ファイル システムに存在せず、従来のウイルス対策による検出を困難にします。|

### <a name="further-reading"></a>参考資料

Security Center の検出の例と詳細:

* [Azure Security Center によるサイバー攻撃の自動検出のしくみ](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Azure Security Center で管理ツールを使用して脆弱性を検出する方法](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Security Center は、**auditd** (最も一般的な Linux 監査フレームワークの 1 つ) を使用して Linux マシンから監査レコードを収集します。 auditd には、長い間使われていて、主流のカーネルに存在するという利点があります。 

### Linux の auditd アラートと Microsoft Monitoring Agent (MMA) の統合 <a name="linux-auditd"></a>

auditd システムは、カーネル レベルのサブシステムで構成されています。これは、システム呼び出しの監視、特定のルール セットによるそれらのフィルター処理、およびそれらに対するメッセージのソケットへの書き込みを担当します。 Security Center は、Microsoft Monitoring Agent (MMA) 内で auditd パッケージの機能を統合します。 この統合により、前提条件なしで、すべてのサポートされている Linux ディストリビューションで auditd イベントの収集が可能になります。  

auditd レコードは、Linux の MMA エージェントを使用して収集、拡充、およびイベントへの集約が行われます。 Security Center は、Linux シグナルを活用してクラウドおよびオンプレミスの Linux マシン上で悪意のある動作を検出する新しい分析の追加に常に取り組んでいます。 Windows の機能と同様に、これらの分析は、マシンが攻撃を受けているか、侵害されたことを示す、不審なプロセス、不審なログイン試行、カーネル モジュールの読み込み、その他のアクティビティにわたります。  

以下に、攻撃ライフサイクルのさまざまな段階にまたがる分析の例をいくつか示します。

> [!div class="mx-tableFixed"]

|アラート:|説明|
|---|---|
|**Process seen accessing the SSH authorized keys file in an unusual way (通常とは異なる方法で SSH 承認済みキー ファイルにアクセスするプロセスが確認されました)**|SSH 承認済みキー ファイルが、既知のマルウェア活動に似た方法でアクセスされました。 このアクセスは、攻撃者がマシンへの永続的なアクセスを取得しようとしていることを示している可能性があります|
|**Detected Persistence Attempt (永続化の試行が検出されました)**|ホスト データ分析で、シングル ユーザー モードのスタートアップ スクリプトがインストールされていることが検出されました。 <br/>正当なプロセスがそのモードでの実行を要求されることはまれであるため、これは、攻撃者が持続性を保証するために、すべての実行レベルに悪意のあるプロセスを追加したことを示している可能性があります。|
|**Manipulation of scheduled tasks detected (スケジュールされたタスクの操作が検出されました)**|ホスト データ分析で、 スケジュールされたタスクが操作された可能性が検出されました。 攻撃者は持続性を得るために、侵害したマシンにスケジュールされたタスクを追加することがよくあります。|
|**Suspicious file timestamp modification (ファイルのタイムスタンプの不審な変更)**|ホスト データ分析で、不審なタイムスタンプの変更が検出されました。 攻撃者は、既存の正当なファイルから新しいツールにタイムスタンプをコピーし、これらの新しくドロップされたファイルの検出を回避することがよくあります|
|**A new user was added to the sudoers group (新しいユーザーが sudoers グループに追加されました)**|ホスト データ分析で、所属メンバーが高い特権でコマンドを実行できる sudoers グループに新しいユーザーが追加されたことが検出されました。|
|**Likely exploit of DynoRoot vulnerability in dhcp client (DHCP クライアントの DynoRoot 脆弱性の悪用の可能性があります)**|ホスト データ分析で、dhclient スクリプトの親プロセスで異常なコマンドの実行が検出されました。|
|**Suspicious kernel module detected (不審なカーネル モジュールが検出されました)**|ホスト データ分析で、カーネル モジュールとして読み込まれる共有オブジェクト ファイルが検出されました。 これは、正当なアクティビティであるか、マシンのいずれかが侵害されたことを示している可能性があります。|
|**Process associated with digital currency mining detected (デジタル通貨マイニングに関連するプロセスが検出されました)**|ホスト データ分析で、通常はデジタル通貨マイニングに関連するプロセスの実行が検出されました|
|**Potential port forwarding to external IP address (外部 IP アドレスへのポート フォワーディングの可能性があります)**|ホスト データ分析で、外部 IP アドレスへのポート フォワーディングの開始が検出されました。|

> [!NOTE]
> Windows のアラートをシミュレートするには、「[Azure Security Center プレイブック:セキュリティ アラート](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef)」をダウンロードして、提供されているガイドラインに従ってください。


詳細と例については、次の記事をご覧ください。  

* [Azure Security Center を活用して、侵害された Linux マシンの攻撃を検出する ](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Azure Security Center で Linux の新たな脆弱性を検出可能](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 
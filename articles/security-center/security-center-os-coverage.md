---
title: Azure Security Center でサポートされている機能とプラットフォーム | Microsoft Docs
description: このドキュメントでは、Azure Security Center でサポートされる機能とプラットフォームの一覧を示します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 6/03/2019
ms.author: v-mohabe
ms.openlocfilehash: 02d993d760338356fa29ee58a03215e14d6583f1
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295597"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center でサポートされている機能とプラットフォーム

セキュリティ状態の監視と推奨事項は、クラシック デプロイ モデルと Resource Manager デプロイ モデルのどちらかを使用して作成された仮想マシン (VM)、およびコンピューターで利用できます。

> [!NOTE]
> Azure リソースの詳細については、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)に関するページを参照してください。
>

## <a name="platforms-that-support-the-data-collection-agent"></a>データ コレクション エージェントをサポートするプラットフォーム 

このセクションでは、Azure Security Center エージェントで実行できるプラットフォーム、およびデータを収集する元となるプラットフォームが一覧されます。

### <a name="supported-platforms-for-windows-computers-and-vms"></a>Windows コンピューターおよび VM でサポートされているプラットフォーム
次の Windows オペレーティング システムがサポートされています。

* Windows Server 2019
* Windows Server 2016
* Windows Server 2012 R2
* Windows Server 2012
* Windows Server 2008 R2
* Windows Server 2008

> [!NOTE]
> Windows Defender ATP との統合は、Windows Server 2012 R2 および Windows Server 2016 のみをサポートしています。

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux コンピューターおよび VM でサポートされているプラットフォーム

次の Linux オペレーティング システムがサポートされています。

> [!NOTE]
> サポートされている Linux オペレーティング システムの一覧は常に変更されているため、このトピックが最後に公開されてから変更があった場合は、サポートされているバージョンの最新の一覧を表示するために、[ここ](https://github.com/microsoft/OMS-Agent-for-Linux#supported-linux-operating-systems)をクリックしてください。

64 ビット
* CentOS 6 および 7
* Amazon Linux 2017.09
* Oracle Linux 6 および 7
* Red Hat Enterprise Linux Server 6 および 7
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS、16.04 LTS、および 18.04 LTS
* SUSE Linux Enterprise Server 12

32 ビット
* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Debian GNU/Linux 8 および 9
* Ubuntu Linux 14.04 LTS および 16.04 LTS

## <a name="vms-and-cloud-services"></a>VM とクラウド サービス
クラウド サービスで実行する VM もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。


## <a name="supported-iaas-features"></a>サポートされている IaaS 機能

> [!div class="mx-tableFixed"]
> 

|サーバー|Windows|||Linux|||価格|
|----|----|----|----|----|----|----|----|
|**Environment**|**Azure**||**Azure 以外**|**Azure**||**Azure 以外**||
||**仮想マシン**|**Virtual Machine Scale Set**||**仮想マシン**|**Virtual Machine Scale Set**|
|VMBA 脅威検出アラート|✔|✔|✔|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|推奨事項 (無料) 脅威の検出 (Standard)|
|ネットワーク経由の脅威検出アラート|✔|✔|X|✔|✔|X|Standard|
|Windows Defender ATP の統合|✔ (サポートされているバージョン)|✔ (サポートされているバージョン)|✔|X|X|X|Standard|
|不足しているパッチ|✔|✔|✔|✔|✔|✔|無料|
|セキュリティ構成|✔|✔|✔|✔|✔|✔|無料|
|エンドポイント保護の評価|✔|✔|✔|X|X|X|無料|
|JIT VM アクセス|✔|X|X|✔|X|X|Standard|
|アダプティブ アプリケーション制御|✔|X|✔|✔|X|✔|Standard|
|FIM|✔|✔|✔|✔|✔|✔|Standard|
|ディスク暗号化の評価|✔|✔|X|✔|✔|X|無料|
|サード パーティ デプロイ|✔|X|X|✔|X|X|無料|
|NSG の評価|✔|✔|X|✔|✔|X|無料|
|ファイルレス脅威の検出|✔|✔|✔|X|X|X|Standard|
|ネットワーク マップ|✔|✔|X|✔|✔|X|Standard|
|アダプティブ ネットワーク制御|✔|✔|X|✔|✔|X|Standard|
|規制へのコンプライアンス ダッシュボードおよびレポート|✔|✔|✔|✔|✔|✔|Standard|
|Docker でホストされている IaaS コンテナーに対する推奨事項と脅威の検出|X|X|X|✔|✔|✔|Standard|

### <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション

次の表に、次の項目のマトリックスを示します。
 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 これらのエンドポイント保護ソリューションのいずれかが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

これらの各保護に対する推奨事項がいつ生成されるかの情報については、[エンドポイント保護の評価と推奨事項](security-center-endpoint-protection.md)に関するページを参照してください。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – すべてのバージョン | Windows Server ファミリ  | いいえ | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ | はい |
| Kaspersky| Windows Server ファミリ  | いいえ | いいえ  |
| Sophos| Windows Server ファミリ  | いいえ | いいえ  |

> [!NOTE]
> - Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) が検出されるには、PowerShell 3.0 (またはそれ以降) の後で SCEP をインストールする必要があります。

## <a name="supported-paas-features"></a>サポートされている PaaS 機能


|Service|推奨事項 (無料)|脅威検出 (Standard)|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure BLOB ストレージ アカウント|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNet|✔| NA|
|サブネット|✔| NA|
|NIC|✔| NA|
|NSG|✔| NA|
|Subscription|✔ **| ✔|
|Batch|✔| NA|
|Service Fabric|✔| NA|
|Automation アカウント|✔| NA|
|Load Balancer|✔| NA|
|Search|✔| NA|
|Service Bus|✔| NA|
|Stream Analytics|✔| NA|
|イベント ハブ|✔| NA|
|ロジック アプリ|✔| NA|
|ストレージ アカウント|✔| NA|
|Redis|✔| NA|
|Data Lake Analytics|✔| NA|
|Key Vault|✔| NA|




\* 現在、これらの機能はパブリック プレビューでサポートされています。

\*\* AAD の推奨事項は、Standard サブスクリプションだけで使用できます



## <a name="next-steps"></a>次の手順

- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center における仮想マシンの動作分析とクラッシュ ダンプ メモリ分析](security-center-alerts-type.md#virtual-machine-behavioral-analysis)について説明します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。

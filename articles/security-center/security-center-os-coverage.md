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
ms.date: 4/17/2019
ms.author: monhaber
ms.openlocfilehash: b5eafd15344156965d0a191688f602ffe1b5a498
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678311"
---
# <a name="platforms-and-features-supported-by-azure-security-center"></a>Azure Security Center でサポートされている機能とプラットフォーム

セキュリティ状態の監視と推奨事項は、クラシック デプロイ モデルと Resource Manager デプロイ モデルのどちらかを使用して作成された仮想マシン (VM)、およびコンピューターで利用できます。

> [!NOTE]
> Azure リソースの詳細については、[クラシック デプロイ モデルと Resource Manager デプロイ モデル](../azure-classic-rm.md)に関するページを参照してください。
>
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
>
>

### <a name="supported-platforms-for-linux-computers-and-vms"></a>Linux コンピューターおよび VM でサポートされているプラットフォーム
次の Linux オペレーティング システムがサポートされています。

* Ubuntu バージョン 12.04 LTS、14.04 LTS、および 16.04 LTS。
* Debian バージョン 6、7、8、および 9。
* CentOS バージョン 5、6、および 7。
* Red Hat Enterprise Linux (RHEL) バージョン 5、6、および 7。
* SUSE Linux Enterprise Server (SLES) バージョン 11 および 12。
* Oracle Linux バージョン 5、6、および 7。
* Amazon Linux 2012.09 から 2017。
* Openssl 1.1.0 は、x86_64 プラットフォーム (64 ビット) でのみサポートされます。

## <a name="vms-and-cloud-services"></a>VM とクラウド サービス
クラウド サービスで実行する VM もサポートされます。 監視されるのは、運用スロットで実行するクラウド サービスの Web ロールと worker ロールだけです。 Cloud Services の詳細については、「[Azure Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)」をご覧ください。


## <a name="supported-iaas-features"></a>サポートされている IaaS 機能

> [!div class="mx-tableFixed"]
> 

|サーバー|Windows||Linux||
|----|----|----|----|----|
|環境|Azure|Azure 以外|Azure|Azure 以外|
|VMBA 脅威検出アラート|✔|✔|✔ (サポートされているバージョン)|✔|
|ネットワーク経由の脅威検出アラート|✔|X|✔|X|
|Windows Defender ATP の統合|✔ (サポートされているバージョン)|✔|X|X|
|不足しているパッチ|✔|✔|✔|✔|
|セキュリティ構成|✔|✔|✔|✔|
|エンドポイント保護|✔|✔|X|X|
|JIT VM アクセス|✔|X|✔|X|
|アダプティブ アプリケーション制御|✔|X|X|X|
|FIM|✔|✔|✔|✔|
|ディスクの暗号化|✔|X|✔|X|
|サード パーティ デプロイ|✔|X|✔|X|
|NSG|✔|X|✔|X|
|ファイルレス脅威の検出|✔|✔|X|X|
|ネットワーク マップ|✔|X|✔|X|
|アダプティブ ネットワーク制御|✔|X|✔|X|


### <a name="supported-endpoint-protection-solutions"></a>サポートされているエンドポイント保護ソリューション

次の表に、次の項目のマトリックスを示します。
 - Azure Security Center を使用して、各ソリューションをインストールできるかどうか。
 - Security Center で検出できるエンドポイント保護ソリューションはどれか。 これらのエンドポイント保護ソリューションのいずれかが検出された場合、Security Center で 1 つをインストールすることが推奨されることはありません。

| エンドポイント保護| プラットフォーム | Security Center のインストール | Security Center の検出 |
|------|------|-----|-----|
| Windows Defender (Microsoft Antimalware)| Windows Server 2016| いいえ、OS に組み込まれている| はい |
| System Center Endpoint Protection (Microsoft Antimalware) | Windows Server 2012 R2、2012、2008 R2 (下記の注を参照) | 拡張機能を介して | はい |
| Trend Micro – すべてのバージョン | Windows Server ファミリ  | いいえ  | はい |
| Symantec v12.1.1100 以降| Windows Server ファミリ  | いいえ  | はい |
| McAfee v10 以降 | Windows Server ファミリ  | いいえ  | はい |
| Kaspersky| Windows Server ファミリ  | いいえ  | いいえ   |
| Sophos| Windows Server ファミリ  | いいえ  | いいえ   |

> [!NOTE]
> - Windows Server 2008 R2 の仮想マシンで System Center Endpoint Protection (SCEP) が検出されるには、PowerShell 3.0 (またはそれ以降) の後で SCEP をインストールする必要があります。
>
>

## <a name="supported-paas-features"></a>サポートされている PaaS 機能 


|Service|Recommendations|脅威の検出|
|----|----|----|
|SQL|✔| ✔|
|PostGreSQL*|✔| ✔|
|MySQL*|✔| ✔|
|Azure BLOB ストレージ アカウント*|✔| ✔|
|App Services|✔| ✔|
|Cloud Services|✔| X|
|VNet|✔| NA|
|サブネット|✔| NA|
|NIC|✔| ✔|
|NSG|✔| NA|
|サブスクリプション|✔| ✔|

\* 現在、これらの機能はパブリック プレビューでサポートされています。 



## <a name="next-steps"></a>次の手順

- [Azure Security Center を導入するための設計上の考慮事項を計画し、理解する](security-center-planning-and-operations-guide.md)方法について説明しています。
- [Security Center における仮想マシンの動作分析とクラッシュ ダンプ メモリ分析](security-center-alerts-type.md#virtual-machine-behavioral-analysis)について説明します。
- [Azure Security Center の使用に関してよく寄せられる質問](security-center-faq.md)が記載されています。
- [Azure のセキュリティとコンプライアンスについてのブログ記事](https://blogs.msdn.com/b/azuresecurity/)を確認できます。

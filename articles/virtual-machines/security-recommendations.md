---
title: Azure の仮想マシンについてのセキュリティに関する推奨事項
description: Azure の VM にこれらの推奨事項を適用することにより、共同責任モデルに記載されたセキュリティに関する義務を果たすことができ、デプロイの全体的なセキュリティが向上します。
author: msmbaldwin
manager: RKarlin
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.reviewer: cynthn
ms.openlocfilehash: 622ebfed8294dbcb441aa509fc4f6ba75114f28d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499358"
---
# <a name="security-recommendations-for-virtual-machines-in-azure"></a>Azure の仮想マシンについてのセキュリティに関する推奨事項


この記事には、Azure Virtual Machines のセキュリティに関する推奨事項が含まれています。 次の推奨事項に従うことで、このモデルで説明されている共有責任のセキュリティ義務を果たすことができます。 これらの推奨事項は、Web アプリ ソリューションの全体的なセキュリティの向上にも役立ちます。 サービス プロバイダーとしての責任を果たすための Microsoft の取り組みの詳細については、「[クラウド コンピューティングについての共有責任](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)」を参照してください。

この記事のいくつかの推奨事項は、Azure Security Center で自動的に対応できます。 Azure Security Center は、Azure にあるリソースの防御の最前線です。 これにより Azure リソースのセキュリティの状態が定期的に分析され、潜在的なセキュリティ脆弱性が特定されます。 その後、それらの脆弱性に対応する方法が推奨されます。 詳細については、「[Azure Security Center でのセキュリティの推奨事項](../security-center/security-center-recommendations.md)」を参照してください。

Azure Security Center の一般的な情報については、「[Azure Security Center とは](../security-center/security-center-introduction.md)」を参照してください。

## <a name="general"></a>全般

| 推奨 | 説明 | Security Center |
|-|----|--|
| カスタム VM イメージを作成する場合、最新の更新プログラムを適用する。 | イメージを作成する前に、オペレーティング システムと、イメージの一部となるすべてのアプリケーションの最新の更新プログラムをインストールしてください。  | - |
| VM を最新の状態に保つ。 | Azure Automation の [Update Management](../automation/update-management/overview.md) ソリューションを使用すると、Azure 内の Windows コンピューターと Linux コンピューターに対するオペレーティング システムの更新プログラムを管理できます。 | [はい](../security-center/asset-inventory.md) |
| VM をバックアップする。 | [Azure Backup](../backup/backup-overview.md) を使用すると、アプリケーション データを保護し、運用コストを最小限にすることができます。 アプリケーション エラーが発生するとデータが破損するおそれがあり、ヒューマン エラーが生じればアプリケーションにバグが生まれる危険があります。 Azure Backup は、Windows と Linux を実行する VM を保護します。 | - |
| 復元性と可用性を高めるために複数の VM を使用する。 | 高可用性を必要とするアプリケーションが VM で実行されている場合は、複数の VM または[可用性セット](./availability.md)を使用します。 | - |
| 事業継続とディザスター リカバリー (BCDR) 戦略を採用する。 | Azure Site Recovery では、事業継続性をサポートするように設計されたさまざまなオプションから選択することができます。 さまざまなレプリケーションおよびフェールオーバーのシナリオがサポートされています。 詳しくは、「[Site Recovery について](../site-recovery/site-recovery-overview.md)」をご覧ください。 | - |

## <a name="data-security"></a>データのセキュリティ

| 推奨 | 説明 | Security Center |
|-|----|--|
| オペレーティング システム ディスクを暗号化する。 | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) を使用すると、Windows および Linux の IaaS VM ディスクを暗号化できます。 必要なキーがないと、暗号化されたディスクの内容は読み取れません。 ディスクの暗号化は、ディスクがコピーされた場合に発生する可能性のある不正アクセスから、格納されているデータを保護します。| [はい](../security-center/asset-inventory.md) |
| データ ディスクを暗号化する。 | [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) を使用すると、Windows および Linux の IaaS VM ディスクを暗号化できます。 必要なキーがないと、暗号化されたディスクの内容は読み取れません。 ディスクの暗号化は、ディスクがコピーされた場合に発生する可能性のある不正アクセスから、格納されているデータを保護します。| -  |
| インストール済みソフトウェアを制限する。 | インストール済みソフトウェアを、ソリューションを正常に適用するために必要なものに制限します。 このガイドラインにより、ソリューションの攻撃対象領域を減らすことができます。 | - |
| ウイルス対策またはマルウェア対策を使用する。 | Azure では、Microsoft、Symantec、Trend Micro、Kaspersky などのセキュリティ ベンダーのマルウェア対策ソフトウェアを使用できます。 このソフトウェアは、悪意のあるファイル、アドウェア、他の脅威から、お使いの VM を保護するのに役立ちます。 アプリケーションのワークロードに基づいて Microsoft Antimalware をデプロイできます。 Microsoft Antimalware は、Windows マシンでのみ使用できます。 基本的な既定のセキュリティまたはカスタム構成のいずれかを使用します。 詳細については、「[Azure Cloud Services および Virtual Machines 向け Microsoft Antimalware](../security/fundamentals/antimalware.md)」を参照してください。 | - |
| キーとシークレットを安全に格納する。 | セキュリティで保護された一元管理オプションをアプリケーション所有者に提供することにより、シークレットとキーの管理を簡素化します。 この管理によって、偶発的な侵害や漏洩のリスクが軽減されます。 Azure Key Vault では、FIPS 140-2 レベル 2 に認定されたハードウェア セキュリティ モジュール (HSM) にキーを安全に格納できます。 FIPS 140-2 レベル 3 を使用してキーとシークレットを格納する必要がある場合は、[Azure Dedicated HSM](../dedicated-hsm/overview.md) を使用できます。 | - |

## <a name="identity-and-access-management"></a>ID 管理とアクセス管理 

| 推奨 | 説明 | Security Center |
|-|----|--|
| VM 認証を一元化する。 | [Azure Active Directory 認証](../active-directory/develop/authentication-vs-authorization.md)を使用することにより、Windows と Linux の VM の認証を一元化できます。 | - |

## <a name="monitoring"></a>監視

| 推奨 | 説明 | Security Center |
|-|----|--|
| VM を監視する。 | [Azure Monitor for VMs](../azure-monitor/vm/vminsights-overview.md) を使用して、ご利用の Azure VM および仮想マシン スケール セットを監視できます。 VM のパフォーマンスの問題はサービス中断に発展する場合があり、そうなれば可用性というセキュリティの原則を損ないます。 | - |

## <a name="networking"></a>ネットワーク

| 推奨 | 説明 | Security Center |
|-|----|--|
| 管理ポートへのアクセスを制限する。 | 攻撃者は、パブリック クラウドの IP 範囲をスキャンして、開いている管理ポートを探し、よく使用されるパスワードや、修正プログラムが適用されていない既知の脆弱性などの "簡単な" 攻撃を試みます。 [Just-In-Time (JIT) VM アクセス](../security-center/security-center-just-in-time.md)を使用すると、Azure VM への受信トラフィックをロックダウンできます。これにより、必要なときに VM への容易な接続を提供しながら、攻撃に対する露出を減らすことができます。 | - |
| ネットワーク アクセスを制限する。 | ネットワーク セキュリティ グループを使用すると、ネットワーク アクセスを制限し、公開するエンドポイントの数を制御できます。 詳細については、「[ネットワーク セキュリティ グループを作成、変更、削除](../virtual-network/manage-network-security-group.md)」をご覧ください。 | - |

## <a name="next-steps"></a>次のステップ

追加のセキュリティ要件については、アプリケーション プロバイダーに確認してください。 セキュリティで保護されたアプリケーションの開発の詳細については、[セキュリティで保護された開発に関するドキュメント](https://azure.microsoft.com/resources/develop-secure-applications-on-azure/)を参照してください。
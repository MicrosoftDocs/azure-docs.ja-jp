---
title: "Azure Security Center での仮想マシンの保護 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center での推奨事項に従ってご使用の仮想マシンを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/07/2017
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c048263ab1ae453a10f0471ae33a122e21151d94


---
# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Azure Security Center での仮想マシンの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、およびアプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、VM に適用される推奨事項について説明します。  VM に関する推奨事項は、データの収集、システム更新プログラムの適用、マルウェア対策のプロビジョニング、VM ディスクの暗号化などが中心です。  次の表を参考にすると、VM に関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## <a name="available-vm-recommendations"></a>VM に関する利用可能な推奨事項
| 推奨 | Description |
| --- | --- |
| [サブスクリプションのデータ収集の有効化](security-center-enable-data-collection.md) |各サブスクリプションおよびサブスクリプションのすべての仮想マシン (VM) に対して、セキュリティ ポリシーでデータ収集を有効にすることをお勧めします。 |
| [OS の脆弱性の修復](security-center-remediate-os-vulnerabilities.md) |OS の構成を推奨される構成規則 (パスワードの保存を許可しないなど) に合わせることをお勧めします。 |
| [システムの更新の適用](security-center-apply-system-updates.md) |システムの不足しているセキュリティ更新プログラムおよび重要な更新プログラムを VM にデプロイすることをお勧めします。 |
| [システムの更新後に再起動する](security-center-apply-system-updates.md#reboot-after-system-updates) |VM を再起動してシステムの更新プログラムの適用プロセスを完了するよう推奨します。 |
| [Endpoint Protection をインストールします](security-center-install-endpoint-protection.md) |マルウェア対策プログラムを VM (Windows VM のみ) にプロビジョニングすることをお勧めします。 |
| [Endpoint Protection の正常性アラートの解決](security-center-resolve-endpoint-protection-health-alerts.md) |Endpoint Protection のエラーを解決することをお勧めします。 |
| [VM エージェントの有効化](security-center-enable-vm-agent.md) |VM エージェントを必要とする VM を確認できます。 パッチのスキャン、基準のスキャン、およびマルウェア対策プログラムをプロビジョニングするには、VM 上に VM エージェントをインストールする必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 「 [VM エージェントと拡張機能 – パート 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 」の記事には、VM エージェントのインストール方法が記載されています。 |
| [ディスク暗号化の適用](security-center-apply-disk-encryption.md) |Azure Disk Encryption を使用して VM ディスクを暗号化することをお勧めします (Windows VM および Linux VM)。 VM 上の OS とデータ ボリュームの両方を暗号化することをお勧めします。 |
| [OS バージョンの更新](security-center-update-os-version.md) |クラウド サービスのオペレーティング システム (OS) のバージョンを、ご利用の OS ファミリで利用できる最新のバージョンに更新するようお勧めします。  Cloud Services の詳細については、 [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。 |
| [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md) |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| [脆弱性の修復](security-center-vulnerability-assessment-recommendations.md#review-recommendation) |VM にインストールされている脆弱性評価ソリューションによって検出された、システムとアプリケーションの脆弱性を確認できます。 |

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。




<!--HONumber=Nov16_HO3-->



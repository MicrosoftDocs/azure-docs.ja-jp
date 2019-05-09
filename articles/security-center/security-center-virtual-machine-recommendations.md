---
title: Azure Security Center での仮想マシンに関する推奨事項 | Microsoft Docs
description: このドキュメントでは、仮想マシン、コンピューター、Web アプリ、および App Service 環境の保護に役立つ方法に関する、Azure Security Center の推奨事項について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104823"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Azure Security Center リソースの推奨事項を理解する


## <a name="recommendations"></a>Recommendations
次の表を参考にすると、計算とアプリ サービスに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

### <a name="computers"></a>[Computers (コンピューター)]
| 推奨 | 説明 |
| --- | --- |
| [サブスクリプションのデータ収集の有効化](security-center-enable-data-collection.md) |各サブスクリプションおよびサブスクリプションのすべての仮想マシン (VM) に対して、セキュリティ ポリシーでデータ収集を有効にすることをお勧めします。 |
| [Azure Storage アカウント暗号化の有効化](security-center-enable-encryption-for-storage-account.md) | Azure Storage Service Encryption for Data at Rest を有効化することを推奨します。 Storage Service Encryption (SSE) は、データが Azure ストレージに書き込まれたときに暗号化し、取得される前に復号化します。 現在、SSE は Azure Blob service のみに対応し、ブロック BLOB、ページ BLOB、追加 BLOB で使用できます。 詳細については、「[Storage Service Encryption for Data at Rest](../storage/common/storage-service-encryption.md)」を参照してください。</br>SSE は Resource Manager ストレージ アカウントでのみサポートされます。 現在、クラシック ストレージ アカウントはサポートされていません。 クラシック デプロイ モデルと Resource Manager デプロイ モデルについて理解するには、[Azure デプロイ モデル](../azure-classic-rm.md)に関する記事を参照してください。 |
| [セキュリティ構成の修復](security-center-remediate-os-vulnerabilities.md) |OS 構成を推奨されるセキュリティ構成規則 (パスワードの保存を許可しないなど) に合わせることをお勧めします。 |
| [システムの更新の適用](security-center-apply-system-updates.md) |システムの不足しているセキュリティ更新プログラムおよび重要な更新プログラムを VM にデプロイすることをお勧めします。 |
| [Just-In-Time ネットワーク アクセス制御の適用](security-center-just-in-time.md) | ジャスト イン タイム VM アクセスを適用することをお勧めします。 ジャスト イン タイム機能はプレビュー段階であり、Security Center の Standard レベルで利用できます。 Security Center の価格レベルの詳細については、[価格](security-center-pricing.md)に関するページを参照してください。 |
| [システムの更新後に再起動する](security-center-apply-system-updates.md#reboot-after-system-updates) |VM を再起動してシステムの更新プログラムの適用プロセスを完了するよう推奨します。 |
| [Endpoint Protection をインストールします](security-center-install-endpoint-protection.md) |マルウェア対策プログラムを VM (Windows VM のみ) にプロビジョニングすることをお勧めします。 |
| [VM エージェントの有効化](security-center-enable-vm-agent.md) |VM エージェントを必要とする VM を確認できます。 パッチのスキャン、基準のスキャン、およびマルウェア対策プログラムをプロビジョニングするには、VM 上に VM エージェントをインストールする必要があります。 既定では、Azure Marketplace からデプロイされた VM に VM エージェントがインストールされます。 「 [VM エージェントと拡張機能 – パート 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) 」の記事には、VM エージェントのインストール方法が記載されています。 |
| [ディスク暗号化の適用](security-center-apply-disk-encryption.md) |Azure Disk Encryption を使用して VM ディスクを暗号化することをお勧めします (Windows VM および Linux VM)。 VM 上の OS とデータ ボリュームの両方を暗号化することをお勧めします。 |
| [OS バージョンの更新](security-center-update-os-version.md) |クラウド サービスのオペレーティング システム (OS) のバージョンを、ご利用の OS ファミリで利用できる最新のバージョンに更新するようお勧めします。  Cloud Services の詳細については、 [Cloud Services の概要](../cloud-services/cloud-services-choose-me.md)に関するページをご覧ください。 |
| [脆弱性評価がインストールされていません](security-center-vulnerability-assessment-recommendations.md) |VM に脆弱性評価ソリューションをインストールすることをお勧めします。 |
| [脆弱性の修復](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |VM にインストールされている脆弱性評価ソリューションによって検出された、システムとアプリケーションの脆弱性を確認できます。 |

### App Service<a name="app-services"></a>
| 推奨 | 説明 |
| --- | --- |
| App Service には HTTPS 経由でのみアクセスできるようにする | App Service へのアクセスを HTTPS 経由のみに制限することをお勧めします。 |
| Web アプリケーションで Web ソケットを無効にする| Web アプリケーション内での Web ソケットの使用を慎重に見直すことをお勧めします。  Web ソケット プロトコルは、さまざまな種類のセキュリティの脅威に対して脆弱です。 |
| Web アプリケーションにカスタム ドメインを使用する | カスタム ドメインを使用して、フィッシングや他の DNS 関連攻撃などの一般的な攻撃から Web アプリケーションを保護することをお勧めします。 |
| Web アプリケーションに対する IP 制限を構成する | アプリケーションへのアクセスを許可されている IP アドレスの一覧を定義することをお勧めします。  IP 制限を使用することで、一般的な攻撃から Web アプリケーションを保護します。 |
| すべての ('*') リソースにアプリケーションへのアクセスを許可しない | WEBSITE_LOAD_CERTIFICATES パラメーターを '*' に設定しないことをお勧めします。パラメーターを '*' に設定することは、すべての証明書が Web アプリケーションの個人証明書ストアに読み込まれることを意味します。  実行時にサイトがすべての証明書へのアクセスを必要とすることは考えにくいため、これでは、最小限の権限という原理をうまく活用できないことになります。 |
| すべてのリソースがアプリケーションにアクセスすることを CORS で許可しない | Web アプリケーションの操作に必要なドメインのみを許可することをお勧めします。 クロス オリジン リソース共有 (CORS) で、すべてのドメインに Web アプリケーションへのアクセスを許可してはいけません。 |
| Web アプリケーションでサポートされている最新の .NET Framework を使用する | 最新のセキュリティ クラスには、最新の .NET Framework のバージョンを使用することをお勧めします。 古いクラスや型を使用すると、アプリケーションが脆弱になる可能性があります。 |
| Web アプリケーションでサポートされている最新の Java バージョンを使用する | 最新のセキュリティ クラスには、最新の Java バージョンを使用することをお勧めします。 古いクラスや型を使用すると、アプリケーションが脆弱になる可能性があります。 |
| Web アプリケーションでサポートされている最新の PHP バージョンを使用する | 最新のセキュリティ クラスには、最新の PHP バージョンを使用することをお勧めします。 古いクラスや型を使用すると、アプリケーションが脆弱になる可能性があります。 |
| [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md) |Web エンドポイントに Web アプリケーション ファイアウォール (WAF) をデプロイすることをお勧めします。 WAF の推奨事項は、開いている受信 Web ポート (80,443) にネットワーク セキュリティ グループが関連付けられている公開 IP (インスタンス レベルの IP または負荷分散された IP) に対して表示されます。</br></br>Security Center では、仮想マシン上および App Service 環境 (ASE) の Web アプリケーションを対象とする攻撃から保護するために WAF をプロビジョニングするよう勧めます。 App Service 環境 (ASE) は、Azure App Service アプリを安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の [Premium](https://azure.microsoft.com/pricing/details/app-service/) サービス プラン オプションです。 ASE の詳細については、 [App Service 環境のドキュメント](../app-service/environment/intro.md)をご覧ください。</br></br>セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。 |
| [アプリケーション保護を完了する](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF の構成を完了するには、WAF アプライアンスにトラフィックを再ルーティングする必要があります。 この推奨事項に従うと、必要なセットアップの変更が完了します。 |
| Web アプリケーションでサポートされている最新の Node.js バージョンを使用する | 最新のセキュリティ クラスには、最新の Node.js バージョンを使用することをお勧めします。 古いクラスや型を使用すると、アプリケーションが脆弱になる可能性があります。 |
| すべてのリソースが Function App にアクセスすることを CORS で許可しない | Web アプリケーションの操作に必要なドメインのみを許可することをお勧めします。 クロス オリジン リソース共有 (CORS) で、すべてのドメインに Function Application へのアクセスを許可してはいけません。 |
| Function App にカスタム ドメインを使用する | カスタム ドメインを使用して、フィッシングや他の DNS 関連攻撃などの一般的な攻撃から Function App を保護することをお勧めします。 |
| Function App に対する IP 制限を構成する | アプリケーションへのアクセスを許可されている IP アドレスの一覧を定義することをお勧めします。 IP 制限を使用することで、一般的な攻撃から Function App を保護します。 |
| Function App には HTTPS 経由でのみアクセスできるようにする | Function App へのアクセスを HTTPS 経由のみに制限することをお勧めします。 |
| Function App でリモート デバッグを無効にする | 使用する必要がなくなった場合は、Function App のデバッグを無効にすることをお勧めします。 リモート デバッグを実行するには、受信ポートが Function App 上で開かれている必要があります。 |
| Function App で Web ソケットを無効にする | Function App 内での Web ソケットの使用を慎重に見直すことをお勧めします。 Web ソケット プロトコルは、さまざまな種類のセキュリティの脅威に対して脆弱です。 |


## <a name="next-steps"></a>次の手順
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での ID とアクセスの監視](security-center-identity-access.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのマシンとアプリケーションの保護](security-center-virtual-machine-protection.md)
* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png

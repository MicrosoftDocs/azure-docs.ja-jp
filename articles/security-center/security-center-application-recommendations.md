---
title: "Azure Security Center でのアプリケーションの保護 | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center での推奨事項に従ってご使用の Azure アプリケーションを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 2286437f4ab13384f895e906ccda48ac1b4c553d
ms.openlocfilehash: dfc7d14b95082842ba658bd94b15c8191ee5dca3


---
# <a name="protecting-your-applications-in-azure-security-center"></a>Azure Security Center でのアプリケーションの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、およびアプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、アプリケーションに適用される推奨事項について説明します。  アプリケーションに関する推奨事項は、Web アプリケーション ファイアウォールのデプロイが中心です。  次の表を参考にすると、アプリケーションに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## <a name="available-application-recommendations"></a>アプリケーションに関する利用可能な推奨事項
| 推奨 | Description |
| --- | --- |
| [Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md) |Web エンドポイントに Web アプリケーション ファイアウォール (WAF) をデプロイすることをお勧めします。 WAF の推奨事項は、開いている受信 Web ポート (80,443) にネットワーク セキュリティ グループが関連付けられている公開 IP (インスタンス レベルの IP または負荷分散された IP) に対して表示されます。</br></br>Security Center では、仮想マシン上および App Service 環境 (ASE) の Web アプリケーションを対象とする攻撃から保護するために WAF をプロビジョニングするよう勧めます。 App Service 環境 (ASE) は、Azure App Service アプリを安全に実行するために完全に分離された専用の環境を提供する、Azure App Service の [Premium](https://azure.microsoft.com/pricing/details/app-service/) サービス プラン オプションです。 ASE の詳細については、 [App Service 環境のドキュメント](../app-service/app-service-app-service-environments-readme.md)をご覧ください。</br></br>セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。 |
| [アプリケーション保護を完了する](security-center-add-web-application-firewall.md#finalize-application-protection) |WAF の構成を完了するには、WAF アプライアンスにトラフィックを再ルーティングする必要があります。 この推奨事項に従うと、必要なセットアップの変更が完了します。 |

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
* [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。



<!--HONumber=Dec16_HO1-->



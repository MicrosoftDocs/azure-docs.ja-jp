---
title: Azure Security Center のコンテナーのセキュリティ | Microsoft Docs
description: Azure Security Center のコンテナーのセキュリティ機能について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800607"
---
# <a name="container-security-in-security-center"></a>Security Center のコンテナーのセキュリティ

Azure Security Center は、コンテナーをセキュリティ保護するための Azure ネイティブ ソリューションです。 Security Center では、次のコンテナー リソースの種類を保護できます。



|リソース |名前  |詳細  |
|:---------:|---------|---------|
|![コンテナー ホスト](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|コンテナー ホスト (Docker を実行している仮想マシン)|Security Center は、Docker 構成をスキャンし、評価されたすべての失敗したルールの一覧を提供することによって、構成の誤りに対する可視性を提供します。 これらの問題を迅速に解決して時間を節約するのに役立つガイドラインが Security Center から提供されます。 Security Center は、Docker 構成を継続的に評価し、それらの最新の状態をユーザーに提供します。|
|![Kubernetes サービス](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Azure Kubernetes Service (AKS) クラスター|Standard レベル ユーザー向けの [Security Center のオプションの AKS バンドル](azure-kubernetes-service-integration.md)を使用すると、AKS ノード、クラウド トラフィック、およびセキュリティ制御に対する可視性が高まります。|
|![コンテナー レジストリ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Azure Container Registry (ACR) レジストリ|Standard レベル ユーザー向けの [Security Center のオプションの ACR バンドル](azure-kubernetes-service-integration.md)を使用すると、ARM ベースの ACR レジストリ内のイメージの脆弱性に対する可視性が高まります。|
||||


この記事では、これらのバンドルを使用して、コンテナーとそのアプリのセキュリティを向上、監視、および管理する方法について説明します。 ここでは、コンテナーのセキュリティのこうした主要な側面について、Security Center がどのように役立つかを説明します。

- [脆弱性管理 - コンテナー イメージのスキャン](#vulnerability-management---scanning-container-images)
- [環境の強化 - Docker の構成と Kubernetes クラスターの継続的な監視](#environment-hardening)
- [実行時保護 - リアルタイムの脅威検出](#run-time-protection---real-time-threat-detection)

[![Azure Security Center のコンテナーのセキュリティ タブ](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

これらの機能を使用する方法については、「[コンテナーのセキュリティを監視する](monitor-container-security.md)」を参照してください。

## <a name="vulnerability-management---scanning-container-images"></a>脆弱性管理 - コンテナー イメージのスキャン
ARM ベースの Azure コンテナー レジストリを監視するには、Security Center の Standard レベルになっていることを確認してください ([価格](/azure/security-center/security-center-pricing)に関するページを参照してください)。 次に、オプションのコンテナー レジストリ バンドルを有効にします。 新しいイメージがプッシュされると、Security Center では、業界最高レベルの脆弱性スキャン ベンダーである Qualys のスキャナーを使用してイメージがスキャンされます。

Qualys または Security Center によって問題が見つかった場合は、Security Center ダッシュボードで通知されます。 Security Center には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、および問題の修正方法に関するガイダンスが表示されます。 コンテナーの Security Center の推奨事項の詳細については、[推奨事項の参照リスト](recommendations-reference.md#recs-containers)を参照してください。

Security Center では、スキャナーによる検出結果がフィルター処理および分類されます。 イメージが正常な場合、Security Center ではそのように示されます。 Security Center では、解決の必要な問題があるイメージに対してのみ、セキュリティに関する推奨事項が生成されます。 問題があるときにだけ通知することにより、Security Center では不要な情報アラートの可能性が減ります。

## <a name="environment-hardening"></a>環境のセキュリティ強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 構成の継続的な監視
Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 不適切な構成が検出されると、Security Center によってセキュリティの推奨事項が生成されます。 **推奨事項ページ**を使用すると、推奨事項を表示したり、問題を修復したりすることができます。 また、Docker でデプロイされたすべての仮想マシンが表示される **[コンテナー]** タブにも推奨事項が表示されます。 

この機能には関連する Security Center 推奨事項が表示されることがありますが、その詳細については、推奨事項参照テーブルの[コンテナー セクション](recommendations-reference.md#recs-containers)をご覧ください。

VM のセキュリティ問題を調査している場合、Security Center にはマシン上のコンテナーに関する追加情報が表示されます。 こうした情報には、Docker のバージョンとホストで実行されているイメージの数が含まれます。 

>[!NOTE]
> これらの CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes クラスターの継続的な監視
Security Center は Azure Kubernetes Service (AKS) と連携して機能します。これは、コンテナー化されたアプリケーションを開発、デプロイ、および管理するための Microsoft のマネージド コンテナー オーケストレーション サービスです。

AKS には、クラスターのセキュリティ体制をセキュリティで制御し、把握する機能があります。 Security Center では、次の機能を使用します。
* AKS クラスターの構成を常に監視する
* 業界標準に合わせてセキュリティに関する推奨事項を生成する

この機能には関連する Security Center 推奨事項が表示されることがありますが、その詳細については、推奨事項参照テーブルの[コンテナー セクション](recommendations-reference.md#recs-containers)をご覧ください。

## <a name="run-time-protection---real-time-threat-detection"></a>実行時保護 - リアルタイムの脅威検出

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>次のステップ

この概要では、Azure Security Center でのコンテナー セキュリティの中核となる要素について説明しました。 [コンテナーのセキュリティを監視する方法](monitor-container-security.md)に進んでください。
> [!div class="nextstepaction"]
> [コンテナーのセキュリティの監視](monitor-container-security.md)
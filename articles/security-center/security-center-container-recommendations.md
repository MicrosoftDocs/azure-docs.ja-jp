---
title: Azure Security Center でのコンテナーに関する推奨事項 | Microsoft Docs
description: このドキュメントでは、コンテナーを保護する方法に関する Azure Security Center の推奨事項について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202396"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Azure Security Center のコンテナーの推奨事項を理解する

モノリシック アプリケーションを移行して、ミッション クリティカルなコンテナー化されたクラウド ネイティブ アプリケーションを運用環境で実行すると、簡単かつ迅速なデプロイや更新など、コンテナーの機能を利用できます。 デプロイするコンテナーの数が増え続けるのに応じて、コンテナーのセキュリティ状態に対する可視性を提供し、コンテナーを脅威から保護するための支援策として、セキュリティ ソリューションを導入する必要があります。

Azure Security Center では、コンテナーを保護するのに役立つ次の機能を提供します。

- **IaaS Linux マシンでホストされているコンテナーの可視性**<br>Azure Security Center の [コンテナー] タブには、Docker を使用してデプロイされたすべての仮想マシンが表示されます。 仮想マシンのセキュリティの問題を調査する際に、Docker のバージョンやホスト上で実行されているイメージの数など、マシン上のコンテナーに関連する追加情報が Security Center から提供されます。

    ![[コンテナー] タブ](./media/security-center-container-recommendations/docker-recommendation.png)


- **Docker の CIS ベンチマークに基づくセキュリティに関する推奨事項**<br>Security Center は、Docker 構成をスキャンし、評価されたすべての失敗したルールの一覧を提供することによって、構成の誤りに対する可視性を提供します。 これらの問題を迅速に解決して時間を節約するのに役立つガイドラインが Security Center から提供されます。 Security Center は、Docker 構成を継続的に評価し、それらの最新の状態をユーザーに提供します。

    ![[コンテナー] タブ](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **コンテナーに対する脅威のリアルタイムの検出**<br> Security Center は、AuditD コンポーネントを含む Linux マシン上のコンテナーに対してリアルタイムの脅威検出を提供します。 アラートは、ホスト上での特権を持つコンテナーの作成、Secure Shell (SSH) サーバーが Docker コンテナー内で実行されているしるし、暗号マイナーの使用など、いくつかの疑わしい Docker アクティビティを識別します。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

    ![[コンテナー] タブ](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Recommendations
IaaS Linux マシン上でホストされている利用可能なコンテナーや Docker 構成のセキュリティ評価を理解するために、以下の表を参考にしてください。

| 推奨 | 説明 | Remediation |
| --- | --- | --- |
|コンテナーのセキュリティ構成の脆弱性を修復します |構成のベスト プラクティスに基づいて、コンテナーのセキュリティ構成の脆弱性を修復します。| コンテナーのセキュリティ構成の脆弱性を修復するには:<br>1.失敗した規則の一覧を確認します。<br>2.指定された手順に従って、各ルールを修正します。|


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


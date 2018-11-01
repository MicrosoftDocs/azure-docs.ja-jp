---
title: Azure Stack セキュリティ コントロールを理解する
description: サービス管理者として、Azure Stack に適用されるセキュリティ コントロールについて学びます。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/9/2018
ms.author: patricka
ms.openlocfilehash: 32c268c1e4a0ff4d17c5b03f0ffd33b0ddf5b927
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901483"
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure Stack インフラストラクチャのセキュリティ体制

*適用対象: Azure Stack 統合システム*

セキュリティに関する考慮事項と法令遵守規定は、ハイブリッド クラウドを使用する際の中核となる推進力です。 Azure Stack は、これらのシナリオに適しています。 この記事では、Azure Stack 用のセキュリティ コントロールについて説明します。

Azure Stack には、2 つのセキュリティ体制レイヤーが共存しています。 1 つ目のレイヤーは Azure Stack インフラストラクチャであり、Azure Resource Manager へのハードウェア コンポーネントを含みます。 1 つ目のレイヤーには、管理者ポータルおよびテナント ポータルが含まれています。 2 つ目のレイヤーは、テナントによって作成、デプロイ、および管理されるワークロードで構成されています。 2 つ目のレイヤーには、仮想マシンおよび App Services の Web サイトなどの項目が含まれています。

## <a name="security-approach"></a>セキュリティ手法

Azure Stack のセキュリティ手法は、最新の脅威から防御するために設計され、主要なコンプライアンス標準の要件を満たすように構築されています。 その結果、Azure Stack インフラストラクチャのセキュリティ体制が、次の 2 本の柱で構成されます。

 - **セキュリティ侵害の想定**  
システムが既に侵害されているものと想定して、"*侵害の影響の検出と抑制*" および攻撃の防止に重点を置きます。 
 - **既定でのセキュリティ機能の組込み**  
適切に定義されたハードウェアとソフトウェア上でインフラストラクチャが実行されているため、Azure Stack では既定で*すべてのセキュリティ機能を有効にして、構成および検証*しています。

Azure Stack が統合システムとして配布されるため、、Azure Stack インフラストラクチャのセキュリティ体制は Microsoft によって定義されます。 Azure と同様に、テナントは、テナント ワークロードのセキュリティ体制を定義する責任を負います。 このドキュメントでは、Azure Stack インフラストラクチャのセキュリティ体制に関する基本的知識について説明します。

## <a name="data-at-rest-encryption"></a>保存データの暗号化
すべての Azure Stack インフラストラクチャとテナントのデータは、保存時に Bitlocker を使用して暗号化されます。 この暗号化は、Azure Stack の ストレージ コンポーネントの物理的損失や盗難から保護します。 

## <a name="data-in-transit-encryption"></a>転送中データの暗号化
Azure Stack インフラストラクチャのコンポーネントは、TLS 1.2 で暗号化されたチャネルを使用して通信します。 暗号化証明書は、インフラストラクチャが自己管理します。 

REST エンドポイントや Azure Stack ポータルなど、すべての外部インフラストラクチャ エンドポイントは、セキュリティで保護された通信用の TLS 1.2 をサポートします。 これらのエンドポイントには、サード パーティまたはエンタープライズ証明機関のいずれかが発行する暗号化証明書を指定する必要があります。 

自己署名証明書は、これらの外部エンドポイントに使用できますが、Microsoft では、自己署名証明書の使用を控えるよう強く推奨します。 

## <a name="secret-management"></a>シークレットの管理
Azure Stack インフラストラクチャは、パスワードなど、さまざまなシークレット情報を使用して機能します。 これらのほとんどは、24 時間ごとに入れ換えるグループ管理サービス アカウントであるため、頻繁に自動的に入れ換えが行われます。

グループ管理サービス アカウントではない残りのシークレットは、特権エンドポイントでスクリプトを使用して手動で入れ換えることができます。

## <a name="code-integrity"></a>コードの整合性
Azure Stack スタックは、最新の Windows Server 2016 セキュリティ機能を使用します。 これらの 1 つに Windows Defender Device Guard があります。この機能は、アプリケーション ホワイトリスト登録を提供し、Azure Stack インフラストラクチャ内では、承認済みのコードだけが実行されることを保証します。 

承認済みのコードは Microsoft または OEM パートナーのいずれかによって署名されます。 署名され認証されたコードは、Microsoft が定義したポリシーで指定されている認定ソフトウェアのリストに含まれています。 つまり、Azure Stack インフラストラクチャでの実行が承認されているソフトウェアのみを実行することができます。 未承認のコードを実行しようとしてもブロックされ、監査が生成されます。

Device Guard ポリシーは、Azure Stack インフラストラクチャでサード パーティ製のエージェントまたはソフトウェアを実行することを禁止しています。

## <a name="credential-guard"></a>資格情報の保護
Azure Stack の別の Windows Server 2016 セキュリティ機能として Windows Defender Credential Guard があります。この機能は、Azure Stack インフラストラクチャの資格情報を Pass-the-Hash および Pass-the-Ticket 攻撃から防護するために使用されます。

## <a name="antimalware"></a>マルウェア対策
Azure Stack (HYPER-V ホストと Virtual Machines の両方) のすべてのコンポーネントは、Windows Defender Antivirus によって保護されています。

接続されているシナリオでは、ウイルス対策の定義とエンジンの更新プログラムが、1 日に複数回適用されます。 接続されていないシナリオでは、マルウェア対策の更新プログラムが、月次の Azure Stack 更新プログラムの一部として適用されます。 詳細については、「[Azure Stack 上で Windows Defender ウイルス対策を更新する](azure-stack-security-av.md)」を参照してください。

## <a name="constrained-administration-model"></a>制約付き管理モデル
Azure Stack 内の管理は、それぞれが特定の目的をもつ、次の 3 つのエントリ ポイントを使用して制御されます。 
1. [管理者ポータル](azure-stack-manage-portals.md)は、日常の管理操作にポイントアンドクリック エクスペリエンスを提供します。
2. Azure Resource Manager は、PowerShell および Azure CLI で使用される、REST API を介した Administrator Portal のすべての管理操作を公開します。 
3. データ センターの統合やサポート シナリオなど、特定の低レベルの操作では、Azure Stack は、[特権エンドポイント](azure-stack-privileged-endpoint.md)と呼ばれる PowerShell エンドポイントを公開します。 このエンドポイントは、コマンドレットのホワイトリストのセットのみを公開し、これは厳重に監査されます。

## <a name="network-controls"></a>ネットワーク制御
Azure Stack インフラストラクチャには、ネットワーク アクセス制御リスト (ACL) の複数のレイヤーが付属しています。 ACL は、インフラストラクチャ コンポーネントへの不正アクセスを防止し、インフラストラクチャがその機能を果たすために必要なパスとだけ通信するように制限します。 

ネットワークの ACL には、次の 3 つのレイヤーが適用されます。
1.  ラック スイッチの最上部
2.  ソフトウェア定義ネットワーク
3.  ホストおよび VM のオペレーティング システムのファイアウォール

## <a name="regulatory-compliance"></a>規制に対するコンプライアンス

Azure Stack はサード パーティの独立した監査法人による正式な評価を受けています。 その結果、Azure Stack インフラストラクチャが、いくつかの主要なコンプライアンス標準からの、該当するコントロールを満たす方法を示した文書を入手できます。 このドキュメントは、複数の担当者に関連する、およびプロセスに関連するコントロールを含む標準により、Azure Stack の認定資格ではありません。 代わりに、お客様は、このドキュメントを利用して、認定プロセスをすぐに開始できます。

評価は次のような標準が含まれます。

- [PCI DSS](https://www.pcisecuritystandards.org/pci_security/) ペイメント カード業界に対応します。
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) は、FedRAMP Moderate、ISO27001、HIPAA、HITRUST、ITAR、NIST SP800-53 などの複数の標準間での包括的なマッピングです。
- 政府顧客向け [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/)。

このコンプライアンス ドキュメントは [Microsoft Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/Blueprint) で確認できます。 コンプライアンス ガイドは、保護されたリソースであり、Azure クラウド サービスの資格情報でサインインする必要があります。

## <a name="next-steps"></a>次の手順

- [Azure Stack でシークレットをローテーションする方法を確認する](azure-stack-rotate-secrets.md)
- [Azure Stack 用の PCI-DSS および CSA-CCM ドキュメント ](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Azure Stack 用の DoD および NIST ドキュメント](https://servicetrust.microsoft.com/ViewPage/Blueprint)

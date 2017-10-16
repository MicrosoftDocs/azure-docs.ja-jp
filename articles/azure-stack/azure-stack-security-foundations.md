---
title: "Azure Stack セキュリティ コントロールを理解する | Microsoft Docs"
description: "サービス管理者として、Azure Stack に適用されるセキュリティ コントロールについて学びます。"
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 106fcf7b0edc095a52e82d58ad48a73084b65d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-infrastructure-security-posture"></a>Azure Stack インフラストラクチャのセキュリティ体制

*適用対象: Azure Stack 統合システム*

セキュリティに関する考慮事項と法令遵守規定は、ハイブリッド クラウドを使用する際の中核となる推進力です。 Azure Stack は、これらのシナリオを実現するために設計されており、Azure Stack の導入時には、既に実行されているコントロールを理解することが重要です。

Azure Stack では、2 つのセキュリティ体制レイヤーが共存しています。 最初のレイヤーは、Azure Resource Manager に至るまでのハードウェア コンポーネント全体からなる Azure Stack のインフラストラクチャで構成され、管理者とテナント ポータルが含まれています。 2 番目のレイヤーは、テナントの作成、配置、および管理を行うワークロードで構成され、仮想マシンまたは App Services の Web サイトなどが含まれています。  

## <a name="security-approach"></a>セキュリティ手法
Azure Stack は、セキュリティ手法を使って最新の脅威から防御するために設計され、主要なコンプライアンス標準の要件を満たすように構築されています。 その結果、Azure Stack インフラストラクチャのセキュリティ体制が、次の 2 本の柱で構成されます。

 - **セキュリティ侵害の想定。** システムが既に侵害されているものと想定して、*侵害の影響の検出と抑制*および攻撃の防止に重点を置いて取り組みます。 
 - **既定でセキュリティ機能を組込み。**  適切に定義されたハードウェアとソフトウェア上でインフラストラクチャが実行されているため、通常は実装が顧客に委ねられる*セキュリティ機能の有効化、構成、および検証*を当社が行います。

Azure Stack が統合システムとして配布されるため、、Azure Stack インフラストラクチャのセキュリティ体制は Microsoft によって定義されます。  Azure と同様に、テナントは、テナント ワークロードのセキュリティ体制を定義する責任を負います。 このドキュメントでは、Azure Stack インフラストラクチャのセキュリティ体制に関する基本的知識について説明します。

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

承認済みのコードは Microsoft または OEM パートナーのいずれかによって署名され、Microsoft が定義したポリシーで指定されている認定ソフトウェアのリストに含まれています。 つまり、Azure Stack インフラストラクチャでの実行が承認されているソフトウェアのみを実行することができます。 未承認のコードを実行しようとしてもブロックされて、監査が生成されます。

Device Guard ポリシーは、Azure Stack インフラストラクチャでサード パーティ製のエージェントまたはソフトウェアを実行することを禁止しています。

## <a name="credential-guard"></a>資格情報の保護
Azure Stack の別の Windows Server 2016 セキュリティ機能として Windows Defender Credential Guard があります。この機能は、Azure Stack インフラストラクチャの資格情報を Pass-the-Hash および Pass-the-Ticket 攻撃から防護するために使用されます。

## <a name="antimalware"></a>マルウェア対策
Azure Stack (HYPER-V ホストと Virtual Machines の両方) のすべてのコンポーネントは、Windows Defender Antivirus によって保護されています。

## <a name="constrained-administration-model"></a>制約付き管理モデル
Azure Stack 内の管理は、それぞれが特定の目的をもつ、次の 3 つのエントリ ポイントを使用して制御されます。 
1. [管理者ポータル](azure-stack-manage-portals.md)は、日常の管理操作にポイントアンドクリック エクスペリエンスを提供します。
2. Azure Resource Manager は、PowerShell および Azure CLI で使用される、REST API を介した Administrator Portal のすべての管理操作を公開します。 
3. データ センターの統合やサポート シナリオなど、特定の低レベルの操作では、Azure Stack は、[特権エンドポイント](azure-stack-privileged-endpoint.md)と呼ばれる PowerShell エンドポイントを公開します。 このエンドポイントは、コマンドレットのホワイトリストのセットのみを公開し、これは厳重に監査されます。

## <a name="network-controls"></a>ネットワーク制御
Azure Stack インフラストラクチャには、ネットワーク アクセス制御リスト (ACL) の複数のレイヤーが付属しています。  ACL は、インフラストラクチャ コンポーネントへの不正アクセスを防止し、インフラストラクチャがその機能を果たすために必要なパスとだけ通信するように制限します。 

ネットワークの ACL には、次の 3 つのレイヤーが適用されます。
1.  ラック スイッチの最上部
2.  ソフトウェア定義ネットワーク
3.  ホストおよび VM のオペレーティング システムのファイアウォール 



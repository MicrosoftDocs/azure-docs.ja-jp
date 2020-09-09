---
title: Azure Attestation の概要
description: 信頼できる実行環境 (TEE) を証明するためのソリューションである Microsoft Azure Attestation の概要
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: references_regions
ms.openlocfilehash: ad164f8af3e5506ae5ac9121010b99303286dd1f
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89320598"
---
# <a name="microsoft-azure-attestation-preview"></a>Microsoft Azure Attestation (プレビュー)

Microsoft Azure Attestation (プレビュー) は、[Intel ソフトウェア ガード エクステンションズ](https://www.intel.com/content/www/us/en/architecture-and-technology/software-guard-extensions.html) (SGX) エンクレーブや[仮想化ベースのセキュリティ](/windows-hardware/design/device-experiences/oem-vbs) (VBS) エンクレーブなどの信頼できる実行環境 (TEE) を証明するためのソリューションです。 エンクレーブ構成証明は、エンクレーブがセキュリティで保護され、信頼できることを検証するためのプロセスです。

構成証明は、ソフトウェア バイナリが信頼できるプラットフォームで適切にインスタンス化されたことを示すためのプロセスです。 リモートの証明書利用者は、信頼できるハードウェアで上で実行されているのがこのような意図されたソフトウェアのみであるという確信を得ることができます。 Azure Attestation は、構成証明を目的とした、統合された顧客向けのサービスとフレームワークです。

Azure Attestation により、[Azure Confidential Computing](../confidential-computing/overview.md) やインテリジェント エッジ保護などの最先端のセキュリティ パラダイムが実現されます。 これまで、マシンの場所、そのマシン上の仮想マシン (VM) の状況、その VM 上でエンクレーブが実行されている環境を個別に検証する機能がお客様から求められてきました。 Azure Attestation は、こうしたお客様や、それ以外の多くのお客様からの要望に応えるものです。

Azure Attestation は、コンピューティング エンティティから証拠を受け取り、それらを要求のセットに変換します。さらに、構成可能なポリシーに照らして検証し、要求ベースのアプリケーション (証明書利用者や監査機関など) のための暗号的証明を生成します。

## <a name="use-cases"></a>ユース ケース

Azure Attestation は、複数の環境と特徴的なユース ケースに対して包括的な構成証明サービスを提供します。

### <a name="sgx-attestation"></a>SGX の構成証明

SGX とは、特定の Intel CPU モデルでサポートされているハードウェアグレードの分離を指します。 SGX を使用すると、SGX エンクレーブと呼ばれるサニタイズされたコンパートメントでコードを実行できます。 その後、アクセスとメモリのアクセス許可はハードウェアによって管理され、攻撃対象領域は適切に分離された最小限のものになります。

クライアント アプリケーションは、機密性の高いタスクがそれらのエンクレーブ内で実行されるように委任することで、SGX エンクレーブを利用するように設計できます。 このようなアプリケーションでは、Azure Attestation を利用して定期的にエンクレーブ内で信頼を確立し、その機能を使用して機密データにアクセスすることができます。

### <a name="vbs-attestation"></a>VBS の構成証明

VBS は、Hyper-V に基づくエンクレーブ メモリ保護のためのソフトウェアベース アーキテクチャです。 ホスト管理者コードだけでなく、ローカルおよびクラウド サービスの管理者が VBS エンクレーブ内のデータにアクセスしたり、その実行に影響を与えたりするのを防ぎます。

Azure Attestation は、SGX テクノロジと同様に、構成されているポリシーと照らし合わせた VBS エンクレーブの検証と、有効性の証明としての認定ステートメントの発行をサポートします。

### <a name="open-enclave"></a>Open Enclave
[Open Enclave](https://openenclave.io/sdk/) (OE) は、開発者が TEE ベースのアプリケーションの構築に利用する、単一の統合エンクレーブ抽象化の作成を目的としたライブラリのコレクションです。 これは、プラットフォームの特異性を最小限に抑える、セキュリティ保護されたユニバーサルなアプリ モデルを提供します。 Microsoft では、これを、SGX などのハードウェアベースのエンクレーブ テクノロジをだれもが使えるようにし、Azure での利用を拡大するための重要な手段と考えています。

OE は、エンクレーブ証拠の検証に関する特定の要件を標準化します。 これにより、OE は Azure Attestation の非常に適切な構成証明コンシューマーと見なされます。

## <a name="azure-attestation-can-run-in-a-tee"></a>Azure Attestation は TEE で実行可能

Azure Attestation は、次のアクションを実行するため、コンフィデンシャル コンピューティングのシナリオに不可欠です。

- エンクレーブ証拠が有効かどうかを検証する。
- 顧客が定義したポリシーと照らし合わせてエンクレーブ証拠を評価する。
- テナント固有のポリシーを管理および格納する。
- 証明書利用者がエンクレーブと対話するために使用するトークンを生成して署名する。

Azure Attestation は、次の 2 種類の環境で実行されるように構築されています。
- SGX 対応 TEE で実行される Azure Attestation。
- 非 TEE で実行される Azure Attestation。

Azure Attestation のお客様は、Microsoft が信頼できるコンピューティング ベース (TCB) の運用から外れる必要性を表明しています。 これは、VM 管理者、ホスト管理者、Microsoft 開発者などの Microsoft エンティティが構成証明要求、ポリシー、Azure Attestation によって発行されたトークンを変更できないようにするためです。 Azure Attestation は TEE でも実行されるよう構築されており、見積もりの検証、トークンの生成、トークン署名などの Azure Attestation の機能が SGX エンクレーブに移動されます。

## <a name="why-use-azure-attestation"></a>Azure Attestation を使用する理由

Azure Attestation には以下のベネフィットがあるため、TEE を証明するうえで推奨される選択肢となっています。 

- SGX エンクレーブや VBS エンクレーブなどの複数の TEE を証明するための統合フレームワーク
- カスタム構成証明プロバイダーとポリシーを構成してトークンの生成を制限できるマルチテナント サービス
- ユーザーによる構成なしで証明できる既定のプロバイダーを提供
- SGX エンクレーブ内での実装により、使用中のデータを保護
- サービス レベル アグリーメント (SLA) を提供する高可用性サービス

## <a name="business-continuity-and-disaster-recovery-bcdr-support"></a>事業継続とディザスター リカバリー (BCDR) のサポート

Azure Attestation の[ビジネス継続性とディザスター リカバリー](/azure/best-practices-availability-paired-regions) (BCDR) を使用すると、リージョン内の重大な可用性の問題や災害イベントによって生じるサービスの中断を軽減できます。

現在 BCDR でサポートされているリージョンは次のとおりです
- 米国東部 2 => 米国中部とペア。
- 米国中部 => 米国東部 2 とペア。

2 つのリージョンにデプロイされたクラスターは、通常の状況下では独立して動作します。 一方のリージョンで障害または停止が発生した場合、次の処理が行われます。

- Azure Attestation BCDR により、シームレスなフェールオーバーが提供されます。お客様は、回復のための手順を別途実行する必要はありません
- 当該リージョンの [Azure Traffic Manager](../traffic-manager/index.yml) が正常性プローブの機能低下を検出し、エンドポイントをペアのリージョンに切り替えます
- 既存の接続は機能せず、内部サーバー エラーまたはタイムアウトの問題が発生します
- すべてのコントロール プレーン操作がブロックされます。 お客様は、プライマリ リージョンでの構成証明プロバイダーの作成やポリシーの更新を行うことができなくなります
- 証明呼び出しを含むすべてのデータ プレーン操作は、プライマリ リージョンで引き続き動作します

## <a name="next-steps"></a>次のステップ
- [Azure Attestation の基本的な概念](basic-concepts.md)について学習する
- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [PowerShell を使用して Azure Attestation を設定する](quickstart-powershell.md)


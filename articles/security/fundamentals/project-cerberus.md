---
title: ファームウェアの整合性 - Azure セキュリティ
description: ファームウェアの整合性を確認するための暗号化測定値について説明します。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f085858a9d550623704efd4f051ed525e55a37e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557424"
---
# <a name="project-cerberus"></a>プロジェクト Cerberus

Cerberus は、複製できない ID を使用する、NIST 800-193 準拠のハードウェアの root-of-trust (信頼の基点) です。 Cerberus の目的は、ファームウェアの整合性に関する強力な anchor of trust (トラスト アンカー) を提供することで、Azure インフラストラクチャのセキュリティ体制をさらに向上させることです。

## <a name="enabling-an-anchor-of-trust"></a>anchor of trust (トラスト アンカー) を有効にする
すべての Cerberus チップには、Microsoft の証明機関 (CA) をルートとする署名証明書チェーンを使用して確立された一意の暗号化 ID があります。 Cerberus から取得される測定値を使用して、以下のようなコンポーネントの整合性を検証できます。

- Host
- ベースボード管理コントローラー (BMC)
- ネットワーク インターフェイス カードと [system-on-a-chip](https://en.wikipedia.org/wiki/System_on_a_chip) (SoC) を含むすべての周辺機器

この anchor of trust (トラスト アンカー) により、以下からプラットフォーム ファームウェアを保護することができます。

- プラットフォームで実行される、セキュリティ侵害されたファームウェア バイナリ
- オペレーティング システム、アプリケーション、またはハイパーバイザーのバグを悪用するマルウェアとハッカー
- 特定の種類のサプライチェーン攻撃 (製造、組み立て、輸送)
- 管理特権またはハードウェアへのアクセス権を持つ悪意のある内部関係者

## <a name="cerberus-attestation"></a>Cerberus の構成証明
Cerberus により、Platform Firmware Manifest (PFM) を使用して、サーバー コンポーネントのファームウェアの整合性が証明されます。 PFM により、承認されたファームウェア バージョンの一覧が定義され、Azure [Host Attestation Service](measured-boot-host-attestation.md) にプラットフォーム測定値が提供されます。 Host Attestation Service は、測定値を検証し、信頼されたホストのみ Azure フリートに参加して、顧客ワークロードをホストすることを許可する決定を行います。

Cerberus の機能は、Host Attestation Service と組み合わせることで、高度なセキュリティで保護された Azure 運用インフラストラクチャを強化および拡張します。

> [!NOTE]
> 詳細については、GitHub の[プロジェクト Cerberus](https://github.com/opencomputeproject/Project_Olympus/tree/master/Project_Cerberus) に関する情報を参照してください。

## <a name="next-steps"></a>次のステップ
プラットフォームの整合性とセキュリティを強化する方法の詳細については、次を参照してください。

- [ファームウェアのセキュリティ](firmware.md)
- [セキュア ブート](secure-boot.md)
- [メジャー ブートとホストの構成証明](measured-boot-host-attestation.md)
- [保存時の暗号化](encryption-atrest.md)
- [ハイパーバイザー セキュリティ](hypervisor.md)
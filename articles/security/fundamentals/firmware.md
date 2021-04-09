---
title: ファームウェアのセキュリティ - Azure のセキュリティ
description: Azure のハードウェアとファームウェアのセキュリティ保護の取り組みについて説明します。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 8233cc714d977083f4d55716d35c0b7094a069ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557445"
---
# <a name="firmware-security"></a>ファームウェアのセキュリティ
この記事では、クラウド ハードウェア エコシステムとサプライ チェーンのセキュリティ保護の取り組みについて説明します。

## <a name="securing-the-cloud-hardware-ecosystem"></a>クラウド ハードウェア エコシステムの保護
Microsoft では、クラウド ハードウェア エコシステム内で積極的に提携を行うことで、次のような方法で継続的なセキュリティ向上を促進しています。

- Azure のハードウェアおよびファームウェアのセキュリティ要件を満たすために、Azure のハードウェアおよびファームウェアのパートナー (コンポーネントの製造元やシステム インテグレーターなど) と連携する。

- 次のような分野で Microsoft が定義した要件を使用して、パートナーが製品のセキュリティ対策に関して継続的な評価および改善を行えるようにする。

  - ファームウェアのセキュア ブート
  - ファームウェアのセキュア回復
  - ファームウェアのセキュア更新
  - ファームウェアの暗号化
  - ハードウェアのロックダウン
  - 詳細なデバッグ テレメトリ
  - メジャー ブートを可能にする TPM 2.0 ハードウェアに対するシステム サポート

- 仕様の開発を通じて [Open Compute Project (OCP)](https://www.opencompute.org/wiki/Security) セキュリティ プロジェクトに参加し、貢献する。 仕様により、エコシステムにおける安全な設計とアーキテクチャの一貫性と明確さが促進されます。

   > [!NOTE]
   > OCP Security Project への Microsoft の貢献例としては、[ハードウェアのセキュア ブート](https://docs.google.com/document/d/1Se1Dd-raIZhl_xV3MnECeuu_I0nF-keg4kqXyK4k4Wc/edit#heading=h.5z2d7x9gbhk0)仕様があります。

## <a name="securing-hardware-and-firmware-supply-chains"></a>ハードウェアとファームウェアのサプライ チェーンのセキュリティ保護
Azure のクラウド ハードウェア サプライヤーとベンダーは、Microsoft が策定した、サプライ チェーンのセキュリティ プロセスと要件に従う必要もあります。 ハードウェアとファームウェアの開発およびデプロイ プロセスは、次に示す Microsoft [セキュリティ開発ライフサイクル](https://www.microsoft.com/securityengineering/sdl) (SDL) プロセスに従う必要があります。

- 脅威モデリング
- セキュリティで保護された設計レビュー
- ファームウェアのレビューと侵入テスト
- セキュリティで保護されたビルドおよびテスト環境
- セキュリティの脆弱性管理とインシデント対応

## <a name="next-steps"></a>次のステップ
プラットフォームの整合性とセキュリティを強化する方法の詳細については、次を参照してください。

- [セキュア ブート](secure-boot.md)
- [メジャー ブートとホストの構成証明](measured-boot-host-attestation.md)
- [プロジェクト Cerberus](project-cerberus.md)
- [保存時の暗号化](encryption-atrest.md)
- [ハイパーバイザー セキュリティ](hypervisor.md)
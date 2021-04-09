---
title: Azure プラットフォームの整合性とセキュリティ - Azure のセキュリティ
description: Azure プラットフォームの整合性とセキュリティに関する技術概要。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 4755bc19a645d196487f0b8e0f4d1ef2120723ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94557453"
---
# <a name="platform-integrity-and-security-overview"></a>プラットフォームの整合性とセキュリティの概要
Azure フリートは、数百万のサーバー (ホスト) で構成されており、さらに数千が日々追加されています。 また、再起動、オペレーティング システムの更新、または修復によって、毎日数千のホストでメンテナンスが実行されています。 ホストがこのフリートに参加して顧客のワークロードの受け入れを開始する前に、Microsoft では、ホストがセキュリティで保護された信頼できる状態であることを確認します。 この検証により、サプライ チェーンやメンテナンスのワークフロー中に、ブート シーケンス コンポーネントで悪意のある、または不注意による変更が発生していないことが保証されます。

## <a name="securing-azure-hardware-and-firmware"></a>Azure のハードウェアとファームウェアの保護
この一連の記事では、製造から終了まで、ライフサイクルのさまざまな段階を通じてホストの整合性とセキュリティが Microsoft によってどのように確保されているかについて説明します。 これらの記事では、次のことについて説明します。
 
- [ファームウェアのセキュリティ](firmware.md)
- [UEFI セキュア ブート](secure-boot.md)
- [メジャー ブートとホストの構成証明](measured-boot-host-attestation.md)
- [プロジェクト Cerberus](project-cerberus.md)
- [保存時の暗号化](encryption-atrest.md)
- [ハイパーバイザー セキュリティ](hypervisor.md)
 
## <a name="next-steps"></a>次のステップ

- [ファームウェアのセキュリティ強化](firmware.md)を継続的に推進するために、Microsoft が積極的に行っているクラウド ハードウェア エコシステム内の提携について説明します。

- [クラウドにおける共同責任](shared-responsibility.md)について理解します。
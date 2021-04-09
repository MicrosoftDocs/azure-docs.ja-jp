---
title: Azure Defender for DNS アラートに対応する方法
description: Azure Defender for DNS のアラートに対応するために必要な手順について説明します
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 375a81127a000741ca5e0397642800794610bcda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754540"
---
# <a name="respond-to-azure-defender-for-dns-alerts"></a>Azure Defender for DNS アラートに対応する

Azure Defender for DNS からアラートを受け取った場合は、下の説明に従って調査し、アラートに対応することをお勧めします。 Azure Defender for DNS によってすべての接続されたリソースが保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、すべてのアラートを取り巻く状況を確認することが重要です。  


## <a name="step-1-contact"></a>手順 1. Contact

1. リソース所有者に問い合わせて、動作が想定されていたか、意図的であったかを判断します。
1. そのアクティビティが想定されている場合は、アラートを無視します。
1. そのアクティビティが想定されていない場合は、リソースをセキュリティ侵害された可能性があるものとして扱い、次の手順で説明するように軽減します。

## <a name="step-2-immediate-mitigation"></a>手順 2. 即時軽減策 

1. ネットワークからリソースを分離し、横移動を防止します。
1. リソースに対してマルウェア対策のフル スキャンを実行し、結果として得られる修復アドバイスに従います。
1. リソースにインストールされた実行中のソフトウェアを確認し、不明または不要なパッケージを削除します。
1. マシンを既知の正常な状態に戻し、必要に応じてオペレーティング システムを再インストールし、マルウェアのない検証済みソースからソフトウェアを復元します。
1. マシンに対する Azure Security Center の推奨事項をすべて解決し、今後の侵害を防ぐために強調表示されたセキュリティ上の問題を修復します。


## <a name="next-steps"></a>次のステップ

このページでは、Azure Defender for DNS のアラートに対応するプロセスについて説明しました。 関連情報については、次のページを参照してください。

- [Azure Defender for DNS の概要](defender-for-dns-introduction.md)
- [Azure Defender のアラートの抑制](alerts-suppression-rules.md)
- [継続的に Security Center データをエクスポートする](continuous-export.md)
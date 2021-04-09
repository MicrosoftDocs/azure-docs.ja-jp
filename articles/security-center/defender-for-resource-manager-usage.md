---
title: Azure Defender for Resource Manager アラートに対応する方法
description: Azure Defender for Resource Manager のアラートに対応するために必要な手順について説明します
author: memildin
ms.author: memildin
ms.date: 12/07/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 54790795aab8aac247e17198159130d7139dd38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754531"
---
# <a name="respond-to-azure-defender-for-resource-manager-alerts"></a>Azure Defender for Resource Manager アラートに対応する

Azure Defender for Resource Manager からアラートを受け取った場合は、下の説明に従って調査し、アラートに対応することをお勧めします。 Azure Defender for Resource Manager によってすべての接続されたリソースが保護されているため、アラートをトリガーしたアプリケーションまたはユーザーを熟知している場合でも、すべてのアラートを取り巻く状況を確認することが重要です。  


## <a name="step-1-contact"></a>手順 1. Contact

1. リソース所有者に問い合わせて、動作が想定されていたか、意図的であったかを判断します。
1. そのアクティビティが想定されている場合は、アラートを無視します。
1. そのアクティビティが想定されていない場合は、関連するユーザー アカウント、サブスクリプション、および仮想マシンをセキュリティ侵害されたものとして扱い、次の手順で説明するように軽減します。

## <a name="step-2-immediate-mitigation"></a>手順 2. 即時軽減策 

1. 侵害されたユーザー アカウントを修復します。
    - 見慣れないものである場合は、脅威アクターによって作成された可能性があるため、削除します
    - 見慣れたものである場合は、その認証資格情報を変更します
    - Azure アクティビティ ログを使用して、ユーザーによって実行されるすべてのアクティビティを確認し、疑わしいものをすべて特定します

1. 侵害されたサブスクリプションを修復します。
    - 侵害された Automation アカウントから見慣れない Runbook を削除します
    - サブスクリプションの IAM アクセス許可を確認し、見慣れないすべてのユーザー アカウントのアクセス許可を削除します
    - サブスクリプション内のすべての Azure リソースを確認し、見慣れないものをすべて削除します
    - Azure Security Center でサブスクリプションのセキュリティ アラートを確認し、調査します
    - Azure アクティビティ ログを使用して、サブスクリプション内で実行されるすべてのアクティビティを確認し、疑わしいものをすべて特定します

1. 侵害された仮想マシンを修復します
    - すべてのユーザーのパスワードを変更します
    - マシン上でマルウェア対策のフル スキャンを実行します
    - マルウェアのないソースからマシンを再イメージ化します


## <a name="next-steps"></a>次のステップ

このページでは、Azure Defender for Resource Manager のアラートに対応するプロセスについて説明しました。 関連情報については、次のページを参照してください。

- [Azure Defender for Resource Manager の概要](defender-for-resource-manager-introduction.md)
- [Azure Defender のアラートの抑制](alerts-suppression-rules.md)
- [継続的に Security Center データをエクスポートする](continuous-export.md)
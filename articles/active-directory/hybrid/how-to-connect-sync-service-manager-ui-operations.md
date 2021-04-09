---
title: Azure AD Connect の Synchronization Service Manager の操作 | Microsoft Docs
description: Azure AD Connect の Synchronization Service Manager の [操作] タブについて
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85357105"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>Sync Service Manager の [操作] タブの使用

![Sync Service Manager](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

[操作] タブには、最近の操作の結果が表示されます。 このタブは問題を理解し、解決するための鍵となります。

## <a name="understand-the-information-visible-in-the-operations-tab"></a>[操作] タブに表示される情報を理解する
上半分にはすべての実行が時系列で表示されます。 既定では、操作ログには過去 7 日間の情報が保持されますが、この設定は [スケジューラ](how-to-connect-sync-feature-scheduler.md)で変更できます。 状態が成功になっていない実行を探したい場合があります。 見出しをクリックすると、並べ替えを変更できます。

**[Status]** (ステータス) 列は最も重要な情報であり、実行関連で最も深刻な問題を示します。 最も一般的なステータスを調査の優先度に基づいて簡単にまとめると次のようになります (* はエラー文字列が入ることを意味します)。

| Status | 解説 |
| --- | --- |
| stopped-\* |実行を完了できませんでした。 たとえば、リモート システムがダウンし、連絡できない場合などです。 |
| stopped-error-limit |エラーの数が 5,000 を超えています。 大量のエラーに起因し、実行が自動的に停止しました。 |
| completed-\*-errors |実行は完了しましたが、エラーが発生し (5,000 件未満)、調査が必要です。 |
| completed-\*-warnings |実行は完了しましたが、一部のデータが予想と異なります。 エラーがある場合には通常、このメッセージは単なる症状の 1 つにすぎません。 エラーを解決するまでは、警告を調査しないでください。 |
| success |問題ありません。 |

行を選択すると、下の領域が更新され、実行の詳細が表示されます。 下の領域の左端には、 **Step #**(# は番号) という一覧が表示されることがあります。 これは、フォレストに複数のドメインがあり、手順が各ドメインを表す場合にのみ表示されます。 ドメイン名は **[Partition]**(パーティション) という見出しにあります。 **[Synchronization Statistics (同期統計)]** には、処理された変更の数に関する詳細が表示されます。 リンクをクリックすると、変更されたオブジェクトの一覧を取得できます。 オブジェクトにエラーがある場合、 **[Synchronization Errors (同期エラー)]** の下に表示されます。

詳しくは、[同期していないオブジェクトのトラブルシューティング](tshoot-connect-object-not-syncing.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](how-to-connect-sync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。

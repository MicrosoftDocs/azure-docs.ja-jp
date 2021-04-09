---
title: Azure NetApp Files のスナップショット ポリシーのトラブルシューティング | Microsoft Docs
description: Azure NetApp Files のスナップショット ポリシーの管理に関する問題のトラブルシューティングに役立つエラー メッセージと解決策について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 6ba8b18876bdae2754a6a772ce3909ff2f5a71b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651006"
---
# <a name="troubleshoot-snapshot-policies"></a>スナップショット ポリシーのトラブルシューティング

この記事では、Azure NetApp Files のスナップショット ポリシーを管理するときに発生する可能性があるエラーのシナリオについて説明します。 問題に対処するのに役立つ可能性がある解決策も提供します。

## <a name="error-conditions-and-resolutions"></a>エラーの状態と解決策 

|     エラー状態    |     解像度    |
|-|-|
| 無効なスナップショット ポリシー名によってスナップショット ポリシーの作成が失敗します。 | スナップショット ポリシー名が無効な場合は、スナップショット ポリシーの作成中にエラーが発生します。 スナップショット ポリシー名には、以下のガイドラインが適用されます。  <ul><li> スナップショット ポリシー名に、ASCII 以外の文字や特殊文字を含めることはできません。 </li> <li> スナップショット ポリシー名は、文字または数字で始まる必要があり、文字、数字、アンダースコア ("_")、ハイフン ("-") のみを含めることができます。 </li> <li> スナップショット ポリシー名は、1 ～ 64 文字にする必要があります。  </li></ul> ガイドラインに従って、スナップショット ポリシー名を変更します。  |
| 無効な値によってスナップショット ポリシーの作成が失敗します。 | `Number of snapshots to keep` や `Minute on the hour to take snapshot` などのフィールドに無効な値を入力した場合は、Azure NetApp Files でスナップショット ポリシーの作成に失敗します。 有効な値は次のとおりです。  <ul><li>値は有効な数値にする必要があります。</li> <li>値は 0 ～ 59 にする必要があります。</li></ul> フィールドに、有効な値が指定されていることを確認します。 | 
| エラー `Total number of snapshots to keep exceeds 255` によってスナップショット ポリシーの作成が失敗します。 | 各ボリュームに作成できる[スナップショットは最大 255 個](azure-netapp-files-resource-limits.md)です。 この最大値には、時間、日、週、および月ごとのスナップショットの合計数が含まれます。 <br> `Snapshots to keep` の値を減らして、もう一度やり直します。 |
| エラー `Total snapshot policy is over the max '255'` によってボリュームへのポリシーの割り当てが失敗します。 | 各ボリュームに作成できる[スナップショットは最大 255 個](azure-netapp-files-resource-limits.md)です。 オンデマンドのスナップショットと、時間、日、週、および月ごとのスナップショットの合計数が最大値を超えると、エラーが発生します。 <br> `snapshots to keep` の値を減らすか、オンデマンド スナップショットの一部を削除し、再試行します。 | 

## <a name="next-steps"></a>次のステップ  

* [スナップショット ポリシーを管理する](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

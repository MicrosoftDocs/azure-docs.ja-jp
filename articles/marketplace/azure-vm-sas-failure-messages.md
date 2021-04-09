---
title: 仮想マシンの SAS のエラー メッセージ - Azure Marketplace
description: Shared Access Signature (SAS) を使用するときによく寄せられる質問。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126840"
---
# <a name="virtual-machine-sas-failure-messages"></a>仮想マシンの SAS のエラー メッセージ

以下は、共有アクセス署名 (ソリューション用のアップロードされた VHD を識別して共有するために使用されます) を使用するときに発生する一般的な問題と、推奨される解決策です。

| 問題 | エラー メッセージ | Fix |
| --------- | ------------------- | ------- |
| *イメージのコピーの失敗* |  |  |
| SAS URI に "?" が見つからない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 推奨ツールを使用して SAS URI を更新します。 |
| "st" および "se" パラメーターが SAS URI にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 適切な **開始日** と **終了日** の値で SAS URI を更新します。 |
| "sp=rl" が SAS URI にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | `Read` および `List` として設定されたアクセス許可で SAS URI を更新します。 |
| SAS URI の VHD 名に空白文字がある | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URI を更新して空白文字を削除します。 |
| SAS URI の承認エラー | `Failure: Copying Images. Not able to download blob due to authorization error.` | SAS URI の形式を確認して修正します。 必要に応じて再生成します。 |
| SAS URI の "st" および "se" パラメーターで完全な日付と時刻が指定されていない | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS URI の **開始日** および **終了日** パラメーター (`st` および`se` 部分文字列) は、`11-02-2017T00:00:00Z` のように、完全な日時形式になっている必要があります。 短縮バージョンは無効です (Azure CLI の一部のコマンドでは、簡略化された値が既定で生成されることがあります)。 |
|  |  |  |

詳細については、[Shared Access Signature (SAS) の使用](../storage/common/storage-sas-overview.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

- [SAS URI の生成](azure-vm-get-sas-uri.md)
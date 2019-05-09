---
title: Azure Marketplace 向けの SAS URL に関する一般的な問題と解決策 | Microsoft Docs
description: 共有アクセス署名 URI の使用に関する一般的な問題とその可能な解決策の一覧を示します。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: abb29cd0d31288ba7bfab7024cf7657ab6b9a3d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58879218"
---
# <a name="common-sas-url-issues-and-fixes"></a>SAS URL に関する一般的な問題と解決策

次の表では、共有アクセス署名 (ソリューション用のアップロードされた VHD を識別して共有するために使用されます) を使用するときに発生する一般的な問題と、推奨される解決策を示します。

| **問題** | **エラー メッセージ** | **解決策** | 
| --------- | ------------------- | ------- | 
| &emsp;  *イメージ コピーでのエラー* |  |  |
| SAS URL に "?" が見つからない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 推奨ツールを使用して SAS URL を更新します |
| "st" および "se" パラメーターが SAS URL にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | 適切な**開始日**と**終了日**の値で SAS URL を更新します。 | 
| "sp=rl" が SAS URL にない | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | `Read` および `List` として設定されたアクセス許可で SAS URL を更新します | 
| SAS URL の VHD 名に空白文字がある | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | SAS URL を更新して空白文字を削除します。 |
| SAS URL の承認エラー | `Failure: Copying Images. Not able to download blob due to authorization error` | SAS URI の形式を確認して修正します。 必要に応じて再生成します。 |
| SAS URL の "st" および "se" パラメーターで完全な日付と時刻が指定されていない | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS URL の**開始日**および**終了日**パラメーター (`st` および`se` 部分文字列) は、`11-02-2017T00:00:00Z` のように、完全な日時形式になっている必要があります。 短縮バージョンは無効です。 (Azure CLI の一部のコマンドでは、簡略化された値が既定で生成されることがあります。) | 
|  |  |  |

詳細については、「[Shared Access Signatures (SAS) の使用](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)」をご覧ください。

---
title: Azure Blob Storage の Azure PowerShell サンプル | Microsoft Docs
description: ストレージ アカウントの作成、アカウント間での BLOB の移行など、Azure Blob Storage を操作するための Azure PowerShell スクリプト サンプルへのリンクを参照してください。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/07/2017
ms.service: storage
ms.subservice: blobs
ms.topic: sample
ms.openlocfilehash: 1d343c952f01185d2360d6cf238dd3a7512849fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95912129"
---
# <a name="azure-powershell-samples-for-azure-blob-storage"></a>Azure Blob Storage の Azure PowerShell サンプル

次の表は、Azure Storage を作成および管理する PowerShell サンプル スクリプトへのリンク一覧です。

| スクリプト | 説明 |
|---|---|
|**ストレージ アカウント**||
| [Create a storage account and retrieve/rotate the access keys](../scripts/storage-common-rotate-account-keys-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (ストレージ アカウントを作成し、アクセス キーの取得とローテーションを行う)| Azure Storage アカウントを作成し、アクセス キーのいずれかを取得し、ローテーションを行います。 |
| [Windows での AzCopy を使用したストレージ アカウント間での BLOB の移行](/previous-versions/azure/storage/storage-common-transfer-between-storage-accounts?toc=%2fpowershell%2fmodule%2ftoc.json)| Windows で AzCopy を使用して、Azure ストレージ アカウント間で BLOB を移行します。 |
|**Blob Storage**||
| [Calculate the total size of a Blob storage container](../scripts/storage-blobs-container-calculate-size-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (Blob ストレージ コンテナーの合計サイズを計算する) | コンテナー内のすべての BLOB の合計サイズを計算します。 |
| [課金のために Blob Storage コンテナーのサイズを計算する](../scripts/storage-blobs-container-calculate-billing-size-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 請求額を見積もるために Blob Storage 内のコンテナーのサイズを計算します。 |
| [Delete containers with a specific prefix](../scripts/storage-blobs-container-delete-by-prefix-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) (特定のプレフィックスを含むコンテナーを削除する) | 指定した文字列を含むコンテナーを削除します。 |
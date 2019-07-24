---
title: Azure 無料アカウントのサービスと測定のマッピング | Microsoft Docs
description: 無料アカウントに含まれているサービスの、サービスと測定のマッピングを理解します。
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 2468f61c187d9b10ed9fe55ccf76e5d2561d0505
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889813"
---
# <a name="understand-free-service-to-meter-mapping"></a>無料サービスと測定のマッピングについて

すべての Azure サービスでは各測定値に対する使用状況が出力されます。これを使用して、Azure 課金システムがサービスの料金をユーザーに請求します。 無料サービスの使用について理解を深めるために、これらのサービスについて、サービスと測定のマッピングを見てみましょう。 無料サービスの作成方法については、「[Create free services with Azure free account (Azure の無料アカウントで無料サービスを作成する)](billing-create-free-services-included-free-account.md)」をご覧ください。

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>無料アカウント対象サービスの、サービスと測定のマッピング 

|    Service   | Azure Portal での測定名 | 使用状況ファイル/API での測定名 | 測定 ID |
| ------------ | -------------------------- | -------------------------| -------- |
| B1S Linux VM | コンピューティング時間 - Standard_B1 VM | コンピューティング時間 - 無料 | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | コンピューティング時間 - Standard_B1 VM (Windows) | コンピューティング時間 - 無料 | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - パブリック IP アドレス  | IP アドレス時間 - パブリック IP アドレス | IP アドレス時間 - 無料 | ae56b367-2708-4454-a3d9-2be7b2364ea1
| Cosmos DB | ストレージ (GB) - Cosmos DB | ストレージ (GB) - 無料 | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| Cosmos DB | 100 要求ユニット (時間) - Cosmos DB | 100 要求ユニット (時間) - 無料 | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| File Storage | Standard IO - ファイル (GB) - ローカル冗長 | Standard IO - ファイル (GB) - 無料 | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| File Storage | Standard IO - ファイル読み取り操作ユニット (10,000 件) | Standard IO - ファイル読み取り操作ユニット (10,000 件) - 無料 | 6207404d-3389-4d20-9087-cc078ddc3fd9
| File Storage | Standard IO - ファイル書き込み操作ユニット (10,000 件) | Standard IO - ファイル書き込み操作ユニット (10,000 件) - 無料 | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| File Storage | Standard IO - ファイル プロトコル操作ユニット (10,000 件) | Standard IO - ファイル プロトコル操作ユニット (10,000 件) - 無料 | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| File Storage | Standard IO - ファイル リスト操作ユニット (10,000 件) | Standard IO - ファイル リスト操作ユニット (10,000 件) - 無料 | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| ホット ブロック BLOB Storage | Standard IO - ホット ブロック BLOB 読み取り操作 (10,000 件) | Standard IO - ホット ブロック BLOB 読み取り操作 (10,000 件) - 無料 |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| ホット ブロック BLOB Storage | Standard IO - ホット ブロック BLOB (GB) - ローカル冗長 | Standard IO - ホット ブロック BLOB (GB) - 無料 | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| ホット ブロック BLOB Storage | Standard IO - ホット ブロック BLOB 書き込み操作 (10,000 件) | Standard IO - ホット ブロック BLOB 書き込み操作 (10,000 件) - 無料 | b34bbb76-edce-4c2d-a288-81a2db1fea53
| ホット ブロック BLOB Storage  | Standard IO - ホット ブロック BLOB 書き込み/リスト操作 (10,000 件) | Standard IO - ホット ブロック BLOB 書き込み/リスト操作 (10,000 件) - 無料 | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| 管理ディスク *  | Standard マネージド ディスク/スナップショット (GB) - ローカル冗長 | Standard マネージド ディスク/スナップショット (GB) - 無料 | ad94c237-52a5-4804-ae65-38c5bf85ef42
| 管理ディスク *  | Standard マネージド ディスク操作 (10,000 件) | Standard マネージド ディスク操作 (10,000 件) - 無料 | 82cc6ea4-0abd-43ac-acc0-ec34edf0f14c
| 管理ディスク *  | Premium Storage - ページ BLOB/P6 (ユニット) - ローカル冗長 | Premium Storage - ページ BLOB/P6 (ユニット) - 無料 | 2b98c168-27ca-4cc1-b509-e887dec87657
| SQL Database | Standard S0 Database 日数 - SQL Database | Standard S0 Database 日数 - 無料 | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| 共有 - 帯域幅 ** | データ転送 (送信) (GB) | データ転送 (送信) (GB) - 無料 | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\* Windows 仮想マシンを作成してマネージド ディスクを選択すると、仮想マシンの一部としてマネージド ディスク メーターが消費されます。

\** 共有メーターは、複数のサービスで使用できます。 たとえば、仮想マシンとストレージの両方が、データ転送 (送信) (GB) メーターに対して使用状況を出力します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

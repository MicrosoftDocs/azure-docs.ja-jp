---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004350"
---
## <a name="what-happens-to-my-app-during-deployment"></a>使用しているアプリはデプロイ中にどうなりますか?

正式にサポートされているすべてのデプロイ方法は、アプリの `/home/site/wwwroot` フォルダー内のファイルに変更を加えます。 アプリの実行には、それらのファイルが使用されます。 したがって、ファイルがロックされているため、デプロイに失敗する可能性があります。 すべてのファイルが同時に更新されるわけではないため、アプリはデプロイ中に予期しない動作をすることもあります。 これは、顧客向けのアプリでは好ましくありません。 これらの問題を回避するにはいくつかの方法があります。

- [ZIP パッケージから展開せずに直接アプリを実行](../articles/app-service/deploy-run-package.md)します。
- デプロイ中に、アプリを停止するか、またはアプリのオフライン モードを有効にします。 詳細については、「[Dealing with locked files during deployment](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)」 (デプロイ中にロックされているファイルに対処する) を参照してください。
- [自動スワップ](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)を有効にした状態で[ステージング スロット](../articles/app-service/deploy-staging-slots.md)にデプロイします。 

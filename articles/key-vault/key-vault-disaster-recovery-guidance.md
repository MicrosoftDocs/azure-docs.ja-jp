---
title: "Azure Key Vault に影響を与える Azure サービスの中断が発生した場合の対処方法 | Microsoft Docs"
description: "Azure Key Vault に影響を与える Azure サービスの中断が発生した場合の対処方法について説明します。"
services: key-vault
documentationcenter: 
author: adamglick
manager: mbaldwin
editor: 
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: sumedhb;aglick
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d2fb323e8cf6d14d0b5cf10071475cf4669545ed


---
# <a name="azure-key-vault-availability-and-redundancy"></a>Azure Key Vault の可用性と冗長性
Azure Key Vault には複数層の冗長性が備わっています。そのため、サービスの個々のコンポーネントで障害が発生した場合でも、アプリケーションでは引き続きキーとシークレットを使用することができます。

Key Vault のコンテンツは、リージョン内と、同じ地域内の 150 マイル (約 241 km) 以上離れたセカンダリ リージョンにレプリケートされます。 これにより、キーとシークレットの高い持続性が保たれます。

Key Vault サービス内の個々のコンポーネントで障害が発生した場合、リージョン内の代替コンポーネントが要求を処理し、機能が低下しないようにします。 この処理をトリガーするために、何かの措置を講じる必要はありません。 ユーザーからは見えないところで自動的に実行されます。

Azure リージョン全体が使用できない場合がまれにありますが、そのような場合でもそのリージョン内の Azure Key Vault に対する要求は自動的にセカンダリ リージョンにルーティング ("*フェールオーバー*") されます。 プライマリ リージョンが再び使用できるようになったときに、要求は再びプライマリ リージョンにルーティング ("*フェールバック*") されます。 この動作も自動的に行われるため、ユーザーによる操作は必要ありません。

その場合の注意事項をいくつか次に示します。

* リージョン フェールオーバーの場合、サービスのフェールオーバーには数分かかることがあります。 その間に行われた要求は、フェールオーバーが完了するまで失敗する可能性があります。
* フェールオーバーが完了すると、Key Vault は読み取り専用モードになります。 このモードでサポートされる要求は次のとおりです。
  * Key Vault のリスト
  * Key Vault のプロパティの取得
  * シークレットのリスト
  * シークレットの取得
  * キーのリスト
  * キー (のプロパティ) の取得
  * 暗号化
  * 復号化
  * ラップ
  * ラップ解除
  * 確認
  * 署名
  * バックアップ
* フェールオーバーがフェールバックされると、すべての種類の要求 (つまり、読み取り "*および*" 書き込み要求) が可能になります。




<!--HONumber=Nov16_HO3-->



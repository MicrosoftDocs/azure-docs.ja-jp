---
title: Azure Data Box Gateway 2101 リリース ノート | Microsoft Docs
description: Azure Data Box Gateway の現行 2101 リリースの重大な未解決の問題と解決策について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072522"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Azure Data Box Gateway 2101 リリース ノート

次のリリース ノートでは、Azure Data Box Gateway の 2101 リリースに関する未解決の重大な問題と解決済みの問題を明らかにします。

リリース ノートは、随時更新されます。 回避策を必要とする重大な問題が見つかった場合は、追加されます。 Azure Data Box Gateway をデプロイする前に、リリース ノート内の情報を十分に確認してください。  

今回のリリースは、次のソフトウェア バージョンに対応します。

- **Data Box Gateway 2101 (1.6.1475.2528)** - KB 4603462

> [!NOTE]
> Update 2101 は、ソフトウェアの一般提供 (GA) バージョン以降を実行しているすべてのデバイスに適用できます。

## <a name="whats-new"></a>新機能

このリリースには、次のバグ修正が含まれています。

- **アップロードの問題** - このリリースでは、失敗のためにアップロードを再開するとアップロード完了の速度が低下する可能性があるというアップロードの問題が修正されます。 この問題は、使用可能な帯域幅に対してサイズが大きいファイルで主に構成されるデータセットをアップロードするとき、特に (これに限りませんが)、帯域幅調整がアクティブになっているときに発生する可能性があります。 この変更により、特定のファイルのアップロードを再開する前に、アップロード完了のための十分な機会が保証されます。

このリリースには、次の更新も含まれています。

- 2020 年 10 月中にリリースされた、すべての累積的な Windows 更新プログラムと .NET Framework 更新プログラム。
- ソフトウェア更新プログラム間で Azure Data Box Gateway の静的 IP アドレスが保持される。

## <a name="known-issues-in-this-release"></a>このリリースの既知の問題

今回のリリースで特にリリースされた新しい問題はありません。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 既知の問題の一覧を表示するには、「[GA リリースの既知の問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)

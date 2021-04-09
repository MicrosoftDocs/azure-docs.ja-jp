---
title: Azure Stack Edge および Azure Data Box Gateway 2007 のリリース ノート | Microsoft Docs
description: 2007 リリースを実行する Azure Stack Edge および Data Box Gateway に関する未解決の重大な問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581248"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Azure Stack Edge および Azure Data Box Gateway 2007 のリリース ノート

次のリリース ノートでは、Azure Stack Edge および Data Box Gateway の 2007 リリースに関する未解決の重大な問題と解決済みの問題を明らかにします。

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 Azure Stack Edge または Data Box Gateway をデプロイする前に、リリース ノートに含まれている情報を注意深く確認してください。

今回のリリースは、次のソフトウェア バージョンに対応します。

- **Azure Stack Edge 2007 (1.6.1280.1667)** - KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** - KB 4566550

> [!NOTE]
> Update 2007 は、ソフトウェアの一般提供 (GA) バージョン以降を実行しているすべてのデバイスに適用できます。

## <a name="whats-new"></a>新機能

このリリースには、次のバグ修正が含まれています。

- **アップロードの問題** - このリリースでは、失敗が原因でアップロードを再開するとアップロード完了の速度が低下する可能性があるというアップロードの問題が修正されます。 この問題は、使用可能な帯域幅に対してサイズが大きいファイルで主に構成されるデータセットをアップロードするとき、特に (これに限りませんが)、帯域幅調整がアクティブになっているときに発生する可能性があります。 この変更により、特定のファイルのアップロードを再開する前に、アップロードの完了に十分な機会が与えられるようになります。

このリリースには、次の更新も含まれています。

- Windows VHD の基本イメージが更新されました。
- 2020 年 5 月中にリリースされた累積的な Windows 更新プログラムと .NET Framework 更新プログラムがすべて含まれています。
- このリリースでは、Azure Stack Edge デバイスでの IoT Edge 1.0.9.3 がサポートされます。

## <a name="known-issues-in-this-release"></a>このリリースの既知の問題

今回のリリースで特にリリースされた新しい問題はありません。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 既知の問題の一覧を表示するには、「[GA リリースの既知の問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge のデプロイを準備する](../databox-online/azure-stack-edge-deploy-prep.md)
- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)

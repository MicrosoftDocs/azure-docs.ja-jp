---
title: FPGA が組み込まれた Azure Stack Edge Pro 2101 リリース ノート | Microsoft Docs
description: Azure Stack Edge 2101 リリースの重大な未解決の問題と解決策について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727551"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>FPGA が組み込まれた Azure Stack Edge Pro 2101 リリース ノート

次のリリース ノートでは、Field Programmable Gate Array (FPGA) が組み込まれた Azure Stack Edge Pro の 2101 リリースについて、未解決の重大な問題と解決された問題を説明します。

リリース ノートは、随時更新されます。 回避策を必要とする重大な問題が見つかった場合は、追加されます。 Azure Stack Edge デバイスをデプロイする前に、リリース ノート内の情報を注意深くご確認ください。  

このリリースは、次のソフトウェア バージョンに対応します:

- **Azure Stack Edge 2101 (1.6.1475.2528)** - KB 4599267

> [!NOTE]
> Update 2101 は、ソフトウェアの一般提供 (GA) バージョン以降を実行しているデバイスにのみ適用できます。

## <a name="whats-new"></a>新機能

このリリースには、次のバグ修正が含まれています。

- **アップロードの問題** - このリリースでは、失敗が原因でアップロードを再開するとアップロード完了の速度が低下するおそれがあるというアップロードの問題が修正されます。 この問題は、使用可能な帯域幅に比べてサイズが大きいファイルで主に構成されるデータセットをアップロードする場合、特に (これに限りませんが)、帯域幅調整がアクティブになっていると、発生するおそれがあります この変更により、特定のファイルのアップロードを再開する前に、アップロード完了に十分適した条件が保証されます。

このリリースには、次の更新も含まれています。

- 2020 年 10 月中にリリースされた、すべての累積的な Windows 更新プログラムと .NET Framework 更新プログラム。
- Dell の新しい電源装置ユニットとの非互換性に対処するため、ベースボード管理コントローラー (BMC) のファームウェア バージョンは工場でのインストール時に 3.32.32.32 から 3.36.36.36 にアップグレードされます。
- このリリースでは、Azure Stack Edge デバイスでの IoT Edge 1.0.9.3 がサポートされます。

## <a name="known-issues-in-this-release"></a>このリリースの既知の問題

今回のリリースで特にリリースされた新しい問題はありません。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 既知の問題の一覧を表示するには、「[GA リリースの既知の問題](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge の配置を準備する](../databox-online/azure-stack-edge-deploy-prep.md)
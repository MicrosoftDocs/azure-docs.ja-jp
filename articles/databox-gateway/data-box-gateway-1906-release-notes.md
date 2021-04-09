---
title: Azure Data Box Gateway および Azure Data Box Edge 1906 リリース ノート | Microsoft Docs
description: Azure Data Box Gateway および Azure Data Box Edge の現行の 1906 リリースにおいて、重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 51f4995ffd6a86022d95df15ae0eb7694d878c60
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96581265"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge と Azure Data Box Gateway 1906 のリリース ノート

次のリリース ノートでは、Azure Data Box Edge および Azure Data Box Gateway の 1906 リリースの重大な未解決の問題と解決済みの問題を識別します。 

リリース ノートは継続的に更新されます。対応策を必要とする重大な問題が見つかった場合は、それらの問題が追加されます。 Data Box Edge/Data Box Gateway をデプロイする前に、リリース ノートに含まれている情報を注意深く確認してください。

今回のリリースは、次のソフトウェア バージョンに対応します。

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Update 1906 は、ソフトウェアの一般提供 (GA) または 1905 バージョンを実行している Data Box Edge デバイスにのみ適用できます。

## <a name="whats-new"></a>新機能

- **回復キー管理ワークフローでのバグ修正** - 以前のリリースでは、回復キーが適用されなかったことに起因するバグがありました。 このバグは今回のリリースで修正されました。 デバイスが起動されない場合に、回復キーを使用してデバイス上のデータを復元できるため、この更新プログラムを適用することを強くお勧めします。 詳細については、[Data Box Edge または Data Box Gateway をデプロイするときに回復キーを保存する](../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)方法に関するページを参照してください。
- **Field Programmable Gate Array (FPGA) のログ記録の強化** - 1905 リリース以降では、FPGA に関連するログ記録とアラートの機能強化が行われました。 Edge コンピューティング機能を FPGA と共に使用している場合、これは引き続き Data Box Edge にとって必須の更新プログラムとなります。 詳細については、[Edge コンピューティングを使用した Data Box Edge 上のデータの変換](../databox-online/azure-stack-edge-deploy-configure-compute-advanced.md)に関する記事を参照してください。

## <a name="known-issues-in-ga-release"></a>GA リリースの既知の問題

今回のリリースで特にリリースされた新しい問題はありません。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 既知の問題の一覧を表示するには、「[GA リリースの既知の問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge のデプロイを準備する](../databox-online/azure-stack-edge-deploy-prep.md)

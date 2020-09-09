---
title: Azure Data Box Gateway および Azure Data Box Edge 1906 リリース ノート | Microsoft Docs
description: Azure Data Box Gateway および Azure Data Box Edge の現行の 1906 リリースにおいて、重大な未解決の問題と解決策について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 9f4633a3fcafcb2b3151270715036e7940eb1c91
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82561851"
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

- **回復キー管理ワークフローでのバグ修正** - 以前のリリースでは、回復キーが適用されなかったことに起因するバグがありました。 このバグは今回のリリースで修正されました。 デバイスが起動されない場合に、回復キーを使用してデバイス上のデータを復元できるため、この更新プログラムを適用することを強くお勧めします。 詳細については、[Data Box Edge または Data Box Gateway をデプロイするときに回復キーを保存する](azure-stack-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device)方法に関するページを参照してください。
- **Field Programmable Gate Array (FPGA) のログ記録の強化** - 1905 リリース以降では、FPGA に関連するログ記録とアラートの機能強化が行われました。 Edge コンピューティング機能を FPGA と共に使用している場合、これは引き続き Data Box Edge にとって必須の更新プログラムとなります。 詳細については、[Edge コンピューティングを使用した Data Box Edge 上のデータの変換](azure-stack-edge-deploy-configure-compute-advanced.md)に関する記事を参照してください。

## <a name="known-issues-in-ga-release"></a>GA リリースの既知の問題

今回のリリースで特にリリースされた新しい問題はありません。 リリース ノートに記載されている問題はすべて、以前のリリースからの持ち越しです。 既知の問題の一覧を表示するには、「[GA リリースの既知の問題](data-box-gateway-release-notes.md#known-issues-in-ga-release)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
- [Azure Data Box Edge のデプロイを準備する](azure-stack-edge-deploy-prep.md)

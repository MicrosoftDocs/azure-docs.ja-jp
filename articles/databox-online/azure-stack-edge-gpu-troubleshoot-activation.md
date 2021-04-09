---
title: Azure portal を使用して Azure Stack Edge Pro と GPU に関連するアクティブ化エラーをトラブルシューティングする | Microsoft Docs
description: Azure Stack Edge Pro GPU のアクティブ化とキー コンテナー関連の問題をトラブルシューティングする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102442147"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスのアクティブ化に関する問題をトラブルシューティングする 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスのアクティブ化に関する問題をトラブルシューティングする方法について説明します。 


## <a name="activation-errors"></a>アクティブ化エラー

次の表は、デバイスのアクティブ化に関連するエラーと、対応する推奨される解決策をまとめたものです。

| エラー メッセージ| 推奨される解決策 |
|------------------------------------------------------|--------------------------------------|
| アクティブ化キーを使用してデバイスをアクティブ化する前にアクティブ化に使用される Azure Key Vault が削除された場合、このエラーが発生します。 <br> ![キー コンテナー エラー 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | キー コンテナーが削除されている場合、コンテナーが消去防止期間内であれば、キー コンテナーを回復できます。 [キー コンテナーを回復する](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates)手順に従います。 <br>消去防止期間が経過した場合、キー コンテナーを回復することはできません。 Microsoft サポートに手順をお問い合わせください。 |
| デバイスがアクティブ化された後で Azure Key Vault が削除されている場合に、暗号化を含む操作 (たとえば、**ユーザーの追加**、**共有の追加**、**コンピューティングの構成**) を実行しようとすると、このエラーが発生します。 <br> ![キー コンテナー エラー 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | キー コンテナーが削除されている場合、コンテナーが消去防止期間内であれば、キー コンテナーを回復できます。 キー コンテナーを回復する手順に従います。 <br>消去防止期間が経過した場合、キー コンテナーを回復することはできません。 Microsoft サポートに手順をお問い合わせください。 |
| Azure Key Vault のチャネル整合性キーが削除されている場合に、暗号化を含む操作 (たとえば、**ユーザーの追加**、**共有の追加**、**コンピューティングの構成**) を実行しようとすると、このエラーが発生します。 <br> ![キー コンテナー エラー 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | キー コンテナーのチャネル整合性キーが削除されていても、まだ消去期間内の場合は、[キー コンテナーのキーの削除を元に戻す](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval)手順に従います。 <br>消去防止期間を経過しているが、キーがバックアップされている場合は、バックアップから復元できます。そうでない場合、キーを回復することはできません。 Microsoft サポートに手順をお問い合わせください。 |
| エラーが発生したためにアクティブ化キーの生成が失敗した場合、このエラーが発生します。 追加の詳細が通知内に表示されます。 <br> ![キー コンテナー エラー 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Key Vault にアクセスするために、「[ファイアウォールの向こう側にある Azure Key Vault へのアクセス](../key-vault/general/access-behind-firewall.md)」で示されているポートと URL がファイアウォールで開いていることを確認します。 数分間待ってから操作をやり直してください。 問題が解決しない場合は、Microsoft サポートにお問い合わせください。 |
| ユーザーが読み取り専用アクセス許可を持っている場合、アクティブ化キーの生成は許可されず、このエラーが表示されます。 <br> ![キー コンテナー エラー 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | これは、適切なアクセス権を持っていないか、*Microsoft.KeyVault* が登録されていないことが原因である可能性があります。<li>Azure Stack Edge リソースに対して使用されるリソース グループ レベルで、所有者または共同作成者アクセス権があることを確認します。</li><li>Microsoft.KeyVault リソース プロバイダーが登録されていることを確認します。 リソース プロバイダーを登録するには、Azure Stack Edge リソースに対して使用されているサブスクリプションに移動します。 **[リソース プロバイダー]** に移動し、*Microsoft.KeyVault* を探し、選択して **登録** します。</li> |

## <a name="next-steps"></a>次のステップ

- [デバイスの問題をトラブルシューティングする](azure-stack-edge-gpu-troubleshoot.md)方法の詳細を確認します。

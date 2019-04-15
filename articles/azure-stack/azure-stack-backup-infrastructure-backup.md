---
title: インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復 | Microsoft Docs
description: インフラストラクチャ バックアップ サービスを使用して、構成およびサービス データをバックアップおよび復元できます。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: da1ae76925ffeba7a1df57b4121f8cfe20b2887f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882108"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービスを使用した Azure Stack のバックアップとデータの回復

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

インフラストラクチャ バックアップ サービスを使用して、構成およびサービス データをバックアップおよび復元できます。 Azure Stack の各インストールには、サービスのインスタンスが含まれています。 Azure Stack Cloud を再デプロイし、ID、セキュリティ、Azure Resource Manager データを復元するには、サービスによって作成されたバックアップを使用できます。 

クラウドを実稼働に移行する準備ができたら、バックアップを有効にすることができます。 テストと検証を長期間実行する予定の場合は、バックアップを有効にしないでください。

バックアップ サービスを有効にする前に、[要件が満たされている](#verify-requirements-for-the-infrastructure-backup-service)ことを確認してください。

> [!Note]  
> インフラストラクチャ バックアップ サービスには、ユーザー データとアプリケーションは含まれません。 IaaS VM ベースのアプリケーションを保護する方法の詳細については、[Azure Stack にデプロイされた VM の保護](user/azure-stack-manage-vm-protect.md)に関する説明を参照してください。 Azure Stack 上でアプリケーションを保護する方法を包括的に理解するには、[事業継続とディザスター リカバリーのための Azure Stack の考慮事項に関するホワイトペーパー](https://aka.ms/azurestackbcdrconsiderationswp)を参照してください。

## <a name="the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービス

このサービスには以下の機能が含まれています。

| 機能                                            | 説明                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| バックアップ インフラストラクチャ サービス                     | Azure Stack でインフラストラクチャ サービスのサブセット全体のバックアップを調整します。 災害が発生した場合、再デプロイの一環としてデータを復元できます。 |
| エクスポートされるバックアップ データの圧縮と暗号化 | バックアップ データは、システムによって圧縮および暗号化されてから、管理者が提供する外部ストレージの場所にエクスポートされます。                |
| バックアップ ジョブの監視                              | システムから、バックアップ ジョブが失敗した場合と修復の手順が通知されます。                                                                                                |
| 管理エクスペリエンスのバックアップ                       | バックアップ RP はバックアップの有効化をサポートしています。                                                                                                                         |
| クラウドの復旧                                     | 致命的なデータ損失が発生した場合、バックアップを使用して、デプロイの一環として Azure Stack のコア情報を復元できます。                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>インフラストラクチャ バックアップ サービスの要件を確認する

- **保存先**  
  7 個のバックアップを保存できる、Azure Stack からアクセス可能なファイル共有が必要です。 各バックアップは約 10 GB です。 共有には 140 GB のバックアップを保存できる必要があります。 Azure Stack インフラストラクチャ バックアップ サービスの保存場所の選択の詳細については、「[Backup Controller requirements](azure-stack-backup-reference.md#backup-controller-requirements)」(バックアップ コントローラーの要件) を参照してください。
- **資格情報**  
  ドメイン ユーザー アカウントと資格情報が必要です。たとえば、Azure Stack 管理者の資格情報を使用できます。
- **暗号化証明書**  
  バックアップ ファイルは証明書内の公開キーを使用して暗号化されます。 必ず、この証明書は安全な場所に保存してください。 


## <a name="next-steps"></a>次の手順

[管理ポータルで Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-console.md)方法を学びます。

[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)方法を学びます。

[Azure Stack のバックアップ](azure-stack-backup-back-up-azure-stack.md )方法を学びます。

[致命的なデータの損失からの復旧](azure-stack-backup-recover-data.md)方法を学びます。

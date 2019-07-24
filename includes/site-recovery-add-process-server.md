---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: c9a0d4387511bbfa033bcb90d9f83e1a7bb39719
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58124676"
---
1. Azure Site Recovery UnifiedSetup.exe の起動
2. **[開始する前に]** で **[Add additional process servers to scale out deployment] \(デプロイをスケールアウトするためにプロセス サーバーを追加する)** を選択します。

   ![プロセス サーバーの追加](./media/site-recovery-add-process-server/ps-page-1.png)

3. **[Configuration Server Details (構成サーバーの詳細)]** で、構成サーバーの IP アドレスとパスフレーズを指定します。

   ![プロセス サーバーの追加 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

   ![プロセス サーバーの追加 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * マシンで現在セットアップされているプロキシを使用して接続する場合は、**[Connect with existing proxy settings] \(既存のプロキシ設定を使用して接続する)** を選択します。
   * プロバイダーから直接接続するには、**[Connect directly without a proxy] \(プロキシなしで直接接続する)** を選択します。
   * 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。

     * カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
     * プロキシを使用する場合は、サービス URL へのアクセスを許可しておく必要があります。

5. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。 **グローバル時刻の同期チェック**に関する警告が表示された場合は、システム クロックの時刻 (**[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

     ![プロセス サーバーの追加 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかがチェックされます。

     ![プロセス サーバーの追加 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. **[インストール場所]** で、バイナリをインストールしキャッシュを格納する場所を選択します。 選択するドライブには使用可能なディスク領域が 5 GB 以上必要ですが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。
     ![プロセス サーバーの追加 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. **[ネットワークの選択]** で、構成サーバーがレプリケーション データを送受信するリスナー (ネットワーク アダプターと SSL ポート) を指定します。 既定では、ポート 9443 がレプリケーション トラフィックの送受信用に使用されます。このポート番号は、実際の環境の要件に合わせて変更できます。 ポート 9443 に加え、ポート 443 も開きます。このポートは、Web サーバーがレプリケーション操作を調整するために使用されます。 ポート 443 はレプリケーション トラフィックの送受信用に使用しないでください。

     ![プロセス サーバーの追加 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. **[概要]** で情報を確認し、**[インストール]** をクリックします。 インストールが完了すると、パスフレーズが生成されます。 このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。

     ![プロセス サーバーの追加 7](./media/site-recovery-add-process-server/ps-page-8.png)

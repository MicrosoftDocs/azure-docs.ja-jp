1. 統合セットアップ インストール ファイルを実行します。
2. **[開始する前に]** で **[Install the configuration server and process server] \(構成サーバーとプロセス サーバーをインストールする)** を選択します。
    ![開始する前に](./media/site-recovery-add-configuration-server/combined-wiz1.png)
3. **[Third-Party Software License (サードパーティ製ソフトウェア ライセンス)]** で、**[同意する]** をクリックして MySQL をダウンロードし、インストールします。

    ![サードパーティ製ソフトウェア](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. **[登録]** で、コンテナーからダウンロードした登録キーを選択します。

    ![登録](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. **[インターネット設定]** で、構成サーバーで実行されているプロバイダーがインターネット経由で Azure Site Recovery に接続する方法を指定します。

   * マシンで現在セットアップされているプロキシを使用して接続する場合は、**[Connect with existing proxy settings] \(既存のプロキシ設定を使用して接続する)** を選択します。
   * プロバイダーから直接接続するには、**[Connect directly without a proxy] \(プロキシなしで直接接続する)** を選択します。
   * 既存のプロキシで認証が必要な場合、またはプロバイダー接続にカスタム プロキシを使用する場合は、**[Connect with custom proxy settings (カスタム プロキシ設定を使用して接続する)]** を選択します。

     * カスタム プロキシを使用する場合、アドレス、ポート、資格情報を指定する必要があります。
     * プロキシを使用している場合は、[前提条件](#prerequisites)に記載されている URL をあらかじめ許可しておく必要があります。

     ![ファイアウォール](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. **[前提条件の確認]** では、インストールを実行できることを確認するためのチェックが実行されます。 **グローバル時刻の同期チェック**に関する警告が表示された場合は、システム クロックの時刻 (**[日付と時刻]** 設定) がタイム ゾーンと同じであることを確認します。

    ![前提条件](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. **[MySQL Configuration (MySQL の構成)]** で、インストールする MySQL サーバー インスタンスにログオンするための資格情報を作成します。

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. **[環境の詳細]** で、VMware VM をレプリケートするかどうかを選択します。 レプリケートする場合、PowerCLI 6.0 がインストールされているかどうかがチェックされます。

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz7.png)

9. **[インストール場所]** で、バイナリをインストールしキャッシュを格納する場所を選択します。 選択するドライブには使用可能なディスク領域が 5 GB 以上必要ですが、600 GB 以上の空き領域があるキャッシュ ドライブを使用することをお勧めします。

    ![インストール場所](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. **[ネットワークの選択]** で、構成サーバーがレプリケーション データを送受信するリスナー (ネットワーク アダプターと SSL ポート) を指定します。 既定では、ポート 9443 がレプリケーション トラフィックの送受信用に使用されます。このポート番号は、実際の環境の要件に合わせて変更できます。 ポート 9443 に加え、ポート 443 も開きます。このポートは、Web サーバーがレプリケーション操作を調整するために使用されます。 ポート 443 はレプリケーション トラフィックの送受信用に使用しないでください。

    ![[ネットワークの選択]](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. **[概要]** で情報を確認し、**[インストール]** をクリックします。 インストールが完了すると、パスフレーズが生成されます。 このパスフレーズは、レプリケーションを有効にするときに必要になるので、コピーしてセキュリティで保護された場所に保管してください。

    ![概要](./media/site-recovery-add-configuration-server/combined-wiz10.png)

登録が完了すると、コンテナーの **[設定]** > **[サーバー]** ブレードに、サーバーが表示されます。

### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Windows コンピューターへのプッシュ インストールの準備

1. Windows コンピューターとプロセス サーバーの間にネットワーク接続が存在することを確認します。
2. プロセス サーバーがコンピューターへのアクセスに使用できるアカウントを作成します。 このアカウントには管理者権限 (ローカルまたはドメイン) が必要です  (このアカウントは、プッシュ インストールとエージェントの更新にのみ使用します)。

   > [!NOTE]
   > ドメイン アカウントを使用していない場合、ローカル コンピューターで "リモート ユーザー アクセス" コントロールを無効にしてください。 リモート ユーザー アクセス制御を無効にするには、HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System レジストリ キーに新しい DWORD として **LocalAccountTokenFilterPolicy** を追加して、 その値を **1** に設定します。 これをコマンド プロンプトから行うには、次のコマンドを実行します。  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. 保護対象コンピューターの Windows ファイアウォールで、**[ファイアウォールを介したアプリまたは機能を許可する]** を選択します。 **[ファイルとプリンターの共有]** と **[Windows Management Instrumentation (WMI)]** を有効にします。 コンピューターがドメインに属している場合は、グループ ポリシー オブジェクト (GPO) を使用してファイアウォールの設定を構成できます。

   ![ファイアウォールの設定](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. CSPSConfigtool で作成したアカウントを追加します。
    1.  構成サーバーにサインインします。
    2.  **cspsconfigtool.exe** を開きます。 これはデスクトップにショートカットがあり、%ProgramData%\home\svsystems\bin フォルダーに保存されています。
    3.  **[アカウントの管理]** タブの **[アカウントの追加]** を選択します。
    4.  作成したアカウントを追加します。
    5.  コンピューターのレプリケーションを有効にするときに使用する資格情報を入力します。

UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <データ転送に使用する IP アドレス] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

パラメーター:

* /ServerMode: 必須。 構成サーバーとプロセス サーバーの両方をインストールするか、プロセス サーバーだけをインストールするかを指定します。 入力値: CS、PS。
* InstallLocation: 必須。 コンポーネントがインストールされているフォルダー。
* /MySQLCredsFilePath:  必須。 MySQL サーバーの資格情報が保存されているファイルのパス。 このファイルの形式は次のとおりです。
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath:  必須。 コンテナーの資格情報ファイルの場所。
* /EnvType: 必須。 インストールの種類。 値: VMware、NonVMware
* /PSIP と /CSIP: 必須。 プロセス サーバーと構成サーバーの IP アドレス。
* /PassphraseFilePath: 必須。 パスフレーズ ファイルの場所。
* /BypassProxy:  省略可能。 構成サーバーがプロキシを介さずに Azure に接続することを指定します。
* /ProxySettingsFilePath: 省略可能。 プロキシ設定 (認証を必要とする既定のプロキシ、またはカスタム プロキシ)。 このファイルの形式は次のとおりです。
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort:  省略可能。 レプリケーション データのポート番号。
* SkipSpaceCheck:  省略可能。 キャッシュの領域チェックをスキップします。
* AcceptThirdpartyEULA:  必須。 サード パーティのライセンス条項を受け入れます。
* ShowThirdpartyEULA:  必須。 サード パーティのライセンス条項を表示します。 入力として提供された場合、他のすべてのパラメーターが無視されます。


<!--HONumber=Feb17_HO2-->



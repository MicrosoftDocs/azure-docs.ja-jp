



環境や選択肢によっては、このスクリプトにより、Azure 仮想ネットワーク、ストレージ アカウント、クラウド サービス、ドメイン コントローラー、リモートまたはローカルの SQL データベース、ヘッド ノード、その他のクラスター ノードを含む、すべてのクラスター インフラストラクチャを作成できます。 また、既存の Azure インフラストラクチャを使用し、HPC クラスター ノードだけを作成することもできます。

HPC Pack クラスターの計画に関する背景情報については、HPC Pack 2012 R2 TechNet ライブラリの[製品の評価と計画](https://technet.microsoft.com/library/jj899596.aspx)および[概要](https://technet.microsoft.com/library/jj899590.aspx)に関する各ページを参照してください。

## <a name="prerequisites"></a>前提条件
* **Azure サブスクリプション**: Azure Global または Azure China サービスのサブスクリプションを利用できます。 ご利用のサブスクリプションの制限により、デプロイ可能なクラスター ノードの数と種類が変わります。 詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../articles/azure-subscription-service-limits.md)」をご覧ください。
* **Azure PowerShell 0.8.10 以降がインストールおよび構成されている Windows クライアント コンピューター**: インストール方法と Azure サブスクリプションへの接続手順については、[Azure PowerShell の概要](/powershell/azureps-cmdlets-docs)に関するページを参照してください。
* **HPC Pack IaaS デプロイ スクリプト**: [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) から最新版のスクリプトをダウンロードし、解凍します。 `New-HPCIaaSCluster.ps1 –Version`を実行して、スクリプトのバージョンを確認します。 この記事はバージョン 4.5.2 のスクリプトに基づきます。
* **スクリプトの設定ファイル**: スクリプトが HPC クラスターの構成に使用する XML ファイルを作成します。 詳細および例については、この記事の後続のセクションとデプロイ スクリプトに付属の Manual.rtf ファイルを参照してください。

## <a name="syntax"></a>構文
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> スクリプトは管理者として実行してください。
> 
> 

### <a name="parameters"></a>parameters
* **ConfigFile**: HPC クラスターについて説明する構成ファイルのファイル パスを指定します。 構成ファイルの詳細については、このトピックか、またはスクリプトが格納されているフォルダーの Manual.rtf ファイルを参照してください。
* **AdminUserName**: ユーザー名を指定します。 スクリプトによりドメイン フォレストが作成される場合、これがすべての VM のローカル管理者ユーザー名とドメイン管理者名になります。 ドメイン フォレストが既に存在する場合、HPC Pack をインストールするために、ドメイン ユーザーがローカル管理者ユーザー名として指定されます。
* **AdminPassword**: 管理者のパスワードを指定します。 コマンド ラインで指定しない場合、パスワードを入力するように求められます。
* **HPCImageName** (省略可能): HPC クラスターのデプロイに使用する HPC Pack VM イメージ名を指定します。 これは Azure Marketplace から Microsoft が提供する HPC Pack イメージにする必要があります。 指定しない場合 (ほとんどの場合に推奨)、公開されている最新の [HPC Pack 2012 R2 のイメージ](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)が選択されます。 最新のイメージは、HPC Pack 2012 R2 Update 3 がインストールされた Windows Server 2012 R2 Datacenter に基づいています。
  
  > [!NOTE]
  > 有効な HPC Pack イメージを指定しない場合、デプロイは失敗します。
  > 
  > 
* **LogFile** (省略可能): デプロイ ログ ファイルのパスを指定します。 指定しない場合、スクリプトを実行するコンピューターの一時ディレクトリにログ ファイルが作成されます。
* **Force** (省略可能): すべての確認プロンプトを非表示にします。
* **NoCleanOnFailure** (省略可能): デプロイできなかった Azure VM を削除しません。 デプロイを継続するには、デプロイできなかった VM を手動で削除してからスクリプトを再実行します。そうしないと、デプロイは失敗します。
* **PSSessionSkipCACheck** (省略可能): このスクリプトで VM がデプロイされたすべてのクラウド サービスに対して、Azure により自己署名証明書を自動で生成します。この証明書は、クラウド サービスのすべての VM が既定の Windows リモート管理 (WinRM) 証明書として使用します。 これらの Azure VM に HPC 機能をデプロイするために、スクリプトでは、これらの証明書がローカル コンピューター\\クライアント コンピューターの信頼されたルート証明機関ストアに既定で一時的にインストールされ、スクリプトの実行中、"信頼できない CA" セキュリティ エラーが抑制されます。 証明書はスクリプトの実行後に削除されます。 このパラメーターが指定されている場合、証明書はクライアント コンピューターにインストールされず、セキュリティ警告が非表示になります。
  
  > [!IMPORTANT]
  > 本稼働デプロイの場合、このパラメーターは推奨されません。
  > 
  > 

### <a name="example"></a>例
次の例では、*MyConfigFile.xml* 構成ファイルを使用して HPC Pack クラスターを作成し、クラスターをインストールするための管理者資格情報を指定しています。

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>追加の考慮事項
* このスクリプトでは、任意で、HPC Pack Web ポータルまたは HPC Pack REST API を通してジョブ送信を有効にできます。
* このスクリプトでは、追加ソフトウェアをインストールするか、その他の設定を構成する場合、カスタムの構成前スクリプトと構成後スクリプトを任意で実行できます。

## <a name="configuration-file"></a>構成ファイル
デプロイ スクリプトの構成ファイルは XML ファイルです。 「HPCIaaSClusterConfig.xsd」という名前のスキーマ ファイルは HPC Pack IaaS デプロイ スクリプト フォルダーにあります。 **IaaSClusterConfig** は構成ファイルのルート要素であり、デプロイ スクリプト フォルダーの Manual.rtf ファイルに詳細がある子要素が含まれています。



<!--HONumber=Feb17_HO3-->



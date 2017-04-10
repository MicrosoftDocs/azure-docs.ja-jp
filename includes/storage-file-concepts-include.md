## <a name="what-is-azure-file-storage"></a>Azure File ストレージとは
File ストレージは、標準の SMB 2.1 または SMB 3.0 プロトコルを使用して、アプリケーション用の共有ストレージを提供します。 Microsoft Azure Virtual Machines または Cloud Services では、マウントされている共有を介して、アプリケーション コンポーネント間でファイル データを共有できます。オンプレミスのアプリケーションでは、File ストレージ API を介して、共有内のファイル データにアクセスできます。

Azure Virtual Machines または Cloud Services で実行しているアプリケーションでは、デスクトップ アプリケーションで通常の SMB 共有をマウントする場合と同じように、ファイル ストレージ共有をマウントすることで、ファイル データにアクセスできます。 File ストレージ共有は、任意の数の Azure 仮想マシンまたはロールが同時にマウントしてアクセスすることができます。

File ストレージ共有は SMB プロトコルを使用した Azure での標準ファイル共有であるため、Azure で実行されているアプリケーションは、ファイル I/O API を介して共有内のデータにアクセスできます。 そのため、開発者は、既存のコードとスキルを活用して、既存のアプリケーションを移行することができます。 IT プロフェッショナルは、Azure アプリケーションの管理の一環として、PowerShell コマンドレットを使用して、ファイル ストレージ共有を作成、マウント、管理できます。 このガイドでは、両方の例を紹介します。

File ストレージの一般的な用途には、次のようなものがあります。

* ファイル共有を利用しているオンプレミスのアプリケーションを、コストの高い書き換えを行わずに、Azure Virtual Machines または Cloud Services 上で実行できるように移行する
* 共有のアプリケーション設定を構成ファイルなどに格納する
* ログ、メトリック、クラッシュ ダンプなどの診断データを共有の場所に格納する 
* Azure Virtual Machines または Cloud Services の開発や管理に必要なツールおよびユーティリティを格納する

## <a name="file-storage-concepts"></a>File ストレージの概念
File ストレージには次の構成要素があります。

![files-concepts](./media/storage-file-concepts-include/files-concepts.png)

* **ストレージ アカウント** : Azure Storage にアクセスする場合には必ず、ストレージ アカウントを使用します。 ストレージ アカウントの容量の詳細については、 [Azure Storage の拡張性とパフォーマンスのターゲットに関するページ](../articles/storage/storage-scalability-targets.md) を参照してください。
* **共有** : File ストレージ共有は、Azure 内の SMB ファイル共有です。 
  ディレクトリとファイルはすべて親の共有に作成する必要があります。 アカウントに含まれる共有の数と、共有に格納できるファイル数には制限がなく、ファイル共有の合計容量 5 TB まで増やすことができます。
* **ディレクトリ** : ディレクトリの階層 (オプション)。 
* **ファイル:** 共有内のファイル。 ファイルのサイズの上限は 1 TB です。
* **URL 形式:** ファイルは、次の URL 形式を使用してアドレス指定できます。   
  https://`<storage
  account>`.file.core.windows.net/`<share>`/`<directory/directories>`/`<file>`  
  
  次の例の URL を使用すると、上の図のいずれかのファイルをアドレス指定できます。  
  `http://samples.file.core.windows.net/logs/CustomLogs/Log1.txt`

共有、ディレクトリ、およびファイルの詳しい命名方法については、「 [Naming and Referencing Shares, Directories, Files, and Metadata (共有、ディレクトリ、ファイル、およびメタデータの命名と参照)](http://msdn.microsoft.com/library/azure/dn167011.aspx)」を参照してください。


<!--HONumber=Nov16_HO3-->



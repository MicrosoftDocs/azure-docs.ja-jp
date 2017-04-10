

カスタム スクリプト拡張機能は、導入以来、Windows VM と Linux VM でワークロードを構成するために広く使用されてきました。 Azure リソース マネージャー テンプレートの導入により、VM をプロビジョニングするだけでなく、VM でワークロードも構成する単一のテンプレートを作成できるようになりました。

## <a name="about-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートについて
Azure Resource Manager テンプレートでは、リソース間の依存関係を定義することで、JSON 言語で Azure IaaS インフラストラクチャを宣言によって指定できます。 Azure リソース マネージャー テンプレートの概要については、次の記事をご覧ください。

* [リソース グループの概要](../articles/azure-resource-manager/resource-group-overview.md)
* [Azure PowerShell を使用したテンプレートのデプロイ](../articles/virtual-machines/windows/ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="prerequisites"></a>前提条件
1. お使いのオペレーティング システムの Azure コマンドライン ツールを[ここ](https://azure.microsoft.com/downloads/)からダウンロードします。
2. スクリプトが既存の VM で実行される場合、VM エージェントがその VM 上で有効であることをご確認ください。そうでない場合は、[Linux](../articles/virtual-machines/linux/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json) または [Windows](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) のガイダンスに従ってインストールしてください。
3. VM で実行するスクリプトを Azure Storage にアップロードします。 スクリプトは、1 つか複数のストレージ コンテナーから取得できます。
4. スクリプトを GitHub アカウントにアップロードすることもできます。
5. スクリプトは、拡張機能によって起動されるエントリ スクリプトが、他のスクリプトを順に起動するように記述されている必要があります。

## <a name="using-the-custom-script-extension"></a>カスタム スクリプト拡張機能の使用
テンプレートを使用したデプロイでは、Azure サービス管理 API で使用できるバージョンと同じバージョンのカスタム スクリプト拡張機能を使用します。 拡張機能では、同じパラメーターとシナリオ (Azure ストレージ アカウントまたは GitHub へのファイルのアップロードなど) をサポートします。 テンプレートで使用する際の重要な違いは、拡張機能のバージョンを majorversion.* 形式で指定するのではなく、正確なバージョンを指定する必要があることです。


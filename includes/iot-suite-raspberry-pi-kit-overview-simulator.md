## <a name="overview"></a>概要

このチュートリアルでは、次の手順を実行します。

- リモート監視構成済みソリューションのインスタンスを Azure サブスクリプションにデプロイします。 この手順では、複数の Azure サービスが自動的にデプロイおよび構成されます。
- お使いのコンピューターやリモート監視ソリューションと通信するようにデバイスを設定します。
- サンプル デバイス コードを更新することで、リモート監視ソリューションに接続し、シミュレートされたテレメトリを送信してソリューション ダッシュボードに表示できるようにします。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure サブスクリプションが必要になります。

> [!NOTE]
> アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、[Azure の無料試用版サイト][lnk-free-trial]をご覧ください。

### <a name="required-software"></a>必要なソフトウェア

Raspberry Pi でコマンド ラインにリモートでアクセスするための SSH クライアントがデスクトップ コンピューターに必要です。

- Windows には SSH クライアントは含まれていません。 [PuTTY](http://www.putty.org/) を使用することをお勧めします。
- ほとんどの Linux ディストリビューションと Mac OS には、コマンド ライン SSH ユーティリティが含まれています。 詳細については、「[SSH Using Linux or Mac OS (Linux または Mac OS を使用した SSH 接続)](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)」を参照してください。

### <a name="required-hardware"></a>必要なハードウェア

Raspberry Pi でコマンド ラインにリモート接続するためのデスクトップ コンピューター。

[Raspberry Pi 3 用 Microsoft IoT スタート キット][lnk-starter-kits]または同等のコンポーネント。 このチュートリアルでは、キット内の次のものを使用します。

- Raspberry Pi 3
- microSD カード (NOOBS をインストール済み)
- USB ミニ ケーブル
- イーサネット ケーブル

[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
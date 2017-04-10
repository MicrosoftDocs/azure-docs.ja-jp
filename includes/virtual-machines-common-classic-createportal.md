

*カスタム*仮想マシンとは、**Marketplace** の**おすすめアプリ**を使用して作成する仮想マシンのことです。おすすめアプリを使用すると、ほとんどの作業が自動的に実行されますが、 ユーザーは構成を選択できます。選択できる項目は次のとおりです。

* 仮想ネットワークへ仮想マシンを接続する
* マルウェア対策としてなどで Azure Virtual Machine Agent や Azure Virtual Machine Extensions をインストールする。
* 仮想マシンを既存のクラウド サービスに追加する。
* 仮想マシンを既存のストレージ アカウントに追加する。
* 仮想マシンを可用性セットに追加する。

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> 仮想マシンが仮想ネットワークを使用する場合、仮想マシンの作成時に仮想ネットワークを指定する必要があります。

> * 仮想ネットワークを使用する&2; つの利点は、仮想マシンに直接接続できることと、クロスプレミス接続を設定できることです。

> * 仮想マシンが仮想ネットワークに接続するように設定できるのは、仮想マシンの作成時に限られています。 仮想ネットワークの詳細については、「 [Virtual Network の概要](../articles/virtual-network/virtual-networks-overview.md)」を参照してください。
>
>

## <a name="to-create-the-virtual-machine"></a>仮想マシンを作成するには

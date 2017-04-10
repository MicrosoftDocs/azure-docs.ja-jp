| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| クラウド サービスあたりの[仮想マシン数](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)<sup>1</sup> |50 |50 |
| クラウド サービスあたりの入力エンドポイント数 <sup>2</sup> |150 |150 |

<sup>1</sup>サービス管理 (リソース マネージャーではなく) で作成された仮想マシンは自動的にクラウド サービスに格納されます。 より多くの仮想マシンをそのクラウド サービスに追加することで、負荷分散と可用性を実現できます。 [仮想マシンを仮想ネットワークまたはクラウド サービスと接続する方法](../articles/virtual-machines/linux/classic/connect-vms.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)に関するセクションを参照してください。

<sup>2</sup>入力エンドポイントは、仮想マシンのクラウド サービスの外部から仮想マシンへの通信を可能にします。 同じクラウド サービスまたは仮想ネットワーク内の仮想マシンは自動的に相互に通信できます。 [仮想マシンにエンドポイントをセットアップする方法](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するセクションを参照してください。 


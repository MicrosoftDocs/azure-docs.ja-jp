Azure 仮想マシン (VM) で実行されているアプリケーションを起動できなかったり、アプリケーションに接続できなかったりする理由はいろいろあります。 アプリケーションが適切なポートで実行されていない、適切なポートでリッスンしていない、リスニング ポートがブロックされている、ネットワーク ルールがトラフィックをアプリケーションに正しく渡していない、などの理由です。 この記事では、問題を検出して解決するための系統的アプローチについて説明します。

RDP または SSH を使用した VM への接続で問題が発生している場合は、最初に、次のいずれかの記事を参照してください。

* [Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../articles/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、両方のモデルについて取り上げていますが、最新のデプロイではリソース マネージャー モデルの使用をお勧めします。
> 
> 

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]**をクリックします。

## <a name="quick-start-troubleshooting-endpoint-connectivity-problems"></a>エンドポイント接続問題のトラブルシューティングのクイック スタート
アプリケーションへの接続に問題がある場合は、次の一般的なトラブルシューティング手順を実行してください。 各手順の実行後に、アプリケーションへの接続を再度試してください。

* 仮想マシンを再起動します。
* Recreate the エンドポイント/ファイアウォール規則/ネットワーク セキュリティ グループ (NSG) 規則を再作成します。
  * [従来のモデル - Cloud Services エンドポイントの管理](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [リソース マネージャー モデル - ネットワーク セキュリティ グループの管理](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
* 別の Azure 仮想ネットワークなどの別の場所から接続します。
* 仮想マシンを再デプロイします。
  * [Windows VM の再デプロイ](../articles/virtual-machines/windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
  * [Linux VM の再デプロイ](../articles/virtual-machines/linux/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* 仮想マシンを再作成します。

詳細については、「 [Troubleshooting Endpoint Connectivity (RDP/SSH/HTTP, etc. failures) (エンドポイントの接続のトラブルシューティング (RDP/SSH/HTTP などの障害))](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)」を参照してください。

## <a name="detailed-troubleshooting-overview"></a>詳細なトラブルシューティングの手順
Azure 仮想マシンで実行されているアプリケーションへのアクセスに問題がある場合は、次の 4 つの主要領域からトラブルシューティングを行います。

![troubleshoot cannot start application](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. Azure 仮想マシンで実行されているアプリケーション。
   * アプリケーション自体は正常に実行されているか。
2. Azure 仮想マシン。
   * VM 自体は正常に実行され、要求に応答しているか。
3. Azure のネットワーク エンドポイント。
   * クラシック デプロイメント モデルの仮想マシンのクラウド サービス エンドポイント。
   * Resource Manager デプロイメント モデルの仮想マシンのネットワーク セキュリティ グループと受信 NAT 規則。
   * ユーザーから VM/アプリケーションへのトラフィック フローは、期待どおりのポートで行われているか。
4. インターネット エッジ デバイス。
   * 所定のファイアウォール規則がトラフィック フローを適切に処理しているか。

サイト間 VPN または ExpressRoute 接続を介してアプリケーションにアクセスするクライアント コンピューターの場合、問題が起こる主な領域はアプリケーションと Azure 仮想マシンです。
原因を特定して問題を修正するには、次の手順に従ってください。

## <a name="step-1-access-application-from-target-vm"></a>手順 1: ターゲットの VM からアプリケーションにアクセスする
適切なクライアント プログラムが実行されている VM からそのクライアント プログラムでアプリケーションへのアクセスを試みます。 ローカル ホスト名、ローカル IP アドレス、またはループバック アドレス (127.0.0.1) を使用します。

![start application directly from the VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

たとえば、アプリケーションが Web サーバーである場合は、VM でブラウザーを開き、VM でホストされている Web ページへのアクセスを試行します。

アプリケーションにアクセスできる場合は、 [手順 2](#step2)に進んでください。

アプリケーションにアクセスできない場合は、次の設定をご確認ください。

* アプリケーションがターゲットの仮想マシンで実行されている。
* アプリケーションが正しい TCP  ポートと UDP ポートをリッスンしている。

Windows ベースと Linux ベースの両方の仮想マシンで、 **netstat-a** コマンドを使用してアクティブなリスニング ポートを表示します。 アプリケーションがリッスンしているポートの出力を確認します。 アプリケーションを再起動するか、必要であれば、正しいポートを使用するように構成を変更し、アプリケーションのローカルでのアクセスを試みます。

## <a id="step2"></a> 2: 同じ仮想ネットワークの別の VM からアプリケーションにアクセスする
同じ仮想ネットワーク内の異なる VM からアプリケーションにアクセスしてみます。その VM のホスト名または Azure 割り当てのパブリック、プライベート、またはプロバイダー IP アドレスを使用します。 クラシック デプロイ モデルを使用して作成された VM の場合、クラウド サービスのパブリック IP アドレスは使用しないでください。

![start application from a different VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

たとえば、アプリケーションが Web サーバーである場合は、同じ仮想ネットワーク内の別の VM 上のブラウザーから、Web ページへのアクセスを試行します。

アプリケーションにアクセスできる場合は、 [手順 3](#step3)に進んでください。

アプリケーションにアクセスできない場合は、次の設定をご確認ください。

* ターゲット VM 上のホスト ファイアウォールで、リクエスト受信と応答送信のトラフィックが許可されている。
* ターゲット VM で実行されている侵入検出ソフトウェアやネットワーク監視ソフトウェアで、トラフィックが許可されている。
* Cloud Services エンドポイントまたはネットワーク セキュリティ グループでトラフィックが許可されている。
  * [従来のモデル - Cloud Services エンドポイントの管理](../articles/cloud-services/cloud-services-enable-communication-role-instances.md)
  * [リソース マネージャー モデル - ネットワーク セキュリティ グループの管理](../articles/virtual-network/virtual-networks-create-nsg-arm-pportal.md)
* テスト VM と VM 間のパスにある VM 内で実行されている別のコンポーネント (ロード バランサーやファイアウォールなど) で、トラフィックが許可されている。

Windows ベースの仮想マシンについては、セキュリティ強化機能搭載 Windows ファイアウォールを使用して、ファイアウォール規則がアプリケーションの受信トラフィックと送信トラフィックを除外していないかどうかを確認してください。

## <a id="step3"></a>手順 3: 仮想ネットワーク外からアプリケーションにアクセスする
アプリケーションが実行されている VM の仮想ネットワーク外のコンピューターから、アプリケーションへのアクセスを試行してください。 元のクライアント コンピューターには別のネットワークを使用します。

![start application from a computer outside the virtual network](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

たとえば、アプリケーションが Web サーバーである場合は、仮想ネットワーク内にないコンピューターで実行されているブラウザーから、Web ページへのアクセスを試行します。

アプリケーションにアクセスできない場合は、次の設定をご確認ください。

* クラシック デプロイメント モデルを使用して作成された VM:
  
  * VM のエンドポイント構成で、着信トラフィック (特にプロトコル (TCP または UDP) とパブリックおよびプライベート ポート番号) が許可されているかどうかを確認します。
  * エンドポイント上のアクセス制御リスト (ACL) によって、インターネットからの着信トラフィックが遮断されていないかどうかを確認します。
  * 詳細については、[仮想マシンに対してエンドポイントを設定する方法](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に関するページを参照してください。
* Resource Manager デプロイメント モデルを使用して作成された VM:
  
  * VM の受信 NAT 規則構成で、着信トラフィック (特にプロトコル (TCP または UDP) とパブリックおよびプライベート ポート番号) が許可されているかどうかを確認します。
  * ネットワーク セキュリティ グループで、リクエスト受信と応答送信のトラフィックが許可されているかどうかを確認します。
  * 詳細については、「 [ネットワーク セキュリティ グループ (NSG) について](../articles/virtual-network/virtual-networks-nsg.md)

仮想マシンまたはエンドポイントが負荷分散セットのメンバーである場合:

* プローブ プロトコル (TCP または UDP) とポート番号が正しいことを確認してください。
* プローブ プロトコルとポートが、負荷分散セットのプロトコルおよびポートと異なる場合は、次の点を確認してください。
  * アプリケーションがプローブ プロトコル (TCP または UDP) とポート番号をリッスンしている (ターゲット VM で **netstat – a** を使用します)。
  * ターゲット VM 上のホスト ファイアウォールで、プローブ リクエスト受信とプローブ応答送信のトラフィックが許可されていることを確認します。

アプリケーションにアクセスできる場合は、インターネット エッジ デバイスで次のトラフィックが許可されていることを確認してください。

* クライアント コンピューターから Azure 仮想マシンへのアプリケーション リクエスト送信トラフィック。
* Azure 仮想マシンからのアプリケーション応答受信トラフィック。

## <a name="additional-resources"></a>その他のリソース
[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Linux ベースの Azure 仮想マシンに対する Secure Shell (SSH) 接続のトラブルシューティング](../articles/virtual-machines/linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


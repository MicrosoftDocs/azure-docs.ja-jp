Azure で新たに 2 つのデバッグ機能が利用できるようになりました。Azure Virtual Machines Resource Manager デプロイメント モデルでコンソール出力とスクリーンショットがサポートされます。 

独自のイメージを Azure に導入した場合に限らず、いずれかのプラットフォーム イメージをブートするときでも、仮想マシンは、さまざまな理由で起動できない状態に陥ることがあります。 これらの機能を利用することで仮想マシンを簡単に診断し、起動エラーから復旧することができます。

Linux 仮想マシンについては、コンソール ログの出力をポータルから簡単に表示することができます。

![Azure Portal](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
一方、Windows 仮想マシンと Linux 仮想マシンの両方に関して、ハイパーバイザーからの VM のスクリーンショットを表示することもできます。

![エラー](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

この 2 つの機能は、すべてのリージョンの Azure Virtual Machines でサポートされます。 スクリーンショットと出力結果がストレージ アカウントに表示されるまでに最大 10 分かかる場合があります。

## <a name="common-boot-errors"></a>一般的な起動エラー

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [An operating system wasn't found (オペレーティング システムが見つかりませんでした)](https://support.microsoft.com/help/4010142)
- [Boot failure or INACCESSIBLE_BOOT_DEVICE (起動エラーまたは INACCESSIBLE_BOOT_DEVICE)](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>新しい仮想マシンで診断を有効にする
1. プレビュー ポータルから新しい仮想マシンを作成するとき、デプロイメント モデルのドロップダウンから **[Azure Resource Manager]** を選択します。
 
    ![リソース マネージャー](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. [監視] のオプションを構成し、これらの診断ファイルの格納先となるストレージ アカウントを選択します。
 
    ![VM を作成する](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. Azure Resource Manager テンプレートからデプロイする場合は、ご利用の仮想マシン リソースに移動し、診断プロファイル セクションを追加します。 必ず "2015-06-15" という API バージョン ヘッダーを使用してください。

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. これらのログの格納先となるストレージ アカウントは、診断プロファイルによって選択することができます。

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

ブート診断が有効な状態でサンプル仮想マシンをデプロイするには、こちらのリポジトリをチェックアウトしてください。

## <a name="update-an-existing-virtual-machine"></a>既存の仮想マシンを更新する ##

ポータルからブート診断を有効にするために、既存の仮想マシンをポータルで更新することもできます。 [ブート診断] オプションを選択し、[保存] を選択します。 VM を再起動すると変更が反映されます。

![既存の VM の更新](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)


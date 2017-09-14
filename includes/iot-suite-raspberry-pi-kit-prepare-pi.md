## <a name="prepare-your-raspberry-pi"></a>Raspberry Pi を準備する

### <a name="install-raspbian"></a>Raspbian をインストールする

初めて Raspberry Pi を使用する場合、NOOBS を使用して、キットに含まれている SD カードに Raspbian オペレーティング システムをインストールする必要があります。 「[Raspberry Pi Software Guide (Raspberry Pi ソフトウェア ガイド)][lnk-install-raspbian]」では、Raspberry Pi にオペレーティング システムをインストールする方法について説明しています。 このチュートリアルでは、Raspberry Pi に Raspbian オペレーティング システムをインストール済みであると想定しています。

> [!NOTE]
> [Raspberry Pi 3 用 Microsoft Azure IoT スタート キット][lnk-starter-kits]に含まれる SD カードには、既に NOOBS がインストールされています。 このカードから Raspberry Pi を起動し、Raspbian OS をインストールできます。

### <a name="set-up-the-hardware"></a>ハードウェアを設定する

このチュートリアルでは、[Raspberry Pi 3 用 Microsoft Azure IoT スタート キット][lnk-starter-kits]に含まれる BME280 センサーを使用して、テレメトリ データを生成します。 ここでは、LED を使用して、Raspberry Pi がソリューション ダッシュボードからのメソッドの呼び出しをいつ処理するかを確認します。

ブレッドボード上のコンポーネントは次のとおりです。

- 赤色 LED
- 220 Ω の抵抗 (赤色、赤色、茶色)
- BME280 センサー

次の図は、ハードウェアを接続する方法を示しています。

![Raspberry Pi 用のハードウェア設定][img-connection-diagram]

次の表は、Raspberry Pi からブレッドボード上のコンポーネントへの接続の概要です。

| Raspberry Pi            | ブレッドボード             |色         |
| ----------------------- | ---------------------- | ------------- |
| GND (ピン 14)            | LED 陰極ピン (18A)      | 紫          |
| GPCLK0 (ピン 7)          | 抵抗 (25A)         | オレンジ          |
| SPI_CE0 (ピン 24)        | CS (39A)               | 青          |
| SPI_SCLK (ピン 23)       | SCK (36A)              | 黄        |
| SPI_MISO (ピン 21)       | SDO (37A)              | 白         |
| SPI_MOSI (ピン 19)       | SDI (38A)              | 緑         |
| GND (ピン 6)             | GND (35A)              | 黒         |
| 3.3 V (ピン 1)           | 3Vo (34A)              | 赤           |

ハードウェアの設定を完了するには、以下のことを行う必要があります。

- Raspberry Pi を、キットに含まれている電源装置に接続します。
- キットに含まれているイーサネット ケーブルを使用して、Raspberry Pi をネットワークに接続します。 別の方法として、Raspberry Pi 用の[ワイヤレス接続][lnk-pi-wireless]を設定することもできます。

これで、Raspberry Pi のハードウェアの設定が完了しました。

### <a name="sign-in-and-access-the-terminal"></a>ターミナルにサインインまたはアクセスする

Raspberry Pi でターミナル環境にアクセスする方法は 2 とおりあります。

- Raspberry Pi に接続されているキーボードとモニターがある場合は、Raspbian GUI を使用してターミナル ウィンドウにアクセスできます。

- SSH を使用して、デスクトップ コンピューターから Raspberry Pi でコマンド ラインにアクセスします。

#### <a name="use-a-terminal-window-in-the-gui"></a>GUI でターミナル ウィンドウを使用する

Raspbian の既定の資格情報は、**pi** というユーザー名と **raspberry** というパスワードです。 GUI のタスク バーでは、モニターのようなアイコンを使用して、**[Terminal (ターミナル)]** ユーティリティを起動できます。

#### <a name="sign-in-with-ssh"></a>SSH を使用してサインインする

Raspberry Pi へのコマンド ライン アクセスに SSH を使用できます。 「[SSH (Secure Shell)][lnk-pi-ssh]」では、Raspberry Pi で SSH を構成する方法と、[Windows][lnk-ssh-windows] または [Linux および Mac OS][lnk-ssh-linux] から接続する方法を説明しています。

**pi** というユーザー名と **raspberry** というパスワードでサインインします。

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>省略可能: Raspberry Pi でフォルダーを共有する

必要に応じて、デスクトップ環境を使用して Raspberry Pi でフォルダーを共有することができます。 フォルダーを共有すると、`nano` や `vi` を使用しなくても、任意のデスクトップ テキスト エディター ([Visual Studio Code](https://code.visualstudio.com/) や [Sublime Text](http://www.sublimetext.com/) など) を使用して、Raspberry Pi でファイルを編集できるようになります。

Windows でフォルダーを共有するために、Raspberry Pi で Samba サーバーを構成します。 別の方法として、デスクトップの SFTP クライアントで、組み込みの [SFTP](https://www.raspberrypi.org/documentation/remote-access/) サーバーを使用することもできます。

### <a name="enable-spi"></a>SPI を有効にする

サンプル アプリケーションを実行できるようにするには、Raspberry Pi でシリアル ペリフェラル インターフェイス (SPI) バスを有効にする必要があります。 Raspberry Pi は、SPI バス経由で BME280 センサー デバイスと通信します。 次のコマンドを使用して、構成ファイルを編集します。

```sh
sudo nano /boot/config.txt
```

次の行を見つけます。

`#dtparam=spi=on`

- 行をコメント解除するには、先頭の `#` を削除します。
- 変更を保存し (**Ctrl + O** キー、**Enter** キー)、エディターを終了します (**Ctrl + X** キー)。
- SPI を有効にするために、Raspberry Pi を再起動します。 再起動するとターミナルとの接続が切断されるので、Raspberry Pi が再起動したらもう一度サインインする必要があります。

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/
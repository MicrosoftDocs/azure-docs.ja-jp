---
title: "MyDriving Azure IoT の例 - クイック スタート | Microsoft Docs"
description: "Microsoft Azure を使用して、Stream Analytics、Machine Learning、Event Hubs などの IoT システムを設計する方法を総合的に示すアプリを初めて使用します。"
services: 
documentationcenter: .net
suite: 
author: harikmenon
manager: douge
ms.assetid: f40ea71b-5721-4a6b-a886-53c2e9dffe8f
ms.service: multiple
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2016
ms.author: harikm
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: c6366d42b91748963ec14c258f92746e5d2fde92


---
# <a name="mydriving-iot-system-quick-start"></a>MyDriving IoT システム: クイック スタート
MyDriving は、デバイスからテレメトリを収集し、そのデータをクラウドで処理して、機械学習を適用することで状況に適した応答を返すという、典型的な [モノのインターネット](iot-suite-overview.md) (IoT) ソリューションの設計と実装を示すシステムです。 このシステムは、ユーザーの携帯電話と、自動車の制御システムから情報を取得するアダプターの両方からのデータを使用して、車の走行に関するデータを記録します。 また、このデータを基に、ユーザーの運転スタイルを他のユーザーと比較してフィードバックします。

MyDriving の真の目的は、皆さんが独自の IoT ソリューションを作成する出発点になることです。 しかしその前に、私たちのテスト ユーザー チームの一員として、MyDriving アプリそのものをご覧いただくことしましょう。 そうすることで、アーキテクチャを詳しく検討する前に、コンシューマーとして、このアプリケーションとシステムに触れることができます。 さらに、テスト ユーザーに対するアルファ版やベータ版のアプリ配布を管理する優れたツール、HockeyApp をご紹介します。

## <a name="use-the-mobile-experience"></a>モバイル機器での利用
Android、iOS、または Windows 10 デバイスをお持ちの場合、MyDriving アプリをお使いいただけます。

### <a name="android-and-windows-10-mobile-installation"></a>Android および Windows 10 Mobile でのインストール
ご利用のデバイスで以下を実行してください。

1. 開発アプリを許可します。
   
   * Android: **[設定]** > **[セキュリティ]** に移動し、**[提供元不明のアプリ]** でアプリを許可します。
   * Windows 10: **[設定]** > **[更新]** > **[開発者向け]** の順に移動し、**[開発者モード]** を設定します。
2. [HockeyApp](https://rink.hockeyapp.net)でサインアップするか、HockeyApp にサインインして、ベータ テスト チームに参加します。 HockeyApp を使用すると、簡単にアプリの最新リリースがテスト ユーザーに配布されます。
   
   Windows 10をお使いの場合は、Microsoft Edgeブラウザーをご使用ください。
   
   Build 2016 参加者の場合は、いずれかの Microsoft ボタンを押して、カンファレンスに登録したものと同じ Microsoft アカウント電子メールでサインインします。 HockeyApp でサインアップ済みです。
   
   ![HockeyApp サインイン画面](./media/iot-solution-get-started/image1.png)
3. 次の場所からアプリをダウンロードしてインストールします。
   
   * [Android](http://rink.io/spMyDrivingAndroid)
   * [Windows 10](http://rink.io/spMyDrivingUWP)
   
   2 つのアイテムがあります。 **[信頼されたユーザー]**で証明書をインストールします。 次にアプリをインストールします。

*Windows 10 Mobile でのアプリの起動で問題が発生しますか?*  ご使用の携帯電話で、ここ 1 ～ 2 回の更新が行われていない可能性があります。 最新の更新プログラムが適用されていることを確認し、そうでない場合は以下をインストールしてください。

* [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 
* [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 
* [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)

### <a name="ios-installation"></a>iOS でのインストール
Build 2016 の参加者は、私たちのテスト チームのメンバーとして、HockeyApp でアプリをダウンロードしてください。

1. ご使用の iOS デバイスで、[HockeyApp](https://rink.hockeyapp.net) にサインインします。
   いずれかの Microsoft サインイン ボタンを押して、カンファレンスに登録したものと同じ Microsoft アカウント電子メールでサインインします (メールとパスワードのフィールドは使用しないでください)。
   
   ![HockeyApp サインイン画面](./media/iot-solution-get-started/image1.png)
2. HockeyApp ダッシュボードで MyDriving を選択し、ダウンロードします。
3. 次の手順で、HockeyApp から入手したベータ版を承認します。
   
   a. **[設定]** > **[全般]** > **[プロファイルとデバイスの管理]** の順に選択します。
   
   b. **Bit Stadium GmbH** の証明書を信頼します。

Build 2016年に参加していない場合は、アプリを自身で構築してデプロイします。

1. [GitHub から]コードをダウンロードします。
2. [Xamarin を使用]して、ビルドとデプロイを実行します。

『 [MyDriving Reference Guide (MyDriving リファレンス ガイド)](http://aka.ms/mydrivingdocs)』で詳細をご確認ください。

## <a name="get-an-obd-adapter-optional"></a>OBD アダプターの入手 (オプション)
これは真のモノのインターネット システムを実現するパーツです。 これなしでもアプリは使用できますが、実際のモノと組み合わせた方がおもしろく、それでいて費用はかさみません。

オンボード診断 (OBD) は自動車に備わった機能であり、整備工場で調整を行ったり、異音や警告ランプの原因を診断したりするために使用されます。 年代物の車でなければ、ソケットが車室内のどこかに配置されており、通常はダッシュボード下方のパネルの後ろにあります。 適切なコネクタを使用して、エンジン性能の指標を収集し、特定の調整を行うことができます。 OBD コネクタは取扱い店が多く、安価で購入できます。 Bluetooth または Wi-Fi を使用して、携帯電話のアプリに接続します。

ここで私たちがやろうとしているのは、自動車をクラウドに接続しようという試みです。 OBD と直接接続するのは携帯電話ですが、アプリがリレーとして機能します。 自動車のテレメトリは MyDriving IoT Hub に直接送られ、そこで走行経路の記録と運転スタイルの評価が行われます。

OBD デバイスを接続するには、以下を実行します。

1. 車に OBD ソケットがあることを確認します。
2. OBD アダプターを入手します。
   
   * Android または Windows Phone を使用している場合は、Bluetooth 対応の OBD II アダプターが必要です。 私たちは、 [BAFX Products 34t5 Bluetooth OBDII Scan Tool]を使用しました。
   * iOS 搭載の携帯電話を使用している場合は、Wi-Fi 対応の OBD アダプターが必要です。 私たちは、 [ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]を使用しました。
3. OBD アダプターに付属の指示に従い、携帯電話に接続します。 次の点に注意してください。
   
   * Bluetooth アダプターは **[設定]** ページで携帯電話とペアリングする必要があります。
   * Wi-Fi アダプターには、192.168.xxx.xxx の範囲のアドレスを設定する必要があります。
4. 複数台の車両で利用する場合は、それぞれに別個のアダプターを用意してください (3 個まで)。

OBD アダプターがない場合でも、アプリは携帯電話の GPS レシーバーで取得した位置情報と速度データをバックエンドに送信します。さらに、OBD をシミュレートするかどうかの確認が行われます。

アプリによる OBD アダプターのデータの使用方法と、独自の OBD デバイスを作成するオプションについては、『 [MyDriving Reference Guide (MyDriving リファレンス ガイド)](http://aka.ms/mydrivingdocs)』のセクション 2.1「IoT Devices (IoT デバイス)」を参照してください。

## <a name="use-the-app"></a>アプリの使用
アプリを起動します。 初回のクイックスタートで、機能をひととおり確認できます。

### <a name="track-your-trips"></a>走行をトラッキングします。
走行を開始するときは記録ボタン (画面下部の大きな赤い丸) をタップし、終了するときは再びタップします。

![走行のトラッキングの記録ボタンの図](./media/iot-solution-get-started/image2.png)

OBD デバイスを使用していない場合は、走行を開始するたびに、シミュレーターを使用するかどうかの確認が行われます。

走行終了時に停止ボタンをタップすると、下の図のような概要が示されます。

![走行の概要の例](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>走行内容を確認します。
![過去の走行の例](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>自分のプロファイルを確認します。
![運転スタイル プロファイルの例](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>テスト フィードバックを送信します。
MyDriving は、独自の IoT システムの開発を支援するために作成されました。そのため、このアプリの動作に関するコメントは大歓迎です。 次のような場合は、ぜひお知らせください。

* 問題または課題が生じた場合。
* シナリオに合わせて拡張できる点がある場合。
* より効率的に特定のニーズを達成する方法を見つけた場合。
* MyDriving やこのドキュメントの改善につながるご提案がある場合。

MyDriving アプリ内では、組み込みの HockeyApp フィードバック メカニズムを利用できます。iOS と Android では端末を単にシェイクするか、**[フィードバック]** メニュー コマンドを使用してください。 このとき自動でスクリーンショットが添付され、こちらで皆さんの伝達内容を把握することができます。 運悪くクラッシュした場合は、HockeyApp によってクラッシュ ログが収集され、通知されます。 [HockeyApp ポータル]からフィードバックを送ることもできます。

[GitHub の Issue (イシュー)] としてアップするか、下部にコメントを残すこともできます (en-us 版)。

皆様のご意見をお待ちしております。

## <a name="next-steps"></a>次のステップ
* 『 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs) 』を読み、MyDriving システム全体の設計と構築方法について把握します。
* [独自のシステムを作成し、デプロイします](iot-solution-build-system.md) 。 『 [MyDriving リファレンス ガイド](http://aka.ms/mydrivingdocs) 』は、ほとんどのカスタマイズで対象となる領域についても説明しています。

[GitHub から]: https://github.com/Azure-Samples/MyDriving
[Xamarin を使用]: https://developer.xamarin.com/guides/ios/getting_started/installation/
[BAFX Products 34t5 Bluetooth OBDII Scan Tool]: http://www.amazon.com/gp/product/B005NLQAHS
[ScanTool OBDLink MX Wi-Fi: OBD Adapter/Diagnostic Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
[HockeyApp ポータル]: https://rink.hockeyapp.org
[GitHub の Issue (イシュー)]: https://github.com/Azure-Samples/MyDriving/issues



<!--HONumber=Nov16_HO3-->



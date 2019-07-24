---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: e72c4b0cf8f77a057ff07f8bce7acae4e834e28d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59503252"
---
## <a name="setting-up-your-ios-application"></a>iOS アプリケーションをセットアップする

このセクションでは、新しいプロジェクトの作成方法について順を追って説明し、iOS アプリケーション (Swift) に *"Microsoft でサインイン"* を統合して、トークンを必要とする Web API でクエリを実行できるようにする方法を示します。

> 代わりにこのサンプルの XCode プロジェクトをダウンロードすることもできます。 [プロジェクトをダウンロード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)したら[構成](#register-your-application)手順に進み、実行前にコード サンプルを構成します。

## <a name="install-carthage-to-download-and-build-msal"></a>Carthage をインストールし、MSAL をダウンロードしてビルドする

Carthage パッケージ マネージャーは MSAL のプレビュー期間中に使用されます。XCode との統合が行われますが、その間、Microsoft はライブラリを変更できます。

- Carthage の最新リリースを[ここ]からダウンロードし、インストールします(https://github.com/Carthage/Carthage/releases "Carthage ダウンロード URL")。

## <a name="creating-your-application"></a>アプリケーションの作成

1. Xcode を開き、**[Create a new Xcode project (新しい Xcode プロジェクトを作成)]** を選択します。
2. **[iOS] > [Single View Application (単一ビュー アプリケーション)]** を選択し、**[次へ]** を選択します。
3. 製品に名前を付け、**[次へ]** を選択します。
4. アプリを作成するフォルダーを選択し、*[作成]* をクリックします

## <a name="build-the-msal-framework"></a>MSAL フレームワークをビルドする

下の手順に従い、Carthage を利用して MSAL ライブラリの最新版をプルし、ビルドします。

1. Bash ターミナルを開き、アプリのルート フォルダーに移動します。
2. 下のコマンドをコピーし、バッシュ ターミナルに貼り付け、‘Cartfile’ ファイルを作成します

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
下のコマンドをコピーして貼り付けます。 このコマンドは依存性を取り出して Carthage/Checkouts フォルダーに入れ、MSAL ライブラリをビルドします。
</li>
</ol>

```bash
carthage update
```

> 上記のプロセスは Microsoft Authentication Library (MSAL) のダウンロードとビルドに使用されます。 MSAL は、Microsoft ID プラットフォームによって保護されている API シリーズへのアクセスに使用されるユーザー トークンの取得、キャッシュ、および更新を処理します。

## <a name="add-the-msal-framework-to-your-application"></a>MSAL フレームワークをアプリケーションに追加する

1. Xcode で、**[General (全般)]** タブを開きます。
2. **[Linked Frameworks and Libraries (リンクされたフレームワークとライブラリ)]** セクションに移動し、**+** を選択します。
3. **[Add other... (その他を追加...)]** を選択します。
4. **[Carthage] > [Build (ビルド)] > [iOS] > [MSAL.framework]** の順に選択し、**[Open (オープン)]** を選びます。 `MSAL.framework` が一覧に追加されるはずです。
5. **[Build Phases (ビルド フェーズ)]** タブに移動し、**+** アイコンを選択してから、**[New Run Script Phase (新しい実行スクリプト フェーズ)]** を選択します。
6. *スクリプト領域*に次の内容を追加します

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
<code>+</code> をクリックし、<code>Input Files</code> に次の内容を追加します
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>アプリケーションの UI を作成する

Main.storyboard ファイルは、プロジェクト テンプレートの一部として自動的に作成されます。 アプリの UI を作成するには、以下の手順に従います。

1.  Ctrl キーを押しながら `Main.storyboard` をクリックしてコンテキスト メニューを表示し、次をクリックします: `Open As` > `Source Code`
2.  `<scenes>` ノードを下のコードに変更します

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```

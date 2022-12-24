---
title: "Sigfox通信をAzure IoTで可視化する"
emoji: "😀"
type: "tech"
topics: ["20220501","sigfox","azure"]
published: true
---
# Azure IoT hubの設定
Azure PortalでIoT hubの追加を行い、下記設定にします。
![](/images/20220501_sigfox_azure/1.png)

今回は無料枠で使用するので、管理タブの価格とスケールをFreeレベルに変更します。
![](/images/20220501_sigfox_azure/2.png)

デプロイが完了したら、「デバイス」を選択してデバイスの追加を行います。
![](/images/20220501_sigfox_azure/3.png)

作成したIoTHub-nao-sigfoxの共有アクセスポリシーを選択し、「iothubowner」の管理画面を開きます。
表示されるプライマリキーを使用するのでコピーしておきます。
![](/images/20220501_sigfox_azure/4.png)

# Sigfox Backend Cloud側の設定
Event Hubを追加します。
![](/images/20220501_sigfox_azure/5.png)
DeviceのCallback設定画面で「Azure IoT Hub」を選択し、下記のようにプライマリキーを入力します。
JSON bodyには下記を記載します。
```
{ "device" : "{device}", "data" : "{data}", "time" : "{time}", "seqNumber" : "{seqNumber}" } 
```
![](/images/20220501_sigfox_azure/6.png)

Sigfox通信が行われるとAzure IoT hubのデバイス上に新たにデバイスが追加されていることがわかります。

# Parse
EventHubを下記のように設定します。
![](/images/20220501_sigfox_azure/7.png)

Function App（関数アプリ）を作成し、関数を下記のように設定します。






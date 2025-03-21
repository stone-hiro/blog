---
title: SharepointのOfficeファイルを開くと出る「潜在的なセキュリティの問題」画面をIntuneで抑制する
date: 2025-03-21 10:21:34
tags:
---
この記事では、SharepointのOfficeファイルを開くと出る「このコンテンツは潜在的なセキュリティの問題を示します。このコンテンツを信頼しますか？」の画面をIntuneで抑制する方法について考えます。

<!– toc –>

<!-- more -->


### 潜在的なセキュリティの問題とは

わかりやすい解説を他の方が出されているのでご紹介します。

[https://gintachan.com/sharepoint-excel-content-security-alert-popup:embed:cite]



また、Microsoft 365管理センターを閲覧できる方は下記の案件IDでアドバイザリが公開されています。

* OD843843（一部のユーザーは、OneDrive for Business で一部の Office ファイルを開くときに、セキュリティ警告プロンプトを誤って受け取る場合があります）
* SP843810（一部のユーザーは、SharePoint Online で Excel または PowerPoint ファイルを開くときに、セキュリティ警告プロンプトを誤って受け取る場合があります）

私の方でも内容を簡単にまとめます。

* 当該問題は、SharepointもしくはOnedrive for BusinessでWord・Powerpoint・Excelのファイルを開く時に発生する
* 最近実装した別の問題に対する修正対応が原因
* 2024年8月13日(UTC)に修正プログラムを展開予定

![ファイルを開いた際に表示される警告画面](/2025/03/21/sharepoint-potential-issue/warning-image.png)



修正プログラムが展開されるまでは警告ダイアログが出続けてしまいますが、その前に下記の方法で抑制することが可能です。

> 信頼済みサイトの警告を抑制するために、テナントはインターネット ゾーン機能に信頼済みサイトとホスト名を追加できます。
> 
> <cite>「Microsoft管理センター - サービス正常性 - 案件ID：OD843843より」</cite>

### Intune構成プロファイルを設定して警告画面を抑制する

警告画面を抑制する方法は「インターネット ゾーン機能に信頼済みサイトとホスト名を追加」することです。  
各ユーザが上記の設定を行うこともできますが、Intuneで端末を管理している場合には設定を構成プロファイルで配信できます。

設定方法は下記のとおりです。

(1) 構成設定からプロファイルを作成  

* プラットフォーム：Windows10以降
* プロファイルの種類：設定カタログ

![Windows10以降 - 設定カタログのプロファイルを作成](/2025/03/21/sharepoint-potential-issue/setting01.png)

(2) プロファイルの名前を記載

![プロファイル名の設定](/2025/03/21/sharepoint-potential-issue/setting02.png)


(3) 設定する項目を選択

設定ピッカーから信頼済みサイトに関連する設定項目を選択します。  

* 「Windows コンポーネント > Internet Explorer > インターネット コントロール パネル > セキュリティ ページ」を選択します
  * zoneと検索すると見つけやすいです
* 「Site to Zone Assignment List」の項目にチェックを入れます

![設定する項目を検索](/2025/03/21/sharepoint-potential-issue/setting03.png)


(4) 信頼済みリストの割り当てを実施

* 「Site to Zone Assignment List」をEnabledにします
* 「ここにゾーンの割り当てを入力してください。」に信頼済みサイトのリストを指定します。
  * NameにはサイトのURL、Valueには2を指定します((value=2は信頼済みサイトとして割り当てることを意味します。参考サイト：https://letsconfigmgr.com/mem-deploying-trusted-sites/))。

![信頼済みサイトを指定する](/2025/03/21/sharepoint-potential-issue/setting04.png)

あとは、割り当ての設定等を行って完了です。  
実際に私の検証環境でテストしましたが、設定後は警告画面が表示されることなくファイルを開ける様になりました。  
また、Intuneで配信した設定が端末側で反映されていることも確認できました。

![信頼済みサイトを指定する](/2025/03/21/sharepoint-potential-issue/setting05.png)


今回は以上になります。ここまで見ていただきありがとうございました。  

### 更新履歴
* 2025/03/21：記事公開
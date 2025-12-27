---
title: Asterisk×ガラケーで家の電話を純正アプリから送受信したい！！
tags:
  - asterisk
private: false
updated_at: '2025-12-28T04:29:50+09:00'
id: 8231c91b50061f75d62e
organization_url_name: null
slide: false
ignorePublish: false
---
# Asterisk×ガラケーで家の電話を純正アプリから送受信したい！！
## 皆さんは家電話を何で受けていますか？

一般のというかとりあえず家に家電があるという方は普通のPanasonicやPioneerのものがとりあえずあるという形だと思います。
私も家の電話は代々PanasonicのFax機能付きのモデルを使っていました。
### ん？そもそも家に電話がないって？ケータイで十分だって？
### ロマンが足りないですね〜
私も半分いらないな〜って思いながらも維持しているのですが、私よりも家族の人間が家の電話がないと友人や親戚と連絡が取りにくいということなので維持しています。
それとほぼ同じ理由で家族の人間が仕事で使うのでFax機能も維持しています。

### ただぁ！
### 家の電話って家でしか送受信できないんですよ

普通に考えて出先で電話を受けられないって不便ですよね？
出先からも電話かけたいし受けたいですよね？

#### ではどうしましょうか？
## 家の電話にAsteriskを導入してスマホから家の電話を送受信できるようにしましょう！
### 皆さんの家の電話はどこから来ていますか？
ほとんどの人はNTTの設備、HGWなどからRJ11(電話線)を使ってPanasonicの電話機に繋がっていると思います。
私の家でもRX-600KIが光ファイバーから光電話という形で電話回線を収容しています。
#### おーそーらーく、ほとんどの人がRXシリーズから光電話から変換で電話設備に繋がっていると思います。
というかもうNTTはメタルのみの[公衆交換電話網（PSTN）](https://www.ntt-west.co.jp/denwa/2035denwa/)での固定電話サービスの提供を終了し今後はIP回線を利用した回線へと変わっていきます。
では単純にIP電話をAsteriskに収容してみましょう
#### 電話設定→内線設定 からIP電話の設定を始めましょう！!
![RX-600KI](https://raw.githubusercontent.com/IT-F-30/qiita-content/main/image/RX-600KI.png)
このメニューまできたら
- 内線番号(任意)
- ニックネーム(任意)
- 端末属性(任意)
- MACアドレス(AsteriskサーバのMACアドレス)
- ダイジェスト認証(行う)
- ユーザID(Astersikのpjsip.confに書かれたもの)
- パスワード(Astersikのpjsip.confに書かれたもの)

を書いて**設定**をクリックすればOKです。

#### かなり簡単じゃないですか？

これくらいの設定ならやってみたいと思いませんか？

## Asteriskの設定内容の紹介
順番が前後しましたがAsteriskの設定を紹介します。
これは一例なので実際にやるときはIPアドレスの設定を変更してください

### pjsip.conf
```
; -------------------------------------------------------------
; --- HGW (ひかり電話) トランク設定 ---
; -------------------------------------------------------------
[hgw-registration]
type=registration
transport=simpletrans
outbound_auth=hgw-auth
server_uri=sip:192.168.0.1
client_uri=sip:4@192.168.0.1
retry_interval=60

[hgw]
type=endpoint
transport=simpletrans
context=from-hgw
disallow=all
allow=ulaw
outbound_auth=hgw-auth
aors=hgw-aor
direct_media=no
from_user=4
from_domain=192.168.0.1
dtmf_mode=inband
fax_detect=yes

[hgw-auth]
type=auth
auth_type=userpass
password=6000
username=6000

[hgw-aor]
type=aor
contact=sip:192.168.0.1:5060

[hgw-identify]
type=identify
endpoint=hgw
match=192.168.0.1
```
HGW(RX-500KI)のIPアドレスは`192.168.0.1`なのでここをご自分のHGWのIPアドレスに変更してください

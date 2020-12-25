# Pingコマンドの実装
## Pingコマンドは何をしているか

pingコマンドは**ICMP Echo Request**というパケットを宛先のIPアドレスに送りつけて、その後 **ICMP Echo Reply**というパケットが返ってくるかを見る。
ICMPとは、Internet Control Message Protocolの略でインターネット通信の制御に使われるメッセージのやりとりをするプロトコルである。

```mermaid
graph LR
    Clinet[192.0.4.12]
    Target[192.0.4.14]
    Clinet --ICMP Echo Request--> Target
    Target --ICMP Echo Reply--> Clinet
```

大まかの処理は以下の通り
- 対象のIPアドレス宛にICMPパケットを送信する
- ICMPパケットが返答されるのを待ち、届いたら画面表示する

## システムコールを確認
以下のコマンドでpingコマンドを実行した際に呼び出されるシステムコールを確認する。
結果はping.trace.txtに書き込む。
```shell
// 182.22.59.229はyahoo.co.jpのIPアドレス
sudo strace -o ping.trace.txt ping -c 3 182.22.59.229
```

143行目から157行目でsendto、recvmsg、writeが3回繰り返されていることがわかる。 sendtoはパケットの送信、recvmsgはパケットの受信、writeは出力、をそれぞれ担っている。

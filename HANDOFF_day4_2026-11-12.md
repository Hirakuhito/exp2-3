# 引継ぎ：ネットワークプログラミング実験 4日目

更新日：2026-11-12（日本時間）

## ユーザーの希望

- 実験は「一度に一つずつ」指示する。ユーザーがコマンドを実行して出力を貼ったら、結果を確認してから次の手順を出す。
- まとめて先の手順を列挙しない。日本語で、実行する端末・コマンド・期待する表示を具体的に伝える。
- ユーザーは作業者としてコマンドを実行する。こちらは画面や出力を見て手順を案内する。

## 作業場所・資料

- ローカル作業ディレクトリ：`\\wsl.localhost\Ubuntu-22.04\home\hito\documents\hokudai\2026\experiments\exp2\3`
- 4日目の演習環境：`host82`、ディレクトリ `~/socket`
- host82ではIPv4/IPv6のTCPサンプルを切り替えるため、同じファイルにIPv4/IPv6用の`with socket.socket(...)`が両方有効なことがあった。実行前に該当箇所を確認する。
- 後半指導書：`C:\Users\takuto\Downloads\ネットワークプログラミング指導書後半20261109.pdf`
- 指導書の4日目課題は4-1〜4-8。4-3のみ「上級課題」と明記。
- レポート全体の要件（前半指導書）：4〜6日目は作成したプログラムのソースコードとコマンド実行結果。4-2、4-3は課題にWireshark確認が含まれるため、キャプチャ画面も記録する。

## 4日目の進捗

### 4-1 UDPサーバ・クライアント：完了

- `~/socket/udp_server.py`を起動し、別端末から`udp_client.py`を実行。
- クライアント出力：`Are you talking to me?`
- サーバ出力：`('127.0.0.1', 40714) said, Hey!`
- クライアントは`Hey!`を送信し、サーバ応答を受信できた。

### 4-2 DNS Query IDとResponse ID：確認完了

- キャプチャコマンド：
  `tcpdump -i eth1 -s 0 -w day4_2_dns.pcap 'not port 4200'`
- 停止出力：35 packets captured、37 received by filter、0 dropped。
- 実行コマンド：`./socket_dns_test.py www.iic.hokudai.ac.jp`
- プログラム出力ID：`52921`（16進数では`0xceb9`）。応答は`133.50.42.161`。
- Wiresharkフィルタ：`dns.qry.name == "www.iic.hokudai.ac.jp"`
- ユーザーはQueryとResponseのTransaction IDがともに`0xceb9`であることを確認し、スクリーンショットを撮影済み。
- pcapは演習環境上の`~/socket/day4_2_dns.pcap`。ローカルWiresharkで開くためダウンロード済み。スクリーンショットは指定名`wireshark_day4_2_dns_id.png`でレポート用フォルダに保存するよう案内したが、保存場所・保存完了は未確認。

### 4-4 TCPサーバ・クライアント：動作確認完了

- `tcp_server.py`の19行目IPv4行と20行目IPv6行が両方有効だったためIndentationErrorが発生。
- ユーザーが20行目をコメントアウト：
  `#with socket.socket(socket.AF_INET6, socket.SOCK_STREAM) as server:`
- 19行目IPv4のサーバを起動できた。
- `tcp_client.py`はIPv4設定：`HOSTNAME = '127.0.0.1'`、`PORT_NUM = 6789`、AF_INET。
- クライアントに`Test message.`を入力し、`Echo back text: Test message.`を確認。
- サーバにも`Received text: Test message. from ('127.0.0.1', 55982)`と表示。

## 現在の状態／次にすること

- 直前の案内は、4-4のTCPサーバ端末で`Ctrl+C`を押して停止すること。ユーザーはまだ停止したと報告していない。
- まずサーバ停止を確認する。
- 次は4-5：`tcp_client.py`を`HOSTNAME = '172.28.0.200'`、`PORT_NUM = 7`に変更し、AF_INETでecho (7/tcp)に接続する。実行時に入力文字列と返ってきた文字列を確認する。
- 一度に一手順だけ案内し、出力を待ってから続ける。

## 残りの演習（指導書記載）

- 4-5：TCPクライアントで`172.28.0.200`のecho（7/tcp）に接続。
- 4-6：TCPクライアントで`172.28.0.200:6789`に接続し、混雑時にすぐ返事が返らないことを観察。
- 4-7：IPv6対応の`tcp_server_select.py`、`tcp_server_fork.py`、`tcp_server_multithreading.py`の動作を確認。クライアントもIPv6対応にする。
- 4-8：`tcp_server_fork.py`実行時に、`ps`でプロセスが増えたことを確認。
- 4-3は上級課題で、ユーザーの希望に応じて実施する。

## 作成済みレポート雛形

`report/tasks/day4_1.tex`〜`report/tasks/day4_8.tex`を作成済み。4-3は`extask`環境。他は`maintask`環境。

要件まとめは`report/day4_requirements.md`。

**未実施：** day4各TeXはまだ`report/main.tex`に追加していないため、現行PDFには反映されていない。ユーザーが実験結果を貼った後、該当するテンプレートを埋め、必要なスクリーンショットとソースコードも反映する。

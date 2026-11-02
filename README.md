
*This project has been created as part of the 42 curriculum by yuito.*

# Born2beRoot

## Description

Born2beRootは、仮想マシン上にLinuxサーバーを構築し、システム管理の基礎を学ぶプロジェクトです。

VirtualBox上にDebian 13をインストールし、ディスク暗号化、LVMによるストレージ管理、ユーザー管理、パスワードポリシー、sudo、SSH、ファイアウォール、システム監視などを設定しました。

GUIを使用しない最小構成のサーバーを構築することで、Linuxの仕組みと安全なサーバー運用の基礎を理解することを目的としています。

## Project description

### システム構成

| 項目 | 構成 |
| --- | --- |
| 仮想化ソフトウェア | Oracle VirtualBox 7.0.26 |
| OS | Debian 13 |
| ホスト名 | `yuito42` |
| 一般ユーザー | `yuito` |
| 所属グループ | `sudo`、`user42` |
| 仮想CPU | 1 vCPU |
| メモリ | 2048 MiB |
| 仮想ディスク | 20 GiB |
| ディスク暗号化 | LUKS |
| ストレージ管理 | LVM |
| アクセス制御 | AppArmor |
| 権限管理 | sudo |
| ファイアウォール | UFW |
| リモート接続 | OpenSSH |
| SSHポート | 4242/TCP |
| システム監視 | Bash、wall |
| 定期実行 | cron |

### 仮想マシンの仕組み

仮想マシンとは、物理コンピューター上に仮想的なコンピューター環境を作成する技術です。

VirtualBoxは、ホストPCのCPU・メモリ・ストレージなどのリソースを利用して、ゲストOSに仮想ハードウェアを提供します。

今回の構成では、ホストOS上でVirtualBoxを動かし、その内部でDebianを独立したOSとして実行しています。

仮想マシンを使用するメリットは以下のとおりです。

- ホストOSへの影響を抑えてOSの設定を試せる
- 仮想CPU・メモリ・ディスクを柔軟に割り当てられる
- 1台の物理コンピューターで複数のOSを利用できる
- サーバー構築やネットワーク設定を安全に学習しやすい

### DebianとRocky Linux

どちらもLinuxカーネルを使用するディストリビューションですが、パッケージ管理や設計方針に違いがあります。

| 比較項目 | Debian | Rocky Linux |
| --- | --- | --- |
| 系統 | Debian系 | RHEL互換系 |
| パッケージ管理 | APT、dpkg | DNF、RPM |
| パッケージ形式 | `.deb` | `.rpm` |
| 開発体制 | コミュニティ主体 | コミュニティ主体 |
| 特徴 | 安定性、豊富なパッケージ、幅広い用途 | RHELとの互換性、企業向け環境との親和性 |
| メリット | 情報が豊富で学習しやすい | RHEL系のサーバー管理技術を学べる |
| デメリット | 安定版ではパッケージが比較的古い場合がある | Debian系とは管理方法が異なり、慣れが必要 |

**Debianを選択した理由**

Linuxサーバーの基礎を学ぶことが目的であるため、ドキュメントが豊富で、初学者でも扱いやすいDebianを選択しました。

APTによるパッケージ管理が分かりやすく、各サービスの設定を通じてLinuxの基本的な仕組みを学習できる点が適していると考えました。

### aptとaptitude

どちらもDebian系Linuxで使用されるパッケージ管理ツールです。

| 比較項目 | apt | aptitude |
| --- | --- | --- |
| 役割 | パッケージ管理 | パッケージ管理 |
| 操作方法 | 主にコマンドライン | コマンドラインと対話型画面 |
| 特徴 | インストール・更新・削除を簡潔に実行できる | 依存関係の解決方法を対話的に検討できる |
| 主な用途 | 一般的なパッケージ操作 | 詳細なパッケージ管理 |

今回の構築では、パッケージのインストールや更新に `apt` を使用しました。

### AppArmorとSELinux

どちらもLinuxの強制アクセス制御（MAC）を実現するセキュリティ機構です。

通常のファイル権限に加えて、プログラムがアクセスできるリソースを制限します。

| 比較項目 | AppArmor | SELinux |
| --- | --- | --- |
| 主な制御方式 | パスベースのプロファイル | セキュリティラベルとポリシー |
| 管理方法 | 主にプログラムごとのプロファイル | プロセスやファイルのセキュリティコンテキスト |
| 特徴 | 制限内容を比較的理解しやすい | 詳細で柔軟なアクセス制御が可能 |
| メリット | プログラムごとの権限を把握しやすい | 複雑なセキュリティ要件に対応できる |
| デメリット | パスベースの制御に固有の制約がある | 設定や管理が複雑になりやすい |

**AppArmorを選択した理由**

Debianで利用できるアクセス制御機構であり、プログラムの動作をプロファイルによって制限できるためです。

通常のユーザー権限とは別に、アプリケーション自体のアクセス権限を制限する仕組みを理解できます。

今回の環境では、AppArmorのサービスとカーネル機能が有効になっていることを確認しました。

### UFWとfirewalld

どちらもLinuxのファイアウォールを管理するためのツールです。

| 比較項目 | UFW | firewalld |
| --- | --- | --- |
| 主な操作 | `ufw` | `firewall-cmd` |
| 設定方法 | ポートや通信の許可・拒否 | ゾーンやサービスを利用した管理 |
| 特徴 | シンプルなコマンド操作 | 柔軟なネットワーク設定 |
| メリット | 設定を理解・管理しやすい | 複数のネットワーク環境に対応しやすい |
| デメリット | 複雑な構成では管理に工夫が必要 | ゾーンなどの概念を理解する必要がある |

**UFWを選択した理由**

今回の環境ではSSHに必要な4242/TCPのみを許可するため、シンプルな操作でルールを管理できるUFWを選択しました。

通信の許可・拒否を明示的に設定でき、ファイアウォールの基本を理解するのに適しています。

### VirtualBoxとUTM

どちらも仮想マシンを作成・実行するためのソフトウェアです。

| 比較項目 | VirtualBox | UTM |
| --- | --- | --- |
| 主な対応環境 | Windows、Linux、macOSなど | 主にmacOS |
| 基盤技術 | VirtualBoxの仮想化技術 | QEMUやAppleの仮想化技術 |
| 特徴 | 幅広いホスト環境で利用できる | Apple環境との統合が特徴 |
| メリット | 利用者が多く、設定資料が豊富 | Apple Silicon環境で利用しやすい |
| デメリット | ホストOSやCPUによって利用可能な機能が異なる | 主な利用環境がApple製品に限られる |

**VirtualBoxを選択した理由**

42のLinux環境で利用でき、仮想CPU・メモリ・ディスク・ネットワークを分かりやすく設定できるためです。

また、NATやポート転送を利用でき、ホストPCから仮想マシンへのSSH接続も構築しやすい点が適していると考えました。

### ストレージ構成

20GiBの仮想ディスクを使用し、LUKSで暗号化した領域の内部にLVMを構築しました。

| 領域 | 役割 |
| --- | --- |
| `/boot` | OSの起動に必要なファイルを保存 |
| `sda5_crypt` | LUKSで暗号化された領域 |
| `/` | OSやシステムファイルを保存 |
| `/home` | 一般ユーザーの個人ファイルを保存 |
| swap | メモリの補助領域 |

**LUKS**

LUKSは、Linuxで利用できるディスク暗号化の仕組みです。

ストレージ内のデータを暗号化することで、ディスクが第三者に取得された場合でも、データを読み取られるリスクを軽減します。

**LVM**

LVMはLogical Volume Managerの略で、ストレージを論理的に管理する仕組みです。

主な構成要素は以下の3つです。

- PV（Physical Volume）：LVMで使用する物理的な保存領域
- VG（Volume Group）：PVをまとめて管理する領域
- LV（Logical Volume）：VGから作成する論理ボリューム

LVMを使用することで、物理的なパーティションとOSが利用する論理ボリュームを分離し、ストレージを柔軟に管理できます。

### ユーザーと権限管理

Linuxでは、ユーザーとグループを利用して操作できる範囲を管理します。

今回の環境には、管理者である `root` と一般ユーザーである `yuito` が存在します。

`yuito` は `sudo` と `user42` グループに所属しています。

通常の操作は一般ユーザーで行い、システム設定の変更など管理者権限が必要な場合のみsudoを使用します。

これにより、常にroot権限で作業する場合と比較して、誤操作による影響を抑えることができます。

### パスワードポリシー

パスワードの有効期限と複雑さを管理するため、`/etc/login.defs`、`/etc/security/pwquality.conf`、PAMを設定しました。

**有効期限**

| 設定 | 値 | 意味 |
| --- | --- | --- |
| `PASS_MAX_DAYS` | 30 | パスワードの最大有効日数 |
| `PASS_MIN_DAYS` | 2 | パスワード変更までの最短日数 |
| `PASS_WARN_AGE` | 7 | 有効期限の警告を開始する日数 |

既存の `root` と `yuito` についても、`chage` により有効期限の設定を適用しました。

**複雑さ**

| 設定 | 値 | 意味 |
| --- | --- | --- |
| `minlen` | 10 | 最低文字数 |
| `ucredit` | -1 | 大文字を最低1文字要求 |
| `lcredit` | -1 | 小文字を最低1文字要求 |
| `dcredit` | -1 | 数字を最低1文字要求 |
| `maxrepeat` | 3 | 同一文字の連続数を制限 |
| `usercheck` | 1 | ユーザー名との一致を検査 |
| `difok` | 7 | 以前のパスワードとの差異を検査 |
| `enforce_for_root` | 有効 | rootにも複雑さの検査を適用 |

パスワードの複雑さの検査には `pam_pwquality` を使用しました。

パスワードポリシーを設定することで、推測されやすいパスワードの使用を防ぎ、不正アクセスのリスクを軽減します。

一方で、複雑なパスワードや頻繁な変更は、利用者の負担になる場合があります。

### sudo

sudoは、一般ユーザーが必要なコマンドを一時的に管理者権限で実行するための仕組みです。

今回の環境では、`/etc/sudoers.d/sudo_config` に以下の設定を行いました。

| 設定 | 役割 |
| --- | --- |
| `passwd_tries=3` | 認証試行回数を3回に制限 |
| `badpass_message` | 認証失敗時のメッセージを変更 |
| `logfile` | sudoの実行履歴を記録 |
| `log_input` | 入力ログを記録 |
| `log_output` | 出力ログを記録 |
| `iolog_dir` | 入出力ログの保存先を指定 |
| `requiretty` | sudo実行時にTTYを要求 |
| `secure_path` | sudoで使用するPATHを制限 |

sudoの実行履歴と入出力ログは `/var/log/sudo/` に保存します。

管理者権限を必要な操作に限定し、実行履歴を記録することで、システムの安全性と操作の追跡可能性を高めます。

### SSH

SSHは、ネットワーク経由で別のコンピューターへ安全に接続するための仕組みです。

通信を暗号化することで、ログイン情報や操作内容を保護します。

今回の環境では、OpenSSHサーバーをインストールし、以下の設定を行いました。

- SSHポート：4242/TCP
- rootユーザーのSSHログインは禁止
- 一般ユーザーによるSSH接続を許可

ホストPCとDebianの間ではVirtualBoxのNATポート転送を利用しています。

ホストPCの4242番ポート宛ての通信を、Debianの4242番ポートへ転送する構成です。

### UFW

UFWは、Linuxのファイアウォールを簡単に管理するためのツールです。

今回の環境では、受信通信を原則拒否し、SSHで使用する4242/TCPのみ許可しています。

不要な通信を遮断することで、外部からのアクセスを制限します。

### システム監視

Bashで `monitoring.sh` を作成し、システム情報を取得・表示する仕組みを実装しました。

スクリプトは `/usr/local/bin/monitoring.sh` に配置しています。

各コマンドで取得した情報を変数に保存し、最後に `wall` を使ってログイン中の端末へまとめて表示します。

表示する情報は以下のとおりです。

- OSのアーキテクチャとカーネル情報
- 物理CPU数
- 仮想CPU数
- メモリの使用量と使用率
- ディスクの使用量と使用率
- CPU使用率
- 最終起動日時
- LVMの使用状況
- 確立済みTCP接続数
- ログインユーザー数
- IPv4アドレスとMACアドレス
- sudoコマンドの実行回数

### cron

cronは、指定された時刻にコマンドやスクリプトを自動実行する仕組みです。

今回の環境では、rootのcrontabに以下の設定を登録しました。

```cron
@reboot /bin/bash /usr/local/bin/monitoring.sh
*/10 * * * * /bin/bash /usr/local/bin/monitoring.sh
```

`@reboot` はcronサービスの起動時に1回実行する指定です。

`*/10 * * * *` は毎時0・10・20・30・40・50分に実行する指定です。

cronでスクリプトを実行することで、手動操作を行わずにシステム情報を定期的に取得できます。

## Instructions

このプロジェクトはコンパイル不要です。

VirtualBoxからDebianの仮想マシンを起動し、暗号化ディスクのパスフレーズを入力した後、一般ユーザーでログインします。

以下のコマンドは、特に記載がない限りDebian仮想マシン内で実行します。

### SSH接続

**目的：** ホストPCからDebianへSSH接続します。

ホストPC側で実行します。

```bash
ssh yuito@localhost -p 4242
```

### SSH接続の終了

**目的：** SSHセッションを終了します。

```bash
exit
```

### 仮想マシンの正常終了

**目的：** Debianを安全にシャットダウンします。

```bash
sudo shutdown -h now
```

## システム情報

### OSとカーネル

**目的：** DebianのバージョンとLinuxカーネルの情報を確認します。

```bash
cat /etc/os-release
uname -a
```

### GUIの状態

**目的：** OSの標準起動ターゲットとGUI関連サービスの状態を確認します。

```bash
systemctl get-default
systemctl status display-manager
```

### AppArmorの状態

**目的：** AppArmorサービスが動作しているか確認します。

```bash
sudo systemctl is-active apparmor
```

**目的：** AppArmorサービスが起動時に有効化されているか確認します。

```bash
sudo systemctl is-enabled apparmor
```

**目的：** LinuxカーネルでAppArmorが有効か確認します。

```bash
cat /sys/module/apparmor/parameters/enabled
```

**目的：** AppArmorのプロファイルと制御状態を確認します。

```bash
sudo aa-status
```

## ストレージ管理

### パーティションとファイルシステム

**目的：** ディスク、パーティション、暗号化領域、LVM、マウント先を確認します。

```bash
lsblk
lsblk -f
```

**目的：** マウントされているファイルシステムの容量と使用率を確認します。

```bash
df -h
```

### LVM

**目的：** 物理ボリューム（PV）を確認します。

```bash
sudo pvs
```

**目的：** ボリュームグループ（VG）を確認します。

```bash
sudo vgs
```

**目的：** 論理ボリューム（LV）を確認します。

```bash
sudo lvs
```

### ディスク暗号化

**目的：** LUKSで暗号化された領域の状態を確認します。

```bash
sudo cryptsetup status sda5_crypt
```

## ホスト名

### ホスト名の確認

**目的：** 現在のホスト名を確認します。

```bash
hostname
```

**目的：** ホスト名とOSに関する情報を確認します。

```bash
hostnamectl
```

**目的：** ホスト名の設定ファイルを確認します。

```bash
cat /etc/hostname
```

### ホスト名の変更

**目的：** ホスト名を変更します。

```bash
sudo hostnamectl set-hostname test42
```

**目的：** `/etc/hosts` に登録されているホスト名を変更します。

```bash
sudo nano /etc/hosts
```

**目的：** 再起動後も設定が維持されるか確認します。

```bash
sudo reboot
```

### ホスト名の復元

**目的：** 元のホスト名 `yuito42` に戻します。

```bash
sudo hostnamectl set-hostname yuito42
sudo nano /etc/hosts
sudo reboot
```

`/etc/hosts` 内のホスト名も `yuito42` に戻します。

## ユーザーとグループ

### ログインユーザー

**目的：** 現在ログインしているユーザーを確認します。

```bash
whoami
```

### ユーザーの所属グループ

**目的：** `yuito` が所属するグループを確認します。

```bash
id yuito
groups yuito
```

### グループの確認

**目的：** `sudo` と `user42` の所属ユーザーを確認します。

```bash
getent group sudo
getent group user42
```

### ユーザーの作成

**目的：** 新しい一般ユーザーを作成します。

```bash
sudo adduser testuser
```

### グループの作成

**目的：** `evaluating` グループを作成します。

```bash
sudo groupadd evaluating
```

### グループへの追加

**目的：** 新しいユーザーを `evaluating` グループに追加します。

```bash
sudo usermod -aG evaluating testuser
id testuser
```

### sudoグループへの追加

**目的：** 新しいユーザーをsudoグループに追加します。

```bash
sudo usermod -aG sudo testuser
id testuser
```

## パスワードポリシー

### 有効期限の設定

**目的：** パスワードの有効期限、変更間隔、警告期間の既定値を確認します。

```bash
grep -E '^[[:space:]]*PASS_(MAX_DAYS|MIN_DAYS|WARN_AGE)' /etc/login.defs
```

### ユーザーごとの有効期限

**目的：** 既存ユーザーに設定されているパスワードの有効期限を確認します。

```bash
sudo chage -l root
sudo chage -l yuito
```

### パスワードの複雑さ

**目的：** 文字数、文字種、連続文字、ユーザー名、旧パスワードとの差異などの設定を確認します。

```bash
sudo cat /etc/security/pwquality.conf
cat /etc/pam.d/common-password
```

## sudo

### インストール状態

**目的：** sudoがインストールされていることを確認します。

```bash
dpkg -l sudo
```

### sudoの動作

**目的：** sudoによってroot権限でコマンドを実行できることを確認します。

```bash
sudo whoami
```

### sudoの設定

**目的：** 認証試行回数、ログ保存、TTY、実行パスの制限などを確認します。

```bash
sudo cat /etc/sudoers.d/sudo_config
sudo ls -l /etc/sudoers.d/sudo_config
sudo visudo -c
```

### sudoの実行履歴

**目的：** sudoによるコマンド実行履歴が保存されていることを確認します。

```bash
sudo ls -la /var/log/sudo/
sudo tail -n 20 /var/log/sudo/sudo_config
```

### sudoログの更新

**目的：** sudoでコマンドを実行し、ログが更新されることを確認します。

```bash
sudo whoami
sudo tail -n 20 /var/log/sudo/sudo_config
```

## UFW

### インストール状態

**目的：** UFWがインストールされていることを確認します。

```bash
dpkg -l ufw
```

### UFWの稼働状態

**目的：** UFWが有効で、4242/TCPが許可されていることを確認します。

```bash
sudo systemctl status ufw
sudo ufw status verbose
sudo ufw status numbered
```

### ファイアウォールルールの追加

**目的：** TCP 8080番ポートへの通信を一時的に許可します。

```bash
sudo ufw allow 8080/tcp
sudo ufw status numbered
```

### ファイアウォールルールの削除

**目的：** 追加したルールを削除し、元の設定に戻します。

```bash
sudo ufw delete allow 8080/tcp
sudo ufw status verbose
```

## SSH

### SSHサーバーのインストール

**目的：** OpenSSHサーバーがインストールされていることを確認します。

```bash
dpkg -l openssh-server
```

### SSHサービスの状態

**目的：** SSHサービスが稼働していることを確認します。

```bash
sudo systemctl status ssh
```

### SSHの設定

**目的：** SSHのポート番号とrootログインの許可設定を確認します。

```bash
sudo sshd -T | grep -E '^(port|permitrootlogin) '
```

### SSHの待ち受けポート

**目的：** SSHサービスが4242番ポートで待ち受けていることを確認します。

```bash
sudo ss -lntp
```

### 一般ユーザーでの接続

**目的：** 一般ユーザーがSSH接続できることを確認します。

ホストPC側で実行します。

```bash
ssh yuito@localhost -p 4242
```

### 新規ユーザーでの接続

**目的：** 新しく作成した一般ユーザーがSSH接続できることを確認します。

ホストPC側で実行します。

```bash
ssh testuser@localhost -p 4242
```

### rootログインの禁止

**目的：** rootユーザーでSSHログインできないことを確認します。

ホストPC側で実行します。

```bash
ssh root@localhost -p 4242
```

## システム監視

### monitoring.shの内容

**目的：** 監視スクリプトの内容とアクセス権限を確認します。

```bash
sudo ls -l /usr/local/bin/monitoring.sh
sudo cat /usr/local/bin/monitoring.sh
```

### 構文チェック

**目的：** Bashの構文エラーがないか確認します。

```bash
bash -n /usr/local/bin/monitoring.sh
```

### 手動実行

**目的：** システム情報を取得し、ログイン中の端末へ表示します。

```bash
sudo bash /usr/local/bin/monitoring.sh
```

### cronの状態

**目的：** cronサービスが動作しているか確認します。

```bash
sudo systemctl is-active cron
sudo systemctl is-enabled cron
```

### 定期実行の設定確認

**目的：** cronに登録されているコマンドと実行間隔を確認します。

```bash
sudo crontab -l
```

### 定期実行の記録

**目的：** cronが監視スクリプトを起動した履歴を確認します。

```bash
sudo journalctl -u cron -b --no-pager
```

### 定期実行の設定変更

**目的：** 監視スクリプトの実行間隔を変更します。

```bash
sudo crontab -e
```

毎分実行する場合：

```cron
* * * * * /bin/bash /usr/local/bin/monitoring.sh
```

10分ごとに実行する場合：

```cron
*/10 * * * * /bin/bash /usr/local/bin/monitoring.sh
```

起動時に実行する場合：

```cron
@reboot /bin/bash /usr/local/bin/monitoring.sh
```

### 定期実行の停止

**目的：** スクリプト本体を変更せず、cronによる自動実行を停止します。

```bash
sudo crontab -e
```

対象の設定行をコメントアウトします。

```cron
# @reboot /bin/bash /usr/local/bin/monitoring.sh
# */10 * * * * /bin/bash /usr/local/bin/monitoring.sh
```

再開する場合は、行頭の `#` を削除します。

## 仮想ディスクの整合性確認

### SHA-1ハッシュ値の取得

**目的：** 仮想ディスクの内容からSHA-1ハッシュ値を計算し、ディスクの同一性を確認します。

仮想マシンを正常にシャットダウンした状態で、ホストPC側のターミナルから実行します。

```bash
sha1sum /sgoinfre/yuito/born2beroot/born2beroot.vdi
```

### ハッシュ値の照合

**目的：** 仮想ディスクから計算したSHA-1ハッシュ値と、`signature.txt` に記録されているハッシュ値が一致するか確認します。

Gitリポジトリのルートディレクトリで実行します。

```bash
sha1sum /sgoinfre/yuito/born2beroot/born2beroot.vdi | awk '{print $1}' | diff - signature.txt
```

`diff` が何も出力しなければ、2つのハッシュ値は一致しています。

SHA-1は、ファイルの内容から固定長のハッシュ値を計算する仕組みです。

仮想ディスクの内容が変更されるとハッシュ値も変わる可能性があるため、ハッシュ値の照合は仮想マシンを停止した状態で行います。

## Resources

### 参考資料

- [Debian公式ドキュメント](https://www.debian.org/doc/)
  - Debianのインストール、パッケージ管理、システム管理などに関する資料。

- [Debian Administrator's Handbook](https://www.debian.org/doc/manuals/debian-handbook/)
  - Debianのユーザー管理、ネットワーク、ストレージ、セキュリティなどを体系的に説明した資料。

- [Oracle VirtualBox公式マニュアル](https://www.virtualbox.org/manual/)
  - 仮想マシン、仮想ディスク、仮想ネットワーク、NAT、ポート転送などに関する公式資料。

### AIの利用

AIは、Linux、OS、仮想化、ネットワーク、セキュリティなどの概念の理解、コマンドや設定項目の検索、設定内容の確認方法の整理、シェルスクリプトの動作理解などに補助的に利用しました。

実際の設定や動作については、仮想マシン上でコマンドを実行して確認しました。

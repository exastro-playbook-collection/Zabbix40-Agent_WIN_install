# Ansible Role: Zabbix40-Agent_WIN_install

# Trademarks
-----------
* Linuxは、Linus Torvalds氏の米国およびその他の国における登録商標または商標です。
* RedHat、RHEL、CentOSは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* Windows、PowerShellは、Microsoft Corporation の米国およびその他の国における登録商標または商標です。
* Ansibleは、Red Hat, Inc.の米国およびその他の国における登録商標または商標です。
* pythonは、Python Software Foundationの登録商標または商標です。
* Zabbixは、ラトビア共和国にあるZabbix LLCの商標です。
* Oracleは、Oracle International Corporation の米国およびその他の国における登録商標または商標です。
* NECは、日本電気株式会社の登録商標または商標です。
* その他、本ロールのコード、ファイルに記載されている会社名および製品名は、各社の登録商標または商標です。

## Description
本Roleは統合監視ソフトウェア"Zabbix"のエージェント"Zabbix Agent"のインストールを行います。
対象バージョンは以下のバージョンです。
- Zabbix 4.0
  - コミュニティ版


以下のWindows版ZabbixAgentバイナリをインストールします。

- ZabbixAgentバイナリのファイル（コミュニティからダウンロード）
  - zabbix_agents-4.0.*x*-win-amd64.zip
    - *x* は マイナーバージョンを示す

- インストール対象ファイル（実行ファイル）
  - zabbix_agentd.exe
  - zabbix_get.exe
  - zabbix_sender.exe

- インストール対象ファイル（設定ファイル）
  - zabbix_agentd.win.conf

- インストール先のディレクトリ構成(デフォルト設定の場合)
~~~
  C:\Program Files\zabbix_agent\
    |-bin\
        |- zabbix_agentd.exe
        |- zabbix_get.exe
        |- zabbix_sender.exe
    |-conf\
       |- zabbix_agentd.win.conf
~~~


以下のサービスをWindowsサービスに登録します
- Zabbix Agent
  - サービス名は固定です。変更できません。

## Supports
- 管理マシン(Ansibleサーバ)
  - Linux系OS（RHEL/CentOS）
  - Ansible バージョン2.5 以上 (動作検証済み：2.5、2.6)
  - Python バージョン2.6 または 2.7


- 管理対象マシン(インストール対象マシン)
  - Microsoft Windows Server 2016, Microsoft Windows Server 2012 R2
  - PowerShell 5.0

## Requirements
- 管理マシン(Ansibleサーバ)
  - 管理対象マシンへ	WinRM接続できること
  - WinRM接続設定は次のサイトを参考にすること
    - http://docs.ansible.com/ansible/intro_windows.html#windows-system-prep


- 管理対象マシン(インストール対象マシン)
  - PowerShell 5.0を利用できること
  - ファイアフォールが適切に設定されていること


## Role Variables
### Mandatory Variables

以下の変数は必ず指定します。

- WinRM設定（group_varsもしくはhost_varsに設定）
  * ''ansible_user'': ユーザ名(string)
  * ''ansible_password'': パスワード(string)
  * ''ansible_port'': ポート(number)
  * ''ansible_connection'': 接続方式(string)
  * ''ansible_winrm_server_cert_validation'': 暗号化有無(string)


- ZabbixAgentバイナリのファイル名
  * ''VAR_Zabbix40AG_WIN_localpkg_Zabbix_file'': ZabbixAgentバイナリのファイル名 (string)
     +  例： zabbix_agents-4.0.5-win-amd64.zip


### Optional Variables

以下の変数は任意で指定します。

- ZabbixAgentバイナリの配置したディレクトリ(管理マシン)
  * ''VAR_Zabbix40AG_WIN_localpkg_src'': Ansibleサーバ上のZabbixAgentバイナリの配置ディレクトリ (string, default:"/tmp")


- ZabbixAgentのインストール先(管理対象マシン)
  * ''VAR_Zabbix40AG_WIN_path'': ZabbixAgentインストール先フォルダ (string, default:"'C:\Program Files\zabbix_agent'")
     + 空白を含むパスを指定する場合は、'' で囲むこと
  * ''VAR_Zabbix40AG_WIN_conf'': ZabbixAgent設定ファイル名 (string, default:"zabbix_agentd.win.conf")
  * ''VAR_Zabbix40AG_WIN_localpkg_upload'': ZabbixAgentバイナリファイルをアップロードするかどうか (on|off, default:"on")
     + off に指定した場合は、ZabbixAgentバイナリファイルのアップロードを行いません。
      予め、以下で指定した一時保存先のフォルダにバイナリを配置してください。
  * ''VAR_Zabbix40AG_WIN_localpkg_dst'': ZabbixAgentバイナリファイルの一時保存先 (string, default:"'C:\temp'")
     + 空白を含むパスを指定する場合は、'' で囲むこと
     + 一時保存先フォルダが存在しない場合はロール実行時に作成されます。ロール実行後はフォルダ自体は削除されません。


## Usage
1. 本Roleを用いたPlaybookを作成します。
2. ZabbixAgentバイナリをダウンロードします。
  - コミュニティサイト「 https://www.zabbix.com/jp/download_agents 」にアクセスし、Version：Zabbix 4.0、OS：Windows (All)の行の「download」ボタンから、zip形式のZabbixAgentバイナリをダウンロードします。
3. ZabbixAgentバイナリを、管理マシン上に配置します。
  - Ansibleサーバ上の配置先ディレクトリは変数「VAR_Zabbix40AG_WIN_localpkg_src」です。（デフォルトは/tmp）
4. 必須変数を指定します。
5. 任意変数を必要に応じて指定します。
6. Playbookを実行します。

## Example Playbook

インストールとすべての設定をする場合は、提供した以下のRoleを"roles"ディレクトリに配置した上で、以下のようなPlaybookを作成してください。

- フォルダ構成
~~~
  - group_vars/
    ・ all
    ・ server1
    ・ server2
  - host_vars/
    ・ host1
    ・ host2
  - roles/
    ・ Zabbix40-Agent_WIN_install/
    ・ Zabbix40-Agent_WIN_ossetup/
    ・ Zabbix40-Agent_WIN_register/
    ・ Zabbix40-Agent_WIN_setup/
  - Zabbix40-Agent_WIN_install.yml
  - Zabbix40-Agent_WIN_ossetup.yml
  - Zabbix40-Agent_WIN_register.yml
  - Zabbix40-Agent_WIN_setup.yml
  - conf.yml
  - hosts
  - site.yml
~~~


- マスターPlaybookサンプル 「Zabbix40-Agent_WIN_install.yml」
~~~
# Zabbix40-Agent_WIN_install.yml
  - name: install Zabbix Agent
    hosts: all
    gather_facts: yes
    become: no
    tags:
      - install
    roles:
      - Zabbix40-Agent_WIN_install
~~~


## Running Playbook
- extra-vars を利用する場合の実行例
> ansible-playbook site.yml  -i hosts --extra-vars="@conf.yml"

- group_vars を利用する場合の実行例  
group_vars で指定したグループ名が webserver1 の場合
> ansible-playbook site.yml  -i hosts -l webserver1

- host_vars を利用する場合の実行例  
host_vars で指定したグループ名が server1 の場合
> ansible-playbook site.yml  -i hosts -l server1

- 本Roleのみを実行する場合は、 --tags "install" を付け加える
> ansible-playbook site.yml  -i hosts --extra-vars="@conf.yml" --tags "install"


# Copyright
Copyright (c) 2018 NEC Corporation

# Author Information
NEC Corporation
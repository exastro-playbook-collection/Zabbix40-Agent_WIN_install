# Zabbix Agent 4.0 for windows Server 2012R2/2016

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
本プロジェクトのリポジトリで統合監視ソフトウェアであるZabbix-agentのインストール、セットアップRoleを公開しています。  
OSSの対象バージョンは以下のバージョンとなります。  
 * Zabbix 4.0  
  
対応OSは以下となります。  
 * Microsoft Windows Server 2016  
 * Microsoft Windows Server 2012 R2
 
各ロールの説明はREADME_role.mdをご参照ください。  

## Supports

- 管理マシン(Ansibleサーバ)
 * Linux系OS（RHEL/CentOS）
 * Ansible バージョン 2.5 以上 (動作確認済みバージョン：2.5、2.6)
 * Python バージョン 2.6 または2.7


- 管理対象マシン(構築対象マシン)
  * Microsoft Windows Server 2016  
  * Microsoft Windows Server 2012 R2  


## roles

  - Zabbix40-Agent_WIN_install  
     Zabbix Agent 4.0のパッケージのインストールを行うroleです。  
     提供している機能は以下です。

    * WindowsServer用ZabbixAgentパッケージのインストール
    * WindowsServerへZabbixAgentのサービス登録


  - Zabbix40-Agent_WIN_setup  
     Zabbix Agent 4.0のconfigファイルへの設定を行うroleです。  
     提供している機能は以下です。

     * Zabbix Agentのconfigファイルの設定
     　　-　zabbix_Agentd.win.conf


  - Zabbix40-Agent_WIN_ossetup  
     Zabbix Agentサービスの起動設定／再起動を行うroleです。  
     提供している機能は以下です。

       * ZabbixAgentサービスの自動起動設定
       * ZabbixAgentサービスの再起動


  - Zabbix40-Agent_WIN_register  
     ZabbixAgentをZabbixServerへ監視対象として登録を行うroleです。  
     提供している機能は以下です。

    * 指定されたZabbixServerへホスト登録する
    * ホスト登録時に以下の情報の登録も可能
       - Hostgroup（指定されたgroupがなければ作成）
       - 適用するTemplate（複数指定可能）


## Evidence

本ロールは、gatheringに公開されているエビデンス取得対応をしています。
各ロールにエビデンスを定義してありますので、エビデンス取得ロールを利用して設定内容のエビデンスを取得することができます。
詳しい利用方法については、gathering ロールのREADME.md等をご参照ください。

 - 定義されているエビデンス

    * roles/Zabbix40-Agent_WIN_install/vars/gathering_definition.yml   
        - Zabbixに関連するインストールパッケージ環境の取得  
        - 関連情報の取得  

   ~~~
   ---
   command:
     - systeminfo
     - $PSVersionTable.PSVersion
     - dir C:\
     - dir C:\temp
     - dir 'C:\Program Files'
     - tree 'C:\Program Files\zabbix_agent'
     - type 'C:\Program Files\zabbix_agent\zabbix_agentd.log'
     - type C:\zabbix_agentd.log
     - tasklist /FI "SERVICES eq Zabbix Agent"
     - Get-WmiObject Win32_Service -filter "Name='Zabbix Agent'"
   ~~~

    * roles/Zabbix40-Agent_WIN_setup/vars/gathering_definition.yml 
         - 設定されるconfファイルの取得
         - 設定したサービスの状態

   ~~~
    ---
    command:
      - type 'C:\Program Files\zabbix_agent\conf\zabbix_agentd.win.conf'
      - dir C:\
      - dir C:\temp
    file:
      - C:\Program Files\zabbix_agent\zabbix_agentd.log
   ~~~

     * roles/Zabbix40-Agent_WIN_ossetup/vars/gathering_definition.yml 
        - サービスの登録、動作状態
        - Zabbix Agentのログ

   ~~~
   ---
   command:
     - tasklist /FI "SERVICES eq Zabbix Agent"
     - Get-WmiObject Win32_Service -filter "Name='Zabbix Agent'"
   file:
     - C:\Program Files\zabbix_agent\zabbix_agentd.log
   ~~~

      * roles/Zabbix40-Agent_WIN_register/vars/gathering_definition.yml 
         ・ 登録後のZabbix Agentのログ

   ~~~
   ---
   file:
     - C:\Program Files\zabbix_agent\zabbix_agentd.log
   ~~~


 - エビデンス取得のためのマスタPlaybook(gathering_definition.yml)の例

~~~
   ###  install master playbook for Evidence

   ---
    - name: evidence Zabbix40-Agent_WIN_install
      hosts: all
      become: no
      roles:
       - role: gathering
         VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Agent_WIN_install"

    - name: evidence Zabbix40-Agent_WIN_setup
      hosts: all
      become: no
      roles:
       - role: gathering
         VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Agent_WIN_setup"

    - name: evidence Zabbix40-Agent_WIN_ossetup
      hosts: all
      become: no
      roles:
       - role: gathering
         VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Agent_WIN_ossetup"

    - name: evidence Zabbix40-Agent_WIN_regist
      hosts: all
      become: no
      roles:
       - role: gathering
         VAR_gathering_definition_role_path: "{{ inventory_dir }}/roles/Zabbix40-Agent_WIN_register"
~~~

## 備考
本プロジェクトのリポジトリで公開しているRoleにはインストール媒体は含まれません。 

# Copyright
Copyright (c) 2018 NEC Corporation

# Author Information
NEC Corporation
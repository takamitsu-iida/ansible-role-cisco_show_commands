show_run
=========

Cisco IOS装置に対してshowコマンドを実行して出力結果をファイルに保存します。

show interfacesコマンドおよびshow ip routeコマンドの出力はCSVに変換して保存します。

以下のコマンドでインストールした後、お好みのロール名になるようにフォルダ名を変更してください。

```
ansible-galaxy install -p ./roles git+https://github.com/takamitsu-iida/ansible-role-cisco_show_commands.git
```

Requirements
------------

対象はCisco装置であることを前提にしています。
対象機器にTelnetで接続したい場合は別モジュールが別途必要です。

<https://github.com/takamitsu-iida/ansible-mytelnet>

Role Variables
--------------

PREFIX: ログファイルの先頭につける文字列です。デフォルトは''です。

LOG_PATH: ログを残すフォルダです。デフォルトは `"{{ lookup('env', 'PWD') + '/log' }}"` です。

Dependencies
------------

依存するロールはありません。

Example Playbook
----------------

設定変更するタスクの前後に挿入するとよいでしょう。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
```

作業前と作業後でログ置き場を変えたい場合は、このようにします。
作業前はbefore、作業後はafterにログが残ります。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands
      vars:
        LOG_PATH: "{{ lookup('env', 'PWD') + '/log/before' }}"

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
      vars:
        LOG_PATH: "{{ lookup('env', 'PWD') + '/log/after' }}"
```

ログの置き場は同じにして、ファイル名の先頭になにか付けたい場合は、このようにします。

```yml
- name: execute show commands
  hosts: routers
  gather_facts: false

  tasks:
    - include_role:
        name: cisco_show_commands
      vars:
        PREFIX: before_

    - 設定変更するタスク

    - include_role:
        name: cisco_show_commands
      vars:
        PREFIX: after_
```

License
-------

BSD

Author Information
------------------

Takamitsu IIDA

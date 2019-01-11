cisco_show_commands
===================

Cisco IOS装置に対してshowコマンドを実行して出力結果をファイルに保存します。

これ（↓）をもとに改造したものです。
<https://github.com/takamitsu-iida/ansible-role-cisco_show_run>

大きな違いはshow interfacesコマンドおよびshow ip routeコマンドの出力をCSVに変換してから保存している点です。

以下のコマンドでインストールした後、お好みのロール名になるようにフォルダ名を変更してください。

```bash
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

実行すると対象ノードあたり６個のファイルが生成されます（作業前３個、作業後３個）。

```bash
log
├── r1_show_commands_2019-01-11@13:36:54_interfaces.txt
├── r1_show_commands_2019-01-11@13:36:54_interfaces_interface.csv
├── r1_show_commands_2019-01-11@13:36:54_interfaces_route.csv
├── r2_show_commands_2019-01-11@13:36:54_interfaces.txt
├── r2_show_commands_2019-01-11@13:36:54_interfaces_interface.csv
├── r2_show_commands_2019-01-11@13:36:54_interfaces_route.csv
├── r3_show_commands_2019-01-11@13:36:54_interfaces.txt
├── r3_show_commands_2019-01-11@13:36:54_interfaces_interface.csv
├── r3_show_commands_2019-01-11@13:36:54_interfaces_route.csv
├── r4_show_commands_2019-01-11@13:36:54_interfaces.txt
├── r4_show_commands_2019-01-11@13:36:54_interfaces_interface.csv
└── r4_show_commands_2019-01-11@13:36:54_interfaces_route.csv
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

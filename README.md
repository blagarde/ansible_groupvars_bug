# ansible_groupvars_bug
Minimal playbook to repro a bug with Ansible groupvars
```bash
$ tree 
.
├── group_vars
│   └── all.yml     <- bar: "{{foo == 'value2'}}"
├── test
│   ├── group_vars
│   │   └── all.yml <- foo: value1
│   └── inventory
└── test.yml        <- "when: not bar" gets incorrectly skipped
```

## Repro steps
```bash
 ~/ansible_groupvars_bug$ ansible-playbook test.yml -i test
 ```

## Actual result
```bash
PLAY [Roles for test] ********************************************************* 

GATHERING FACTS *************************************************************** 
ok: [localhost]

TASK: [debug var=foo] ********************************************************* 
ok: [localhost] => {
    "var": {
        "foo": "value1"
    }
}

TASK: [debug var=bar] ********************************************************* 
ok: [localhost] => {
    "var": {
        "bar": "False"
    }
}

TASK: [bar] ******************************************************************* 
skipping: [localhost]

TASK: [not bar] *************************************************************** 
skipping: [localhost]

PLAY RECAP ******************************************************************** 
localhost                  : ok=3    changed=0    unreachable=0    failed=0   

```

## Expected result
`bar` is `False`, therefore `TASK [not bar]` should not be skipped.

# afl fuzzing demos
Fuzzing things with [afl](http://lcamtuf.coredump.cx/afl/) and [python-afl](https://bitbucket.org/jwilk/python-afl/overview)

## Installing Fuzzing Tools

- create a working directory and virtual env
- afl
    - [Download](http://lcamtuf.coredump.cx/afl/releases/afl-latest.tgz) and unpack from http://lcamtuf.coredump.cx/afl/
    - follow instructions in [docs/INSTALL](https://github.com/mcarpenter/afl/blob/master/docs/INSTALL)
- python-afl
    - https://bitbucket.org/jwilk/python-afl/overview
    - `hg clone ...` and `pip install -e python-afl/`

## Fuzzing Python's JSON library

assuming a layout:

    python-afl/
    └── py-afl-fuzz
    afl-fuzzing-demos/
    ├── json
    │   ├── afl_findings/
    │   ├── afl_testcases/
    │   ├── demo.py
    │   └── README.md
    └── README.md

- cd `afl-fuzzing-demos`
- `../python-afl/py-afl-fuzz -i json/afl_testcases/ -o json/afl_findings/ -- python json/demo.py`

*Note*: afl makes constant reads & writes to the file system, so if you're running on an SSD it's a good idea to replace the output directory with an in-memory RAM disk folder. E.g. on debian based systems, the shared memory directory `/run/shm/`. But obviously remember, the results won't survive a reboot, so a compound command like this might be best:


    mkdir -p /run/shm/json/afl_findings

    ../python-afl/py-afl-fuzz \
        -i json/afl_testcases/ \
        -o /run/shm/json/afl_findings \
        -- python json/demo.py; cp -r /run/shm/json/afl_findings json/

When you're finished, Ctrl-C and see what you come up with in `json/afl_findings`.
You'll start to see output after a few seconds, but it's usual to leave a fuzzer running for a few days.

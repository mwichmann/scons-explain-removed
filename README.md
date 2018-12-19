Reproducer for the `--debug=explain` case where
a node is reported as removed then immediately
as added - when in fact neither of those is the case.

The three subdirectories contain identical code except
for variant dir stuff invocation:

```python
test-novar:
    SConscript('src/SConscript')

test-var-dup:
    SConscript('src/SConscript', variant_dir='build', duplicate=1)

test-var-nodup:
    SConscript('src/SConscript', variant_dir='build', duplicate=0)
```

Only the third reproduces the issue.

Run once to build the sconsign file.

Editing `src/hello.c` so the sig will be changed, should result in:

```
scons: rebuilding `build/hello.o' because:
           `src/hello.c' is no longer a dependency
           `src/hello.c' is a new dependency
```

Edit `src/hello.h` so the sig will be changed, should result in:

```
scons: rebuilding `build/hello.o' because:
           `src/hello.c' is no longer a dependency
           `src/hello.c' is a new dependency
           `src/hello.h' changed
```

That is, the fact that the source file needs rebuilding seems to be
enough to trigger, whether or not the deps changed.



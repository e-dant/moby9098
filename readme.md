```
NAME
    moby9098 - Spawn a uniquely `ps`-identifiable process

SYNOPSIS
    moby9098 <unique> <command> [args...]
        Mirror the behavior of `command [args...]`, but with a unique identifier.
    moby9098 -h, --help
        Show this help and exit.

DESCRIPTION
    A tool for uniquely identifying a process without pidfile cooperation or similar.
    The first argument is intended to be any unique value. It is otherwise ignored. It exists
    solely to make the child process uniue, in the eyes of process monitoring tools. Maintaining
    PID files may work better in some cases, especially for daemons. For the "one-off" programs,
    like common Unix tools, this might work better. Spawning a (potentially long-running) `grep`,
    killing the docker exec context it spawned from, and expecting it to go away, is not going to
    be a good time. If you run lots of these utilities, particularly with similar arguments, have
    fun trying to decipher which of them made your laptop sound like it was entering LEO.
    This requires only that the "calling" program be able to pass a unique value (should be easy)
    to this wrapper program. The second argument will be invoked as a command, with every argument
    after that being passed to the command. Importantly, this program will otherwise exactly mimic
    the behavior of the command it is wrapping. The standard file descriptors stdin/out/err will be
    passed through to the wrapped command. The return code of the wrapped command will be the
    return code of this program. This program will not daemonize itself, nor will it do anything
    else unexcepted, with two exceptions:
    - If there is an error spawning the wrapped command, in which case a diagnostic message will
      be printed on standard error and this program will exit with a return code of 127.
    - The spawned process is killed with an unhandled signal, in which case this program will
      report that error to stderr and exit with a return code of 127 + the signal number.
    - The spawned process has exited, but did not return an exit code and was not killed by a
      signal. In this (impossible?) case, this program will exit with a return code of 127.
    In some cases, this is indistinguishable from the wrapped command being printing a similar
    diagnostic message to stderr and exiting with a return code of 127 (in the first case) or
    exiting with a return code of 127 + the signal number (in the second case).
```

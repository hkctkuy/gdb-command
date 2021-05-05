# gdb-command

`gdb-command` is a library providing API for manipulating gdb in batch mode. It supports:

* Execution of target program (Local type).
* Opening core of target program (Core type).
* Attaching to remote process (Remote type).

# Example

```rust
use std::process::Command;
use std::thread;
use std::time::Duration;
use gdb_command::*;

fn main () -> error::Result<()> {
    // Get stacktrace from running program (stopped at crash)
    let result = GdbCommand::new(&ExecType::Local(&["tests/bins/test_abort", "A"])).bt()?;

    // Get stacktrace from core
    let result = GdbCommand::new(
            &ExecType::Core {target: "tests/bins/test_canary",
                core: "tests/bins/core.test_canary"})
        .bt()?;

    // Get stacktrace from remote attach to process
    let mut child = Command::new("tests/bins/test_callstack_remote")
       .spawn()
       .expect("failed to execute child");

    thread::sleep(Duration::from_millis(10));

    // To run this test: echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
    let result = GdbCommand::new(&ExecType::Remote(&child.id().to_string())).bt();
    child.kill().unwrap();

    Ok(())
}

```

## License

This crate is licensed under the [MIT license].

[MIT license]: LICENSE
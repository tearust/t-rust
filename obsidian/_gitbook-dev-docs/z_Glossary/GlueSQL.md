# GlueSQL
[GlueSQL](https://github.com/gluesql/gluesql) is a SQL database library written in Rust.

TEA Project uses GlueSQL as an embedded SQL database **inside** of a [state machine replica's](state_machine_replica.md) enclave. All data is stored in RAM only. The [state machine](state_machine.md) keeps all SQL states consistent across all replicas.

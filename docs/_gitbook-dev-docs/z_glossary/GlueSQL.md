[GlueSQL](https://github.com/gluesql/gluesql) is a SQL database library written in Rust.

TEA Project uses GlueSQL as an embedded SQL database **inside** of a [state machine replica's](State_Machine_Replica.md) enclave. All data is stored in RAM only. The [state machine](State_Machine.md) keeps all SQL states consistent across all replicas.

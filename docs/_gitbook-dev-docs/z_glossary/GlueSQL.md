GlueSQL (https://github.com/gluesql/gluesql) s a SQL database library written in Rust.

TEA Project uses GlueSQL as an embedded SQL database **inside** a [State_Machine_Replica](State_Machine_Replica.md)'s enclave. All data is stored in RAM only. The [State_Machine](t-rust/docs/_gitbook-dev-docs/1_core_docs/State_Machine.md) keeps all SQL states consistent across all replicas.

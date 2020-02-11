---
title: "Golang sql"
date: 2020-02-09
draft: true
author: "Iven"
---

## 索引

```golang
func Drivers() []string
func Register(name string, driver driver.Driver)
type ColumnType
    func (ci *ColumnType) DatabaseTypeName() string
    func (ci *ColumnType) DecimalSize() (precision, scale int64, ok bool)
    func (ci *ColumnType) Length() (length int64, ok bool)
    func (ci *ColumnType) Name() string
    func (ci *ColumnType) Nullable() (nullable, ok bool)
    func (ci *ColumnType) ScanType() reflect.Type
type Conn
    func (c *Conn) BeginTx(ctx context.Context, opts *TxOptions) (*Tx, error)
    func (c *Conn) Close() error
    func (c *Conn) ExecContext(ctx context.Context, query string, args ...interface{}) (Result, error)
    func (c *Conn) PingContext(ctx context.Context) error
    func (c *Conn) PrepareContext(ctx context.Context, query string) (*Stmt, error)
    func (c *Conn) QueryContext(ctx context.Context, query string, args ...interface{}) (*Rows, error)
    func (c *Conn) QueryRowContext(ctx context.Context, query string, args ...interface{}) *Row
    func (c *Conn) Raw(f func(driverConn interface{}) error) (err error)
type DB
    func Open(driverName, dataSourceName string) (*DB, error)
    func OpenDB(c driver.Connector) *DB
    func (db *DB) Begin() (*Tx, error)
    func (db *DB) BeginTx(ctx context.Context, opts *TxOptions) (*Tx, error)
    func (db *DB) Close() error
    func (db *DB) Conn(ctx context.Context) (*Conn, error)
    func (db *DB) Driver() driver.Driver
    func (db *DB) Exec(query string, args ...interface{}) (Result, error)
    func (db *DB) ExecContext(ctx context.Context, query string, args ...interface{}) (Result, error)
    func (db *DB) Ping() error
    func (db *DB) PingContext(ctx context.Context) error
    func (db *DB) Prepare(query string) (*Stmt, error)
    func (db *DB) PrepareContext(ctx context.Context, query string) (*Stmt, error)
    func (db *DB) Query(query string, args ...interface{}) (*Rows, error)
    func (db *DB) QueryContext(ctx context.Context, query string, args ...interface{}) (*Rows, error)
    func (db *DB) QueryRow(query string, args ...interface{}) *Row
    func (db *DB) QueryRowContext(ctx context.Context, query string, args ...interface{}) *Row
    func (db *DB) SetConnMaxLifetime(d time.Duration)
    func (db *DB) SetMaxIdleConns(n int)
    func (db *DB) SetMaxOpenConns(n int)
    func (db *DB) Stats() DBStats
type DBStats
type IsolationLevel
    func (i IsolationLevel) String() string
type NamedArg
    func Named(name string, value interface{}) NamedArg
type NullBool
    func (n *NullBool) Scan(value interface{}) error
    func (n NullBool) Value() (driver.Value, error)
type NullFloat64
    func (n *NullFloat64) Scan(value interface{}) error
    func (n NullFloat64) Value() (driver.Value, error)
type NullInt32
    func (n *NullInt32) Scan(value interface{}) error
    func (n NullInt32) Value() (driver.Value, error)
type NullInt64
    func (n *NullInt64) Scan(value interface{}) error
    func (n NullInt64) Value() (driver.Value, error)
type NullString
    func (ns *NullString) Scan(value interface{}) error
    func (ns NullString) Value() (driver.Value, error)
type NullTime
    func (n *NullTime) Scan(value interface{}) error
    func (n NullTime) Value() (driver.Value, error)
type Out
type RawBytes
type Result
type Row
    func (r *Row) Scan(dest ...interface{}) error
type Rows
    func (rs *Rows) Close() error
    func (rs *Rows) ColumnTypes() ([]*ColumnType, error)
    func (rs *Rows) Columns() ([]string, error)
    func (rs *Rows) Err() error
    func (rs *Rows) Next() bool
    func (rs *Rows) NextResultSet() bool
    func (rs *Rows) Scan(dest ...interface{}) error
type Scanner
type Stmt
    func (s *Stmt) Close() error
    func (s *Stmt) Exec(args ...interface{}) (Result, error)
    func (s *Stmt) ExecContext(ctx context.Context, args ...interface{}) (Result, error)
    func (s *Stmt) Query(args ...interface{}) (*Rows, error)
    func (s *Stmt) QueryContext(ctx context.Context, args ...interface{}) (*Rows, error)
    func (s *Stmt) QueryRow(args ...interface{}) *Row
    func (s *Stmt) QueryRowContext(ctx context.Context, args ...interface{}) *Row
type Tx
    func (tx *Tx) Commit() error
    func (tx *Tx) Exec(query string, args ...interface{}) (Result, error)
    func (tx *Tx) ExecContext(ctx context.Context, query string, args ...interface{}) (Result, error)
    func (tx *Tx) Prepare(query string) (*Stmt, error)
    func (tx *Tx) PrepareContext(ctx context.Context, query string) (*Stmt, error)
    func (tx *Tx) Query(query string, args ...interface{}) (*Rows, error)
    func (tx *Tx) QueryContext(ctx context.Context, query string, args ...interface{}) (*Rows, error)
    func (tx *Tx) QueryRow(query string, args ...interface{}) *Row
    func (tx *Tx) QueryRowContext(ctx context.Context, query string, args ...interface{}) *Row
    func (tx *Tx) Rollback() error
    func (tx *Tx) Stmt(stmt *Stmt) *Stmt
    func (tx *Tx) StmtContext(ctx context.Context, stmt *Stmt) *Stmt
type TxOptions
```

## 解释

### 打开和空闲连接
db在执行任务的时候该连接会被标记打开(open)，任务完成后，连接会被变为idle(空闲)。  如果连接池里没有空闲的连接，则重新生成连接。打开的最大连接数受`SetMaxOpenConns`这个方法控制，空闲的连接数受`SetMaxIdleConns`这个方法控制。 默认两个方法都不受限制。
包中设置最大打开连接数和空闲连接数如下:
```golang
func (db *DB) SetMaxIdleConns(n int)
func (db *DB) SetMaxOpenConns(n int)
```
设置这两个参数可以控制最大连接数，如果设置超出数据库配置最大连接数，当使用到的连接数超出则会报错。适当控制可以有效的控制最大连接数。db内部如果没有可用连接，会让其等待，一直到可以用的连接（这个也受context的设置控制）。

### 连接生命周期
db可以设置连接生命周期`SetConnMaxLifetime`，当过了这个时间连接就不可用了，这个时间是指创建开始的时间。也可能连接会提前关闭。这个并不是指空闲时间。db每秒会自动检测从池中删除“过期”连接。

```golang
func (db *DB) SetConnMaxLifetime(d time.Duration)
```
### 预处理
一条SQL语句在DB接收到执行完成，大致过程：

1. 词法解析  
2. 优化SQL语句  
3. 制定执行计划，例如PostgreSQL根据条件是否触发并发查询功能
4. 执行并返回结果

如上，一条 SQL 直接是走流程处理，一次编译，单次运行，此类普通语句被称作 Immediate Statements（即时 SQL）

但是，绝大多数情况下，某需求某一条 SQL 语句可能会被反复调用执行，或者每次执行的时候只有个别的值不同（比如 select 的 where 子句值不同，update 的 set 子句值不同，insert 的 values 值不同）。如果每次都需要经过上面的词法语义解析、语句优化、制定执行计划等，则效率就明显不行了。  
所谓预编译语句就是将此类 SQL 语句中的值用占位符替代，可以视为将 SQL 语句模板化或者说参数化，一般称这类语句叫Prepared Statements。  
预编译语句的优势在于归纳为：一次编译、多次运行，省去了解析优化等过程；此外预编译语句能防止 SQL 注入。  

golang sql包中处理预处理方法
```golang
func (db *DB) Prepare(query string) (*Stmt, error)
func (db *DB) PrepareContext(ctx context.Context, query string) (*Stmt, error)
```
当处于一个事务中可以生成一个预处理
```golang
func (tx *Tx) Prepare(query string) (*Stmt, error)
func (tx *Tx) PrepareContext(ctx context.Context, query string) (*Stmt, error)
```
同时也可以使用以经有的预处理方法
```golang
func (tx *Tx) Stmt(stmt *Stmt) *Stmt
func (tx *Tx) StmtContext(ctx context.Context, stmt *Stmt) *Stmt
```
[TOC]
## mysql2
```
// 安装
npm install mysql2
// 安装类型声明文件在package.json文件中："@types/mysql2": "github:types/mysql2"
// 文档
https://www.npmjs.com/package/mysql2
```
示例mysql2/promise
```javascript
import * as mysql from "mysql2/promise"

// import * as mysql from "mysql2"

class DB {

  /**
   * 实例对象
   * @private
   */
  private static instance: DB

  /**
   * 连接池对象
   * @private
   */
  private pool: mysql.Pool

  /**
   * 数据库配置信息
   * @private
   */
  private config: mysql.PoolOptions = {
    host: '127.0.0.1',
    user: 'root',
    password: 'root',
    database: 'test',
    charset: 'utf8mb4',
    connectTimeout: 1000
  }

  /**
   * 构造函数
   */
  constructor() {
    this.pool = this.initPool()
  }

  /**
   * 单例模式
   */
  static getInstance(): DB {
    if (!this.instance) {
      this.instance = new DB()
    }
    return this.instance
  }

  /**
   * 创建连接层
   * @private
   */
  private initPool(): mysql.Pool {
    return mysql.createPool(this.config)
  }

  /**
   * 关闭连接层
   */
  async closePool(): Promise<void> {
    return this.pool.end()
  }

  /**
   * 执行 INSERT 和 UPDATE 语句
   * @param options SQL内容
   * @returns
   */
  async run(options: mysql.QueryOptions): Promise<{ ok: mysql.OkPacket }> {
    const result = await this.pool.query(options)
    return {
      ok: <mysql.OkPacket>result[0]
    }
  }

  /**
   * 查询一条结果
   * @param options
   * @returns
   */
  async queryOne(options: mysql.QueryOptions) {
    const result = await this.pool.query(options)
    const rows = <mysql.RowDataPacket[]>result[0]
    return {
      row: rows.length > 0 ? rows[0] : null,
      info: <mysql.FieldPacket[]>result[1]
    }
  }

  /**
   * 查询多条结果
   * @param options
   * @returns
   */
  async queryAll(options: mysql.QueryOptions) {
    const result = await this.pool.query(options)
    return {
      rows: <mysql.RowDataPacket[]>result[0],
      info: <mysql.FieldPacket[]>result[1]
    }
  }

  /**
   * execute可以利用MySQL的PrepareStatement机制来预编译SQL语句，性能更高，而query是直接组装SQL，它更灵活
   * @param options
   * @returns
   */
  async execOne(options: mysql.QueryOptions) {
    const result = await this.pool.execute(options)
    const rows = <mysql.RowDataPacket[]>result[0]
    return {
      row: rows.length > 0 ? rows[0] : null,
      info: <mysql.FieldPacket[]>result[1]
    }
  }

  /**
   * 查询全部
   * @param options
   * @returns
   */
  async exec(options: mysql.QueryOptions) {
    const result = await this.pool.execute(options)
    return {
      rows: <mysql.RowDataPacket[]>result[0],
      info: <mysql.FieldPacket[]>result[1],
    }
  }
}

export default DB.getInstance()

```
测试
```javascript
import express from 'express'
import sql from './db'


const app = express();
const PORT = 3000;

app.get('/', async (req, res) => {
  // 测试
  await sql.run({ sql: "DROP TABLE IF EXISTS test" })
  await sql.run({ sql: "CREATE TABLE test (id integer auto_increment primary key, name text) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4" })
  await sql.run({ sql: `INSERT INTO test SET name = ?`, values: ['lily'] })
  const { ok: runOk, } = await sql.run({
    sql: "UPDATE test SET name = ? WHERE id = 3",
    values: ["lily"]
  })
  const { row: queryOneRow, info: queryOneInfo } = await sql.queryOne({
    sql: "SELECT * FROM test WHERE name = ?",
    values: ["lily"]
  })
  const { row: nullData } = await sql.queryOne({ sql: "SELECT * FROM test WHERE id = 100" })
  const { rows: fetchRows, info: fetchInfo, } = await sql.exec({ sql: "SELECT * FROM test" })
  const { row: fetchOneRow, info: fetchOneInfo, } = await sql.execOne({
    sql: "SELECT * FROM test WHERE id = 1"
  })

  const result = await sql.queryAll({ sql: 'select * from test_tb' })
  res.send(result.rows);
});

app.listen(PORT, () => {
  console.log(`Express with Typescript! http://localhost:${PORT}`);
});

```




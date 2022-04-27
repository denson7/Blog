[TOC]
## Node  Mysql示例
```javascript
// 文档
https://github.com/mysqljs/mysql
```
### 连接Mysql
方法一：通过创建连接
```javascript
const mysql = require('mysql');

// 连接 mysql 服务器
// 备注：用 createConnection 创建 Mysql 连接，每执行一次 connection.query 都是一个全新的连接，会造成一个资源的浪费，降低性能。
const connection = mysql.createConnection({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: '',
  database: 'test',
  // 开启多SQL语句查询
  multipleStatements: true,
});

// 链接
connection.connect();

// 添加执行 sql 函数
function exec(sql) {
  return new Promise((resolve, reject) => {
    // 执行SQL
    connection.query(sql, (err, result) => {
      if (err) {
        reject(err);
        return
      }
      resolve(result)
    })
  })
}
// 销毁连接
// connection.destroy();
module.exports = {
  exec,
  escape: mysql.escape
};

```
方法二：通过连接池连接
```javascript
const mysql = require('mysql');

// 链接池：创建多个链接、复用与分发链接
// 备注：连接池是另外的一种执行方法，它一次性的创建了多个连接，然后根据客户端的查询，自动的 分发、复用、管理 这些连接。
const pool = mysql.createPool({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: '',
  database: 'test'
});

// 封装 sql入口
const exec = (sql, val) => {
  return new Promise((resolve, reject) => {
    pool.getConnection(function (err, connection) {
      if (err) {
        reject(err)
      } else {
        connection.query(sql, val, (err, results) => {
          if (err) {
            reject(err);
          } else {
            resolve(results);
          }
          // 释放连接资源
          connection.release();
        })
      }
    })
  })
};

module.exports = {
  exec
};

```
使用
```javascript
// 控制器
const getList = async ()=>{
    let sql = `select * from user`;
    const res = await exec(sql);
    return res[0]||{}
}
// 路由
router.post('/test', async (context, next) => {
    // 获取列表
    const res = await getList();
    context.body = res;
});

```

### 插入单条记录
```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: 'root',
  database: 'my-db'
});

const value = {
  name: 'John',
  sex: 'male',
  height: '173'
}
// 方法1
const table = 'xxx';
const sql = `INSERT INTO ${table} SET ?`;
connection.query(sql, value, (error, rows) => {
  if (error) {
    console.log(error);
  } else {
    console.log(rows);
  }
});

// 方法2
const column = Object.keys(value);
const values = Object.values(value);
const sql2 = `INSERT INTO ${table} (${column.join(',')}) VALUES (${values.map((item) => '?').join(',')})`;
connection.query(sql2, values, (error, rows) => {
  if (error) {
    console.log(error);
  } else {
    console.log(rows);
  }
});

```

### 插入多条记录
```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: 'root',
  database: 'my-db'
});

const items = [
  {name: 'A', description: 'describes A', score: 100},
  {name: 'B', description: 'describes B', score: 90},
  {name: 'C', description: 'describes C', score: 85}
];

const sql = `INSERT INTO users (user_name, description, score) VALUES ?`;
// 参数形如：[ [ [...], [...], [...] ] ]
connection.query(sql, [items.map(item => [item.name, item.description, item.score])], (error, rows) => {
  if (error) {
    console.log(error);
  }
});

```
### 更新记录
```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: 'root',
  database: 'my-db'
});

const value = {
  name: 'John',
  sex: 'male',
  height: '173'
};
const id = 12;
const table = 'xxx';
const column = Object.keys(value);
const values = Object.values(value);
const sql = `UPDATE ${table} SET ${column.map((key) => key + ' = ?').join(', ')} WHERE id = ?`;
connection.query(sql, [...values, id], (error, rows) => {
  if (error) {
    console.log(error);
  } else {
    console.log(rows);
  }
});

```

## 使用事务
```javascript
import mysql from 'mysql';
// connection
const connection = mysql.createConnection({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: '',
  database: 'test'
});

connection.beginTransaction((err) => {
  if (err) {
    throw err;
  }
  connection.query('INSERT INTO posts SET title=?', 'title', (error, results, fields) => {
    if (error) {
      return connection.rollback(() => {
        throw error;
      });
    }

    var log = 'Post ' + results.insertId + ' added';

    connection.query('INSERT INTO log SET data=?', log, (error, results, fields) => {
      if (error) {
        return connection.rollback(() => {
          throw error;
        });
      }
      connection.commit((err) => {
        if (err) {
          return connection.rollback(() => {
            throw err;
          });
        }
        console.log('success!');
      });
    });
  });
});

```
```javascript
import mysql from 'mysql';

const pool = mysql.createPool({
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: '',
  database: 'test',
  connectionLimit: 100,
  queueLimit: 0,
  connectTimeout: 10000,
});

const connect = (): Promise<any> => {
  return new Promise((resolve, reject) => {
    pool.getConnection((err, connection) => {
      if (err) {
        reject(err);
      } else {
        resolve(connection);
      }
    });
  });
};


/**
 *
 await transaction(async connection => {
  await connection.query('...');
  await connection.query('...');
 });
 * @param callback
 */
export const transaction = async (callback) => {
  const connection = await connect();
  await connection.beginTransaction();
  try {
    await callback(connection);
    await connection.commit();
  } catch (err) {
    await connection.rollback();
    throw err;
  } finally {
    connection.release();
  }
};

```
## 流式查询
.query()方法会返回一个query实例对象，它会发送一些特定的事件，这在进行大量数据查询时非常有用。
```javascript
var query = connection.query('SELECT * FROM posts');
query
  .on('error', function(err) {
    // 错误处理，在这个事件之后会发送一个'end'事件
  })
  .on('fields', function(fields) {
    // 查询行字段包信息
  })
  .on('result', function(row) {
    // 暂停你正在使用进程的I/O操作
    connection.pause();

    processRow(row, function() {
      connection.resume();
    });
  })
  .on('end', function() {
    // 所有行查询完成或发生错误后触发
  });

```
## 多语句查询

出于安全考虑node-mysql默认禁止多语句查询（可以防止SQL注入），启用多语句查询可以将multipleStatements选项设置为true。

```javascript
// 开启多语句查询选项，multipleStatements设为true
const connection = mysql.createConnection({multipleStatements: true});
// 在一个query查询中执行多条语句
connection.query('SELECT 1; SELECT 2', function(err, results) {
  if (err) throw err;
  // `results`是一个包含多个语句查询结果的数组
  console.log(results[0]); // [{1: 1}]
  console.log(results[1]); // [{2: 2}]
});

```
## IN 查询
```javascript
export const getCategoryList = async (): Promise<any> => {
  const result = { code: 200, message: 'success', data: {} };
  try {
    const ids = ['3', '4', '5'];
    // 方法1
    const sql1 = `SELECT * FROM mp_category_months WHERE category_uuid = ? AND id IN (?)`;
    const categoryUuid = 'a2f34723-29e7-4696-82a1-d470bcdc2edf';
    const rsp1 = await exec(sql1, [categoryUuid, ids]);

    // 方法2
    // const inCondition = new Array(ids.length).fill('?').join(',');
    // const sql2 = `SELECT * FROM mp_category_months WHERE category_uuid = ? AND id IN (${inCondition})`;
    // const rsp2 = await exec(sql2, [categoryUuid, ...ids]);

    // 方法3
    // let whereIn = '';
    // for(let i=0; i < ids.length; i++) {
    //   whereIn += `'${ids[i]}',`;
    // }
    // whereIn = whereIn.substring(0, whereIn.length - 1);
    // const sql = `SELECT * FROM mp_category_months WHERE id in (${whereIn} )`;

    if (rsp1.length) {
      result.data = rsp1;
    }
  } catch (error) {
    //
  }
  return result;
};

```

## 检查服务器是否可连接
```javascript
const connection = mysql.createConnection({});

// 当连接服务器没有响应时，会将错误传递到回调函数的error参数
connection.ping(function (err) {
  if (err) throw err;
  console.log('Server responded to ping');
});

```

## 超时
每个操作都应该有一个可选的超时（timeout）选项，在操作超过指定时间后会发送超时错误。超时并不是MySQL协议的组成部分，而是客户端的一个处理。
```javascript
// 60秒后断开查询
connection.query({sql: 'SELECT COUNT(*) AS count FROM big_table', timeout: 60000}, function (err, rows) {
  if (err && err.code === 'PROTOCOL_SEQUENCE_TIMEOUT') {
    throw new Error('表 count 操作超时！');
  }

  if (err) {
    throw err;
  }
  console.log(rows[0].count + ' rows');
});

```
## 示例
```javascript
import mysql, { PoolConfig, Connection, FieldInfo, MysqlError} from 'mysql';
import async from 'async';
import logger from '../../utils/logger';

const option = {
  host: '127.0.0.1',
  port: 3306,
  user: 'root',
  password: '',
  database: 'test',
  // 开启多sql语句查询
  multipleStatements: true,
}

// connection pool
const pool = mysql.createPool({
  connectionLimit: 100,
  queueLimit: 0,
  connectTimeout: 10000,
  ...option
});


const handleDisconnect = () => {
  const connection = mysql.createConnection(option);
  connection.connect((err) => {
    if (err) {
      console.log('error when connecting to db:', err);
      setTimeout(handleDisconnect, 1000);
    }
  });
  connection.on('error', (err) => {
    console.log('handleDisconnect :: db error', err);
    if (err.code === 'PROTOCOL_CONNECTION_LOST' || err.code === 'PROTOCOL_ENQUEUE_AFTER_FATAL_ERROR' || err.code === 'ETIMEDOUT') {
      console.log('it is reconnection ...');
      handleDisconnect();
    } else {
      // connection idle timeout
      throw err;
    }
  });
  return connection;
};

/**
 * do query sql
 * @param sql
 * @param val
 */
const exec = (sql: string, val: any = ''): Promise<any> => {
  return new Promise((resolve, reject) => {
    pool.getConnection(function (err, connection) {
      if (err) {
        reject(err);
      } else {
        const query = connection.query(sql, val, (err, results, fields) => {
          if (err) {
            reject(err);
          } else {
            // 去除每条数据前面的RowDataPacket
            const data = JSON.stringify(results);
            resolve(JSON.parse(data));
          }
        });
        // 释放连接资源
        connection.release();
        // print sql
        logger.info('SQL----->', query.sql);
      }
    });
  });
};

/**
 * eg：
 * const connection = await connect();
 * const query = connection.query(sql, val);
 */
const connect = (): Promise<any> => {
  return new Promise((resolve, reject) => {
    pool.getConnection((err, connection) => {
      if (err) {
        reject(err);
      } else {
        resolve(connection);
      }
    });
  });
};

/**
 * mutli query sql with one transaction
 * @param arr
 */
const execTransaction = (arr) => {
  return new Promise((resolve, reject) => {
    pool.getConnection((err, connection) => {
      if (err) {
        reject(err);
      }
      // begin transaction
      connection.beginTransaction((err) => {
        if (err) {
          reject(err);
        }
        const list = [];
        arr.map((item) => {
          const temp = (cb) => {
            const { sql, params } = item;
            const query = connection.query(sql, params, (error, rows, fields) => {
              if (error) {
                connection.rollback(() => {
                  reject(error);
                });
              } else {
                return cb(null, JSON.parse(JSON.stringify(rows)));
              }
            });
            // print SQL
            logger.info('SQL:', query.sql);
          };
          list.push(temp);
        });
        // promise.all()
        async.series(list, (error, result) => {
          if (error) {
            connection.rollback(() => {
              console.log('transaction error');
              connection.release();
              reject(error);
            });
          } else {
            // commit
            connection.commit((error) => {
              if (error) {
                connection.rollback(() => {
                  connection.release();
                  reject(error);
                });
              } else {
                connection.release();
                resolve(result);
              }
            });
          }
        });
      });
    });
  });
};

/**
 * stream query sql
 * @param sql
 * @param val
 */
const execStreamQuery = (sql: string, val: any = ''): Promise<any> => {
  return new Promise((resolve, reject) => {
    pool.getConnection((err, connection) => {
      const query = connection.query(sql, val);
      const data = [];
      query
        .on('error', (err) => {
          // console.log('读取数据失败，错误信息为：' + err.message);
          // process.exit();
          reject(err);
        })
        // fields当接收到该表中的所有字段时触发
        .on('fields', (fields) => {
          // console.log('fields字段：', fields);
        })
        // result当接收到该表中的一条数据时触发
        .on('result', (row) => {
          // 暂停读取后续数据
          connection.pause();
          const _row = JSON.parse(JSON.stringify(row));
          data.push(_row);
          // 恢复读取后续数据
          connection.resume();
        })
        // end当接收完该表中的所有数据时触发
        .on('end', () => {
          resolve(data);
          connection.release();
        });
    });
  });
};

/**
 *
 await transaction(async (connection, exec) => {
  await connection.query('...');
  await connection.query('...');
 });
 * @param callback
 */
const transaction = async (callback) => {
  const connection = await connect();
  await connection.beginTransaction();
  const execute = (sql: string, val: any = ''): Promise<any> => {
    return new Promise((resolve, reject) => {
      const query = connection.query(sql, val, (err, results, fields) => {
        if (err) {
          reject(err);
        } else {
          const data = JSON.stringify(results);
          resolve(JSON.parse(data));
        }
      });
      logger.info('\n SQL----->', query.sql);
    });
  };
  try {
    await callback(connection, execute);
    await connection.commit();
    logger.info('transaction commit');
  } catch (err) {
    logger.info('transaction rollback', err);
    await connection.rollback();
    throw err;
  } finally {
    connection.release();
  }
};

/**
 * query multiple sql at the same time
 * @param sqlArr
 * eg:
 sqlArr = ['SELECT 1', 'SELECT 2']
 results:
 console.log(results[0]); // [{1: 1}]
 console.log(results[1]); // [{2: 2}]
 */
const multiExec = async (sqlArr: string[]): Promise<any> => {
  const dbConn = handleConnect();
  return new Promise((resolve, reject) => {
    const sql = sqlArr.join(';');
    // dbConn
    dbConn.query(sql, function (error, results, fields) {
      if (error) {
        reject(error);
      } else {
        const arr = results.length ? results.map((o) => JSON.parse(JSON.stringify(o))) : [];
        resolve(arr);
      }
    });
  });
};

export { dbConn, exec, connect, execTransaction, pool, execStreamQuery, transaction, handleConnect, multiExec };

```
### 流式写入文件
```javascript
import fs from 'fs';
import { v4 as uuid } from 'uuid';
import util from 'util';
import stringify from 'csv-stringify';
import stream, { pipeline } from 'stream';
import { exec, handleConnect, dbConn } from '../utils/db';
/**
 * return file path according to stream query
 * @param params
 * eg: 需要开始mysql允许多sql查询开关，multipleStatements 为true
 *
 let sqlCount = `SELECT count(*) AS total FROM office_visitors WHERE residence_uuid = '296f7ec3-c835-4a2b-be38-8fb9348ffb97' `;
 let sql = `SELECT * FROM office_visitors WHERE residence_uuid = '296f7ec3-c835-4a2b-be38-8fb9348ffb97' `
 const option = {
    sql,
    sqlCount,
    maxLimit: 7000,
    functionName: "visitor",
    handleRow: (row) => {
      return {
        name: row.name ? '"' + row.name.replace(/\r/ig, '').replace(/\n/ig, '') + '"' : "",
      };
    },
    headerColumns: {
      name: "VISITOR",
    },
  };
 */
export const getStreamQueryFilePath = async (params: {
  sql: string;
  sqlCount: string;
  headerColumns: Record<string, any>;
  handleRow: any;
  functionName?: string;
  maxLimit?: number;
}): Promise<string> => {
  let fileNamePath = '';
  try {
    const { sqlCount, headerColumns, maxLimit, handleRow, functionName } = params;
    let sql = params.sql;
    //
    const total = await exec(sqlCount);
    const totalNum = total.length ? total[0]['total'] : 0;

    // check directory exist
    // check directory exist
    const downDirectory = "download";
    const makeDir = util.promisify(fs.mkdir);
    if (!fs.existsSync(downDirectory)) {
      await makeDir(downDirectory);
    }
    const _uuid = uuid();
    const filePath = downDirectory + `/${functionName}-${_uuid}.csv`;

    // create write stream
    const writeStream = fs.createWriteStream(filePath, { encoding: 'utf8', flags: 'a' });
    // set UTF8
    const BOM = '\ufeff';
    writeStream.write(BOM);
    // write header
    const columns = Object.keys(headerColumns).map((o) => {
      return { key: o };
    });
    stringify([headerColumns], { columns }, function (err, data) {
      writeStream.write(data);
    });

    // set max export records number
    const maxNumber = maxLimit || 1;

    // 分步查询
    const pSize = 5000;
    const sqlArr = [];
    if (totalNum >= maxNumber) {
      const number = Math.ceil(totalNum / maxNumber);
      const len = number >= 6 ? 6 : number;
      const sqlBak = sql;
      for (let i = 0; i < len; i++) {
        const offset = i * pSize;
        sql += ` LIMIT ${offset}, ${pSize} `;
        sqlArr.push(sql);
        sql = sqlBak;
      }
    } else {
      sqlArr.push(sql);
    }

    // pipe stream
    const transformStream = new stream.Transform({
      objectMode: true,
      transform: function transformer(row, encoding, callback) {
        const obj = JSON.parse(JSON.stringify(row));
        const value = handleRow(obj) || {};
        const val = {
          ...obj,
          ...value,
        };
        this.push(val);
        callback(null);
      },
    });
    // batch query
    const dbConn = handleConnect();
    const conn = dbConn.query(sqlArr.join(';'));
    // const conn = dbConn.query(sql);
    conn
      .stream({ highWaterMark: 16 })
      .pipe(transformStream)
      .pipe(
        stringify({
          header: false,
          columns: headerColumns,
        })
      )
      .pipe(writeStream)
      .on('result', function (row) {
        // const obj = JSON.parse(JSON.stringify(row));
      })
      .on('end', function () {
        //
      })
      .on('error', (error) => {
        //
      });

    // write
    const finish = await new Promise((resolve, reject) => {
      writeStream
        .on('finish', function () {
          // console.log('finish');
          resolve(true);
        })
        .on('error', function () {
          resolve(false);
        });
    });
    fileNamePath = finish ? filePath : '';
  } catch (err) {
    console.log(err)
  }
  return fileNamePath;
};

```
```javascript
import mysql, { PoolConfig, Connection, FieldInfo, MysqlError } from 'mysql';
import { v4 as uuid } from 'uuid';
import moment from 'moment';
import { exec, connect } from '../utils/db';
import csvstringify from 'csv-stringify';
import fs from 'fs';
import util from 'util';
import stream, { pipeline } from 'stream';

export const writeFile = async (): Promise<string> => {
  // office query
  let sql = 'SELECT * FROM big_table';
  const sqlCount = 'SELECT count(*) FROM big_table';
  const sitename = 'Hello';

  // get total records number
  let totalNum = 0;
  const total = await exec(sqlCount);
  if (total.length) {
    totalNum = total[0]['total'];
  }

  // check directory exist
  const downDirectory = 'download';
  const makeDir = util.promisify(fs.mkdir);
  if (!fs.existsSync(downDirectory)) {
    await makeDir(downDirectory);
  }

  // pipe
  const transformStream = new stream.Transform({
    objectMode: true,
    transform: function transformer(row, encoding, callback) {
      // do something
      const _row = JSON.parse(JSON.stringify(row));
      //
      const getTimestamp = (str: string): number => {
        // eg: str = "2020-05-17 13:59:59"
        return new Date(str.replace(/-/g, '/')).getTime() / 1000; // (seconds)
      };
      const val = {
        ..._row,
        user_name: _row.user_name || 'Stranger',
        sign_time: moment
          .unix(getTimestamp(_row.sign_time) * 1)
          .utcOffset(480)
          .format('YYYY-MM-DD (ddd) hh:mm:ss A'),
      };
      this.push(val);
      callback(null);
    },
  });

  const _uuid = uuid();
  const filePath = downDirectory + `/${_uuid}.csv`;

  // create write stream
  const writeStream = fs.createWriteStream(filePath, { encoding: 'utf8', flags: 'a' });
  const BOM = '\ufeff';
  writeStream.write(BOM);
  // write first line
  csvstringify(
    [{ a: 'SITENAME', b: sitename }],
    {
      columns: [{ key: 'a' }, { key: 'b' }],
    },
    function (err, data) {
      writeStream.write(data);
    }
  );

  // set max export records number
  const maxNumber = 40000;
  if (totalNum > maxNumber) {
    sql += ` LIMIT 0, ${maxNumber}`;
  }

  let totalRecords = 0;
  try {
    //
    const connection = await connect();
    const conn = connection.query(sql);
    const dbPromise: { status: boolean; num?: number } = await new Promise((resolve, reject) => {
      conn
        .on('result', function (row) {
          // every row
          ++totalRecords;
          // logger.info(row);
        })
        .stream({ highWaterMark: 10 })
        .on('end', function () {
          // all rows done
          // console.log('dbconn end', totalRecords);
          resolve({ status: true, num: totalRecords });
        })
        .pipe(transformStream)
        .pipe(
          csvstringify({
            header: true,
            columns: {
              user_name: 'NAME',
              job_number: 'STAFF ID',
              did: 'DEVICE ID',
              block: 'DEPARTMENT',
              ic_number: 'IC NUMBER',
              mobile: 'MOBILE NUMBER',
              passport_number: 'PASSPORT NUMBER',
              body_temperature: 'TEMPERATURE',
              hdf_status: 'HDF',
              health_status: 'Health Code',
              sign_time: 'SCAN TIME',
            },
          })
        )
        .pipe(writeStream)
        .on('error', (error) => {
          // handle stream write error
          console.log('destory', error);
          resolve({ status: false, num: totalRecords });
          conn.destroy();
        });
    });

    const finishPromise: { status: boolean; filePath?: string } = await new Promise((resolve, reject) => {
      writeStream
        .on('finish', function () {
          resolve({ status: true, filePath: filePath });
        })
        .on('error', function () {
          resolve({ status: false });
        });
    });

    if (finishPromise.status && dbPromise.status) {
      return filePath;
    } else {
      console.log('finishPromise and dbPromise error: ', finishPromise, dbPromise, totalNum, totalRecords);
    }
  } catch (err) {
    console.log('catch', err);
    return '';
  }
};
// read file
const exportCsv1 = async () => {
  const filePath = await writeFile();
  const fileToBuffer = (filename, cb) => {
    const readStream = fs.createReadStream(filename);
    const chunks = [];
    readStream.on('error', (err) => {
      // handle error
      return cb(err);
    });
    // Listen for data
    readStream.on('data', (chunk) => {
      chunks.push(chunk);
    });
    readStream.on('end', () => {
      console.log('read end');
      // logger.info(chunks.toString());
    });
    // file unlink after read done
    readStream.on('close', () => {
      fs.unlink(filePath, () => {
        // console.log('删除成功');
      });
      return cb(null, Buffer.concat(chunks));
    });
  };
  //
  fileToBuffer(filePath, (err, buf) => {
    // res is express response
    // res.set('Content-Disposition', 'attachment; filename=' + `access-records.csv`);
    // res.end(buf);
  });
};

```


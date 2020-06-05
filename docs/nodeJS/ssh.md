# ssh2

公司项目提测走ci，但是需要把ci服务器上的代码拷贝到补丁服务器上，所以需要用到ssh2

## js代码

看代码吧

```js
let Client = require('ssh2').Client
let conn = new Client()

// 建立连接
conn.on('ready', function () {
  conn.sftp(function (err, sftp) {
    if (err) throw err
    // do something
  })
}).connect({
  host: process.env.host,
  port: process.env.port,
  username: process.env.username,
  password: process.env.password
})

// conn.exec可以执行命令
conn.exec('命令写在这里', function (err, stream) {
 if (err) throw err
  stream.on('close', function (code, signal) {
    console.log('Stream 1:: close :: code: ' + code + ', signal: ' + signal)
    conn.end()
  }).on('data', function (data) {
    console.log(data + '')
  }).stderr.on('data', function (data) {
    console.log('STDERR1: ' + data)
  })
})
```

还有一部分值得记录下，循环创建文件夹

```js
function mkdirs (sftp, dirname, callback) {
  sftp.exists(dirname, function (exists) {
    if (exists) {
      callback()
    } else {
      mkdirs(sftp, path.dirname(dirname), function () {
        sftp.mkdir(dirname, callback)
      })
    }
  })
}
```

1. 当前目录下
启动一个端口号12345的jsonserver 服务器 文件
地址：dataserver目录下的data.json文件
```
json-server  -p 12345 dataserver/data.json

//  执行成功后显示
 \{^_^}/ hi!

  Loading dataserver/data.json
  Done

  Resources
  http://localhost:2000/users

  Home
  http://localhost:2000
```
data.json文件内容如下
```
{
  "users": [
    {
      "id": 1,
      "name": "lzj",
      "phone": "11111",
      "address": "beijing"
    },
    {
      "id": 2,
      "name": "lzj",
      "phone": "11111",
      "address": "beijing"
    },
    {
      "id": 3,
      "name": "lzj",
      "phone": "11111",
      "address": "beijing"
    },
    {
      "id": 4,
      "name": "lzj",
      "phone": "11111",
      "address": "beijing"
    },
    {
      "id": 5,
      "name": "lzj",
      "phone": "11111",
      "address": "beijing"
    }
  ]
}

```

2.浏览器中输入对应的地址出现json数据:http://localhost:12345/users
```
[
  {
    "id": 1,
    "name": "lzj",
    "phone": "11111",
    "address": "beijing"
  },
  {
    "id": 2,
    "name": "lzj",
    "phone": "11111",
    "address": "beijing"
  },
  {
    "id": 3,
    "name": "lzj",
    "phone": "11111",
    "address": "beijing"
  },
  {
    "id": 4,
    "name": "lzj",
    "phone": "11111",
    "address": "beijing"
  },
  {
    "id": 5,
    "name": "lzj",
    "phone": "11111",
    "address": "beijing"
  }
]
```
3. 配合axios模拟接口取数(不需要考虑跨域问题，jsonserver默认可以跨域)
```
created() {
    axios
      .get('http://localhost:12345/users')
      .then(res => {
        console.log(res.data);
        this.userList.push(...res.data);
      })
      .catch(e => {
        console.log(e);
      });
  }
```
3. 配合axios删除数据axios.delete
```
// vue-script -> methods
deleUser(e) {
      // 询问是否删除
      MessageBox.confirm('是否真的要删除', '删除提醒')
        .then(() => {
          // 删除数据
          axios.delete('http://localhost:12345/users/' + e)
            .then(res => {
              // 获取当前数据在数组中的位置
              // const delIndex = this.userList.findIndex(item => item.id === id);
              // if (delIndex >= 0) {
              //   this.userList.splice(delIndex - 1, 1);
              // }
              console.log(e);
              this.delIndex = this.userList.findIndex(item => item.id === e);
              console.log(this.delIndex);
              // 用splice方法切除数组
              this.userList.splice(this.delIndex, 1);
              Message.info('已删除');
            })
            .catch(esg => {
              Message.info('删除失败' + esg);
            });
        })
        .catch(() => {
          Message.info('取消删除了');
        });
    }
```
4. el-form配合axios添加数据
```
//vue-html
<el-dialog title="添加用户" :visible.sync="dialogVisible" width="70%" :before-close="handleClose">
      <h3>添加用户信息</h3>
      <hr>
      <el-form :model="form">
        <el-form-item label="用户名" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话" :label-width="formLabelWidth">
          <el-input v-model="form.phone" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="地址" :label-width="formLabelWidth">
          <el-input v-model="form.address" autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="userAdd">添 加</el-button>
      </span>
    </el-dialog>

// vue-script -> methods
  userAdd() {
    axios.post('http://localhost:12345/users/', this.form)
        .then(res => {
          Message.info('已添加');
          this.userList.push(res.data);
          this.dialogVisible = false;
        })
        .catch(e => {
          Message.info('添加失败' + e);
        });
    },
    handleClose() {
      this.dialogVisible = false;
    },
    showDiolag() {
      this.dialogVisible = true;
    },
```
5. 使用axios修改数据
```
//vue-html
<tr class="row">
    <template v-if="isEdit">
      <td v-for="(item, index) in propsArray"
          :key="index">
        <template v-if="item === 'id'">
          {{userData[item]}}
        </template>
        <template v-else>
          <input type="text"
               v-model="userData[item]">
        </template>
      </td>
      <td>
        <a @click="cancelEdit"
           href="javascript:">取消</a>
        &nbsp;&nbsp;
        <a @click="saveUser()"
           href="javascript:">保存</a>
      </td>
    </template>
    <template v-else>
      <td v-for="(item, index) in propsArray"
          :key="index">{{userData[item]}}</td>
      <td>
        <a @click="deleUser()"
           href="javascript:">删除</a>
        &nbsp;&nbsp;
        <a @click="isEdit=true"
           href="javascript:">编辑</a>
      </td>
    </template>

  </tr>
// vue-script -> methods
saveUser() {
      this.isEdit = false;
      axios.put(`http://localhost:12345/users/${this.rowData.id}`, this.userData)
        .then(res => {
          this.$emit('update:user', { ...this.userData });
        })
        .catch(e => {
          Message.info('修改失败' + e);
        });
    },
```





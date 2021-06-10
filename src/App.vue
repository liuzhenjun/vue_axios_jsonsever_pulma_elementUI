<template>
  <div id="app">
    <h1>用户列表</h1>
    <hr>
    <el-button @click="showDiolag"
               type="success">添加</el-button>
    <hr>
    <table class="table is-striped is-bordered is-hoverable">
      <thead>
        <tr>
          <td>编号</td>
          <td>姓名</td>
          <td>电话</td>
          <td>地址</td>
          <td>编辑</td>
        </tr>
      </thead>
      <tbody>
        <row v-for="item in userList" @update:user="saveUser" @delete:id="deleUser($event)" :key="item.id" :propsArray="['id', 'name', 'phone', 'address']" :rowData="item"></row>
      </tbody>
    </table>
    <el-dialog title="添加用户"
               :visible.sync="dialogVisible"
               width="70%"
               :before-close="handleClose">
      <h3>添加用户信息</h3>
      <hr>
      <el-form :model="form">
        <el-form-item label="用户名"
                      :label-width="formLabelWidth">
          <el-input v-model="form.name"
                    autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="电话"
                      :label-width="formLabelWidth">
          <el-input v-model="form.phone"
                    autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="地址"
                      :label-width="formLabelWidth">
          <el-input v-model="form.address"
                    autocomplete="off"></el-input>
        </el-form-item>
      </el-form>
      <span slot="footer"
            class="dialog-footer">
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary"
                   @click="userAdd">添 加</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from 'axios';
import { Message, MessageBox } from 'element-ui';
import row from './components/row';
export default {
  name: 'app',
  data() {
    return {
      value2: 30,
      userList: [],
      dialogVisible: false,
      formLabelWidth: '120px',
      delIndex: '',
      form: {
        id: '',
        name: '',
        phone: '',
        address: ''
      }
    };
  },
  components: {
    row: row
  },
  methods: {
    saveUser(e) {
      console.log(e);
      const rowIndex = this.userList.findIndex(item => item.id === e.id);
      this.userList.splice(rowIndex, 1, e);
      Message.info('编辑成功');
    },
    userAdd() {
      this.form.id = Date.now();
      axios.post('http://localhost:12345/users/', this.form)
        .then(res => {
          Message.info('已添加');
          this.userList.push(res.data);
          this.dialogVisible = false;
          this.form = {
            id: '',
            name: '',
            phone: '',
            address: ''
          };
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
    // 删除用户
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
  },
  created() {
    axios
      .get('http://localhost:12345/users')
      .then(res => {
        console.log(res.data);
        this.userList.push(...res.data);
      })
      .catch(e => {
        Message.info('加载失败' + e);
      });
  }
};
</script>

<style lang="scss" scoped>
#app{
  padding: 20px;
}
</style>>

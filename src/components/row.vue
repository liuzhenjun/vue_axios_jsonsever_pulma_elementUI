<template>
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
</template>

<script lang="ts">
import axios from 'axios';
import { Message } from 'element-ui';
export default {
  name: 'row',
  props: ['rowData', 'propsArray'], // 属性不能修改
  data() {
    return {
      isEdit: false,
      // userData: { ...this.rowData } // 第一种方法
      userData: Object.assign({}, this.rowData) // 第二种方法
    };
  },
  methods: {
    cancelEdit() {
      Object.assign(this.userData, this.rowData);
      this.isEdit = false;
    },
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
    deleUser() {
      this.$emit('delete:id', this.rowData.id);
    }
  }
};
</script>

<style scoped lang="scss">
</style>

<template>
  <el-dialog
    title="添加用户"
    :visible.sync="dialogFormVisible"
    @close="dialogClose"
    width="40%">
    <!--内容主体区域-->
    <el-form :model="addForm" :rules="addRules" ref="addFormRef" label-width="80px" class="demo-ruleForm">
      <el-form-item label="用户名" prop="username">
        <el-input v-model="addForm.username"></el-input>
      </el-form-item>
      <el-form-item label="昵称" prop="nickname">
        <el-input v-model="addForm.nickname"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="password">
        <el-input v-model="addForm.password" show-password></el-input>
      </el-form-item>
      <el-form-item label="邮箱" prop="email">
        <el-input v-model="addForm.email"></el-input>
      </el-form-item>
      <el-form-item label="角色" prop="roleId">
        <el-select v-model="addForm.roleId" multiple placeholder="请选择角色">
          <el-option
            v-for="item in roleData"
            :key="item.id"
            :label="item.roleName"
            :value="item.id">
          </el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="是否禁用" prop="status">
        <el-switch
          v-model="addForm.status"
          active-color="#13ce66"
          inactive-color="#ff4949"
          active-value="1"
          inactive-value="0">
        </el-switch>
      </el-form-item>
      <el-form-item label="性别" prop="sex">
        <el-radio-group v-model="addForm.sex">
          <el-radio label="0">男性</el-radio>
          <el-radio label="1">女性</el-radio>
          <el-radio label="2">保密</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
    <el-button @click="resetForm">重置</el-button>
<!--    <el-button type="info" @click="addDialogVisible = false">取 消</el-button>-->
    <el-button type="primary" @click="handleSubmit">确 定</el-button>
  </span>
  </el-dialog>
</template>
<script>
import { RandomNumBoth } from '../../../util'
export default {
  name: 'UserAdd',
  props: {
    addDialogVisible: {
      default: false
    }
  },
  data () {
    // 校验邮箱
    let checkEmail = (rule, val, callback) => {
      const regEmail = /^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(\.[a-zA-Z0-9_-])+/
      if (regEmail.test(val)) {
        return callback()
      }
      callback(new Error('请输入合法的邮箱!'))
    }
    return {
      addForm: {
        username: 'test' + RandomNumBoth(1, 100),
        password: '1234qwer',
        nickname: '',
        email: '1@qq.cc',
        status: '1',
        sex: '2',
        roleId: '1'
      },
      addRules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 5, max: 20, message: '长度在 5 到 20 个字符', trigger: 'blur' }
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 20, message: '长度在 6 到 20 个字符', trigger: 'blur' }
        ],
        email: [
          { validator: checkEmail, trigger: 'blur' }
        ],
        // roleId: [
        //   { required: true, message: '请选择角色', trigger: 'change' }
        // ],
        status: [
          { required: true, message: '请选择活动禁用状态', trigger: 'change' }
        ]
      },
      dialogFormVisible: false,
      roleData: [],
      roleInfo: {
        roleName: ''
      }
    }
  },
  watch: {
    addDialogVisible(newVal, oldVal) {
      this.dialogFormVisible = newVal
    }
  },
  mounted() {
    this.getRoleList()
  },
  methods: {
    // 获取角色列表
    getRoleList() {
      this.$get('/system/role', this.roleInfo).then(res => {
        if (res.data.code !== 200) return this.$msg.error('获取角色列表失败！')
        this.roleData = res.data.data.list
      })
    },
    handleSubmit() {
      let that = this
      this.$refs.addFormRef.validate(valid => {
        if (valid) {
          this.$post('/system/user', this.addForm).then(res => {
            if (res.status !== 200) {
              that.$emit('close')
              return that.$msg.error('新增用户失败!')
            }
            that.$emit('success', this.addForm.password)
            that.resetForm()
          })
        } else {
          return false
        }
      })
    },
    resetForm() {
      this.$refs.addFormRef.resetFields()
    },
    dialogClose() {
      this.$emit('close')
    }
  }
}
</script>

<style lang="less" coped>

</style>

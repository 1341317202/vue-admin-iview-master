<template>
  <div class="login">
    <div class="bg">
    </div>
    <Modal class="login-modal" v-model="loginModal" width="360" :closable="false" :mask-closable="false">
      <div>
        <h2 style="margin:10px;color:#abcdef;text-align:center">
          <img :src="require('@/assets/img/宁静致远.png')" alt="" height="50"></h2>
        <Form ref="formValidate" :model="formValidate" :rules="ruleValidate">
          <Form-item prop="name">
            <Input v-model="formValidate.name" placeholder="请输入姓名"></Input>
          </Form-item>
          <Form-item prop="password">
            <Input v-model="formValidate.password" @keyup.native.enter="handleSubmit('formValidate')" type="password" placeholder="请输入密码"></Input>
          </Form-item>

          <Row>
            <Col span="12">
            <Checkbox-group v-model="formValidate.remember">
              <Checkbox label="记住我"></Checkbox>
            </Checkbox-group>
            </Col>
            <Col span="12">
            <a style="float:right" @click="toRegister">新用户注册</a>
            </Col>
          </Row>

        </Form>
      </div>
      <div slot="footer">
        <Button type="primary" size="large" long :loading="modal_loading" @click="handleSubmit('formValidate')">登录
        </Button>
        <small style="text-align: center;display:block" class="subText">©copyright by Artiely</small>
      </div>
    </Modal>
  </div>
</template>
<script>
  /* eslint-disable */ //压制警告
  import Cookies from 'js-cookie'
  import axios from 'axios'
  export default {
    name: 'login',
    data () {
      return {
        loginModal: true,
        modal_loading: false,
        formValidate: {
          name: '',
          password: ''
        },
        ruleValidate: {
          name: [
            {required: true, message: '姓名不能为空', trigger: 'blur'}
          ],
          password: [
            {required: true, message: '密码错误', trigger: 'blur'}
          ]

        }
      }
    },
    methods: {
      handleSubmit (name) { // login
        this.$refs[name].validate((valid) => {
          this.modal_loading = true
          setTimeout(() => {
            this.modal_loading = false
            if (valid) {


              axios.post(this.$hosts.DEVELOPMENT_HOST + '/user/login', {
                username: this.formValidate.name,
                password: this.formValidate.password,

              })
                .then((response) => {
                  console.log(response);

                  if(response.data.status){
                    this.$Message.success(response.data.message);
                    Cookies.set('token', this.formValidate.password);
                    this.$router.push('/index')

                  }else{

                    this.$Message.error(response.data.message);
                  }


                }).catch((error) => {
                console.log(error);

              });


            } else {
              this.$Message.error('表单验证失败!')
              this.$Notice.warning({
                title: '登录提示',
                desc: '请输入正确的用户名或密码'
              })
            }
          }, 2000)
        })
      },
      toRegister () {
        this.$router.push('/register')
      }
    }
  }
</script>



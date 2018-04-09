<template>
  <div class="translate">
    <el-form ref="form" :model="form" label-width="80px" :rules="rules" label-position="top">
      <el-form-item prop="inputText" label="翻译前">
        <el-input type="textarea" v-model="form.inputText"></el-input>
      </el-form-item>
      <el-form-item prop="transType" label="语言种类">
        <el-radio-group v-model="form.transType">
          <el-radio label="中文"></el-radio>
          <el-radio label="英文"></el-radio>
          <el-radio label="韩文"></el-radio>
          <el-radio label="日语"></el-radio>
        </el-radio-group>
      </el-form-item>
      <el-form-item class="submitbtn">
        <el-button type="primary" @click="submitForm()">翻译</el-button>
        <el-button @click="resetForm('form')">清除</el-button>
      </el-form-item>
      <el-form-item label="翻译后">
        <el-input type="textarea" v-model="form.responText" readonly></el-input>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
    import MD5 from '../md5.js';

    export default {
        data(){
          return{
            form:{
              inputText:'',
              responText:'',
              transType:''
            },
            rules:{
              inputText:[
                { required: true, message: '请输入翻译内容', trigger: 'blur' }
              ],
              transType:[
                { required: true, message: '请选择语言种类', trigger: 'change' }
              ]
            }
          }
        },
        methods:{
          submitForm(){
            this.$refs.form.validate((valid) => {
              if (valid) {//表单校验成功
                const reqdata = {
                  q:this.form.inputText,
                  from:'auto',
                  appid:'20180329000141131',//百度翻译用户id
                  salt: new Date().getTime()
                }
                switch(this.form.transType){
                  case '中文':
                    reqdata.to = 'zh';
                    break;
                  case '英文':
                    reqdata.to = 'en';
                    break;
                  case '韩文':
                    reqdata.to = 'kor';
                    break;
                  case '日语':
                    reqdata.to = 'jp';
                    break;
                }
                const transkey  = 'OXNjYo8ayCkIWx6eyeXU';//百度翻译用户key
                const API_PROXY = 'https://bird.ioliu.cn/v1/?url=';
                reqdata.sign = MD5(reqdata.appid + reqdata.q + reqdata.salt + transkey);
                axios.get(`/api/trans/vip/translate?appid=${reqdata.appid}&q=${reqdata.q}&from=${reqdata.from}&to=${reqdata.to}&salt=${reqdata.salt}&sign=${reqdata.sign}`)
                  .then((reponse) => {
                  this.form.responText = reponse.data.trans_result[0].dst;
                }).catch((error) => {
                    console.log(error);
                })
              } else {
                return false;
              }
            });
          }

        }
    }
</script>

<style scoped>

</style>

<template>
    <div class="named">
      <el-form ref="form" :model="form" label-width="80px" :rules="rules" label-position="top">
        <el-form-item prop="inputText" label="中文变量输入">
          <el-input type="textarea" v-model="form.inputText" @keyup.enter.native="submitForm()"></el-input>
        </el-form-item>
        <el-form-item label="命名方式" prop="nameType">
          <el-radio-group v-model="form.nameType">
            <el-radio label="驼峰式命名"></el-radio>
            <el-radio label="下划线命名"></el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item class="submitbtn">
          <el-button type="primary" @click="submitForm()" :loading="loading">命名</el-button>
          <el-button @click="resetForm('form')">清除</el-button>
        </el-form-item>
        <el-form-item label="英文变量输出">
          <el-input type="textarea" v-model="form.responText" readonly></el-input>
        </el-form-item>
      </el-form>
    </div>
</template>

<script>
    import MD5 from '../md5.js';
    export default {
      data(){
        var checkInput = (rule, value, callback) => {
          if (!value || !/^[\u4e00-\u9fa5]{1,}/.test(value)) {
            return callback(new Error('请输入中文喔！'));
          }else{
            callback();
          }
        };
        return {
          form:{
            inputText:'',
            responText:'',
            nameType:''
          },
          loading:false,
          rules:{
            inputText:[
              { validator: checkInput, trigger: 'blur' }
            ],
            nameType:[
              { required: true, message: '请选择命名方式', trigger: 'change' }
            ],
          }

        }
      },
      methods:{
        submitForm(){
          this.$refs.form.validate((valid) => {
            if (valid) {
              //表单校验成功
              this.loading = true;
              const reqdata = {
                q:this.form.inputText,
                from:'zh',
                to:'en',
                appid:'20180329000141131',//百度翻译用户id
                salt: new Date().getTime()
              }
              const transkey  = 'OXNjYo8ayCkIWx6eyeXU';//百度翻译用户key
              const API_PROXY = 'https://bird.ioliu.cn/v1/?url=';
              reqdata.sign = MD5(reqdata.appid + reqdata.q + reqdata.salt + transkey);
              axios.get(`/api/trans/vip/translate?appid=${reqdata.appid}&q=${reqdata.q}&from=${reqdata.from}&to=${reqdata.to}&salt=${reqdata.salt}&sign=${reqdata.sign}`)
                .then((reponse) => {
                let data = this.nameSelect(reponse.data.trans_result[0].dst);
                this.form.responText = data;
                this.loading = false;
              }).catch((error) => {
                  console.log(error);
                  this.loading = false;
              })
            } else {
              this.loading = false;
              return false;
            }
          });
        },
        resetForm(formName){
          this.$refs[formName].resetFields();
          this.form.inputText = '';
          this.form.responText = '';
        },

        //驼峰式
        camelCase(str){
          let arrStr = str.split(' ');
          arrStr = arrStr.map((value,index)=>{
            //前面词语小写后面大写
            return index === 0 ? value.substring(0,1).toLocaleLowerCase()+value.substring(1) : value.substring(0,1).toUpperCase()+value.substring(1);
          })
          return arrStr.join('')
        },

        //下划线
        pascalCase(str){
          let arrStr = str.split(' ');
          arrStr = arrStr.map((value,index)=>{
            return value.substring(0,1).toLocaleLowerCase()+value.substring(1);
          })
          return arrStr.join('_')
        },

        nameSelect(str){
          const camelCase = '驼峰式命名'
          if (this.form.nameType === camelCase) {
            return this.camelCase(str);
          }else{
            return this.pascalCase(str);
          }
        }

      }
    }
</script>

<style lang="scss" scoped>
  .named{
    .el-form{
      .el-textarea{
        & /deep/ textarea{
          resize: none;
        }
      }
      .submitbtn{
        display: flex;
        justify-content: center;
      }
    }


  }


</style>

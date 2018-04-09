<template>
  <div id="app">
    <el-container>
      <el-header>变量命名与翻译</el-header>
      <el-main>
        <el-tabs type="border-card">
          <el-tab-pane>
            <span slot="label"><i class="el-icon-edit"></i>变量命名</span>
            <named/>
          </el-tab-pane>
          <el-tab-pane>
            <span slot="label"><i class="el-icon-document"></i>内容翻译</span>
            <translate/>
          </el-tab-pane>
        </el-tabs>

      </el-main>
    </el-container>
  </div>
</template>

<script>
  import named from './components/named'
  import translate from './components/translate'
export default {

  components: {
    named,
    translate
  }
}
</script>

<style lang="scss" scoped>
#app {
  background:#E9EEF3;
  header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
    font-size: 24px;
    text-align: center;
  }
  main {
    width:800px;
    margin:0 auto;
    .title{
      display:flex;
      justify-content: center;
      padding:10px 0;
      button{

      }
    }
  }

}
</style>

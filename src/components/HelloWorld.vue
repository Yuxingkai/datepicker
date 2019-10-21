<template>
  <div class="hello">
    <span style="position: absolute;left: 20%">{{ value1 }}</span>
    <div style="width: 200px;position: absolute;top: 0;left: 50%">
      <el-date-picker
        v-model="value1"
        type="datetime"
        placeholder="选择日期时间">
      </el-date-picker>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      value1: ''
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->

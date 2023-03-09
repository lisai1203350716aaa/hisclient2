<template>
  <div>
    <div style="text-align: left">
      <el-button type="primary">挂号</el-button>
      <el-button>清空</el-button>
    </div>
    <el-descriptions title="基本信息" :column="3" border style="margin-top: 20px">
      <el-descriptions-item label="病历号">
        <el-input v-model="register.caseNumber" placeholder="请输入内容"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="姓名">
        <el-input v-model="register.RealName" placeholder="请输入姓名"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="性别">
        <el-select v-model="register.Gender" placeholder="请选择">
          <el-option
              v-for="item in register.register_gender"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="年龄">
          <el-input v-model="register.Age" placeholder="请输入" style="width: 100px"></el-input>
          <el-select v-model="register.AgeType" placeholder="请选择" style="width: 100px">
            <el-option
                v-for="item in register.register_age"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="出生日期">
          <el-date-picker
              v-model="register.BirthDate"
              type="date"
              placeholder="选择日期时间">
          </el-date-picker>
      </el-descriptions-item>
      <el-descriptions-item label="身份证号">
        <el-input v-model="register.IDnumber" placeholder="请输入"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="家庭住址">
        <el-input v-model="register.HomeAddress" placeholder="请输入"></el-input>
      </el-descriptions-item>
    </el-descriptions>
    <el-descriptions title="挂号信息" :column="4" border style="margin-top: 20px">
      <el-descriptions-item label="结算类别">
        <el-select v-model="register.settleID" placeholder="请选择">
          <el-option
              v-for="item in register.register_settleID"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="看诊日期">
        <el-date-picker
            disabled
            v-model="register.VisitDate"
            type="date"
            placeholder="选择日期时间">
        </el-date-picker>
      </el-descriptions-item>
      <el-descriptions-item label="午别" :span="2">
        <el-select disabled v-model="register.Noon" placeholder="请选择">
          <el-option
              v-for="item in register.register_Noon"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="挂号科室">
        <el-tag size="small">
          <el-select v-model="register.DeptID" placeholder="请选择">
            <el-option
                v-for="item in male"
                :key="item.value"
                :label="item.label"
                :value="item.value">
            </el-option>
          </el-select>
        </el-tag>
      </el-descriptions-item>
      <el-descriptions-item label="号别">
        <el-select v-model="register.RegistLeID" placeholder="请选择" @change="register_noon_change">
          <el-option
              v-for="item in register.register_RegistLeID"
              :key="item.sequence_no"
              :label="item.regist_name"
              :value="item.sequence_no">
          </el-option>
        </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="看诊医生">
        <el-select v-model="register.physician" placeholder="请选择">
          <el-option
              v-for="item in male"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-descriptions-item>
      <el-descriptions-item label="初始号额">
        <el-input v-model="register.totalNumber"  disabled placeholder="请输入" style="width: 100px"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="已用号额">
        <el-input v-model="register.usedNumber" placeholder="请输入" style="width: 100px"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="病历本">
        <el-switch
            v-model="register.isBook"
            active-color="#13ce66"
            inactive-color="#ff4949"
            @change="register_isBook_change"
        >
        </el-switch>
      </el-descriptions-item>
      <el-descriptions-item label="应收金额">
        <el-input v-model="register.money"  disabled placeholder="请输入" style="width: 100px"></el-input>
      </el-descriptions-item>
      <el-descriptions-item label="收费方式">
        <el-select v-model="register.method" placeholder="请选择">
          <el-option
              v-for="item in register.register_method"
              :key="item.value"
              :label="item.label"
              :value="item.value">
          </el-option>
        </el-select>
      </el-descriptions-item>
    </el-descriptions>


  </div>
</template>

<script>

export default {
  name: "onsite_registration",
  methods:{
    register_noon_change(noonValue){
      this.register.isBook = false;
      for(let item of this.register.register_RegistLeID){
          if(noonValue == item.sequence_no){
              this.register.totalNumber = item.regist_quota;
              this.register.money = item.regist_fee;
          }
      }
    },
    register_isBook_change(){
      if(this.register.isBook){
        this.register.money += 1;
      }else {
        this.register.money -= 1;
      }
    }
  },
  mounted() {
    //获取当前时间
    let nowDate = new Date();
    this.register.VisitDate = nowDate;
    //获取当前的小时
    let nowHours = nowDate.getHours();
    console.log(nowHours)
    if(nowHours <= 12){
      this.register.Noon = 'am';
    }else {
      this.register.Noon = 'pm';
    }
    this.$http.get("http://localhost:8082/getRegistLevelList").then(
        (res)=>{
          // alert(res.data)
          this.register.register_RegistLeID = res.data;
        }
    )
  },
  data(){
    return{
     register:{
       id:'',
       caseNumber:'',
       RealName:'',
       Gender:'',
       register_gender:[
         {
           value:'male',
           label:'男'
         },
         {
           value:'female',
           label:'女'
         },
       ],
       IDnumber:'',
       BirthDate:'',
       Age:'',
       AgeType:'',
       register_age:[
         {
           value:'year',
           label:'年'
         },
         {
           value:'month',
           label:'月'
         },
         {
           value:'day',
           label:'日'
         },
       ],
       HomeAddress:'',
       settleID:'',
       register_settleID:[
         {
           value:'xianjin',
           label:'现金'
         },
         {
           value:'yibao',
           label:'医保'
         },
         {
           value:'card1',
           label:'农合卡'
         },
       ],
       VisitDate:'',
       Noon:'',
       register_Noon:[
         {
           value:'am',
           label:'上午'
         },
         {
           value:'pm',
           label:'下午'
         },
       ],
       DeptID:'',
       UserID:'',
       RegistLeID:'',
       register_RegistLeID:[
         {
           value:'putong',
           label:'普通号'
         },
         {
           value:'zhuanjia',
           label:'专家号'
         },
       ],
       physician:'',
       totalNumber:'',
       usedNumber:'',
       isBook:false,
       money:'',
       method:'',
       register_method:[
         {
           value:'cash',
           label:'现金'
         },
         {
           value:'wechat',
           label:'微信'
         },
         {
           value:'zhifubao',
           label:'支付宝'
         },
         {
           value:'card',
           label:'医保卡'
         },
       ]
     }
    }
  }
}
</script>

<style scoped>

</style>

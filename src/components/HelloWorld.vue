<template>
  <div id="index">
    <el-card shadow="always" class="box-card">
      <div slot="header" class="clearfix">
      <span style="font-weight:500;font-size:25px;font-family:Microsoft YaHei;">防火墙禁用规则</span>
      <el-button type="primary" size="small" style="float:left; margin:3px;" @click="changehost()">设置控制器</el-button>
      <el-button type="danger" size="small" style="float:right; margin:3px;" @click="deleterule()">删除规则</el-button>
      <el-button type="success" size="small" style="float:right; margin:3px;"@click="dialogFormVisible = true">新增规则</el-button>    
      </div>

      <el-dialog title="新增规则" :visible.sync="dialogFormVisible" id="dialog">
      <el-form :model="form">
        <el-form-item label="规则名称" :label-width="formLabelWidth">
          <el-input v-model="form.name" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="源Mac" :label-width="formLabelWidth">
          <el-input v-model="form.srcMac" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="目的Mac" :label-width="formLabelWidth">
          <el-input v-model="form.desMac" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="源IP" :label-width="formLabelWidth">
          <el-input v-model="form.srcIP" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="目的IP" :label-width="formLabelWidth">
          <el-input v-model="form.desIP" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="协议" :label-width="formLabelWidth">
          <el-select v-model="form.IPpro" placeholder="请选择协议类型" style="display:block;">
            <el-option label="TCP" value="TCP"></el-option>
            <el-option label="UDP" value="UDP"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="源端口" :label-width="formLabelWidth">
          <el-input v-model="form.srcPort" autocomplete="off"></el-input>
        </el-form-item>
        <el-form-item label="目的端口" :label-width="formLabelWidth">
          <el-input v-model="form.desPort" autocomplete="off"></el-input>
        </el-form-item>
        
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="dialogFormVisible = false">取 消</el-button>
        <el-button type="primary" @click="addrule">确 定</el-button>
      </div>
    </el-dialog>`

      <el-alert type="success" effect="dark"  :closable="false">控制器IP:{{this.host}}</el-alert>

      <el-table
    ref="multipleTable"
    :data="tableData"
    tooltip-effect="dark"
    style="width: 100%"
    @selection-change="handleSelectionChange">
    <el-table-column
      type="selection"
      width="55">
    </el-table-column>
    <el-table-column
      label="规则名"
      width="120">
      <template slot-scope="scope">{{ scope.row.name }}</template>
    </el-table-column>
    <el-table-column
      prop="srcMac"
      label="源Mac"
      width="200">
    </el-table-column>
    <el-table-column
      prop="desMac"
      label="目的Mac"
      width="200"
      >
    </el-table-column>
    <el-table-column
      prop="srcIP"
      label="源IP"
      width="150">
    </el-table-column>
    <el-table-column
      prop="desIP"
      label="目的IP"
      width="150">
    </el-table-column>
    <el-table-column
      prop="IPpro"
      label="协议"
      width="120">
    </el-table-column>
    <el-table-column
      prop="srcPort"
      label="源端口"
      width="120">
    </el-table-column>
    <el-table-column
      prop="desPort"
      label="目的端口"
      width="120">
    </el-table-column>
   </el-table>
    
    </el-card>
    
  </div>
</template>

<script>
function setCookie(cname,cvalue){
      document.cookie = cname+"="+cvalue+";path=/";
    }
function delCookie(name)
{
var exp = new Date();
exp.setTime(exp.getTime() - 1);
var cval=getCookie(name);
if(cval!=null)
document.cookie= name + "="+cval+";expires="+exp.toGMTString()+";path=/";
}
function getCookie(cname)
{
  var name = cname + "=";
  var ca = document.cookie.split(';');
  for(var i=0; i<ca.length; i++) 
  {
    var c = ca[i].trim();
    if (c.indexOf(name)==0) return c.substring(name.length,c.length);
  }
  return "";
}


export default {
  name:"ok",
  data(){
    return {
      host:null,
      tableData:[],
      //   {
      //     name:"test",
      //     srcMac:"00:00:00:00:00:00",
      //     desMac:"00:00:00:00:00:00",
      //     srcIP:"192.158.12.3",
      //     desIP:"192.158.12.4",
      //     IPpro:"TCP",
      //     srcPort:625,
      //     desPort:65532,
      //   }
      // ], 
      multipleSelection: [],
      dialogFormVisible:false,
      form: {
        name:null,
      },
      formLabelWidth: '120px',
    };
  },
  methods: {
    handleSelectionChange(val) {
        this.multipleSelection = val;
        console.log(this.multipleSelection[0]);
        // console.log(val);
      },
    deleterule()
    {
      if(this.multipleSelection.length==0)
      {
        this.$message.error("所选为空");
        return ;
      }
      for(var i=0;i<this.multipleSelection.length;i++)
      {
        this.deletepost(this.multipleSelection[i]);
      }
    },
    deletepost(ex)//
    {
      console.log(ex);
      fetch("http://"+this.host+":8080/wm/testdemo/rule/json",{
            method:'DELETE',
            body:JSON.stringify(ex)
        }).then(response=>{
          if(response.ok)
          {
            this.$message.success('ok');
            setTimeout(this.$router.go(0),500);
            return ;
          }
          throw 'bad';
        }).catch(()=>{
            this.$message.error("删除失败")
        })
    },
    addrule()//新建一条规则
    {
        this.dialogFormVisible = false;
        // console.log(typeof this.form.name);
        var isnum = /^\d+$/.test(this.form.name);
        console.log(isnum);
        if(isnum==false)
        {
          // console.log("v");
          this.$message.error("规则名必须为数字！");
          this.form={};
          return ;
        }
        console.log(this.form);
        var mydata={};
        mydata.name=this.form.name;
        if(this.form.srcMac!=null)mydata.srcMac=this.form.srcMac;
        if(this.form.desMac!=null)mydata.desMac=this.form.desMac;
        if(this.form.srcIP!=null)mydata.srcIP=this.form.srcIP;
        if(this.form.desIP!=null)mydata.desIP=this.form.desIP;
        if(this.form.IPpro!=null)mydata.IPpro=this.form.IPpro;
        if(this.form.srcPort!=null)mydata.srcPort=this.form.srcPort;
        if(this.form.desPort!=null)mydata.desPort=this.form.desPort;
        fetch("http://"+this.host+":8080/wm/testdemo/rule/json",{
            method:'POST',
            body:JSON.stringify(mydata)
        }).then(response=>{
          if(response.ok)
          {
            this.$message.success('ok');
            setTimeout(this.$router.go(0),500);
            return ;
          }
          throw 'bad';
        }).catch(()=>{
            this.$message.error("添加失败")
        })
        this.form={};
    },
    changehost()
    {
      this.$prompt('请输入控制器IP', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
        }).then(({ value }) => {
          this.host=value;
          setCookie("host",value);
          this.$router.go(0); 
        }).catch(() => {
            // this.$router.go(0);   
        });
    },
    init()//初始化获取tableData
    {
      fetch("http://"+this.host+":8080/wm/testdemo/rule/json",{
            method:'GET',
        }).then(response=>{
          if(response.ok)
          {
            this.$message.success('ok');
            // setTimeout(this.$router.go(0),500)
            return response.json();
          }
          throw 'bad';
        }).then(res=>{
          this.tableData=res;//获取数据
        })
        .catch(()=>{
            this.$message.error("获取数据失败")
        })
    }
  },
  created:function()
  {
    this.host=getCookie("host");
    if(this.host=="")
    {
      this.$prompt('请输入控制器IP', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
        }).then(({ value }) => {
          this.host=value;
          setCookie("host",value);
        }).catch(() => {
            this.$router.go(0);   
        });
    }
    this.init();//获取tableData
  }
}
</script>

<style>
#index
{
  padding-top:3vh;
  width:100%;
  height:100%;
  position: relative;
}
.box-card
{
  width:70%;
  position: relative;
  left:15%;

}
#dialog
{
  width:100%;
  position: fixed;
  top:0%;
}
</style>
